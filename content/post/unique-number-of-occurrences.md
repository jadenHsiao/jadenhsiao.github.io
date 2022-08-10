+++
title = "LeetCode - （算法）独一无二的出现次数"
categories = ["算法","LeetCode"]
tags = ["算法","LeetCode"]
date = "2022-07-20T10:11:28+08:00"
draft = false

+++

给你一个整数数组 arr，请你帮忙统计数组中每个数的出现次数。

如果每个数的出现次数都是独一无二的，就返回 true；否则返回 false。

> 示例 1：
> 
> 输入：arr = [1,2,2,1,1,3] 输出：true 解释：在该数组中，1 出现了 3 次，2 出现了 2 次，3 只出现了 1
> 次。没有两个数的出现次数相同。



> 示例 2：
> 
> 输入：arr = [1,2] 输出：false
 

> 示例 3：
> 
> 输入：arr = [-3,0,1,-3,1,1,1,-3,10,0] 输出：true

代码内容：

```php
    function uniqueOccurrences($arr) {
            $result = array_count_values($arr);
            if(count(array_unique($result)) == count($result)){
                return true;
            }
            return false;
    }

```

思路：先用array_count_values获取到数组内每一项出现的次数，再对上一步的结果进行去重，如果去重结果和上一步的结果数量一致，则每一项出现次数都是独一无二。
