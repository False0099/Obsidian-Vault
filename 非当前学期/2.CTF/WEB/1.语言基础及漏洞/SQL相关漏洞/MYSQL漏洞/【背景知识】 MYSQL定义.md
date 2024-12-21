## Mysql 基础知识
RDBMS 指关系型数据库，有着统一的种类，RDBMS 中的数据存储在称为表的数据库对象中。表是相关的数据项的集合，它由行和列构成。（库->表->列->行，类似于我们的 EXCEL）

SQL 是用于访问和处理数据库的标准语言，SQL 的范围包括数据插入，查询，更新，删除。

## 基础语法：
```sql
show databases 显示数据库
use db_name 打开数据库
show tables 显示数据表
describe table_name 显示表结构（有多少个点，每个点包含哪些信息）
show columns from table_name 显示表中各字段信息（有哪些行）
show create table 
status 列出mysql相关信息
drop databases 删除数据库
delete from table_name 清空数据表
drop table table_name 删除数据表
mysql -uroot -proot 数据库连接
```

## 注入原因：
因为我们的 web 程序和我们的 sql 采用的语言不互通，所以我们的 web 程序是需要执行我们的对应的 sql 语句的。因此，我们的应用会将我们的用户输入当作我们的一个 sql 语句的参数，去执行对应的 sql 语句。

```sql
select *from users where username ='$u' and password ='$p' 
```

这个时候，如果我们给我们的 u 输入为：
```
u=admin' or 1=1 #
```

最后就会被当作一个：
```
select *from users where username='admin' or 1=1 # and password='$p'
```
