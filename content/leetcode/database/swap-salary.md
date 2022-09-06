+++
title = "LeetCode - （MySQL）变更性别"
categories = ["MySQL","LeetCode"]
tags = ["MySQL","LeetCode"]
date = "2022-08-23T13:13:28+08:00"
draft = false

+++

[题目链接](https://leetcode.cn/problems/swap-salary/)

使用到下面的 Salary 表，id 是这个表的主键，sex 这一列的值是 ENUM 类型，只能从 ('m', 'f') 中取，本表包含公司雇员的信息，表的结构如下：


| Column Name | Type    |
|  ----  | ----  |
| id          | int      |
| name        | varchar  |
| sex         | ENUM     |
| salary      | int      |


创建和填充数据：
```sql
Create  table  If  Not  Exists  Salary (id  int,  name  varchar(100), sex  char(1), salary  int)
Truncate  table  Salary
insert  into  Salary (id,  name, sex, salary)  values  ('1',  'A',  'm',  '2500')
insert  into  Salary (id,  name, sex, salary)  values  ('2',  'B',  'f',  '1500')
insert  into  Salary (id,  name, sex, salary)  values  ('3',  'C',  'm',  '5500')
insert  into  Salary (id,  name, sex, salary)  values  ('4',  'D',  'f',  '500')
```



请你编写一个 SQL 查询来交换所有的 'f' 和 'm' （即，将所有 'f' 变为 'm' ，反之亦然），仅使用 单个 update 语句 ，且不产生中间临时表。

注意，你必须仅使用一条 update 语句，且 不能 使用 select 语句。

输入：
Courses 表：

| id | name | sex | salary |
|  ----  | ----  |----  |----  |
| 1  | A    | m   | 2500   |
| 2  | B    | f   | 1500   |
| 3  | C    | m   | 5500   |
| 4  | D    | f   | 500    |





输出: 

Result 表：

| id | name | sex | salary |
|  ----  | ----  |----  |----  |
| 1  | A    | f   | 2500   |
| 2  | B    | m   | 1500   |
| 3  | C    | f   | 5500   |
| 4  | D    | m   | 500    |



解释：
(1, A) 和 (3, C) 从 'm' 变为 'f' 。
(2, B) 和 (4, D) 从 'f' 变为 'm' 。

解答：
在set条件设置的时候，根据获取到的性别来置换，可以使用 ``if`` 或者 ``case when``：
```sql
update Salary set sex = (if(sex="m","f","m"))

update Salary set sex = (case sex when  'm'  then  'f'  else  'm'  end)
```


