+++
title = "LeetCode - （MySQL）上升的温度"
categories = ["MySQL","LeetCode"]
tags = ["MySQL","LeetCode"]
date = "2022-08-03T13:11:28+08:00"
draft = false

+++

[题目链接](https://leetcode.cn/problems/rising-temperature)

使用到下面的 Weather 表，id 是这个表的主键，该表包含特定日期的温度信息，表的结构如下：

|  Column Name   | Type  |
|  ----  | ----  |
|  id    | int      |
| recordDate   | date  |
| temperature  | int    |

创建和填充数据：
```sql
Create table If Not Exists Weather (id int, recordDate date, temperature int)
Truncate table Weather
insert into Weather (id, recordDate, temperature) values ('1', '2015-01-01', '10')
insert into Weather (id, recordDate, temperature) values ('2', '2015-01-02', '25')
insert into Weather (id, recordDate, temperature) values ('3', '2015-01-03', '20')
insert into Weather (id, recordDate, temperature) values ('4', '2015-01-04', '30')
```

编写一个 SQL 查询，来查找与之前（昨天的）日期相比温度更高的所有日期的 id 。
返回结果 不要求顺序 。
查询结果格式如下例。

输入：
Weather 表：

| id | recordDate | Temperature |
|  ----  | ----  | ----  |
| 1  | 2015-01-01 | 10          |
| 2  | 2015-01-02 | 25          |
| 3  | 2015-01-03 | 20          |
| 4  | 2015-01-04 | 30          |

输出：

| id |
|  ----  | 
| 2  |
| 4  |

解答：
```sql
select w.id 
  from Weather w 
  left join Weather cw 
  on date_sub(w.recordDate, interval 1 day) = cw.recordDate 
  where (w.Temperature-cw.Temperature) > 0
```

我的解题思路主要是对表自身进行连接，再用``date_sub``来获取前一天，判断是否存在于连接表，再筛选出比前一天温度高的 ID 即可，该题目考察对日期函数的熟悉，难度相对来说比较简单。