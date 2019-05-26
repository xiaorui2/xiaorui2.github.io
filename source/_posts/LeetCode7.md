---
title: LeetCode7
date: 2019-05-26 21:20:42
tags: Algorithm
categories: LeetCode
---

# Reverse Integer

Given a 32-bit signed integer, reverse digits of an integer.

**Example 1:**

```
Input: 123
Output: 321
```

**Example 2:**

```
Input: -123
Output: -321
```

**Example 3:**

```
Input: 120
Output: 21
```

# 思路

题意是让一个32位的带符号的整数倒置过来。

就先取绝对值，然后初始化一个`result = 0`，把给定的数字`n`除以十，得到余数作为最低位，商作为新的数字`n`，`result = result + 余数*10`，这样每次得到的最低位就会随着循环不断提升数位，从而得到逆序的`result`。

# AC代码

```javascript
/**
 * @param {number} x
 * @return {number}
 */
var reverse = function(x) {
    var y=Math.abs(x);
    var res=0;
    while(y>0){
        res=res*10+y%10;
        y=parseInt(y/10);
    }
    res=x<0?-res:res;
    return res>=-Math.pow(2,31)&&res<=Math.pow(2,31)-1?res:0;
};
```

