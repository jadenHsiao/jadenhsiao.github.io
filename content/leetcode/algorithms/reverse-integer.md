+++
title = "LeetCode - （算法）整数反转"
categories = ["算法","LeetCode"]
tags = ["算法","LeetCode"]
date = "2022-07-20T10:11:28+08:00"
draft = false

+++

给出一个 32 位的有符号整数，你需要将这个整数中每位上的数字进行反转。

> 示例 1:
> 
> 输入: 123 输出: 321 



> 示例 2:
> 
> 输入: -123 输出: -321



 
> 示例 3:
> 
> 输入: 120 输出: 21

注意:

假设我们的环境只能存储得下 32 位的有符号整数，则其数值范围为 [−231,  231 − 1]。请根据这个假设，如果反转后整数溢出那么就返回 0。
代码内容：

```php
    class Solution {
    
    /**
     * @param Integer $x
     * @return Integer
     */
    function reverse($x) {
        $b = $x;
        if($x<0){
            $x = $x*-1;
        }
        if($b == 0){
            return $b;
        }
        $result = implode(array_reverse(str_split($x)));
        $t = trim($result,0);
        if($t>pow(2,31)){
                return 0;
        }
        return $b<0?$t*-1:$t;
    }
    }

```

思路：如果提供的值小于0，就转化为正数再进行转化为数组，翻转再成为数组，同时去掉前面的0和判断是否大于2的31次方，对应情况就是如果为小于0就返回为翻转后小于0的数，如果大于2的31次方就返回0。