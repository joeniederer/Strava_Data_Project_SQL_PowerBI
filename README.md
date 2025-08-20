#  Strava Running Descriptive Analysis

##  Project Background

I've been running casually over the past 21 months, recording all of my runs through the Strava app. Recently I have signed up for a marathon in April 2026, and now I need to start thinking about a marathon training plan.

To make the most effective plan, I want to run a descriptive analysis on my Strava running data to get a view on my current running level.

Using these findings, I'll have an accurate baseline to pass on to a running coach, or to start to build a marathon training plan as a future project.

## Documentation

- [Data Cleaning Process](README_strava_data_cleaning.md)
- [SQL Queries](README_strava_sql_queries.md)

##  Analytical Focus Areas

### 1. Volume & Consistency
- Total distance run
- Frequency of runs

### 2. Run Type Distribution
- Overall % distribution of high-effort / low-effort / easy-run

  Definitions:
    - high-effort: fast pace relative to distance
    - low-effort: non-fast pace relative to distance
    - easy run: deliberate controlled slow pace

### 3. Speed & Performance
- Average pace on high-effort runs
- Fastest pace achieved
- Fastest 5K / 10K / 15K times

### 4. Cadence Progression & Stability
- Average cadence
- Cadence varia

##  Methodology

### 1. Download Data from Strava
- Export raw Strava data
- Select only the relevant fields for analysis

### 2. Clean & Transform Data Using SQL
- Perform all data cleaning and transformations using SQL

### 3. Visualise Data in Power BI
- Handle all data visualisation exclusively in Power BI

---

## Data Structure 

The databse structure consists of 2 main tables (strava_working / strava_cadence) and 2 view tables (main_time_effort / time_metrics) which include transformations.

The consistent primary key to link all tables / views is activity_id.

<img width="612" height="663" alt="image" src="https://github.com/user-attachments/assets/393d34a0-c2fb-49f7-98ff-8ed6392a0572" />


##  Findings

Over the past 21 months (Nov 2023 – Aug 2025), I recorded 96 runs in Strava, covering 487.93 km — an average of 5.08 km per run. This analysis provides a clear baseline of my current running ability ahead of marathon training for April 2026.

<br>

### 1. Volume & Consistency

Longest run:
   
-  21 km

Fastest run:
   
-  4:17 min/km (257 sec/km)

Annual totals:

- 2023 – 21 runs (partial year)
    
- 2024 – 41 runs (full year)
    
- 2025 – On track for ~53 runs (YTD projection)
<br>

While frequency is improving in 2025, total distance per run has remained stable (~5 km average). Increasing the number of longer runs will be important for marathon preparation.

<br>

### 2. Run Type Distribution

Runs were classified based on pace relative to distance:

  High effort – 37.5% (goal: maintain)

  Intentional-Easy runs – 15.6% (goal: increase)

  Low effort – 46.9% (least desirable, suggests untargeted training)
  
<br>

  The high proportion of low-effort runs indicates that nearly half my training volume lacks clear performance or  intent — an area for improvement when building a structured training plan.

<br>

<img width="1426" height="802" alt="image" src="https://github.com/user-attachments/assets/920151ad-9d24-48c0-91aa-04f64b92ebe1" />

<br><br>

### 3. Speed & Performance Improvements

Personal records have steadily improved over the analysis period:

5 km – 4:41 min/km (281 sec/km), +12% faster vs 2023

10 km – 5:22 min/km (322 sec/km), +16% faster vs 2023 (largest improvement)

15 km – 6:17 min/km (364 sec/km), +3% faster vs 2023 (smallest improvement)

<br>

Observation: While short to mid-distance paces have improved significantly, longer-distance pacing remains almost unchanged — highlighting the need to focus on endurance speed.

<br>

<img width="1424" height="801" alt="123b02df-82bd-4d36-865c-34045c14f3ca" src="https://github.com/user-attachments/assets/19c3f5cf-1432-4e92-88bc-a12f2f342888" />

<br><br>

### 4. Cadence & Form

Average cadence increased from 139 spm (2023) → 143.91 spm (2024) → 152.45 spm (2025) — a 9.6% rise over the 21-month period.

Variance was highest in 2024 (~38), but dropped sharply in 2025 (~21), indicating improved pacing control and running form consistency.

The overall trendline shows a steady cadence increase across activities as well as a decrease in variance, suggesting technique improvements and a shift toward more efficient stride patterns.

<br>

<img width="1423" height="799" alt="image" src="https://github.com/user-attachments/assets/52b1474c-8e95-44a0-bbfe-16af1d96d8ec" />

<br><br>

### Summary & Next Steps

This analysis shows consistent improvements in 10 km pace and cadence stability, with room for growth in longer-distance speed and training session structure.
Basesd on the insights of this analysis project, the pillars of my marathon plan will be:

<br>

Reduce the proportion of low-effort runs.

Increase long-run frequency and distance.

Maintain high cadence while extending endurance.

