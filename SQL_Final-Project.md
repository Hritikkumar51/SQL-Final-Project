    SQL Final-Project(code/queries)


<!--Blockquote-->
> Task-1 creating table "matches" for match related data 
```SQL

create table matches(
     id int,
     city varchar,
	 date date,
	player_of_match varchar,
	venue varchar,
	neutral_venue int,
	team1 varchar,
	team2 varchar,
	toss_winner varchar,
	toss_decision varchar,
	winner varchar,
	result varchar,
	result_margin int,
	eliminator varchar,
	method varchar,
	umpire1 varchar,
	umpire2 varchar
);

```
<!--Blockquote-->
> Task-2 creating table "deliveries" for ball-to-ball related data 
```SQL
create table deliveries(
    id int,
	inning int,
	over int,
	ball int,
	batsman varchar,
	non_striker varchar,
	bowler varchar,
	batsman_runs int,
	extra_runs int,
	total_runs int,
	is_wicket int,
	dismissal_kind varchar,
	player_dismissed varchar,
	fielder varchar,
	extras_type varchar,
	batting_team varchar,
	bowling_team varchar
);
```
<!--Blockquote-->
> Task-3 Importing data from CSV file 'IPL_matches.csv' --- downloaded file from resources 

```CMD
copy matches from 'C:\Program Files\PostgreSQL\13\data\New folder\Data for final project - IPL\IPL_matches.csv'CSV header;
```

<!--Blockquote-->
> Task-4 Importing data from CSV file 'IPL_Ball.csv' --- downloaded file from resources 
```CMD
copy deliveries from 'C:\Program Files\PostgreSQL\13\data\New folder\Data for final project - IPL\IPL_Ball.csv'CSV header;
```
<!--Blockquote-->
> Task-5 Select the top 20 rows of the deliveries table 
```SQL
select * from deliveries limit 20;
```
<!--Blockquote-->
> Task-6 Select the top 20 rows of the matches table.
```SQL
select * from matches limit 20;
```
<!--Blockquote-->
> Task-7 Fetch data of all the matches played on 2nd May 2013

```SQL
select * from matches where date='02-05-2013';
```
<!--Blockquote-->
> Task-8 Fetch data of all the matches where the margin of victory is more than 100 runs
```SQL
select * from matches where result='runs' and result_margin>100;
```
<!--Blockquote-->
> Task-9 Fetch data of all the matches where the final scores of both teams tied and order it in descending order of the date
```SQL
select * from matches where result='tie'
order by date desc;
```
<!--Blockquote-->
> Task-10 Get the count of cities that have hosted an IPL match
```SQL
select count(distinct city) from matches;
```
<!--Blockquote-->
> Task-11 Create table deliveries_v02 with all the columns of deliveries and an additional column ball_result containing value boundary,
dot or other depending on the total_run (boundary for >= 4, dot for 0 and other for any other number)
```SQL
create table deliveries_v02
as select *,
  case
     when total_runs >=4 then 'boundary'
	 when total_runs =0 then 'dot'
	 else 'other'
  end as ball_result
from deliveries;
 ```
 <!--Blockquote-->
> Task-12 Write a query to fetch the total number of boundaries and dot balls
```SQL
select ball_result,
   count(*) from deliveries_v02
   group by ball_result;
```   
<!--Blockquote-->
> Task-13 Write a query to fetch the total number of boundaries scored by each team
```SQL
select batting_team, count(*)
  from deliveries_v02
  where ball_result='boundary'
  group by batting_team
  order by count desc;
 ``` 
 <!--Blockquote-->
> Task-14 Write a query to fetch the total number of dot balls bowled by each team
 ```SQL 
select batting_team, count(*)
 from deliveries_v02
 where ball_result='dot'
 group by batting_team
 order by count desc;
 ```
 <!--Blockquote-->
