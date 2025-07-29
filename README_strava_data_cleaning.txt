# Data Cleaning

This document outlines the data cleaning process applied to a Strava dataset in preparation for running data diagnostic analysis. The aim was to prepare cleaned, standardised, and relevant data.

---

##  Data Cleaning Workflow

1. **Import Data**
2. **Check for Duplicates**
3. **Standardize User Input**
4. **Remove Non-Relevant Data**
5. **Optimize Data Types**
6. **Create Time-Based Metrics**
7. **Remove Outliers**

---

## 1️ Import & Create Working Table

To preserve the raw data:

```sql
CREATE TABLE strava_working (LIKE strava INCLUDING ALL);
INSERT INTO strava_working SELECT * FROM strava;
```

---

## 2️⃣ Check for Duplicates

We checked for duplicate entries based on a combination of assumed unique fields:

```sql
SELECT *
FROM (
  SELECT *,
    ROW_NUMBER() OVER (PARTITION BY activity_id, activity_date, activity_name, activity_type, distancekm) AS row_nm
  FROM strava_working
) AS dupe
WHERE row_nm > 1;
```

 **No duplicates were found.**

---

## 3️⃣ Standardize `activity_name`

Strava-generated names like `"Afternoon Run"` are consistent, but user-inputted names vary.

### Example:

```sql
SELECT DISTINCT activity_name
FROM strava_working
WHERE activity_type = 'Run'
ORDER BY 1;
```

To unify terms like `"zone 2"` and `"ez"` under `"Easy Run"` (excluding complex labels):

```sql
UPDATE strava_working
SET activity_name = 'Easy Run'
WHERE (
  activity_name ILIKE '%easy%' 
  OR activity_name ILIKE '%zone 2%' 
  OR activity_name ILIKE '%ez%'
)
AND ctid <> '(0,100)';  -- excludes mixed zone entry
```

---

## 4️⃣ Remove Non-Relevant or Inconsistent Data

### Normalize activity name labels:

```sql
UPDATE strava_working SET activity_name = 'Morning Run' WHERE activity_name ILIKE 'morning run';
-- Similar updates for: Afternoon Run, Evening Run, Tempo Run, Warm Up/Down, Recovery Run, Lunch Run
```

### Delete non-running activities:

```sql
DELETE FROM strava_working
WHERE activity_type != 'Run';
```

### Drop irrelevant column:

```sql
ALTER TABLE strava_working
DROP COLUMN max_heart_rate;
```

---

## 5️⃣ Optimize Data Types

Convert `elapsed_time` from numeric to `time`:

```sql
ALTER TABLE strava_working
ALTER COLUMN elapsed_time TYPE time
USING (elapsed_time || ' seconds')::interval::time;
```

---

## 6️⃣ Create Time-Based Metrics

To calculate pace:

```sql
SELECT
  distancekm,
  elapsed_time,
  EXTRACT(EPOCH FROM elapsed_time::interval) AS total_time_secs,
  ROUND(EXTRACT(EPOCH FROM elapsed_time::interval)/distancekm) AS secs_per_km,
  FLOOR(EXTRACT(EPOCH FROM elapsed_time::interval) / distancekm / 60)::text 
    || ' min ' || 
  ROUND(MOD(EXTRACT(EPOCH FROM elapsed_time::interval) / distancekm, 60))::text 
    || ' sec' AS pace_formatted
FROM strava_working;
```

### Create a view:

```sql
CREATE OR REPLACE VIEW time_metrics AS
SELECT
  activity_id,
  distancekm,
  elapsed_time,
  ROUND(EXTRACT(EPOCH FROM elapsed_time::interval)) AS total_time_secs,
  ROUND(EXTRACT(EPOCH FROM elapsed_time::interval)/distancekm) AS secs_per_km,
  FLOOR(EXTRACT(EPOCH FROM elapsed_time::interval) / distancekm / 60)::text 
    || ' min ' || 
  ROUND(MOD(EXTRACT(EPOCH FROM elapsed_time::interval) / distancekm, 60))::text 
    || ' sec' AS pace_formatted
FROM strava_working;
```

---

## 7️⃣ Remove Outliers

Some activities misclassified as runs had extremely slow paces:

```sql
DELETE FROM time_metrics
WHERE secs_per_km > 700;
```

---

## ✅ Final Notes

- `activity_type` was dropped from `strava_working` since all entries were filtered to `'Run'`.
- The `time_metrics` view provides an enriched table for further analysis.
