+++
title = "LeetCode - （MySQL）游戏玩法分析 I"
categories = ["MySQL","LeetCode"]
tags = ["MySQL","LeetCode"]
date = "2022-08-07T13:08:28+08:00"
draft = false

+++

[题目链接](https://leetcode.cn/problems/game-play-analysis-i/comments/)

使用到下面的 Activity 表，表的主键是 (player_id, event_date)。
这张表展示了一些游戏玩家在游戏平台上的行为活动。
每行数据记录了一名玩家在退出平台之前，当天使用同一台设备登录平台后打开的游戏的数目（可能是 0 个），表的结构如下：


| Column Name  | Type    |
|  ----  | ----  |
| player_id    | int     |
| device_id    | int     |
| event_date   | date    |
| games_played | int     |

创建和填充数据：
```sql
Create  table  If  Not  Exists  Activity (player_id  int, device_id  int, event_date  date, games_played  int)
Truncate  table  Activity
insert  into  Activity (player_id, device_id, event_date, games_played)  values  ('1',  '2',  '2016-03-01',  '5')
insert  into  Activity (player_id, device_id, event_date, games_played)  values  ('1',  '2',  '2016-05-02',  '6')
insert  into  Activity (player_id, device_id, event_date, games_played)  values  ('2',  '3',  '2017-06-25',  '1')
insert  into  Activity (player_id, device_id, event_date, games_played)  values  ('3',  '1',  '2016-03-02',  '0')
insert  into  Activity (player_id, device_id, event_date, games_played)  values  ('3',  '4',  '2018-07-03',  '5')
```



写一条 SQL 查询语句获取每位玩家  **第一次登陆平台的日期**。

查询结果的格式如下所示：

输入：
Activity 表：

| player_id | device_id | event_date | games_played |
|  ----  | ----  | ----  | ----  |
| 1         | 2         | 2016-03-01 | 5            |
| 1         | 2         | 2016-05-02 | 6            |
| 2         | 3         | 2017-06-25 | 1            |
| 3         | 1         | 2016-03-02 | 0            |
| 3         | 4         | 2018-07-03 | 5            |


输出: 


Result 表：

| player_id | first_login |
|  ----  | ----  |
| 1         | 2016-03-01  |
| 2         | 2017-06-25  |
| 3         | 2016-03-02  |



解答：
```sql
select player_id,min(event_date) as first_login from Activity group by player_id;
```
先将登录的时间根据用户ID进行分组，然后在获取的结果集根据分组内的最小值进行输出，就可以获得用户第一次登陆的日期。
