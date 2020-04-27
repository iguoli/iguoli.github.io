---
title: 关系型数据库笔记
date: 2017-07-04
tags: Database RDBMS
key: 2017-07-04-RDBMS-Note
---

## 第三章 SQL

## 3.3 SQL查询的基本结构

### 3.3.2 多关系查询（SQL查询的通用形式）

`select`子句，`from`子句，`where`子句。每种子句作用如下：  

- `select`子句用于列出查询结果中所需要的属性
- `from`子句是需要访问的关系列表
- `where`子句是一个作用在`from`子句中关系的属性上的谓词

### 3.3.3 自然连接

自然连接只考虑那些**在两个关系模式中都出现的属性（两个关系中具有相同名称的所有属性）上取值相同**的元组对。

```sql
--找出授课教师的的姓名及他们讲授的所有课程ID
select name, course_id
from instructor, teaches
where instructor.ID = teaches.ID;

--更简洁的写法
select name, course_id
from instructor natural join teaches;
```

SQL提供了一种自然连接的构造形式(`join...using`)，允许用户来指定需要哪些列相等。

```sql
select name, title
from (instructor natural join teaches) join course using (course_id);
```

## 3.4 附加的基本运算

### 3.4.1 更名运算

`as`子句：`old_name as new_name`
as子句即可以出现在`select`子句中，也可以出现在`from`子句中。

### 3.4.2 字符串运算

SQL使用单引号来标示字符串，例如`'Computer'`。如果单引号是字符串组成部分，就用两个单引号来表示。在SQL标准中，字符串上的相等运算是大小写敏感的。通过`like`操作符来实现模式匹配，模式是大小写敏感的。

- 百分号(%)：匹配任意字符串
- 下划线(\_)：匹配任意字符

```sql
select dept_name
from department
where builiding like '%Watson%';
```

为使模式中能够包含特殊字符（即`%`和`\_`)，SQL允许定义转义字符。在`like`语句中使用`escape`关键字来定义转义字符。

```sql
like 'ab\%cd%' escape '\'  --匹配所有以ab%cd开头的字符串
like 'ab\\cd%' escape '\'  --匹配所有以ab\cd开头的字符串
```

### 3.4.4 排列元组的显示次序

`order by`子句

```sql
-- 按字母顺序列出在Physics系的所有教师
select name
from instructor
where dept_name 'Physics'
order by name;
```

`order by`子句默认使用升序，可以使用`desc`表示降序，或显式的使用'asc'表示升序。

## 3.5 集合运算

SQL作用在关系上的`union`、`intersect`和`except`运算对应于数学集合论中的`∪(并集)`、`∩（交集)`和`-(差）`

### 3.5.1 并运算

```sql
--找出在2009年秋季或2010年春季或两个学期都开课的所有课程
(select course_id
from section
where semester='Fall' and year=2009)
union
(select course_id
from section
where semester="Sprint' and year=2010);
```

与`select`子句不同，`union`运算自动去除重复。如果希望保留重复，需要使用`union all`

## 3.6 空值

SQL在谓词中使用特殊的关键字`null`测试空值。

```sql
select name
from instructor
where salary is null;

select name
from instructor
where salary is not null;
```

当一个查询使用`select distinct`子句时，重复元组将被去除。

## 3.7 聚集函数

**聚集函数** 是以 **值的一个集合（集或多重集）为输入，返回单个值** 的函数。SQL提供了5个固有聚集函数：

- 平均值：`avg`
- 最小值：`min`
- 最大值：`max`
- 总和：`sum`
- 计数：`count`

`sum`和`avg`必须是数字集，但其它运算符还可以用在非数字数据类型的集合上，如字符串。

### 3.7.1 基本聚集

```sql
--找出Computer Science系的老师平均工资
select avg(salary) as avg_salary
from instructor
where dept_name='Comp. Sci.';
```

某些情况下，在计算聚集函数前还应该先去掉重复元组。

```sql
--找出在2010年春季讲授课程的老师总数
select count(distinct ID)
from teaches
where semester='Sprit' and year=2010;
```

计算关系中的元组个数

```sql
select count(*)
from course;
```

### 3.7.2 分组聚集

有时候希望将聚集函数作用到 **一组元组集** 上，可以使用`group by`子句来构造分组。  

```sql
--找出每个系的平均工资
select dept_name, avg(salary) as avg_salary
from instructor
group by dept_name;

--找出每个系在2010年春季讲授课程的教师人数
select dept_name, count(distinct ID) as instr_count
from instructor natural join teaches
where semester='Spring' and year=2010
group by dept_name;
```

**注意：在`select`子句中没有被聚合的属性，只能是`group by`子句中用来分组的属性。**

### 3.7.3 having子句

`having`子句针对`group by`子句构成的分组进行条件限定。

```sql
--对于在2009年讲授的每个课程段，如果该课程段至少有两名学生选课，找出选修该课程段的所有学生总学分的(tot_cred)的平均值
select course_id, semester, year, sec_id, avg(tot_cred)
from takes natural join student
where year=2009
group by course_id, semester, year, sec_id
having count(ID) >= 2;
```

