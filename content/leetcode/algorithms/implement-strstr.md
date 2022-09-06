+++
title = "LeetCode - （算法）实现 strStr()"
categories = ["算法","LeetCode"]
tags = ["算法","LeetCode"]
date = "2022-08-07T16:48:28+08:00"
draft = false

+++

[题目链接](https://leetcode.cn/problems/implement-strstr)

实现 strStr() 函数。

给你两个字符串 haystack 和 needle ，请你在 haystack 字符串中找出 needle 字符串出现的第一个位置（下标从 0 开始）。如果不存在，则返回  -1 。

说明：

当 needle 是空字符串时，我们应当返回什么值呢？这是一个在面试中很好的问题。

对于本题而言，当 needle 是空字符串时我们应当返回 0 。这与 C 语言的 strstr() 以及 Java 的 indexOf() 定义相符。

> 示例 1:
> 
> 输入：haystack = "hello", needle = "ll"
> 输出：2



> 示例 2:
> 
> 输入：haystack = "aaaaa", needle = "bba"
> 输出：-1


代码如下：

```php
 class Solution {

    /**
     * @param String $haystack
     * @param String $needle
     * @return Integer
     */
    function strStr($haystack, $needle) {
        $length = strlen($haystack);
        $needle_length = strlen($needle);
		if($needle_length == 0){
			return 0;
		}
        for($i = 0;$i <= $length;$i++){
			if($haystack[$i] == $needle[0]){
				$temp_str = $haystack[$i];
				$index = 1;
				$start_index = $i+1;
				while($index < $needle_length){
					$temp_str .= $haystack[$start_index];
					$index++;
					$start_index++;
				}
				if($temp_str == $needle){
					return $i;
				}
			}
        }
		return -1;
    }
}
```

思路：把查询的字符串当作数组，进行遍历，当字符和目标字符的第一个字符相同的情况下，进行后面字符的匹配，对应长度匹配字符相同后，就返回出现的第一个字符；如果目标为空字符就返回0；如果目标字符长度大于查询的字符串，就返回-1。
