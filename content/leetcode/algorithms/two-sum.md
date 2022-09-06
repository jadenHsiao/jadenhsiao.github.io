+++
title = "LeetCode - （算法）两数之和"
categories = ["算法","LeetCode"]
tags = ["算法","LeetCode"]
date = "2022-07-20T10:11:28+08:00"
draft = false

+++

给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍。

> 示例:
> 
> 给定 nums = [2, 7, 11, 15], target = 9 因为 nums[0] + nums[1] = 2 + 7 = 9
> 所以返回 [0, 1]

代码内容：

```php
    class Solution {
    
    /**
     * @param Integer[] $nums
     * @param Integer $target
     * @return Integer[]
     */
    function twoSum($nums, $target) {
        $res = [];
        for($i = 0;$i < count($nums);$i++){
            foreach($nums as $item=>$value){
                if($nums[$i] + $nums[$item] == $target && $i!=$item){
                        $temp[] = $i;
                        $temp[] = $item;
                        sort($temp);
                        $res[] = $temp;
            $temp = [];
                }
            }
        }
        $res = array_unique($res, SORT_REGULAR);
        if(count($res)==1){
            return $res[0];
        }
    }
    }
```

思路：题目要求是在一个给定数组内找出两个相加为给定值，因此将该给定的数组进行遍历后再遍历，遍历时两个值是不能相等的，同时相加的结果为给定的值，遍历出来后会出现下标是一样的情况但是位置不同，所以要对结果进行去重，确保可以去重得先对每一次获得的结果进行升序，再进行去重就可以得到最终的结果。