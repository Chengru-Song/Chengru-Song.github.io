---
layout: article
title: 【工作】数据库基础知识
key: database_sql
tags:
- 301-work-database
- 301-work-basics
- 301-work-interview
category: [work, interview, database]
---


- [整体架构](#整体架构)
- [基础知识](#基础知识)
  - [数据库名词解释](#数据库名词解释)
  - [数据库的基本概念](#数据库的基本概念)
  - [数据库三大范式](#数据库三大范式)
  - [数据库关联查询](#数据库关联查询)
- [练习](#练习)
  - [单表](#单表)
    - [排序](#排序)
  - [多表查询](#多表查询)
- [Take Away Notes](#take-away-notes)
# 整体架构

了解关系型（MySQL）和非关系型数据库（Redis）可以参考下面这个图：
![Image](/assets/images/database.png)

其中相关的基础概念可以分别参考这两个链接作为查询：

Mysql: <https://blog.csdn.net/ThinkWon/article/details/104778621>

Redis: <https://blog.csdn.net/ThinkWon/article/details/103522351>

# 基础知识

首先我们需要了解一些**数据库的基本概念**，因为这些概念有助于我们构造出**合理的数据库和查询语句**。其次我们会讲一些相关的**查询语句**，以及**如何跨表查询**，各种不同的跨表查询有什么区别。最后会根据现有的一些搭建看板的需求进行一些补充，这部分可以配合业务的理解进行更复杂的查询。

## 数据库名词解释

| 名词                        | 解释                                                                                                                                                                                                         |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 实体（entity）              | 就是实际应用中用于数据描述的事物，一般为名词，如学生、老师、课程。                                                                                                                                           |
| 属性（attribute）           | 事物（实体）必不可少的性质。如学生有：学号、姓名、年龄等属性。                                                                                                                                               |
| 字段（fields）              | 数据项。是属性在数据库中的表示形式，即我们常说的某个表的“列”。                                                                                                                                               |
| 记录（record）              | 实体的一个案例。如学生实体中的张三、李四、王五都是一条记录。                                                                                                                                                 |
| 键（key）                   | 也称为码，可以对记录进行唯一标识的属性或属性集。如学生有属性（身份证，学号，姓名，年龄），其中身份证或学号都可以唯一标识一个学生。那么学生的键就有：身份证、学号、身份证和学号。                             |
| 主属性                      | 包含在键中的属性成为主属性。如身份证、学号都是主属性。其余属性称为非主属性。                                                                                                                                 |
| 主键（primary key）         | 从键中选择一个唯一的、尽量小的、非空、不可更新的最为实体的主键。比如学生的主键可以选择身份证也可以选择学号，但要保证唯一性，只能选择其一。                                                                   |
| 候选键                      | 有时候码很多，但不一定都能作为主键，有时候满足主键要求的又不止一个，这样，我们称满足主键要求的键称为候选键。                                                                                                 |
| 外键（foreign key）         | 对连接父表和子表（一般为多对一关系）的字表主键，在父表中成为外键。如学生（学号，姓名，课程号）和课程（课程号，课程名），其中课程号是子表课程的主键，所以在学生表中称为外键。                                 |
| 关联表（associative table） | 多对多关系中两个父表的子表称为关联表。                                                                                                                                                                       |
| 属性的依赖                  | 函数依赖的简称，可理解为某个属性集A决定某个属性B，可记做：B依赖A或A→B。例如学号→姓名。                                                                                                                       |
| 完全依赖                    | 某个属性集A完全决定属性B，不能部分决定。如（学号，课程号）→学生姓名是部分依赖；而（学号，课程号）→课程成绩是完全依赖，因为只有学号不知道课程号无法查某科成绩，同样，只知道课程号不知道是哪个学生也无法查询。 |
| 传递依赖                    | 属性A→属性B，属性B→属性C，则有A→C，这样的依赖关系称为传递依赖。                                                                                                                                              |

## 数据库的基本概念

对于像Excel一样一条条的数据，一个非常intuitive的问题就是如何用好几个不同的表把所有数据组合在一起。我们会用和学生上学相关的信息作为例子来理解相关的概念。

|   学号    | 姓名 | 年龄 | 年级 |  学院  |   课程名   | 课程编号 |
| :-------: | :--: | :--: | :--: | :----: | :--------: | :------: |
| 201511122 | 承儒 |  27  | 2015 | 计算机 | 数据库基础 |  20003   |

这里首先介绍数据库设计范式的概念，为什么需要数据库范式，因为我们要避免**数据冗余**和**数据不一致性**，例如现在系统中有两张表：

表一：

|   学号    | 姓名 | 年龄 | 年级 |  学院  |   课程名   | 课程编号 |
| :-------: | :--: | :--: | :--: | :----: | :--------: | :------: |
| 201511122 | 承儒 |  27  | 2015 | 计算机 | 数据库基础 |  20003   |

表二：

| 课程编号 | 课程名     |
| -------- | ---------- |
| 20003    | 数据库基础 |

首先这里面有数据冗余，课程相关信息被存储了两次。其次如果现在需要更新课程名为*数据库理论*，那么我必须同时更新表一和表二，这就可能带来数据不一致性。

因此，数据库设计范式就是为了避免以上问题。

## 数据库三大范式

1. 第一范式 - **原子性**
所有字段都不可以再分割。
  
    下表就不满足第一范式。

    | 学号     | 姓名 | 年级专业       |
    | -------- | ---- | -------------- |
    | 20111111 | 承儒 | 2014级信息安全 |

    进一步拆分成一下成下表则满足第一范式

    | 学号     | 姓名 | 年级   | 专业     |
    | -------- | ---- | ------ | -------- |
    | 20111111 | 承儒 | 2014级 | 信息安全 |

2. 第二范式 - **唯一性约束**

    一条数据中至少有一个主键，所有不是主键的列必须依赖于主键，而不能依赖主键的一部分。
    还是以下表为例，如果下表的主键是“学号”，那么课程名是依赖于课程编号的，所以不满足第二范式。

    |   学号    | 姓名 | 年龄 | 年级 |  学院  |   课程名   | 课程编号 |
    | :-------: | :--: | :--: | :--: | :----: | :--------: | :------: |
    | 201511122 | 承儒 |  27  | 2015 | 计算机 | 数据库基础 |  20003   |

    还有一种情况叫做联合主键，就是两列共同作为该表的主键，那么所有其他列**必须依赖于这两列**而不是其中一列，例如如果把上表的“学号”和“课程编号”作为联合主键，那么“课程名”依赖于“课程编号”而不是“学号”和“课程编号”的组合，这种情况下也是不满足第二范式的。

3. 第三范式
   
    - 原子性：所有属性不可分割；
    - 有主键：必须有一个主键；
    - 完全依赖：必须完全依赖主键；
    - 无传递依赖：必须直接依赖，不能传递依赖。

    举个:chestnut:，假如有一个记录书籍和作者的表，

    | AUTHOR_ID |       作者       |     书     | Author_Nationality |
    | :-------: | :--------------: | :--------: | :----------------: |
    | Auth_001  |   奥森斯科特卡   | 安德的游戏 |        美国        |
    | Auth_001  |   奥森斯科特卡   | 安德的游戏 |        美国        |
    | Auth_002  | 玛格丽特阿特伍德 | 婢女的故事 |       加拿大       |
    
    在上面这个表里面，有这种关系book-> Author -> Author_Nationality，这就是传递依赖，不符合第三范式。可以进行如下拆解避免传递依赖：

    | AUTHOR_ID |       Author       |     Book     |
    | :-------: | :--------------: | :--------: |
    | Auth_001  |   奥森斯科特卡   | 安德的游戏 |
    | Auth_001  |   奥森斯科特卡   | 安德的游戏 |
    | Auth_002  | 玛格丽特阿特伍德 | 婢女的故事 |

    | Author_ID |    Author    | Author_Nationality |
    | :-------: | :----------: | :----------------: |
    | Auth_001  | 奥森斯科特卡 |        美国        |

## 数据库关联查询

关联查询就是把好几张表的内容，通过其中的一些列放在一起，也可以不通过这些列简单的放在一起，一共这几种关联查询的语句：

- 交叉连接（CROSS JOIN）
- 内连接（INNER JOIN）
- 外连接（LEFT JOIN/RIGHT JOIN）
- 联合查询（UNION/UNION ALL）
- 全连接（FULL JOIN）

如果有不理解的内容，可以参考给出的链接，里面有非常全的examples。

1. Inner Join

    The `INNER JOIN` keyword selects records that have matching values in both tables.
    Grammer:

    ```sql
    SELECT column_name(s)
    FROM table1
    INNER JOIN table2
    ON table1.column_name = table2.column_name;
    ```

    Inner Join选取的内容：
    ![Image](https://www.w3schools.com/sql/img_innerjoin.gif)

    Ref: <https://www.w3schools.com/sql/sql_join_inner.asp>

2. Left Join
    
    The `LEFT JOIN` keyword returns all records from the left table (table1), and the matching records from the right table (table2). The result is 0 records from the right side, if there is no match.

    Grammer:
    
    ```sql
    SELECT column_name(s)
    FROM table1
    LEFT JOIN table2
    ON table1.column_name = table2.column_name;
    ```

    Left Join选取内容：
    ![Image](https://www.w3schools.com/sql/img_leftjoin.gif)

    Ref：<https://www.w3schools.com/sql/sql_join_left.asp>

3. Right Join

    不建议用这个，统一用`LEFT JOIN`.

    The `RIGHT JOIN` keyword returns all records from the right table (table2), and the matching records from the left table (table1). The result is 0 records from the left side, if there is no match.

    Grammer: 

    ```sql
    SELECT column_name(s)
    FROM table1
    RIGHT JOIN table2
    ON table1.column_name = table2.column_name;
    ```

    `RIGHT JOIN`选取内容：
    ![Image](https://www.w3schools.com/sql/img_rightjoin.gif)

4. FULL JOIN
   
    The `FULL OUTER JOIN` keyword returns all records when there is a match in left (table1) or right (table2) table records.

    Grammer:

    ```sql
    SELECT column_name(s)
    FROM table1
    FULL OUTER JOIN table2
    ON table1.column_name = table2.column_name
    WHERE condition;
    ```

    全连接，全外连接的选取内容：
    ![Image](https://www.w3schools.com/sql/img_fulljoin.gif)

    Ref: <https://www.w3schools.com/sql/sql_join_full.asp>

5. UNION

    The `UNION` operator is used to combine the result-set of two or more SELECT statements.

    - Every `SELECT` statement within UNION must have the same number of columns
    - The columns must also have similar data types
    - The columns in every `SELECT` statement must also be in the same order

    Syntax:

    ```sql
    SELECT column_name(s) FROM table1
    UNION
    SELECT column_name(s) FROM table2;
    ```

    Ref: <https://www.w3schools.com/sql/sql_union.asp>


# 练习

## 单表

### 排序

1. 查找最晚入职员工的所有信息：

    <https://www.nowcoder.com/practice/218ae58dfdcd4af195fff264e062138f?tpId=82&&tqId=29753&rp=1&ru=/ta/sql&qru=/ta/sql/question-ranking>

    ```sql
    select * from employees order by hire_date desc limit 1;
    ```

2. 查找入职员工时间排名为倒数第三的员工的所有信息

    <https://www.nowcoder.com/practice/ec1ca44c62c14ceb990c3c40def1ec6c?tpId=82&&tqId=29754&rp=1&ru=/ta/sql&qru=/ta/sql/question-ranking>

    ```sql
    select * from employees order by hire_date desc limit 1 offset 2
    ```

## 多表查询

3. 查找当前薪水详情以及部门编号dept_no

    <https://www.nowcoder.com/practice/c63c5b54d86e4c6d880e4834bfd70c3b?tpId=82&&tqId=29755&rp=1&ru=/ta/sql&qru=/ta/sql/question-ranking>

    ```sql
    SELECT 
    s.*,
    d.dept_no 
    FROM
    salaries s 
    LEFT JOIN dept_manager d 
        ON s.emp_no = d.emp_no 
    WHERE s.to_date = '9999-01-01' 
    AND d.to_date = '9999-01-01' 
    ORDER BY s.emp_no 
    ```

# Take Away Notes

数据库首先要了解一些基本概念，如何组织数据库表，如何设计出一个冗余较少，一致性强的数据库，这有利于理解一些数据库的组成结构。

可以了解一些基本的查询语句，无论多复杂的查询语句，也是基本查询语句的组合而已，可以由简入繁，逐渐掌握。
