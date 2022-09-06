+++
title = "LeetCode - （MySQL）部门工资最高的员工"
categories = ["MySQL","LeetCode"]
tags = ["MySQL","LeetCode"]
date = "2022-09-06T23:43:28+08:00"
draft = false

+++

[题目链接](https://leetcode.cn/problems/department-highest-salary/)

使用到下面的 Employee 表，id是此表的主键列，departmentId是Department表中ID的外键，此表的每一行都表示员工的ID、姓名和工资，它还包含他们所在部门的ID，表的结构如下：

* Employee 表

| Column Name | Type    |
|  ----  | ----  |
| id           | int     |
| name         | varchar |
| salary       | int     |
| departmentId | int     |

* Department 表

| Column Name | Type    |
|  ----  | ----  |
| id          | int     |
| name        | varchar |



创建和填充数据：
```sql
Create  table  If  Not  Exists  Employee (id  int,  name  varchar(255), salary  int, departmentId  int)
Create  table  If  Not  Exists  Department (id  int,  name  varchar(255))
Truncate  table  Employee
insert  into  Employee (id,  name, salary, departmentId)  values  ('1',  'Joe',  '70000',  '1')
insert  into  Employee (id,  name, salary, departmentId)  values  ('2',  'Jim',  '90000',  '1')
insert  into  Employee (id,  name, salary, departmentId)  values  ('3',  'Henry',  '80000',  '2')
insert  into  Employee (id,  name, salary, departmentId)  values  ('4',  'Sam',  '60000',  '2')
insert  into  Employee (id,  name, salary, departmentId)  values  ('5',  'Max',  '90000',  '1')
Truncate  table  Department
insert  into  Department (id,  name)  values  ('1',  'IT')
insert  into  Department (id,  name)  values  ('2',  'Sales')
```



编写SQL查询以查找每个部门中薪资最高的员工。  
按 **任意顺序** 返回结果表。  
查询结果格式如下例所示。

输入：
Employee表：

| id | name  | salary | departmentId |
|  ----  | ----  |----  |----  |
| 1  | Joe   | 70000  | 1            |
| 2  | Jim   | 90000  | 1            |
| 3  | Henry | 80000  | 2            |
| 4  | Sam   | 60000  | 2            |
| 5  | Max   | 90000  | 1            |

Department 表

| id | name  |
|  ----  | ----  |
| 1  | IT    |
| 2  | Sales |

输出: 

Result 表：

| Department | Employee | Salary |
|  ----  | ----  |----  |
| IT         | Jim      | 90000  |
| Sales      | Henry    | 80000  |
| IT         | Max      | 90000  |



解释：Max 和 Jim 在 IT 部门的工资都是最高的，Henry 在销售部的工资最高。

解答：先使用子查询，联表分组查询出每个部门里薪资最高的的金额和部门名称，再将部门表和子查询结果进行联表查询，筛选出所有对应部门的薪资。
```sql
select 
a.name as Department,
e.name as Employee,
a.salary as Salary 
from 
employee e 
left join 
(
	select 
		max(salary) as salary,
		departmentId,
		d.`name` 
		from 
		employee e 
		left join 
		department d on 
		e.departmentId = d.id 
		group by e.departmentId
	) a 
on a.departmentId = e.departmentId 
and e.salary = a.salary 
where 
a.name is not null
```


