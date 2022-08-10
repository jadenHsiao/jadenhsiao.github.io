+++
title = "LeetCode - （算法）最后一个单词的长度"
categories = ["算法","LeetCode"]
tags = ["算法","LeetCode"]
date = "2022-08-09T22:15:28+08:00"
draft = false

+++

[题目链接](https://leetcode.cn/classic/problems/length-of-last-word/description/)

给你一个字符串  `s`，由若干单词组成，单词前后用一些空格字符隔开。返回字符串中  **最后一个**  单词的长度。

**单词**  是指仅由字母组成、不包含任何空格字符的最大子字符串。

>输入：s = "Hello World"
输出：5
解释：最后一个单词是“World”，长度为5。

>输入：s = "   fly me   to   the moon  "
输出：4 
解释：最后一个单词是“moon”，长度为4。

>输入：s = "luffy is still joyboy"
输出：6
解释：最后一个单词是长度为6的“joyboy”。	

```php
class Solution {

    /**
     * @param String $s
     * @return Integer
     */
    function lengthOfLastWord($s) {
        $str_length = strlen($s);
        $str_start = false;
        $length = 0;
        for($i = $str_length-1;$i >= 0;$i--){
            if(' '==$s[$i] && $str_start == false){
                continue;
            }
            if(' '==$s[$i] && $str_start == true ){
                break;
            }
            if(' '!==$s[$i]){
                $str_start = true;
                $length++;
            }
        }
        return $length;
    }
}
```

思路：把字符串参数从后面往前判断，如果是空格就跳过，如果已经出现了非空格字符串就继续往前移动，直到前面是空格，再统计长度即可。