注意：与`select`子句类似，`having`子句中没有被聚合的属性，只能是`group by`子句中用来分组的属性。上述查询可以通过下述操作序列来说明：

1. 先根据`from`子句来计算出一个关系；
2. 如果出现`where`子句，`where`子句中的谓词将应用到`from`子句的结果关系上；
3. 如果出现`group by`子句，满足`where`子句的元组通过`group by`子句形成分组；
4. 如果出现`having`子句，它将应用到每个分组上，不满足`having`子句谓词的分组将被丢弃；
5. `select`子句利用剩下的分组产生出查询结果中的元组，然后在每个分组上应用聚集函数来得到单个的结果元组。

## 3.8 嵌套子查询

子查询是嵌套在另一个查询中的select-from-where表达式。子查询嵌套在where子句中，通常用于对集合的成员资格、集合的比较以及集合的基数进行检查。

### 3.8.1 集合成员资格

连接词`in`测试元组是否在集合中，集合是由`select`子句产生的一组值构成的。连接词`not in`测试元组是否不在集合中。

```sql
--查找2009年秋季和2010春季同时开课的课（可以使用集合的交运算，但这次使用`in`连接词）
select distinct course_id
from section
where semester='Fall' and year=2009 and
      course_id in (select course_id
                    from section
                    where semester='Spring' and year=2010);
```

### 3.8.2 集合的比较

- `> some`，表示至少比某一个要大
- `< some`
- `<= some`
- `>= some`
- `= some`，等价于`in`
- `<> some`，不等价于`not in`
- `> all`，表示比所有的都大
- `< all`
- `<= all`
- `>= all`
- `= all`，不等于`in`
- `<> all`，等价于`not in`

### 3.8.3 空关系测试

`exsits`关键字测试一个子查询的结果中是否存在元组，子查询非空时返回true。

```sql
select course_id
from section as S
where semester='Fall' and year=2009 and
exists (select course_id
        from section as T
        where semester='Spring' and year=2010 and
              S.course_id=T.course_id);
```

使用来自外层查询相关名称的子查询被称作**相关子查询(correlated subquery)**

### 3.8.4 重复元组存在性测试

`unique`用于测试在一个子查询的结果中是否存在重复元组，如果没有重复元组，返回true。

```sql
select T.course_id
from course as T
where unique (select R.course_id
              from section as R
              where T.course_id = R.course_id and
                    R.year > 2009);
```

### 3.8.5 from子句中的子查询

任何 **select-from-where** 表达式返回的结果都是关系，因而可以插入到另一个 **select-from-where** 中任何关系可以出现的位置。

```sql
--找出系平均工资超过42000的那些系的教师平均工资，使用having子句
select dept_name, avg(salary) as avg_salary
from instructor
group by dept_name
having avg(salary) > 42000;

--使用from子查询
select dept_name, avg_salary
from (select dept_name avg(salary) as avg_salary
      from instructor
      group by dept_name)
where avg_salary > 42000;

--找出在所有系中工资总额最大的系
select dept_name, max(total_salary)
from (select dept_name, sum(salary) as total_salary
      from instructor
      group by dept_name);
```

### 3.8.6 with子句

`with`子句提供定义临时关系的方法

```sql
--找出所有工资总额大于所有系平均工资总额的系
with dept_total (dept_name, value) as
    (select dept_name, sum(salary)
     from instructor
     group by dept_name),
    dept_total_avg (value) as
    (select avg(value)
     from dept_total)
select dept_name
from dept_total, dept_total_avg
where dept_total.value >= dept_total_avg.value;
```

### 3.8.7 标量子查询

SQL允许子查询出现在返回单个值的表达式能够出现的任何地方，只要该子查询只返回包含单个属性的单个元组，这样的子查询称为**标量子查询(scalar subquery)**。

## 3.9 数据库的修改

### 3.9.1 删除

```sql
delete from R
where P;
```

`delete`命令只能作用于一个关系

### 3.9.2 插入

```sql
--在查询结果的基础上插入元组，让Music系每个修满144学习的学生成为Music系的教师，其工资为18000。
insert into instructor
    select ID, name, dept_name, 18000
    from student
    where dept_name='Music' and tot_cred>144

```

### 3.9.3 更新

```sql
update instructor
set salary = salary * 1.05;
where salary > 70000;
```

## 第4章 中级SQL

## 4.1 连接表达式

### 4.1.1 连接条件

- `join...using`子句，是一种自然连接的形式，只需要在指定属性（两个关系中具有相同名称的属性）上的取值相等。
- `on`条件允许在参与连接(内连接、左连接、右连接、全连接)的关系上设置通用的谓词，写法与`where`子句谓词类似。

### 4.1.2 外连接

- 左外连接(left outer join)，只保留出现在**左外连接**运算**左边**关系中的元组
- 右外连接(right outer join)，只保留出现在**右外连接**运算**右边**关系中的元组
- 全外连接(full outer join)，保留出现在两个关系中的元组  

