#  Strava Running Descriptive Analysis

##  Project Background

I've been running casually over the past three years, recording all of my runs through the Strava app. Recently I have signed up for a marathon in April 2026, and now I need to start thinking about a marathon training plan.

To make the most effective plan, I want to run a descriptive analysis on my Strava running data to get a view on my current running level.

Using these findings, I'll have an accurate baseline to pass on to a running coach, or to start to build a marathon training plan as a future project.

##  Areas of Focus

### 1. Volume & Consistency
- Total distance run
- Frequency of runs

### 2. Run Type Distribution
- Overall % distribution of high-effort / low-effort / easy-run

  definitions:
    - high-effort: fast pace relative to distance
    - low-effort: non-fast pace relative to distance
    - easy run: deliberate controlled slow pace

### 3. Speed & Performance
- Average pace on high-effort runs
- Fastest pace achieved
- Fastest 5K / 10K / 15K times

##  Methodology

### 1. Download Data from Strava
- Export raw Strava data
- Select only the relevant fields for analysis

### 2. Clean & Transform Data Using SQL
- Perform all data cleaning and transformations using SQL

### 3. Visualise Data in Power BI
- Handle all data visualisation exclusively in Power BI

---

## Documentation

- [Data Cleaning Process](README_strava_data_cleaning.md)


## Data Structure 

The databse structure consists of one main table (strava_working) and 2 view tables (main_time_effort / time_metrics) which are include transformations.

<img width="845" height="773" alt="Untitled" src="https://github.com/user-attachments/assets/50a360ee-dafb-4719-8188-56b558f155b4" />


##  Summary of Findings

I've been running since November 2023 (21 months). Over the course of this period I've run a total of 96 times, achieving a total distance of 487.93 km. This averages to 5.08 km per run.

The longest run I have done is 21 km, and the fastest pace I have achieved is 257 seconds/km.

- 37.5% of my runs are run with high effort.

- 15.6% of my runs are run at an deliberate easy pace.

- 46.9% of my runs are run with low effort, but not with a deliberate easy pace.
  


<img width="1242" height="697" alt="image" src="https://github.com/user-attachments/assets/e3d89500-cca1-439c-b933-d47ea9a04d98" />




