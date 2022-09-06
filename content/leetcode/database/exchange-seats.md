+++
title = "LeetCode - （MySQL）换座位"
categories = ["MySQL","LeetCode"]
tags = ["MySQL","LeetCode"]
date = "2022-09-06T12:50:28+08:00"
draft = false

+++

[题目链接](https://leetcode.cn/problems/exchange-seats/)

使用到下面的 Seat 表，Id是该表的主键列，该表的每一行都表示学生的姓名和ID，Id是一个连续的增量。

* User 表 

| Column Name | Type    |
|  ----  | ----  |
| id          | int     |
| name        | varchar |



创建和填充数据：
```sql
Create  table  If  Not  Exists  Seat (id  int, student  varchar(255))
Truncate  table  Seat
insert  into  Seat (id, student)  values  ('1',  'Abbot')
insert  into  Seat (id, student)  values  ('2',  'Doris')
insert  into  Seat (id, student)  values  ('3',  'Emerson')
insert  into  Seat (id, student)  values  ('4',  'Green')
insert  into  Seat (id, student)  values  ('5',  'Jeames')
```


编写SQL查询来交换每两个连续的学生的座位号。如果学生的数量是奇数，则最后一个学生的id不交换。
查询结果格式如下例所示：

输入：
Users 表：

| id| student|
|  ----  | ----  |
| 1  | Abbot   |
| 2  | Doris   |
| 3  | Emerson |
| 4  | Green   |
| 5  | Jeames  |


输出: 

Result 表：

| id| student|
|  ----  | ----  |
| 1  | Doris   |
| 2  | Abbot   |
| 3  | Green   |
| 4  | Emerson |
| 5  | Jeames  |




解释：
请注意，如果学生人数为奇数，则不需要更换最后一名学生的座位。

解答：
查询时根据id判断位置是奇数还是偶数，如果是奇数则往后开窗，如果是偶数则往前开窗，将开窗后的id座位最后显示的id，如果开窗后值为null，则使用自身的id（最后一个座位的情况），利用子查询查询结果集：
```sql
select 
ifnull(id,origin_id) as id,student 
from 
(
select 
if(
	id%2=0,
	lag(id) over (order by id),
	lead(id) over (order by id)
) as id,
student,
id as origin_id 
from Seat
) 
s order by id
```


