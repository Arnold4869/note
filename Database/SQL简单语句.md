[TOC]
# 函数

## 单行函数

### 字符函数
- INITCAP(char)
- lower(char)
- upper(char)
- replace(char,from,to) 
- substr(char,pos,len)
...

### 数值函数
- abs(n):取绝对值
- ceil(n):向上取整
- floor(n):向下取整
- mod(m,n):取余
- round(m,n):四舍五入
...

### 日期函数

- months_between:返回两个日期间的月份
- add_months:返回把月份加到日期上的新日期
- next_day:返回指定日期后的星期对应的新日期
- last_day:返回指定日期所在的月的最后一天
- round:按选定的格式四舍五入
- trunc:对日期按指定的方式进行截断

### 转换函数

- to_date
- to_char
- to_number

其中，**字符串**可以和其余两个互相转换，但日期和数字不能直接转换

### 通用函数
- nvl(exp1,exp2):如果exp1的值为null,则返回exp2的值，否则返回exp1的值
- nvl2(exp1,exp2):类似于if..else
- decode(value,if1,if2,if3...,else):类似于if..else if ..else if .......else..

## 多行函数
**仅用于数值型**
- sum()
- avg()

**适用于任何数据**
- count()
- max()
- min()

### group by
多行函数中不能直接使用普通字段，除非`group by`

```sql
//查询不同部门的最高工资
select deptno, max(sal) frome emp group by deptno
```

### having
使用`group by`分组后在进行数据筛选时，`where`中不能出现多行函数，所以使用新的关键字`having`进行条件筛选
## 其它函数

# 查询

## 排序
直接通过在语句后方添加order by 语句来进行排序，默认是asc排序，也就是从小到大排序，可以设置desc关键词来设置成倒序排序

```sql
select * from emp order by ename(asc);//默认从小到大排序
select * from emp order by ename desc;//desc 修饰后反过来排序
```

## 模糊查询
- “**_**”代表一个占位
- “**%**”代表任意多个占位
- “**escape**”代表设定转义字符
使用**like**进行模糊查询

```sql
select * from emp where ename like 'S%';//查找ename是以S开头的
```
## where限定
```sql
select * from emp where sal > 3000;//
```

## 空值判断和 in

```sql
select * from emp where comm is null;//显示comm为空的数据
select * from emp where comm is not null;//显示comm非kong的数据
select * from emp where job in('CLERK','MANAGER');//显示job为CLERK或者MANAGER的数据
```

## group by
- 多行函数中不能直接使用普通字段，除非group by
- 多行函数中不能直接使用单行函数，除非group by
用法类似于order by，格式如下：
`select 多行函数, 分组字段 from 表名 group by 分组字段`
查询不同部门的不同工作岗位的人数：

```sql
select deptno, job, count(*) from emp group by deptno, job;
```
## having 
功能和where类似，但效率**低于**where
一般只在使用group by的情况下使用，在计算时，优先级**低于**group by，而where的优先级高于group by

