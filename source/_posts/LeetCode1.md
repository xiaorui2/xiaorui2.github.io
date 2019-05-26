---
title: LeetCode1
date: 2019-05-26 21:15:06
tags: Algorithm
categories: LeetCode
---

# Two Sum

Given an array of integers, return **indices** of the two numbers such that they add up to a specific target.

You may assume that each input would have **exactly** one solution, and you may not use the *same* element twice.

**Example:**

```
Given nums = [2, 7, 11, 15], target = 9,
Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1].
```

# 思路

这个找到两个数，使得相加和为`target`。最简单的两重循环找到这个符合题意的值。

也可以一遍扫过去，每次找`target-nums[i]`值是否出现过，出现了就返回，不然就存到数组中。

# AC代码

```javascript
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number[]}
 */
var twoSum = function(nums, target) {
    var len=nums.length;
    var i,j;
    var hash=[];
    for(i=0;i<len;i++){
        j=target-nums[i];
        if(hash[j]!==undefined){
            return [i,hash[j]];
        }
        else{
            hash[nums[i]]=i;
        }
    }
};
```

