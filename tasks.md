# Football Matches - Tasks

Each of the questions/tasks below can be answered using a `SELECT` query. When you find a solution copy it into the code block under the question before pushing your solution to GitHub.

1) Find all the matches from 2017.

```sql
select * 
from matches 
where season = 2017;
```

2) Find all the matches featuring Barcelona.

```sql
select *
from matches
where lower(hometeam) like lower('%Barcelona%') or lower(awayteam) like lower('%Barcelona%');
```

3) What are the names of the Scottish divisions included?

```sql
select name
from divisions
where lower(name) like lower('%Scottish%');
```

4) Find the division code for the Bundesliga. Use that code to find out how many matches Freiburg have played in the Bundesliga since the data started being collected.

```sql
select COUNT(*) TotalCount,
divisions.code,
matches.division_code
from divisions divisions
inner join matches matches 
on divisions.code = matches.division_code
where (lower(hometeam) like lower('%Freiburg%') or lower(awayteam) like lower('%Freiburg%')) and (lower(name) like lower('%Bundesliga%')) 
group by divisions.code, matches.division_code;
```

5) Find the unique names of the teams which include the word "City" in their name (as entered in the database)

```sql
select distinct hometeam, awayteam 
from matches 
where (lower(hometeam) like lower('%City%') 
or lower(awayteam) like lower('%City%'));
```

6) How many different teams have played in matches recorded in a French division?

```sql
select count ( distinct country ) AS "Unique teams" 
from divisions divisions
join matches matches on divisions.code = matches.division_code
where country = 'France';
-- Solution Answer
select count(distinct hometeam) from matches where division_code  IN ('F1','F2');
```

7) Have Huddersfield played Swansea in the period covered?

```sql
select case when exists (
select * from matches 
where 
	(lower(hometeam) like lower('%Huddersfield%') and lower(awayteam) like lower('%Swansea%')) 
	or (lower(hometeam) like lower('%Swansea%') and lower(awayteam) like lower('%Huddersfield%'))
)
then cast('yes' as varchar)
else cast('no' as varchar) end
```

8) How many draws were there in the Eredivisie between 2010 and 2015?

```sql
select count (*)
from divisions divisions
join matches matches on divisions.code = matches.division_code
where season >=2010 and season <=2015 and fthg=ftag and name = 'Eredivisie';
-- Could have used between rather than <= and >=
```

9) Select the matches played in the Premier League in order of total goals scored from highest to lowest. Where there is a tie the match with more home goals should come first.

```sql
select division_code, (fthg + ftag) as TotalGoal
from divisions divisions
join matches matches on divisions.code = matches.division_code
where name = 'Premier League' 
order by 
case when fthg=ftag then fthg end desc,
case when fthg<>ftag then fthg + ftag end desc;
-- Correct Solution: can order by multiple things fthg and fthg+ftag desc
```

10) In which division and which season were the most goals scored?

```sql
select distinct name, season, sum(fthg + ftag)
from divisions divisions
join matches matches on divisions.code = matches.division_code
group by divisions.name, matches.season
order by sum desc
limit 1;
```

### Useful Resources

- [Filtering results](https://www.w3schools.com/sql/sql_where.asp)
- [Ordering results](https://www.w3schools.com/sql/sql_orderby.asp)
- [Grouping results](https://www.w3schools.com/sql/sql_groupby.asp)