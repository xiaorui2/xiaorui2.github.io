---
title: LeetCode5
date: 2019-05-26 21:20:32
tags: Algorithm
categories: LeetCode
---

# Longest Palindromic Substring

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

