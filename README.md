# sqlzoo_solution
[sqlzoo](https://sqlzoo.net/)詳解

## select basics

## select from world

## select from nobel

## select in select
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
where continent='europe' and gdp/population > (select gdp/population from world where name='united kingdom') 
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
```
SELECT continent, name, area FROM world x
  WHERE area>= ALL
    (SELECT area FROM world y
        WHERE y.continent=x.continent
          AND area>0)
```
不是join，腦袋別打結。
8.
列出各洲國名字母排序最前面的國家
```
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

## sum and count
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

select distinct continent from world
```
雖然使用distinct更直覺，但使用groupb by有助於了解它的原理
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

## join[]
- 預設是 intersect/inner join
- 其他join
  - left/right
  - full 

## self join
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

## References
1. https://www.essentialsql.com/learn-to-use-union-intersect-and-except-clauses/ , 表格的交集與聯集
2. https://stevenjhu.com/2020/01/18/sql%E5%AD%B8%E7%BF%92-join-%E9%80%A3%E6%8E%A5-sql-join/ , join的種類
3. https://www.w3schools.com/sql/sql_join.asp , join種類
4. https://www.sqlshack.com/a-step-by-step-walkthrough-of-sql-inner-join/ , Inner join 實際表格範例
3. https://www.sqlshack.com/sql-outer-join-overview-and-examples/ , outer join 實際表格範例表格範例
