+++
title = "LeetCode - （算法）最大间距"
categories = ["算法","LeetCode"]
tags = ["算法","LeetCode"]
date = "2022-07-20T10:11:28+08:00"
draft = false

+++

给定一个无序的数组，找出数组在排序之后，相邻元素之间最大的差值。

如果数组元素个数小于 2，则返回 0。

> 示例 1:
> 
> 输入: [3,6,9,1] 输出: 3 解释: 排序后的数组是 [1,3,6,9], 其中相邻元素 (3,6) 和 (6,9)
> 之间都存在最大差值 3。



> 示例 2:
> 
> 输入: [10] 输出: 0 解释: 数组元素个数小于 2，因此返回 0。


代码如下：

```php
    function maximumGap($nums) {
            sort($nums);
            if(count($nums) == 1 || empty($nums)){
                return 0;
            }
            else{
                $total = [];
                foreach($nums as $a=>$v){
    	            if($a != count($nums)-1){
    		       $total[] = $nums[$a+1]-$v;
    	            }
                 }
                 unset($v);
                 return max($total);
            }
        }
```

思路：先将参数数组进行排序，然后判断个数如果为1和0就直接返回最大值0，否则将数组每一项和前一项进行比对，放入到result，最后使用max获取到最大值。