> Task-15 Write a query to fetch the total number of dismissals by dismissal kinds
```SQL
select dismissal_kind, count(is_wicket)
  from deliveries
  where is_wicket=1
  group by dismissal_kind
  order by count desc;
  ```
  <!--Blockquote-->
> Task-16 Write a query to get the top 5 bowlers who conceded maximum extra runs
```SQL
select bowler, sum(extra_runs) as maximum_extra_runs
from deliveries
group by bowler
order by maximum_extra_runs desc
limit 5;
```
<!--Blockquote-->
> Task-17 Write a query to create a table named deliveries_v03 with all the columns of deliveries_v02 table and two additional column
(named venue and match_date) of venue and date from table matches
```SQL
create table deliveries_v03 as select a.* , b.venue , b.match_date
from deliveries_v02 as a
left join(select max(venue) as venue ,max(date) as match_date, id from matches group by id) as b
on a.id=b.id;
```
<!--Blockquote-->
> Task-18 Write a query to fetch the total runs scored for each venue and order it in the descending order of total runs scored.
```SQL
select venue , sum(total_runs) as runs
from deliveries_v03
group by venue
order by runs desc;
```
<!--Blockquote-->
> Task-19 Write a query to fetch the year-wise total runs scored at Eden Gardens and order it in the descending order of total runs scored
```SQL
select extract(year from match_date) as years,
       sum(total_runs) as runs
from deliveries_v03
where venue='Eden Gardens'
group by years
order by runs desc;
```
<!--Blockquote-->
> Task-20 Get unique team1 names from the matches table, you will notice that there are two entries
for Rising Pune Supergiant one with Rising Pune Supergiant and another one with Rising Pune Supergiants. 
Your task is to create a matches_corrected table with two additional columns team1_corr and team2_corr containing team names
with replacing Rising Pune Supergiants with Rising Pune Supergiant. Now analyse these newly created columns.

```SQL
select distinct team1 from matches;

create table matches_corrected as select *,
replace(team1,'Rising Pune Supergaints','Rising Pune Supergaint') as team1_corr,
replace(team2,'Rising Pune Supergaints','Rising Pune Supergaint') as team2_corr
from matches;
```
<!--Blockquote-->
> Task-21 Create a new table deliveries_v04 with the first column as ball_id containing information of match_id, inning, over and ball separated by ‘-’ 
(For ex. 335982-1-0-1 match_id-inning-over-ball) and rest of the columns same as deliveries_v03)
```SQL
create table deliveries_v04
as select concat(id,'-',inning,'-',over,'-',ball)
as ball_id , * from deliveries_v03;
```
<!--Blockquote-->
> Task-22 Compare the total count of rows and total count of distinct ball_id in deliveries_v04
```SQL
select count(distinct ball_id) from deliveries_v04;

select count(*) from deliveries_v04;
```

### ------OR------
```SQL
select count(id) - count(distinct ball_id) from deliveries_v04;  ----my query which gives the difference b/w total number of id and distinct id
  ```
<!--Blockquote-->
> Task-23 Create table deliveries_v05 with all columns of deliveries_v04 and an additional column for row number partition over ball_id.
(HINT :  row_number() over (partition by ball_id) as r_num) 
```SQL  
create table deliveries_v05 as select * ,
row_number() over (partition by ball_id) as r_num
from deliveries_v04;
``` 
<!--Blockquote-->
> Task-24 Use the r_num created in deliveries_v05 to identify instances where ball_id is repeating. (HINT : select * from deliveries_v05 WHERE r_num=2;)
 ```SQL 
 select * from deliveries_v05 WHERE r_num=2;
 ``` 
 <!--Blockquote-->
> Task-25 Use subqueries to fetch data of all the ball_id which are repeating.
(HINT: SELECT * FROM deliveries_v05 WHERE ball_id in (select BALL_ID from deliveries_v05 WHERE r_num=2);
```SQL
SELECT * FROM deliveries_v05 WHERE ball_id in (select BALL_ID from deliveries_v05 WHERE r_num=2);
```



### -------------------------END-----------------------------------
