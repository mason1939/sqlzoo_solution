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
[top](#topics)
## Select from Nobel
[top](#topics)
## Select in Select

用途: 把另一個查詢當做常數來使用。
特徵: 通常只會select一個欄位出來

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
不是join，腦袋別打結。

8.
列出各洲國名字母排序最前面的國家
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

用途: 使用聚合函數
特徵: 會用到group by,having

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

- 預設是 intersect/inner join
- 其他join
  - left/right
  - full 

1.
```sql
SELECT matchid,player FROM goal 
  WHERE teamid='GER'
```
2.
```sql
SELECT id,stadium,team1,team2
  FROM game where id=1012
```
3.
```sql
SELECT player,teamid,stadium,mdate
  FROM game JOIN goal ON (id=matchid) where teamid='GER'
```
4.
```sql
SELECT team1,team2,player
  FROM game JOIN goal ON (id=matchid) where player like 'Mario%'
```
5.
```sql
SELECT player, teamid,coach, gtime
  FROM goal join eteam on (teamid=id)
 WHERE gtime<=10
```
6.
```sql
select mdate,teamname from game join eteam on (team1=eteam.id)
where coach = 'Fernando Santos'
```
7.
```sql
select player from game join goal on (id=matchid)
where stadium='National Stadium, Warsaw'
```
8.
```sql
SELECT distinct player
  FROM game JOIN goal ON matchid = id 
    WHERE teamid<>'GER' and (team1='GER' or Team2='GER')
```
9.
```sql
SELECT teamname,count(teamname)
  FROM eteam JOIN goal ON id=teamid
group by teamname
ORDER BY teamname
```
10.
```sql
SELECT stadium,count(teamid)
  FROM game JOIN goal ON id=matchid
group by stadium
```
Aggregate with stadium

11.
```sql
select matchid,mdate,count(teamid) from goal join game on (id=matchid)
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
select matchid,mdate,count(teamid) from goal join game on (id=matchid)
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
select matchid,count(teamid) from goal where teamid='ESP'
group by matchid
select id,mdate,count(teamid) from goal join game on (id=matchid) where team1='ESP' or team2='ESP'
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
And (id,mdate,team1,team2,score_1,score_2,gtime) is considered as a candidate key.
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
Initially I also don't feek comfortable when taking (id,mdate,team1,team2) as a group.
What I'm worried about is group team1 and team2 at the same time might ruin the result.
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
select distinct name from (select actorid from casting
                            where movieid in (select movieid from casting
                                              where actorid in (select id from actor
                                                                  where name='Art Garfunkel')
                                             )
                          )a
                    join actor on (actor.id=a.actorid)
where name <> 'Art Garfunkel'
```
[top](#topics)

## Self Join

用途: 想要撈取的資料在同一張表。將垂直的資料橫向展開。

例如: 

employee(id,name,duty,manager_id): manager_id是也是員工id, 現在想撈出主管的姓名

bus_route(num,company,pos,stop): 合併時把公車依路線做笛卡爾乘積後就列舉能某路公車任兩點間的路線集合。如同sql zoo中的範例

7. 
- 直接硬幹。笛卡爾乘積做完後再篩出要的
```sql
select distinct a.company,a.num 
from route a join route b on(a.num=b.num) 
where (a.stop=115 and b.stop=137)
```
- 先篩出想要的資料再做笛卡爾乘積
```sql
select distinct a.company,b.num 
from (select * from route where stop=115) a,
      (select * from route where stop=137) b
where a.num=b.num
```
- 等同上式
```sql
select distinct a.company,b.num from (select * from route where stop=115) a join
(select * from route where stop=137) b on (a.num=b.num)
```
[top](#topics)

## References

1. https://www.essentialsql.com/learn-to-use-union-intersect-and-except-clauses/ , 表格的交集與聯集
2. https://stevenjhu.com/2020/01/18/sql%E5%AD%B8%E7%BF%92-join-%E9%80%A3%E6%8E%A5-sql-join/ , join的種類
3. https://www.w3schools.com/sql/sql_join.asp , join種類
4. https://www.sqlshack.com/a-step-by-step-walkthrough-of-sql-inner-join/ , Inner join 實際表格範例
3. https://www.sqlshack.com/sql-outer-join-overview-and-examples/ , outer join 實際表格範例表格範例

[top](#topics)
