+++
title = "LeetCode - （MySQL）超过5名学生的课"
categories = ["MySQL","LeetCode"]
tags = ["MySQL","LeetCode"]
date = "2022-08-23T13:27:28+08:00"
draft = false

+++

[题目链接](https://leetcode.cn/problems/classes-more-than-5-students/)

使用到下面的 Courses 表，(student, class)是该表的主键列，该表的每一行表示学生的名字和他们注册的班级，表的结构如下：


| Column Name | Type    |
|  ----  | ----  |
| student     | varchar |
| class       | varchar |

创建和填充数据：
```sql
Create  table  If  Not  Exists  Courses (student  varchar(255),  class  varchar(255))
Truncate  table  Courses
insert  into  Courses (student,  class)  values  ('A',  'Math')
insert  into  Courses (student,  class)  values  ('B',  'English')
insert  into  Courses (student,  class)  values  ('C',  'Math')
insert  into  Courses (student,  class)  values  ('D',  'Biology')
insert  into  Courses (student,  class)  values  ('E',  'Math')
insert  into  Courses (student,  class)  values  ('F',  'Computer')
insert  into  Courses (student,  class)  values  ('G',  'Math')
insert  into  Courses (student,  class)  values  ('H',  'Math')
insert  into  Courses (student,  class)  values  ('I',  'Math')
```


编写一个SQL查询来报告 **至少有5个学生** 的所有班级。

返回的结果表中的数据可以按  **任意顺序**  排列。

查询结果格式如下面的例子所示：

输入：
Courses 表：

| student | class    |
|  ----  | ----  |
| A       | Math     |
| B       | English  |
| C       | Math     |
| D       | Biology  |
| E       | Math     |
| F       | Computer |
| G       | Math     |
| H       | Math     |
| I       | Math     |




输出: 

Result 表：

| class|
|  ----  | ----  |
| Math           |

解释：
-数学课有6个学生，所以我们包括它。
-英语课有1名学生，所以我们不包括它。
-生物课有1名学生，所以我们不包括它。
-计算机课有1个学生，所以我们不包括它。

解答一：
分组后用 `having` 筛选出大于5的课：
```sql
select  class  from Courses group  by  class  having  count(*) >= 5
```


