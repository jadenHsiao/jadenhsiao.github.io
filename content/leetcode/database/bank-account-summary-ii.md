+++
title = "LeetCode - （MySQL）银行账户概要 II"
categories = ["MySQL","LeetCode"]
tags = ["MySQL","LeetCode"]
date = "2022-09-05T12:43:28+08:00"
draft = false

+++

[题目链接](https://leetcode.cn/problems/bank-account-summary-ii/)

使用到下面的 Users 表和Transactions表，Users 表中account 是该表的主键，表中的每一行包含银行里中每一个用户的账号，Transactions表中，trans_id 是该表主键，该表的每一行包含了所有账户的交易改变情况；如果用户收到了钱, 那么金额是正的; 如果用户转了钱, 那么金额是负的，所有账户的起始余额为 0：

* User 表 

| Column Name | Type    |
|  ----  | ----  |
| account      | int     |
| name         | varchar |

* Transactions表


| Column Name   | Type    |
|  ----  | ----  |
| trans_id      | int     |
| account       | int     |
| amount        | int     |
| transacted_on | date    |


创建和填充数据：
```sql
Create  table  If  Not  Exists  Users  (account  int,  name  varchar(20))
Create  table  If  Not  Exists  Transactions (trans_id  int,  account  int, amount  int, transacted_on  date)
Truncate  table  Users
insert  into  Users  (account,  name)  values  ('900001',  'Alice')
insert  into  Users  (account,  name)  values  ('900002',  'Bob')
insert  into  Users  (account,  name)  values  ('900003',  'Charlie')
Truncate  table  Transactions
insert  into  Transactions (trans_id,  account, amount, transacted_on)  values  ('1',  '900001',  '7000',  '2020-08-01')
insert  into  Transactions (trans_id,  account, amount, transacted_on)  values  ('2',  '900001',  '7000',  '2020-09-01')
insert  into  Transactions (trans_id,  account, amount, transacted_on)  values  ('3',  '900001',  '-3000',  '2020-09-02')
insert  into  Transactions (trans_id,  account, amount, transacted_on)  values  ('4',  '900002',  '1000',  '2020-09-12')
insert  into  Transactions (trans_id,  account, amount, transacted_on)  values  ('5',  '900003',  '6000',  '2020-08-07')
insert  into  Transactions (trans_id,  account, amount, transacted_on)  values  ('6',  '900003',  '6000',  '2020-09-07')
insert  into  Transactions (trans_id,  account, amount, transacted_on)  values  ('7',  '900003',  '-4000',  '2020-09-11')
```


写一个 SQL, 报告余额高于 10000 的所有用户的名字和余额. 账户的余额等于包含该账户的所有交易的总和.
返回结果表单没有顺序要求.
查询结果格式如下例所示：

输入：
Users 表：

| account    | name         |
|  ----  | ----  |
| 900001     | Alice        |
| 900002     | Bob          |
| 900003     | Charlie      |


Transactions 表：
| trans_id   | account    | amount     | transacted_on |
|  ----  | ----  |----  |----  |
| 1          | 900001     | 7000       |  2020-08-01   |
| 2          | 900001     | 7000       |  2020-09-01   |
| 3          | 900001     | -3000      |  2020-09-02   |
| 4          | 900002     | 1000       |  2020-09-12   |
| 5          | 900003     | 6000       |  2020-08-07   |
| 6          | 900003     | 6000       |  2020-09-07   |
| 7          | 900003     | -4000      |  2020-09-11   |


输出: 

Result 表：

| name       | balance    |
|  ----  | ----  |
| Alice      | 11000      |


解释：
Alice 的余额为(7000 + 7000 - 3000) = 11000.
Bob 的余额为1000.
Charlie 的余额为(6000 + 6000 - 4000) = 8000.

解答一：
统计出每个人账户余额作为子查询，然后查询出余额大于10000的记录即可：
```sql
select 
u.name as name,balance 
from 
Users u 
left join 
(select account,sum(amount) as balance from Transactions group by account) t 
on t.account = u.account 
where balance > 10000
```


