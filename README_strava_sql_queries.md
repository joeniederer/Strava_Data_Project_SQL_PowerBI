# Strava Power BI SQL Queries

This README contains the SQL queries used for analyzing Strava running data in Power BI. Queries are grouped by analytical focus area.

## 1. Volume & Consistency

Total distance run

```sql
select
sum(distancekm)
from strava_working
```

### Frequency of runs

```sql
select
	year,
	month,
	count(*)
from strava_working
group by year,month
order by year,month asc
```

## 2. Cadence & Form 

```sql
select
	*,
	round(avg(average_cadence) over (partition by year)) as avg_cadence_year
from (
	select 
		*,
		extract(year from activity_date) as year
	from strava_cadence
	order by 4 asc) as t1
```

## 3. Run Type Distribution

    % distribution of run-types

```sql
with cte as (
select 
	distancekm,
	secs_per_km,
	pace_formatted,
	case
		when distancekm between 3.00 and 7.50 and secs_per_km < 345 then 'high_effort'
		when distancekm between 7.51 and 10.1 and secs_per_km < 375 then 'high_effort'
			--> 10.1 used to capture overshot on 10km runs, same logic for 15km
		when distancekm between 10.2 and 15.1 and secs_per_km < 390 then 'high_effort'
		when distancekm > 15.2 and secs_per_km < 410 then 'high_effort'
		when activity_name ilike '%Easy Run%' then 'easy_run'
		else 'low_effort'
		end
		as effort_type
from time_metrics)

select
	*,
	round(((high_effort_count *1.0 / total_count)*100),1) as high_effort_percent,
	round(((low_effort_count *1.0 / total_count)*100),1) as low_effort_percent,
	round(((easy_run_count *1.0 / total_count)*100),1) as easy_run_percent
from (
	select
		count(*) filter (where effort_type = 'high_effort') as high_effort_count,
		count(*) filter (where effort_type = 'low_effort') as low_effort_count,
		count(*) filter (where effort_type = 'easy_run') as easy_run_count,
		count(*) as total_count
	from cte) as counts
```

## 4. Speed & Performance


    Average pace on effort types

```sql
with cte as (
select 
	activity_id,
	distancekm,
	secs_per_km,
	pace_formatted,
	effort_type,
	round(avg(secs_per_km) over (partition by effort_type)) as avg_pace,
	activity_date
from main_time_effort)


select
	activity_id,
	activity_date,
	distancekm,
	secs_per_km,
	pace_formatted,
	effort_type,
	avg_pace,
	floor(avg_pace/ 60)::text || ' min ' 
	|| 
	round(mod(avg_pace,60))::text || ' sec ' as avg_pace_formatted
from cte
```


Fastest pace achieved

```sql
select 
	* 
from time_metrics
order by secs_per_km asc
limit 1
```

Fastest 5K by year

```sql
select
	*
from (
	select
		*,
		dense_rank() over (partition by year order by secs_per_km) as pace_rank
	from (
		select 
			activity_id,
			distancekm,
			secs_per_km,
			pace_formatted,
			extract(year from activity_date) as year
		from time_metrics
		where distancekm between 5 and 6
		order by secs_per_km asc) as t1) as t2
where pace_rank = 1
```


Fastest 10K by year

```sql
select
	*
from (
	select
		*,
		dense_rank() over (partition by year order by secs_per_km) as pace_rank
	from (
		select 
			activity_id,
			distancekm,
			secs_per_km,
			pace_formatted,
			extract(year from activity_date) as year
		from time_metrics
		where distancekm between 10 and 11
		order by secs_per_km asc) as t1) as t2
where pace_rank = 1
```

Fastest 15K by year

```sql

select
	*
from (
	select
		*,
		dense_rank() over (partition by year order by secs_per_km) as pace_rank
	from (
		select 
			activity_id,
			distancekm,
			secs_per_km,
			pace_formatted,
			extract(year from activity_date) as year
		from time_metrics
		where distancekm between 15 and 16
		order by secs_per_km asc) as t1) as t2
where pace_rank = 1
```
