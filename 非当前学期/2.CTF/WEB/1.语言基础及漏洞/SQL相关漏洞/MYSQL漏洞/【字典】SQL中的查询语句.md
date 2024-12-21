1. 查询某一个表格 (FROM)中的符合特定要求的 (WHERE)数据中的某几行 (SELECT)
```sql
SELECT first_name,last_name FROM users WHERE user_id='$id'
```

2. 查询我们数据库的名称是什么：
```sql
select database()
```

爆所有的数据库
```sql
select group_concat(schema_name) from information_schema.schemata
```

3. 查询我们数据库中的表格有哪些：
```sql
select group_concat(table_name) from information_schema.tables where table_schema = '你要爆的数据库名';
```

用于联合查询时，需要去除我们开头的select
```
```

有些时候，我们用 `group_concat` 会出错，我们考虑使用我们的limit 

![[Pasted image 20240310000814.png]]

4. 查询我们**你想要查询的那一个表格**中的列结构是什么：

(group_concat 是一个聚合函数，功能是把所有的信息都汇聚到一行当中)
```sql
select group_concat(column_name) from information_schema.columns where table_name = '你想要查询的那一个表格';
```

![[Pasted image 20240310000933.png]]

5. 查询**你想要查询的那一个数据库中**,,,,**你想要查询的那一个表格**的特定的字段信息：
```sql
select （列） from (表)
```

![[Pasted image 20240310001415.png]]

6. 查询某一个**元素为固定值**的条件下，我们的答案：

```sql
select (列) from (表) where (条件)
```
![[Pasted image 20240310001513.png]]
