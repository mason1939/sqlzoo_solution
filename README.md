# SQLZoo Solutions

## Topics
- [Introduction](#instroduction)
- [Select Basics](#select-basics)
- [Select from World](#select-from-world)
- [Select from Nobel](#select-from-nobel)
- [Select in Select](#select-in-select)
- [Sum and Count](#sum-and-count)
- [Join](#join)
- [More Join](#more-join)
- [Using Null](#using-null)
- [Self Join](#self-join)
- [References](#references)

## Introduction

SQL is an important skill but DO NOT try to do every thing with SQL.

When attempting to do that, you are messing up your projects and eventually they end up with garbage. 

Complicated SQL queries are not easy to maintain, especially there are tightly coupled with business logic.

It's totally a nightmare. Believe me. I've been there. 

Investing some time to bulild a good software architecture is way more important.

In my opinion, if you are able to solve exercises from SQLZoo without any difficult, I'd say it's sufficient for developers.

Unless you'd like to spend your life in database. I'm gonna says that you'll get bord very fast. 

However, it's your call and good luck.

Here I'll provide solutions and make detailed explainations for hard ones. Enjoy!

## Select Basics
[top](#topics)
## Select from World

1.
```sql
SELECT name, continent, population FROM world
```

2.
```sql
SELECT name 
FROM world
WHERE population>=200000000
```

3.
```sql
select name,gdp/population
from world
where population >=200000000
```

4.
```sql
select name,population/1000000
from world
where continent='South America'
```

5.
```sql
select name,population
from world
where name in ('France','Germany','Italy')
```

6.
```sql
select name from world where name like '%united%'
```

7.
```sql
select name,population,area
from world
where area > 3000000 or population > 250000000
```

8.
```sql
select name,population,area
from world
where (area > 3000000 and population <= 250000000) or
(area <=3000000 and population >250000000)
```

9.
```sql
select name,
        round(population/1000000,2),
        round(gdp/1000000000,2)
from world
where continent='south america'
```

10.
```sql
select name,round(gdp/population,-3)
from world
where gdp >= 1000000000000
```

11.
```sql
SELECT name, capital
FROM world
WHERE LENGTH(name)=LENGTH(capital)
```

12.
```sql
SELECT name,capital
FROM world
where LEFT(name,1)=left(capital,1) and name<>capital
```

13.
```sql
SELECT name
FROM world
WHERE name LIKE '%a%' 
  and name like '%e%' 
  and name like '%i%'
  AND name LIKE '%o%'
  and name like '%u%'
  and name not like '% %'
```
[top](#topics)
## Select from Nobel

1.
```sql
SELECT yr, subject, winner FROM nobel
WHERE yr = 1950
```
2.
```sql
SELECT winner FROM nobel
WHERE yr = 1962 AND subject = 'Literature'
```
3.
```sql
select yr,subject from nobel
where winner='albert einstein'
```
4.
```sql
select winner from nobel
where yr>=2000 and subject='peace'
```
5.
```sql
select * from nobel
where yr between 1980 and 1989
        and subject='literature'
```
6.
```sql
select * from nobel
where winner in ('Theodore Roosevelt','woodrow wilson','jimmy carter','barack obama')
```
7.
```sql
select winner from nobel where winner like 'John%'
```
8.
```sql
select * from nobel
where 
    (yr=1980 and subject='physics') or 
    (yr=1984 and subject='chemistry')
```
9.
```sql
select * from nobel
where yr=1980 and subject not in ('Chemistry','Medicine')
```
10.
```sql
select * from nobel
where (yr<1910 and subject='Medicine') or 
      (yr>=2004 and subject='literature')
```
11.
```sql
select * from nobel
where winner='peter grünberg'
```
tip: [Alt+252 (with your num lock on)]
(https://www.vistawide.com/languages/typing_foreign_language_characters.htm#german)

12. **Esacpe Character => '**
```sql
select * from nobel where winner='eugene o''neill'
```
13.
```sql
select winner,yr,subject from nobel
where winner like 'sir%'
order by yr desc,winner
```
14.
```sql
SELECT winner, subject
FROM nobel
WHERE yr=1984
ORDER BY subject IN ('Physics','Chemistry'),subject,winner
```
tip:
```sql
SELECT winner, subject,subject IN ('Physics','Chemistry')
FROM nobel
WHERE yr=1984
ORDER BY subject,winner
```
[top](#topics)
## Select in Select

Purpose: Use the select result as a variable.

Only one column will appear in select clause.

1.
```sql
SELECT name FROM world
  WHERE population >
     (SELECT population FROM world
      WHERE name='Romania')
```
2.
```sql
select name from world 
where continent='europe' and 
gdp/population > (select gdp/population from world where name='united kingdom') 
```
3.
```sql
select name,continent from world
where continent in (
    select distinct continent from world
    where name in ('Argentina','Australia')
)
order by name
```
4.
```sql
select name,population
from world
where
population>(select population from world where name='canada')
and
population<(select population from world where name='poland')
```
5.
```sql
select name,concat(
  round(
    100*population/(select population from world where name='Germany')
    ,0),
'%') percentage 
from world 
where continent='europe'
```
6.
```sql
select name from world
where gdp > ALL(select gdp from world where continent='europe' and gdp > 0)
```
7.
```sql
SELECT continent, name, area FROM world x
  WHERE area>= ALL
    (SELECT area FROM world y
        WHERE y.continent=x.continent
          AND area>0)
```
Calm down. It's not join.

8.
```sql
select continent,name from world x
where name <= ALL(select name from world y where y.continent=x.continent)
```
9.
```sql
select name, continent,population from world y
where 25000000 >= ALL(select population from world x where y.continent=x.continent)
```
10.
```sql
select name,continent from world x
where population > ALL(select 3*population from world y where y.name!=x.name and y.continent=x.continent)
```
[top](#topics)

## Sum and Count

- Usually they are used along with group by.
- use having when there are filtering criteria.

1.
```sql
SELECT SUM(population)
FROM world
```
2.
```sql
select continent from world
group by continent

--alternative
select distinct continent from world
```
It is more intuitive to use distinct. However, using group by help you to get better understanding about how group by works.

3.
```sql
select sum(gdp) from world
where continent='africa'
```
4.
```sql
select count(name) from world
where area >=1000000
```
5.
```sql
select sum(population) from world
where name in ('Estonia','Latvia', 'Lithuania')
```
6.
```sql
select continent,count(name) a from world
group by continent
```
7.
```sql
select continent,count(name) a from world
where population>=10000000
group by continent
```
8.
```sql
select continent from world
group by continent
having sum(population)>=100000000
```
[top](#topics)

## Join

- default: intersect/inner join
- other join types
  - left/right
  - full 

1.
```sql
SELECT matchid,player FROM goal WHERE teamid='GER'
```
2.
```sql
SELECT id,stadium,team1,team2 FROM game where id=1012
```
3.
```sql
SELECT player,teamid,stadium,mdate
FROM game JOIN goal ON (id=matchid)
where teamid='GER'
```
4.
```sql
SELECT team1,team2,player
FROM game
  JOIN goal ON (id=matchid)
where player like 'Mario%'
```
5.
```sql
SELECT player, teamid,coach, gtime
FROM goal
  join eteam on (teamid=id)
WHERE gtime<=10
```
6.
```sql
select mdate,teamname
from game
  join eteam on (team1=eteam.id)
where coach = 'Fernando Santos'
```
7.
```sql
select player from game
              join goal on (id=matchid)
where stadium='National Stadium, Warsaw'
```
8.
```sql
SELECT distinct player
FROM game
  JOIN goal ON matchid = id 
WHERE teamid<>'GER' and (team1='GER' or Team2='GER')
```
9.
```sql
SELECT teamname,count(teamname)
FROM eteam
  JOIN goal ON id=teamid
group by teamname
ORDER BY teamname
```
10.
```sql
SELECT stadium,count(teamid)
FROM game
  JOIN goal ON id=matchid
group by stadium
```
Aggregate with stadium

11.
```sql
select matchid,mdate,count(teamid)
from goal
  join game on (id=matchid)
where team1='POL' or team2='POL'
group by matchid,mdate
```
Ignore columns not shown in select clause except ones in aggregate function. they don't affect "group by" effect.

From the example they are goal.teamid, game.team1, game.team2.

tip:

when generating a table using join, you can use 2 steps to make sure you do it right.

- step 1. Test

a. Determin all columns needed

b. Show the joined table to make sure the result is what you expect.

- step 2. Refine

a. Add where clause.

b. Remove unnecessary columns from select clause to perfrom group by

c. filter aggregate function results using having clause

12.
```sql
select matchid,mdate,count(teamid)
from goal
  join game on (id=matchid)
where teamid='GER'
Group by matchid,mdate
```
Tip:

Sometimes joining is to add columns from other tables. In that case, you should not perfrom join operation in the first place.
Instead, you should generate the table with minimal requirements to stay focused. From the example above,
I sum up the scores goaled by Gerymany in each game. After that, I introduce the mdate colum using join.
It perfectly illustrated the bottom up approach .

13.
```sql
select matchid,count(teamid) from goal
where teamid='ESP'
group by matchid

select id,mdate,count(teamid)
from goal 
  join game on (id=matchid)
where team1='ESP' or team2='ESP'
--以上是拆解測試

SELECT mdate,
  team1,
  sum(CASE WHEN teamid=team1 THEN 1 ELSE 0 END) score1,
  team2,
  sum(CASE WHEN teamid=team2 THEN 1 ELSE 0 END) score2
FROM game left JOIN goal ON matchid = id
group by id,mdate,team1,team2
order by mdate,id,team1,team2
```

tip:

Why left join? Consider the query below
```sql
select * from game left join goal on (id=matchid) where teamid is null
```
which means some games in which both teams do not score.
It's quite reasonable that some score data won't be shown in table goal when using equi-join.
The requirement is to list the final scores of each game. In other words, we need all records in table game to be shown.
As a result, Left join is required.

Group by team1 and team2 at the same time?
No worries. Consider the query below.
```sql
SELECT mdate,
  team1,
  (CASE WHEN teamid=team1 THEN 1 ELSE 0 END) score_1,
  team2,
  (CASE WHEN teamid=team2 THEN 1 ELSE 0 END) score_2
FROM game left JOIN goal ON matchid = id
```
Semantically, (id,mdate,team1,team2,score_1,score_2) is a historical scoring record.
Each record is implied as unique because records grows with time.

Is (id,mdate,team1,team2,score_1,score_2) a super key?

Actually yes, but by definition, no. 

What if we add gtime to the previous query?
```sql
SELECT id,mdate,
  team1,
  CASE WHEN teamid=team1 THEN 1 ELSE 0 END score1,
  team2,
  CASE WHEN teamid=team2 THEN 1 ELSE 0 END score2
  ,gtime
FROM game left JOIN goal ON matchid = id
order by mdate,gtime
```
Now it's a table with complete scoring records ordered by time in each game. 
And (id,mdate,team1,team2,score_1,score_2,gtime) is considered as a super key.
Since we want the final scores of each game, all we have to do is take (id,mdate,team1,team2) as a group to sum up score_1 and score_2.
Have you noticed that? (id,mdate,team1,team2) is not a super key. That is, there is something you can sum up.
Still don't get it? Try this.
```sql
SELECT id,mdate,
  team1,
  sum(CASE WHEN teamid=team1 THEN 1 ELSE 0 END) score_1,
  team2,
  sum(CASE WHEN teamid=team2 THEN 1 ELSE 0 END) score_2
FROM game left JOIN goal ON matchid = id
group by id,mdate,team1,team2,gtime
order by mdate,gtime
```
Initially I also don't feel comfortable when taking (id,mdate,team1,team2) as a group.
What I'm worried about is grouping team1 and team2 at the same time might ruin the result.
Just like what gtime did in the last query.
By digging into the tables and doning some experiemnts, I've gained more comprehensive understanding of how joining and grouping by work.
Now, Problem solved.

[top](#topics)

## More Join

1.
```sql
SELECT id, title
 FROM movie
 WHERE yr=1962
```
2.
```sql
select yr from movie where title='citizen kane'
```
3.
```sql
select id,title,yr from movie 
where title like '%star trek%' order by yr
```
4.
```sql
select id from actor where name='glenn close'
```
5.
```sql
select id from movie where title='casablanca'
```
6.
```sql
select name from casting
              join actor on (actorid=id)
where movieid=11768
```
7.
```sql
select name from casting
              join actor on (actorid=id)
where movieid in (select id from movie where title='alien')
```
8.
```sql
select title from movie 
              join casting on (movie.id=casting.movieid)
where actorid=(select id from actor
                where name='Harrison Ford')
```
9.
```sql
select title from movie
            join casting on (movie.id=casting.movieid)
where casting.ord<>1
      and actorid=(select id from actor where name='Harrison Ford')
```
10.
```sql
select title,actor.name from
      (select title,actorid
        from movie
          join casting on (movieid=id)
        where yr=1962 and ord=1)a 
      join actor on(actorid=actor.id)
```
11.
```sql
SELECT yr,COUNT(title) count
FROM movie
    JOIN casting ON movie.id=movieid
    JOIN actor ON actorid=actor.id
WHERE name='rock hudson'
GROUP BY yr
HAVING COUNT(title) > 2
```
12.
```sql
select title,name from movie 
                  join casting on(movie.id=casting.movieid)
                  join actor on (casting.actorid=actor.id)
where movie.id in (SELECT movieid FROM casting
                    WHERE actorid IN (SELECT id FROM actor
                                      WHERE name='Julie Andrews')
                  )
      and casting.ord=1
```
13.
```sql
select name from casting
            join actor on(actorid=id)
where ord=1
group by name
having count(actorid) >= 15
order by name
```
14.
```sql
select title,count(actorid) count
from (select title,id from movie
      where yr=1978)a
    join casting on (id=movieid)
group by title
order by count desc,title
```
15.
```sql
select distinct name
from (select actorid from casting
      where movieid in (select movieid from casting
                        where actorid in (select id from actorwhere name='Art Garfunkel')
                       )
     )a
  join actor on (actor.id=a.actorid)
where name <> 'Art Garfunkel'
```
[top](#topics)
## Using Null

1.
```sql
select name from teacher
where dept is null
```
2.
```sql
SELECT teacher.name, dept.name
FROM teacher inner JOIN dept
           ON (teacher.dept=dept.id)
```

tip: Inner join skip teacher whose dept is NULL.

[Null=dept.id ==> unknown](https://en.wikipedia.org/wiki/Null_%28SQL%29#Comparisons_with_NULL_and_the_three-valued_logic_(3VL))

3.
```sql
SELECT teacher.name, dept.name
FROM teacher left JOIN dept
           ON (teacher.dept=dept.id)
```
4.
```sql
SELECT teacher.name, dept.name
FROM teacher right JOIN dept
           ON (teacher.dept=dept.id)
```
5.
```sql
select name,coalesce(mobile,'07986 444 2266') from teacher
```
'case' works too.
```sql
select name,case when mobile is null then '07986 444 2266' else mobile end
from teacher
```
6.
```sql
select teacher.name,coalesce(dept.name,'None')
from teacher left join dept on (teacher.dept=dept.id)
```
7.
```sql
select count(name),count(mobile) from teacher
```
tip: Aggregate functions ignore NULL

8.
```sql
select dept.name,count(teacher.name)
from teacher right join dept on (teacher.dept=dept.id)
group by dept.name
```
9.
```sql
select name,case when dept=1 then 'Sci'
                when dept=2 then 'Sci'
                else 'Art' end
from teacher
```
10.
```sql
select name,case when dept=1 then 'Sci' 
                when dept=2 then 'Sci'
                when dept=3 then 'Art' 
                else 'None' end
from teacher 
```
[top](#topics)

## Self Join

Purpose: Add columns base on the same table.

Ex1.

employee(id,name,duty,manager_id): 

Because a manager_id is part of employee id. If we want to add a column,which displays name of a manager of a employee, we use self-join

Ex2.
bus_route(num,company,pos,stop):

Generate table show all combinations of 2 points in a route, we use self-join.

1.
```sql
select count(name) from stops
```

2.
```sql
select id from stops where name='Craiglockhart'
```

3.
```sql
select id,name from stops
where id in (select stop from route where num='4' and company='LRT')
```

4.
```sql
SELECT company, num, COUNT(*)
FROM route
WHERE stop=149 OR stop=53
GROUP BY company, num
having count(*)=2
```

5.
```sql
SELECT a.company, a.num, a.stop, b.stop
FROM route a JOIN route b ON
  (a.company=b.company AND a.num=b.num)
WHERE a.stop=53 and b.stop=149
```

6.
```sql
SELECT a.company, a.num, stopa.name, stopb.name
FROM route a JOIN route b ON
  (a.company=b.company AND a.num=b.num)
  JOIN stops stopa ON (a.stop=stopa.id)
  JOIN stops stopb ON (b.stop=stopb.id)
WHERE stopa.name='Craiglockhart' and stopb.name='London Road'
```

7.
```sql
select distinct a.company,a.num
from route a join route b on (a.num=b.num and a.company=b.company)
where a.stop='115' and b.stop='137'
```
The first thought is
```sql
select distinct a.company,b.num 
from (select * from route where stop=115) a join
     (select * from route where stop=137) b
     on (a.num=b.num and a.company=b.company)
```
It's the most straight forward and efficient way.

Or you can perform self join first. It's kind of brutal 
because db will perform Cartesian product for each route which result in a huge table.

After that, apply fileters on it to pick the routes we want.
```sql
select distinct a.company,a.num 
from route a join route b on (a.num=b.num and a.company=b.company)
where a.stop='115' and b.stop='137'
```
If you have difficulty understanding how it works. You can try the sqls below, one at a time. 
```sql
select company,num,pos,stop from route
where stop=115 or stop =137
order by company,num
```
Look for a bus that stops at 115 and 137. That's what we are asked to do.

Let's narrow down the problem to one route and observe how Cartesian product work when using self-join.

From the sql above, obviously, the No.12 of LRT company is one of the bus we're looking for.

We can start from here.
```sql
select * from route where company='LRT' and num='12'
```
List all stops of the route and check both 115 and 137 are in the route.
```sql
select a.company,a.num,a.pos,a.stop,b.stop from
        (select * from route where company='LRT' and num='12') a join
        (select * from route where company='LRT' and num='12') b
        on (a.num=b.num and a.company=b.company)
```
Given the join conditions, DB will generate all combinations of stops in table a and table b, i.e. (a.stop,b.stop) pairs.

It's called Cartesian product.
```sql
select a.company,a.num,a.pos,a.stop,b.stop
from (select * from route where company=LRT and num='12' order by pos) a join
     (select * from route where company=LRT and num='12' order by pos) b
     on (a.num=b.num and a.company=b.company)
where a.stop=115 and b.stop=137
```
All we need to do is find a rcord with stop 115 and 137 in the joined table.

Note that there are 18 records in table a; while there are 324 (18 X 18) records in the joined table which is result from Cartesian product.

Now use the same trick to solve the original problem.

The main idea of this method is to create a table with a header, (company, num, pos, start stop, end stop), by self-join.

The trick is useful when you want to add one column based on another whatever you purpose is.

But there is always a cost when using join.

8.
```sql
select a.company,b.num 
from (select * from route where stop=53) a join
      (select * from route where stop=230) b
on a.num=b.num and a.company=b.company
```

9.
```sql
select b.name,b.company,b.num
from 
 (select * from route) a join
 (select * from route join stops on (route.stop=stops.id)) b
 on (a.num=b.num and a.company=b.company)
where a.stop=53
```

10.
```sql
select g.num,g.company,name,g.num2,g.company2
from (select e.num,e.company,e.conn,f.num num2,f.company company2
      from (select a.company,a.num,a.stop,b.stop conn
            from route a,route b 
            where a.stop=(select id from stops where name='Craiglockhart')  and
                  a.company=b.company and a.num=b.num)e 
           
           join
           
           (select c.company,c.num,c.stop,d.stop conn2
            from route c,route d
            where c.stop=(select id from stops where name='Lochend') and
                  d.company=c.company and c.num=d.num)f
           
           on e.conn=f.conn2
     )g
     
     join stops on (g.conn=stops.id)
order by g.num,name,g.num2
```
The problem is to find a stop,say XYZ, that connnects Craiglockhart and Lochend

Step 1. Find direct route from Craiglockhart to XYZ

Step 2. Find direct route from Lochend to XYZ

Step 3. Find the interection of results of step1 and step2.

[top](#topics)

## References

1. https://www.essentialsql.com/learn-to-use-union-intersect-and-except-clauses/ , 表格的交集與聯集
2. https://stevenjhu.com/2020/01/18/sql%E5%AD%B8%E7%BF%92-join-%E9%80%A3%E6%8E%A5-sql-join/ , join的種類
3. https://www.w3schools.com/sql/sql_join.asp , join種類
4. https://www.sqlshack.com/a-step-by-step-walkthrough-of-sql-inner-join/ , Inner join 實際表格範例
5. https://www.sqlshack.com/sql-outer-join-overview-and-examples/ , outer join 實際表格範例表格範例
6. http://en.wikipedia.org/wiki/Null_%28SQL%29, NULL

[top](#topics)