对应的，SQL中把常规连接称作**内连接(`inner join`)**，`inner`关键字可选，当join子句没有使用`outer`前辍，默认的连接类型就是`inner join`。

### 4.1.3 连接类型和条件

|连接类型
|-------------
|inner join
|left outer join
|right outer join
|full outer join

|连接条件
|---------
|natural
|on &lt;谓词&gt;
|using(col1, col2,...,coln)

## 4.2 视图

### 4.2.1 视图定义

**create view** v **as** &lt;query expression&gt;

```sql
create view faculty as
select ID, name, dept_name
from instructor;
```

### 4.2.3 物化视图

特定数据库系统允许存储视图关系，视图的结果会存放在数据库中。但是它们保证：如果用于定义视图的实际关系改变，视图也跟着修改。  
这样的视图被称为 **物化视图(materialized view)**。

## 4.3 事务

**事务(transaction)** 由查询和（或）更新语句的序列组成。

- `Commit work`：提交当前事务，将该事务所做的更新在数据库中持久保存。事务被提交后，一个新的事务自动开始。
- `Rollback work`：回滚当前事务，即撤销该事务中所有的SQL语句对数据库的更新。

关键词work在两条语句中都是可选的。

## 4.4 完整性约束

### 4.4.1 单个关系上的约束

`create table`命令上可以包括完整性约束语句。包括：

- **not null**
- **unique**
- **check(<谓词>)**

### 4.4.5 参照完整性

在一个关系给定属性集上的取值也在另一关系特定属性集的取值中出现，称为 **参照完整性(referential integrity)**。

更一般地，令关系r1和r2的属性集分别为R1和R2，主码分别为K1和K2。如果要求对r2中任意元组t2，均存在r1中元组t1，使得t1.K1 = t2.a，我们称R2的子集a为参照关系r1中K1的 **外码(foreign key)**。

这种要求称为 **参照完整性约束(referential integrity constraint)** 或 **子集依赖(subset dependency)**。

```sql
foreign key (dept_name) references department

-- 级联删除，级联更新
create table course(
...
foreign key (dept_name) references department
    on delete cascade
    on update cascade,
...);

```

### 4.4.7 复杂check条件与断言

一个 **断言(assertion)** 就是一个谓词，它表达了我们希望数据库总能满足的一个条件。**域约束** 和 **参照完整性约束** 是断言的特殊形式。SQL 中的断言为如下形式：

```sql
CREATE ASSERTION assertion-name CHECK predicate;
```

## 4.5 SQL的数据类型与模式

### 4.5.3 创建索引

在关系的属性上所创建的 **索引(index)** 是一种数据结构，它允许数据库系统高效的找到关系中，那些在索引属性上取给定值的那些元组，而不用扫描关系中的所有元组。创建索引的语法形式：

```sql
create index student_index on student(ID);
```

### 4.5.7 模式、目录与环境

数据库中为解决命名空间问题，提供了三层关系命名机制：最顶层由 **目录(catalog)** 构成，每个目录都可以包含 **模式(schema)**（一些数据库实现用术语 **数据库** 代替术语 **目录** ）。为了唯一标识一个关系，它包含三部分：

**catalog_name.schema_name.table_name**

```sql
-- 创建和删除模式
create schema
drop schema
```

## 4.6 授权

对数据的授权包括：

- 授权读取数据(select)
- 授权插入新数据(insert)
- 授权更新数据(update)
- 授权删除数据(delete)

每种类型的授权都称为一个 **权限(previlege)**。

### 4.6.1 权限的授予与收回

SQL 标准包括`select`、`insert`、`update`和`delete`权限。`grant`语句用来授予权限。

```sql
GRANT 权限列表
ON 关系名或视图名
TO 用户/角色列表;

grant select on department to User1, User2;
grant update (budget) on department to User1, User2;
```

`revoke`语句用来收回权限。

```sql
REVOKE 权限列表
ON 关系名或视图名
FROM 用户/角色列表;

revoke select on department from User1, User2;
revoke update (budget) on department from User1, User2;
```

### 4.6.2 角色

可以为某一类用户设定一个角色，然后给角色授予权限，这类用户就会拥有相同的权限。

```sql
create role instructor;
grant select on dependent to instructor;

-- 角色可以授予给用户，也可以授予给其他角色
create role dean;
grant instructor to dean;
grant dean to User1;
```

## 数据库设计与E-R模型

## 7.5 实体联系图

### 7.5.6 弱实体集

- 没有足够属性以形成主码的实体集称作 **弱实体集(weak entity set)**

- 有主码的实体集称作 **强实体集(strong entity set)**

- 弱实体集必须与另一个称作 **标识(identifying)** 或 **属主实体集(owner entity set)** 的实体集关联才能有意义。

- 每个弱实体集必须和一个标识实体集关联，也就是说弱实体集 **存在依赖(existence dependent)** 于标识实体集。

- 我们称标识实体集 **拥有(own)** 它所标识的弱实体集。

- 将弱实体集与其标识实体集相联的联系称为 **标识性联系(identifying relationship)**。