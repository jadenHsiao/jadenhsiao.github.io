+++
title = "LeetCode - （算法）搜索插入位置"
categories = ["算法","LeetCode"]
tags = ["算法","LeetCode"]
date = "2022-07-20T10:11:28+08:00"
draft = false

+++

给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。

你可以假设数组中无重复元素。

> 示例 1:
> 
> 输入: [1,3,5,6], 5 输出: 2



> 
> 示例 2:
> 
> 输入: [1,3,5,6], 7 输出: 4



> 示例 3:
> 
> 输入: [0,3,5,6], 0 输出: 0

代码内容：

```php
    class Solution {
    
    /**
     * @param Integer[] $nums
     * @param Integer $target
     * @return Integer
     */
    function searchInsert($nums, $target) {
        if(in_array($target,$nums)){
            $result = array_search($target,$nums);
            return $result;
        }else{
            if(min($nums)>$target){
                return 0;
            }
            if(max($nums)<$target){
                return count($nums);
            }
            for($i=0;$i<count($nums);$i++){
                if($nums[$i]<$target && $nums[$i+1]>$target){
                    return $i+1;
                }
            }
        }
    }
    }
```

思路：本题主要是位置查找问题，如果在数组内就返回对应的下标，比最小值的小就返回第一个值的下标，比最大值的大就返回最大值插入的位置，否则就返回插入的位置，因此，先跟最小的比较，再跟最大的比较，如果都不成立就走遍历，从遍历里当前的和往前一位的比较，如果在中间就直接取前一位的位置。
 