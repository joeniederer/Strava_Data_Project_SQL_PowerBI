## Data Cleaning

This document outlines the data cleaning process applied to a Strava dataset in preparation for running data diagnostic analysis. The aim was to prepare cleaned, standardised, and relevant data.

---

## Data Cleaning Workflow

1. **Import data**  
2. **Check for duplicates**  
3. **Standardise user input**  
4. **Remove non-relevant data**  
5. **Optimise data types**  
6. **Create time-based metrics**
7. **Create effort-type category**
8. **Remove outliers**

---

## 1. Import & Create Working Table

To preserve the raw data:

```sql
create table strava_working (like strava including all);
insert into strava_working select * from strava;
```

---

## 2. Check for Duplicates

We checked for duplicate entries based on an assumed unique combination of fields:

```sql
select *
from (
  select *,
    row_number() over (partition by activity_id, activity_date, activity_name, activity_type, distancekm) as row_nm
  from strava_working
) as dupe
where row_nm > 1;
```

**No duplicates were found.**

---

## 3. Standardise `activity_name`

Strava-generated names like `"Afternoon Run"` are consistent, but user-inputted names vary.

### Example:

```sql
select distinct activity_name
from strava_working
where activity_type = 'Run'
order by 1;
```

To unify terms like `"zone 2"` and `"ez"` under `"Easy Run"` (excluding complex labels):

```sql
update strava_working
set activity_name = 'Easy Run'
where (
  activity_name ilike '%easy%' 
  or activity_name ilike '%zone 2%' 
  or activity_name ilike '%ez%'
)
and ctid <> '(0,100)';  -- excludes a specific entry with multiple 'heartrate zones'
```

---

## 4. Remove Non-Relevant or Inconsistent Data

### Normalise activity name labels:

```sql
update strava_working set activity_name = 'Morning Run' where activity_name ilike 'morning run';
-- similar updates for: Afternoon Run, Evening Run, Tempo Run, Warm Up/Down, Recovery Run, Lunch Run
```

### Delete non-running activities:

```sql
delete from strava_working
where activity_type != 'Run';
```

### Drop irrelevant column:

```sql
alter table strava_working
drop column max_heart_rate;
```

---

## 5. Optimise Data Types

Convert `elapsed_time` from numeric to `time`:

```sql
alter table strava_working
alter column elapsed_time type time
using (elapsed_time || ' seconds')::interval::time;
```

Convert `activity_date` from a text field including time to a proper `date` data type:

```sql
select 
  (string_to_array(activity_date, ','))[1] as date
from strava_dates;

update strava_dates
set activity_date = (string_to_array(activity_date, ','))[1];

alter table strava_dates
alter column activity_date type date
using activity_date::date;
```

---

## 6. Create Time-Based Metrics

To calculate pace:

```sql
select
  distancekm,
  elapsed_time,
  extract(epoch from elapsed_time::interval) as total_time_secs,
  round(extract(epoch from elapsed_time::interval)/distancekm) as secs_per_km,
  floor(extract(epoch from elapsed_time::interval) / distancekm / 60)::text 
    || ' min ' || 
  round(mod(extract(epoch from elapsed_time::interval) / distancekm, 60))::text 
    || ' sec' as pace_formatted
from strava_working;
```

### Create a view:

**time_metrics**

```sql
create or replace view time_metrics as
select
  activity_id,
  distancekm,
  elapsed_time,
  round(extract(epoch from elapsed_time::interval)) as total_time_secs,
  round(extract(epoch from elapsed_time::interval)/distancekm) as secs_per_km,
  floor(extract(epoch from elapsed_time::interval) / distancekm / 60)::text 
    || ' min ' || 
  round(mod(extract(epoch from elapsed_time::interval) / distancekm, 60))::text 
    || ' sec' as pace_formatted
from strava_working;
```

## 7. Create Effort-type Category

To calculate effort-type:


```sql
  select
    activity_id,
    distancekm,
    secs_per_km,
    pace_formatted,
    case
      when distancekm between 3.00 and 7.50 and secs_per_km < 345 then 'high_effort'
      when distancekm between 7.51 and 10.1 and secs_per_km < 375 then 'high_effort'
      when distancekm between 10.2 and 15.1 and secs_per_km < 390 then 'high_effort'
      when distancekm > 15.2 and secs_per_km < 410 then 'high_effort'
      else 'low_effort'
    end as effort_type
  from time_metrics
```

**main_time_effort**

```sql
create view main_time_effort as
with cte as (
  select
    activity_id,
    distancekm,
    secs_per_km,
    pace_formatted,
    case
      when distancekm between 3.00 and 7.50 and secs_per_km < 345 then 'high_effort'
      when distancekm between 7.51 and 10.1 and secs_per_km < 375 then 'high_effort'
      when distancekm between 10.2 and 15.1 and secs_per_km < 390 then 'high_effort'
      when distancekm > 15.2 and secs_per_km < 410 then 'high_effort'
      else 'low_effort'
    end as effort_type
  from time_metrics
)
select
  sw.activity_id,
  sw.activity_date,
  sw.activity_name,
  sw.distancekm,
  sw.elapsed_time,
  sw.year,
  sw.month,
  cte.secs_per_km,
  cte.pace_formatted,
  cte.effort_type
from strava_working as sw
join cte
on sw.activity_id = cte.activity_id;
```

---

## 8. Remove Outliers

Some activities misclassified as runs had extremely slow paces:

```sql
delete from time_metrics
where secs_per_km > 700;
```

---


## ✅ Final Notes

- `activity_type` was dropped from `strava_working` since all entries were filtered to `'Run'`.
- The `time_metrics` view provides an enriched table for further analysis.
