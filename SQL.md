# SQL
Structured Query Language
## 表的创建
```SQL
create table table_name
(
  col_name type [完整性约束条件]
  [表级完整性约束条件]
);
```
完整性约束条件有
1. NOT NULL
2. UNIQUE
3. PRIMARY KEY

表级完整性约束
1. PRIMARY KEY(col_name1,...)
2. FOREIGN KEY(col_name) REFERENCE table_name(col_name)

## 修改表
`alter table table_name`
- `add col_name type [完整性约束]`
- `drop col_name`
- `alter column col_name type`
## 删除表
`drop table table_name [restrict|cascade]`

RESTRICT：删除表是有限制的。
- 欲删除的基本表不能被其他表的约束所引用
- 如果存在依赖该表的对象，则此表不能被删除
>例如，在表上建有视图，就无法删除

CASCADE：删除该表没有限制。
- 在删除基本表的同时，相关的依赖对象一起删除
>基本表定义被删除，数据被删除,表上建立的索引、视图、触发器等一般也将被删除
## 索引
表的主人或者DMA可以创建索引，一般为PK和UNIQUE的列会自动创建索引，关系数据库内部实现技术输入内模式
两种数据结构实现
- B+树，动态平衡
- HASH树，查找速度快
### 唯一UNIQUE索引
唯一索引和普通索引的区别是唯一索引的列值不能重复，
```SQL
create index index_name on table_name(col_name [ASC | DESC], ..)
create unique index index_name
```
>ASC = ascend DESC=descend
### 聚簇索引
`create cluster index index_name on`
- B+树的叶子节点，即为数据节点的物理地址，数据在物理上按顺序排列，比较有利于查询区间值
- 一个基本表上最多只能建立一个聚簇索引
- 经常更新的列不宜建立聚簇索引
## 数据查询
```SQL
SELECT [ALL|DISTINCT] <目标列表达式>[，<目标列表达式>]
FROM <表名或视图名 [别名]>[,<表名或视图名 [别名]> ]
[ WHERE <条件表达式> ]
[ GROUP BY <列名1> [ HAVING <条件表达式> ] ]
[ ORDER BY <列名2> [ ASC|DESC ] ]
```
### 目标列表达式
- 函数, `LOWER(name)`
- 别名, `Sname name`
- 算术表达式 `2022-Sage Birth`
### ALL与DISTINCT
- ALL 返回所有数据
- DISTINCT 去掉重复的值

### 查询条件
- 比较，=,>,<,<>,NOT
- 范围 between and,not between and
- 集合 in
- 匹配 like, %表示任意长度字符串, _下划线表示任意单个字符,用ESCAPE '\'表示转义字符
- 空值 is NULL, is not null
- 多重条件 and or not

### 聚集函数
可以配合分组查询使用
- COUNT([ALL|DISTINCT] [*|col_name])
- SUM,AVG
- MAX,MIN
### 分组Group
根据目标列分组

### HAVING短语与WHERE子句的区别
作用对象不同，WHERE子句作用于基表或视图，从中选择满足条件的元组;HAVING短语作用于组，从中选择满足条件的组
### 连接查询
- 等值与非等值连接查询
- 自身连接
- 外连接`from table1 left out join table2 on(condition)`,左/右外连接
- 复合条件连接
### 嵌套查询
子查询的结果作为外层查询where语句的输入
```SQL
select col
from table1
where col in
(
  select col
  from table2
  where ...
)
```
- 子查询无法使用`order by`语句
- 有些情况可以使用连接查询代替

where语句类型
1. IN谓词，嵌套查询返回的是元组集合
2. 比较运算符，内层返回单值(单个元组或者聚集函数), 通常配合ANY(SOME)| ALL使用
3. ANY(SOME)和ALL
4. EXISTS|NOT EXISTS
>存在量词带有EXISTS谓词的子查询不返回任何数据，只产生逻辑真值“true”或逻辑假值“false”。若内层查询结果非空，则外层的WHERE子句返回真值，若内层查询结果为空，则外层的WHERE子句返回假值。由EXISTS引出的子查询，其目标列表达式通常都用* ，因为带EXISTS的子查询只返回真值或假值，给出列名无实际意义

### 集合查询
- union [all] 并集，默认进行去重
- intersect 交集
- except 差
### SELECT一般形式
```
SELECT [ALL|DISTINCT]
   <目标列表达式> [别名] [ ，<目标列表达式> [别名]] …
 FROM  <表名或视图名> [别名] [ ，<表名或视图名> [别名]] …
 [WHERE <条件表达式>]
 [GROUP BY <列名1>
 [HAVING     <条件表达式>]]
 [ORDER BY <列名2> [ASC|DESC]]
```
## 数据更新
### 插入数据
插入元组,缺省的默认值为NULL
```SQL
insert into table1(col1,col2,...)
values()
```
插入子查询结果，子查询结果与目标列的相匹配
```SQL
insert into table1(col1,col2,...)
子查询
```
插入数据时会检查
- 实体完整性
- 参照完整性
- 用户定义完整性
  - NOT NULL约束
  - UNIQUE约束
  - 值域约束
### 修改数据
```SQL
update table1
set col1=expression[,...]
where 条件
```
### 删除数据
```SQL
delete from table
where condition
```
## 空值相关处理
- 判断是否空值，`is NULL | is NOT NULL`
- NOT NULL|UNIQUE|码属性不能取空值
- 空值与另一个空值算术运算为空值，空值的NOT也为空值，空值与正常boolean为视为FALSE，空值的比较运算结果为UNKNOWN
> NULL < 60结果为unknown
## 视图
视图特点
- 虚表，是一个或几个基本表的导出，但是不存放数据，数据仍存在原来的基本表中
- 基本表数据发生变化，视图也会随之改变

### 创建视图
```SQL
create view name[(col1,col2,...)]
AS 子查询
[with check option]
```
>省略列名时，则由子查询中SELECT目标列中的诸字段组成

需要明确指定视图的所有列名:
- 某个目标列是集函数或列表达式
- 目标列为  *
- 多表连接时选出了几个同名列作为视图的字段
- 需要在视图中为某个列启用新的更合适的名字

对于`with check option`
- 修改时自动加上where条件
- 删除时自动加上where条件
- 插入时检查where条件，空值自动定义

### 删除视图
`drop view name [cascade]`
CASCADE参数会删除依赖该视图的视图

### 查询视图
视图消解法（View Resolution）
- 进行有效性检查，检查操作的表、视图等是否存在。如果存在，则从数据字典中取出视图的定义
- 把视图定义中的子查询与用户的查询、更新请求结合起来，转换成等价的对基本表的查询、更新操作
- 执行修正后的查询、更新操作

视图图消解法局限性
- where 子句无法使用聚合函数作为表达式，当以聚合函数生成列作为条件时，导致使用聚合函数的视图无法得到正确的where查询
### 更新视图
- 由于视图是不存储数据的虚表，数据是来自其他基表部分数据，因此，对视图的更新最终是对基表的更新。
- SQL语言标准规定：只能对直接定义在一个基表上的视图进行插入、修改、删除等更新操作，对定义在多个基表或其它视图之上的视图，数据库管理系统不允许进行更新操作。
- 尽管视图数据只来源于一个基表，如果SELECT语句含有GROUP BY、DISTINCT或聚集函数等，除可执行删除操作外，不能进行插入或修改操作。












