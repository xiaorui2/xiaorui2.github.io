---
title: LeetCode9
date: 2019-05-26 21:20:48
tags: Algorithm
categories: LeetCode
---

# Palindrome Number

Determine whether an integer is a palindrome. An integer is a palindrome when it reads the same backward as forward.

**Example 1:**

```
Input: 121
Output: true
```

**Example 2:**

```
Input: -121
Output: false
Explanation: From left to right, it reads -121. From right to left, it becomes 121-. Therefore it is not a palindrome.
```

**Example 3:**

```
Input: 10
Output: false
Explanation: Reads 01 from right to left. Therefore it is not a palindrome.
```

# 思路

给你一个数，看是不是回文数。首先负数肯定不会是回文数，直接`return false`即可，小于10的非负整数都是回文`return true`， 否则就从后往前进行逆序数组转数字运算，得到结果与初值进行比较，相等`return true`。

# AC代码

```javascript
/**
 * @param {number} x
 * @return {boolean}
 */
var isPalindrome = function(x) {
    //能被10整除的非0整数和负数，返回false
    if(x<0) 
        return false;
    if(x<10) 
        return true;
    var temp=0;
    var org=x;//记录x的初始值
    while(x>9){
        temp=x%10+temp*10
        x=parseInt(x/10)
    }
    temp=temp*10+x
    return temp===org
};
```

