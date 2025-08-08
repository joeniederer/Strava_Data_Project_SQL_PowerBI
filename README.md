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


##  Findings

I've been running since November 2023 (21 months). Over the course of this period I've run a total of 96 times, achieving a total distance of 487.93 km. This averages to 5.08 km per run.

The longest run I have done is 21 km, and the fastest average pace I have achieved is 257 seconds/km (4 minutes 17 seconds/km).

Both 2023 and 2025 (analysis conducted 08/2025) are incomplete months. Over the complete year of 2024, 41 runs were completed. Following average trend, 2025 is expected to end with ~ 53 runs.

Run types were categorise in terms of effort, with effort type being categorised by pace relative to distance. However, low effort runs that were run at a deliberate, controlled slow pace are categorised as 'Easy runs'. 

  

'High effort' and 'Easy runs' are desirable, whereas 'Low effort' runs are not.

  


- 37.5% of my runs are 'High effort'.

- 15.6% of my runs are 'Easy runs'.

- 46.9% of my runs are 'Low effort'.

<br>

<img width="1426" height="802" alt="image" src="https://github.com/user-attachments/assets/920151ad-9d24-48c0-91aa-04f64b92ebe1" />

<br><br>

Over the course of 21 months, my personal records have improved.

<br>

- The fastest recorded average 5km pace is 281 seconds/km (4 min 41 seconds/km).
  
  This is a 12% improvement from the personal record of 2023.

- The fastest recorded average 10km pace is 322 seconds/km (5 min 22 seconds/km).
  
  This is a 16% improvement from the personal record of 2023.

- The fastest recorded average 15km pace is 364 seconds/km (6 min 17 seconds/km).
  
   This is a 3% improvement from the personal record of 2023.

<br>

10km is my most improved run, whereas my 15km has barely improved.

<br>

<img width="1424" height="801" alt="image" src="https://github.com/user-attachments/assets/0d4e205d-f10c-44fe-a9e9-9a3507d60396" />


<br><br>


Over the course of 21 months, average cadence per run has increased by roughly 9%, from 139 steps per minute to 152.

Additionally, variation of average cadence has decreased.

This suggests that with each successive month, cadence is increasing and stabilising.


<br>


<img width="1424" height="800" alt="image" src="https://github.com/user-attachments/assets/8127ff71-846b-4b4e-aa89-bb2f269de34e" />





