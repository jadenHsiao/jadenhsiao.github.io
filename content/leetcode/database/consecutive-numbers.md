+++
title = "LeetCode - （MySQL）连续出现的数字"
categories = ["MySQL","LeetCode"]
tags = ["MySQL","LeetCode"]
date = "2022-08-07T03:27:28+08:00"
draft = false

+++

[题目链接](https://leetcode.cn/problems/consecutive-numbers/)

使用到下面的 Logs 表，id 是这个表的主键，表的结构如下：


| Column Name | Type    |
|  ----  | ----  |
| id          | int     |
| num| varchar |

创建和填充数据：
```sql
Create  table  If  Not  Exists  Logs  (id  int,  num  int)
Truncate  table  Logs
insert  into  Logs  (id,  num)  values  ('1',  '1')
insert  into  Logs  (id,  num)  values  ('2',  '1')
insert  into  Logs  (id,  num)  values  ('3',  '1')
insert  into  Logs  (id,  num)  values  ('4',  '2')
insert  into  Logs  (id,  num)  values  ('5',  '1')
insert  into  Logs  (id,  num)  values  ('6',  '2')
insert  into  Logs  (id,  num)  values  ('7',  '2')
```


编写一个 SQL 查询，查找所有至少连续出现三次的数字。

返回的结果表中的数据可以按  **任意顺序**  排列。

查询结果格式如下面的例子所示：

输入：
Person 表：

| Id | Num |
|  ----  | ----  |
| 1  | 1   |
| 2  | 1   |
| 3  | 1   |
| 4  | 2   |
| 5  | 1   |
| 6  | 2   |
| 7  | 2   |


输出: 

Result 表：

| ConsecutiveNums |
|  ----  | ----  |
| 1               |

解释：1 是唯一连续出现至少三次的数字。

解答一：
MySQL 5.7 下使用联表查询：
```sql
SELECT
	DISTINCT ( SELECT Num FROM LOGS l WHERE l.id = b.id ) AS ConsecutiveNums 
FROM
	( SELECT id,( id + 1 ) AS second_id,( id + 2 ) AS third_id FROM LOGS ) b
	where ( SELECT Num FROM LOGS l WHERE l.id = b.id ) = (
	SELECT
		Num 
	FROM
		LOGS l 
	WHERE
		l.id = b.second_id 
		) and ( SELECT Num FROM LOGS l WHERE l.id = b.id ) = (
	SELECT
		Num 
	FROM
		LOGS l 
	WHERE
		l.id = b.third_id 
		)
```

在 MySQL 5.7 下，先把每一条记录的后一位和后两位的id查询出来，联自身表后，用子查询判断第一位的数和第二位、第三位的是否相等，如果相等返回结果，同时需要对结果集进行去重处理。
这种方式是能解答的，但是相对来说效率比较低，在 LeetCode 上执行耗时911 ms；

解答二：
```sql
SELECT DISTINCT
	( a.num ) AS ConsecutiveNums 
FROM
	(
	SELECT
		id,
		num,
		lead( num ) over ( ORDER BY id ) AS next_num,(
			id + 2 
		) AS third_id 
	FROM
	LOGS 
	) a
	LEFT JOIN LOGS b ON a.third_id = b.id 
WHERE
	( b.num = a.next_num ) 
	AND (
	a.num = b.num)
```
在 MySQL 8.0 中，使用了新特性的开窗函数``lead``，用于获取到每条记录后面一条记录的num，左表是获取到当前记录的id、num，同时还有下一条的num，还有下下一条的id；再进行联表 Logs 表，联表的条件是拿下下一条的id和右表连接，获取到下下一条的 num，条件为每一条记录的num和下一条的num还有下下一条的num相同即可，最后一样进行去重。
