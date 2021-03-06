[TOC]
# 序列
用来解决在已经很多数据的情况下，插入新数据，主键不容易重复
- 创建序列

```sql
create sequence 序列名;
start with 初始值
increment by 步长
maxvaluet 最大值
cache 缓存
```

- 使用序列

```sql
select 序列名.currval from dual;//获取当前序列值
select 序列名.nextval from dual;//序列自增后返回当前值
```

在插入数据时，主键位置的值，可以直接用**`序列名.nextval`**代替
- 删除序列
`drop sequence 序列名;`


# 索引
当表中数据量比较大的时候，用来解决查询速度的问题
原理：类似于二分查找，在数据库中使用B树形结构进行数据的存储
- 创建索引
`create index 表名_index_字段名 on 表名（字段名）;`
特点：显式的创建，隐式的执行
- 删除索引
`drop index 索引名`


# 视图

**特点：**在视图中的操作都会在原始表中得到体现
**作用：**将重要的字段隐藏起来。安全

- 创建视图

```sql
create view 视图名 as select * from 表名;
create view 视图名 as select 字段1，字段2...from 表名
```

- 只读视图

```sql
create view 视图名 as select 字段1，字段2...from 表名 with read only;

```

- 删除视图
`drop view 视图名;`

# 事务

## 定义和特点
定义：是一个操作序列。要么都做，要么都不做，是数据库环境中的逻辑环境单位。
特点：**它是为了保证数据库的安全性和完整性。**

## 事务只能结束于以下几种情况：
1. 显示的使用`commit`和`rollback`;
2. 当执行DDL（Create、Alter、Drop）语句事务自动提交;
3. 用户正常断开连接时，`Transation`自动提交;
4. 系统崩溃或断电时事务自动回退

## 开发中常见问题
- sql语句必须**可重复**执行;
- sql语句后必须加分号;
- 必须在结尾加上commit;

# 分页

## rowid
rowid存储了每条记录的内存地址
- rowid在记录创建时生成，而且是不变的，直接指向硬件上的存储位置；
- 用rowid直接访问是最快的，但也是人力无法做到的；
- 只要记录没被搬动过，rowid是不变的

## rownum
- rownum是个伪列，查询的时候除非特别指定，否则不会显示；
- 主要用途是控制查询返回的行数；
- 只能使用**`< , <=`**;
- 当rownum与order by 一起使用时，会首先选出符合rownum的记录然后再进行排序，这会给我们的查询带来困难

查询员工信息，每页显示m条，查看第n页的员工信息

```sql
select rownum, e.* form emp e where ruwnum <= n*m;--第一步
select * from (select rownum r, e.* form emp e where ruwnum <= n*m) where r >= m*n - m;--第二步
```
查询按照工资排序的员工信息，每夜显示5条数据，查看第二页的员工信息

```sql
select rownum, e.* from emp e where rownum <=10 order by sal; --第一步
select * from (select rownum r, e.* from emp e where rownum <=10 order by sal) where r >5; --第二步
```

# 导入导出

## 导出
1. 命令行使用`exp`命令按照提示进行数据的导出（了解）;
2. 使用exp命令`exp  用户名/密码@库 full=y file=存储路径\文件名.dmp  log=存储路径\文件名.log`,但是效率低;
3. 使用数据泵命令`expdp`;

## 导入
1. 在命令行使用`imp`命令按照提示进行操作（了解）
2. 使用imp命令 `imp 用户名/密码@库  file=路径  log=路径`
3. 使用数据泵`impdp`(了解)

## 作用
1. 备份数据
2. 在开发阶段保证数据的安全性

# 三大范式
- 第一范式：设计的字段存储的应当是最小的数据单位
- 第二范式：一张表只能存储一类数据
- 第三范式：字段要和表的主键直接相关

1. **不止这三大范式，只不过其它不会用到，用到也就代表设计的表有问题**
2. **满足了后边的范式，一定满足前边的范式**