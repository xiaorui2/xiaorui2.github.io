---
title: LeetCode刷题（持续更新）
date: 2019-06-24 08:49:46
tags: Algorithm
categories: 算法题训练
---

# LeetCode1 Two Sum

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

# LeetCode5 Longest Palindromic Substring

Given a string **s**, find the longest palindromic substring in **s**. You may assume that the maximum length of **s** is 1000.

**Example 1:**

```
Input: "babad"
Output: "bab"
Note: "aba" is also a valid answer.
```

**Example 2:**

```
Input: "cbbd"
Output: "bb"
```

# 思路

题意是找出一个最长的回文子串，有几种做法，我这里提供两种。

第一种是暴力，回文子串有两种，长度为奇数的和长度为偶数的，对于每个位置点，我向两边进行扩散，分别进行奇数和偶数长度的回文子串判断，然后维护一个长度最大值和这个子串的起始位置。

# AC代码

```javascript
/**
 * @param {string} s
 * @return {string}
 */
var low=0;
var maxn=0;
var longestPalindrome = function(s) {
    var len=s.length;
    if(len < 2){
        return s;
    }
    for(let i=0;i<len-1;i++){
        check(s,i,i);
        check(s,i,i+1);
    }
    let res=s.substring(low,low+maxn);
    low =0;maxn= 0;
    return res;
};
function check(s,start,end){
    while(start>=0 && end<s.length && (s[start]==s[end])){
        start--;
        end++;
    }
    if(maxn<(end-start-1)){
        low=start+1;
        maxn=end-start-1;
    }
}
```

第二种是利用动态规划的思想，我们知道对于长度为1的都是回文子串，然后枚举子串长度和子串起点，判断是否是回文子串，维护一个最大值和子串的起始点。

# AC代码

```javascript
/**
 * @param {string} s
 * @return {string}
 */
var low=0;
var maxn=0;
var longestPalindrome = function(s) {
    var len=s.length;
    if(len<2){
        return s;
    }
    var dp=new Array();
    var i=0;var j;
    var left=0,right=0,len1=0;
    for(;i<len;i++)
        dp[i]=new Array();
    for(i=0;i<len;i++){
        dp[i][i]=1;
        for(j=0;j<i;j++){
            dp[j][i] = (s[i] == s[j] && (i - j < 2 || dp[j + 1][i - 1]));
            if (dp[j][i] && len1 < i - j + 1) {
                len1 = i - j + 1;
                left = j;
                right = i;
            }
        }
    }
    return s.substr(left, right-left+1);
};

```

# LeetCode7 Reverse Integer

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

# LeetCode9 Palindrome Number

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

