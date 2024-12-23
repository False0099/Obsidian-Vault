## 视图：
我们的视图可以理解为虚表，是根据我们实实在在存储在我们的实际磁盘上的数据创建的数据来创建的出局叫做我们的视图。

## 空值：
由于引入了我们的空值，我们再进行我们的布尔表达式进行判断的时候，就可能有了第三种情况。

##  unique
在我们创建一个表格的时候，声明某一个属性是否允许 `重复`.同时注意区别我们的主键。

## Primary_key
也就是说我们这一个键是我们的主键，我们的主键要求我们的这个值可以唯一表示我们的某一个属性。并且我们的这一个属性可以作为我们的

## Foreign_key
我们的 Foreigh_key 是我们的外键，我们的外键必须和我们的主键相对应，也就是说我们如果一个外键出现了，那么一定会存在对应的主键。
# DDL 语言
DDL 语言，也就是我们的 `Data Definition language`,是我们的 SQL 中提供定义表格式，删除表，修改表格式的相关语句。

我们的数据定义语言，也就是我们的 DDL 语言，主要就是包括了三个关键字，也就是我们的 `create`, `drop`, `alter`。

同时，我们的数据定义语言中只包含下面的四个操作对象，也就是我们的模式，表，视图，索引。

## 模式
```
create schema;
drop schema;
```
### 模式的定义
我们的模式的定义，也就是确定了我们的数据库的框架，我们对于我们的模式，可以理解为定义了一个**命名空间**，在这个空间内，可以定义该模式包含的数据库对象，例如基本表，视图，索引等等。

其中我们的语法如下所示：
```
create schema "S-T" authorization wang;
```
我们上面的功能就是给用户王定义一个学生-课程模式 S-T

```
create shcema test authorization zhang
create table tab1(
	col1 smallint,
	col2 int,
);
```

### 模式的删除
格式：
```
drop schema <模式名> <cascade|restrict>
```

·cascade 表示我们是级联删除，删除模式的同时，把该模式中的所有数据库对象都删除
·restrict 表示我们是限制删除，如果该模式中定义了下属的数据库对象（如表，视图等），则拒绝该删除语句的执行，（仅当该模式中没有任何下属中的对象才有）


## 表
### 表格的定义：
```
create table <表名>(
<列名> <数据类型> [<列级完整性约束条件>]
[<表级完整性约束条件>]
);
```

![[Pasted image 20240422204130.png]]

![[Pasted image 20240422205542.png]]
### 表格的修改
```
alter table <表名>
[add[column]] <新列名> <数据类型> [完整性约束]]
[add <表级完整性约束>]
[drop [column]] <列名> [cascade|restrict]]
[drop constrain<完整性约束名>[restrict|cascade]]
[alter column <列名><数据类型>];
```

![[Pasted image 20240422211046.png]]

![[Pasted image 20240422211057.png]]

![[Pasted image 20240422211336.png]]


### 模式与表
![[Pasted image 20240422210627.png]]


## 索引
索引就是为了加快我们的查询速度而创立的，在我们的关系型数据库中常见的索引就是我们的 B+树，哈希索引，位图索引等等。

只有我们的管理员或者我们创建表格的人能够**创建我们的索引**

### 创建索引：
```
create [unique] [cluster] index <索引名>
on <表名> (<列名> [<次序>]...);
```
![[Pasted image 20240422211725.png]]

![[Pasted image 20240422211855.png]]

### 修改索引，删除索引
## 视图
视图就是我们从一个或者几个基本表导出的表，例如我们的学生选课表中，我们进行**表格运算中间的表格**，就叫做我们的一个视图，也可以理解为我们的**虚表**。

### 创建视图：
```
```


```
create view;
drop view;
```
4. 索引
```
create index;
drop index;
alter index;
```


# DML 语言
DML 语言，也就是我们的 `Data Manipulation Language`,是我们的 SQL 中提供提供从表格中增删改查数据的语句。

## 完整性：
SQL DDL 包括定义完整性约束的命令，保存在数据中的数据必须满足所定义的完整性约束。


# 特点：

1. 综合统一：
	1. 集数据定义语言 (DDL, 有什么表，表格怎么连接), 数据操纵语言 (DML, 增删改查我们的数据), 数据控制语言 (DCL, 限制我们的数据当中的控制)功能于一体。
	2. 可以**独立完成数据库生命周期中的全部活动**：定义和修改、删除关系模式，定义和删除视图，插入数据，建立数据库；对数据库中的数据进行查询和更新；数据库重构和维护，数据库安全性、完整性控制，以及事务控制，嵌入式 SQL 和动态 SQL 定义。
	3. 用户数据库投入运行后，可根据需要随时逐步修改模式，不影响数据库运行。
	4. 用户数据操作符统一

2. 高度非过程化
	1. 我们的 SQL 语言只定义我们应该**做什么**，而不告诉我们的硬件，我们应该**怎么做**。同样的，我们也不需要了解我们的具体存储路径。

3. 面向集合的操作方式：
	1. 非关系型数据库采用面向记录的操作方式，操作对象是一条记录
	2. SQL 采用**集合**的操作方式。操作对象，查找结果可以是元组的集合，一次插入删除更新的对象可以是元组的集合。
4. 同一种语言提供多种使用方式：
	1. SQL 是独立的语言，能够独立的用于联机交互的使用方式
	2. SQL 是嵌入的语言，能够嵌入到高级语言程序当中（PHP）