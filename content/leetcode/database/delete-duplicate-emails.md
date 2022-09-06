+++
title = "LeetCode - （MySQL）删除重复的电子邮箱"
categories = ["MySQL","LeetCode"]
tags = ["MySQL","LeetCode"]
date = "2022-08-05T08:27:28+08:00"
draft = false

+++

[题目链接](https://leetcode.cn/problems/delete-duplicate-emails/)

使用到下面的 Person 表，id 是这个表的主键，该表包含邮箱地址，表的结构如下：


| Column Name | Type    |
|  ----  | ----  |
| id          | int     |
| email       | varchar |

创建和填充数据：
```sql
Create table If Not Exists Person (Id int, Email varchar(255))
Truncate table Person
insert into Person (id, email) values ('1', 'john@example.com')
insert into Person (id, email) values ('2', 'bob@example.com')
insert into Person (id, email) values ('3', 'john@example.com')
```

编写一个 SQL 删除语句来 删除 所有重复的电子邮件，只保留一个id最小的唯一电子邮件。

以 任意顺序 返回结果表。 （注意： 仅需要写删除语句，将自动对剩余结果进行查询）

查询结果格式如下所示。

输入：
Person 表：

| id | email            |
|  ----  | ----  |
| 1  | john@example.com |
| 2  | bob@example.com  |
| 3  | john@example.com |

输出: 

| id | email            |
|  ----  | ----  |
| 1  | john@example.com |
| 2  | bob@example.com  |
解释: john@example.com重复两次。我们保留最小的Id = 1。

解答：
```sql
delete 
from Person 
where Id 
not in (
  select min(a.Id) as Id from (
    select * from Person order by id asc
  ) a 
group by a.email
);
```

我的结题思路是先将表的邮箱地址进行去重，然后取出每一项的最小值 ID，删除的时候去排除这些最小值 ID 即可。