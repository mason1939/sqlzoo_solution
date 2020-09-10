# sqlzoo_solution
[sqlzoo](https://sqlzoo.net/)詳解

## select basics

## select from world

## select from nobel

## select in select
用途: 把另一個查詢當做常數來使用。
特徵: 通常只會select一個欄位出來

## sum and count
用途: 使用聚合函數
特徵: 會用到group by,having

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
