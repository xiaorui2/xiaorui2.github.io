---
title: 剑指Offer刷题（持续更新）
date: 2019-06-24 08:51:45
tags: Algorithm
categories: 算法题训练
---

# 题目一 二维数组中的查找

## 题目描述

在一个二维数组中（每个一维数组的长度相同），每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

## 思路

首先我们知道了最小数是第一行第一个，最大数是最后一行的最后一个，每一行最大的一个数是每一行的最后一个，每一行最小一个数是每一行的第一个。

所以先从第一行的最后一个数设为`J`开始比较，如果目标大于`J`，则与下一行的最后一个数比较，如此循环，直到目标比`J`小

当目标比`J`小时，我们就能确定是哪一行然后往该行的前面递减。

## AC代码

`c++`版：

```c++
class Solution {
public:
    bool Find(int target, vector<vector<int> > array) {
        int n = array.size();
        if(n == 0)
            return false;
        int m = array[0].size();
        int i = 0;
        int j = m-1;
        while(i<n && j>=0){
            int temp = array[i][j];
            if(target < temp){
                j--;
            }
            else if(target > temp){
                i++;
            }
            else{
                return true;
            }
        }
        return false;
    }
};
```

`Java`版：

```java
public class Solution {
    public boolean Find(int target, int [][] array) {
        if(array == null){
            return false;
        }
        int n = array.length;
        int m = array[0].length;
        int i = 0;
        int j = m-1;
        while(i<n && j>=0){
            int temp = array[i][j];
            if(target < temp){
                j--;
            }
            else if(target > temp){
                i++;
            }
            else{
                return true;
            }
        }
        return false;
    }
}
```

# 题目二 替换空格

## 题目描述

请实现一个函数，将一个字符串中的每个空格替换成`“%20”`。例如，当字符串为`We Are Happy.`则经过替换之后的字符串为`We%20Are%20Happy`。

## 思路

第一个想法肯定就是从头开始找到一个空格就替换，但是这样的时间复杂度是`O(n^2)`的，因为你要写两重循环，我们可以降低时间复杂度，先预处理出来空格的个数，然后从后往前碰到空格进行替换，最后得到结果。当然这题用`Java`就巨简单了，`Java`提供替代函数。

## AC代码

`c++`版：

```c++
class Solution {
public:
	void replaceSpace(char *str,int length) {
        int blankNumber = 0;
        int oldstringLen;
        for (oldstringLen = 0; str[oldstringLen] != '\0'; oldstringLen++){
            if (str[oldstringLen] == ' ')
                blankNumber++;
        }
        int newstringLen = oldstringLen + blankNumber * 2;
        if (newstringLen>length)
            return;
        str[newstringLen] = '\0';
        int point1 = oldstringLen - 1, point2 = newstringLen - 1;
        while (point1 >= 0 && point2>point1){//两指针相同时，跳出循环
            if (str[point1] == ' '){
                str[point2--] = '0';
                str[point2--] = '2';
                str[point2--] = '%';

            }
            else   
                str[point2--] = str[point1];
            point1--;
        }
	}
};
```

`Java`版：

```java
public class Solution {
    public String replaceSpace(StringBuffer str) {
    	return str.toString().replaceAll("\\s", "%20");
    }
}
```

# 题目三 从尾到头打印链表

## 题目描述

输入一个链表，按链表值从尾到头的顺序返回一个`ArrayList`。

## 思路

因为链表是从头到尾，但是要从尾到头，所以很自然的就想到用Stack先存储对应的值，然后取出来放到`ArrayList`中。

## AC代码

`c++`版：

```c++
/**
*  struct ListNode {
*        int val;
*        struct ListNode *next;
*        ListNode(int x) :
*              val(x), next(NULL) {
*        }
*  };
*/
class Solution {
public:
    vector<int> printListFromTailToHead(ListNode* head) {
        ListNode *q=head;
        stack<int>s;
        vector<int>a;
        while(q!=NULL)
        {
            s.push(q->val);
            q=q->next;
        }
        while(!s.empty())
        {
            a.push_back(s.top());
            s.pop();
        }
        return a;
    }
};
```

`Java`版：

```java
/**
*    public class ListNode {
*        int val;
*        ListNode next = null;
*
*        ListNode(int val) {
*            this.val = val;
*        }
*    }
*
*/
import java.util.ArrayList;
import java.util.Stack;
public class Solution {
    public ArrayList<Integer> printListFromTailToHead(ListNode listNode) {
        ArrayList<Integer> ans = new ArrayList<Integer>();
        Stack<Integer> temp = new Stack<Integer>();
        ListNode head = listNode;
        while(head!=null){
            temp.push(head.val);
            head=head.next;
        }
        while(temp.size()!=0){
            ans.add(temp.peek());
            temp.pop();
        }
        return ans;
    }
}
```

# 题目五 用两个栈实现队列

## 题目描述

用两个栈来实现一个队列，完成队列的`Push`和`Pop`操作。 队列中的元素为`int`类型。

## 思路

一个栈存储放进来的值，一个栈用来`pop`，看下代码就知道了。

## AC代码

`c++`版：

```c++
class Solution
{
public:
    void push(int node) {
        stack1.push(node);
    }

    int pop() {
        int result;
        if(stack2.empty()){
            while(!stack1.empty()){
                stack2.push(stack1.top());
                stack1.pop();
            } 
        }
        result = stack2.top();
        stack2.pop();
        return result;
    }

private:
    stack<int> stack1;
    stack<int> stack2;
};
```

`Java`版：

```java
import java.util.Stack;

public class Solution {
    Stack<Integer> stack1 = new Stack<Integer>();
    Stack<Integer> stack2 = new Stack<Integer>();
    
    public void push(int node) {
        stack1.push(node);
    }
    
    public int pop() {
        if(stack2.size()==0){
            while(stack1.size()!=0){
                stack2.push(stack1.peek());
                stack1.pop();
            }
        }
        int temp=stack2.peek();
        stack2.pop();
        return temp;
    }
}
```

# 题目六 旋转数组的最小数字

## 题目描述

把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。 输入一个非减排序的数组的一个旋转，输出旋转数组的最小元素。 例如数组{3,4,5,1,2}为{1,2,3,4,5}的一个旋转，该数组的最小值为1。 NOTE：给出的所有元素都大于0，若数组大小为0，请返回0。

## 思路

就是找到这个数组的最小值。

## AC代码

`c++`版：

```c++
class Solution {
public:
    int minNumberInRotateArray(vector<int> v) {
        int len=v.size();
        if(len==0){
            return 0;
        }
        int ans=v[0];
        for(int i=1;i<len;i++){
            if(v[i]<ans)
                ans=v[i];
        }
        return ans;
    }
};
```

`Java`版：

```java
import java.util.ArrayList;
public class Solution {
    public int minNumberInRotateArray(int [] array) {
        if(array==null){
            return 0;
        }
        int len=array.length;
        int ans=array[0];
        for(int i=1;i<len;i++){
            if(array[i]<ans){
                ans=array[i];
            }
        }
        return ans;
    }
}
```

# 题目七 斐波那契数列

## 题目描述

大家都知道斐波那契数列，现在要求输入一个整数n，请你输出斐波那契数列的第n项（从0开始，第0项为0）。

n<=39

## 思路

经典斐波那契数列求值。

## AC代码

`c++`版：

```c++
class Solution {
public:
    int Fibonacci(int n) {
        int a[45];
        a[0] = 0;
        a[1] = 1;
        for(int i = 2; i <= n; i++)
            a[i] = a[i-1] + a[i-2];
        return a[n];      
    }
};
```

`Java`版：

```java
public class Solution {
    public int Fibonacci(int n) {
        int[] a = new int[45];
        a[0]=0;
        a[1]=1;
        for(int i=2;i<=n;i++)
            a[i]=a[i-1]+a[i-2];
        return a[n];  
    }
}
```

# 题目七 跳台阶

## 题目描述

一只青蛙一次可以跳上1级台阶，也可以跳上2级。求该青蛙跳上一个n级的台阶总共有多少种跳法（先后次序不同算不同的结果）。

## 思路

每层台阶可由下一层或者下两层跳上来，就是斐波那契数列递归求解即可。

## AC代码

`c++`版：

```c++
class Solution {
public:
    int jumpFloor(int number) {
        if(number == 1)
            return 1;
        else if(number == 2)
            return 2;
        else 
            return jumpFloor(number-1)+jumpFloor(number-2);
    }
};
```

`Java`版：

```java
public class Solution {
    public int JumpFloor(int target) {
        if(target == 1)
            return 1;
        else if(target == 2)
            return 2;
        else 
            return JumpFloor(target-1)+JumpFloor(target-2);
    }
}
```

# 题目八 跳台阶

## 题目描述

一只青蛙一次可以跳上1级台阶，也可以跳上2级。求该青蛙跳上一个n级的台阶总共有多少种跳法（先后次序不同算不同的结果）。

## 思路

每层台阶可由下一层或者下两层跳上来，就是斐波那契数列递归求解即可。

## AC代码

`c++`版：

```c++
class Solution {
public:
    int jumpFloor(int number) {
        if(number == 1)
            return 1;
        else if(number == 2)
            return 2;
        else 
            return jumpFloor(number-1)+jumpFloor(number-2);
    }
};
```

`Java`版：

```java
public class Solution {
    public int JumpFloor(int target) {
        if(target == 1)
            return 1;
        else if(target == 2)
            return 2;
        else 
            return JumpFloor(target-1)+JumpFloor(target-2);
    }
}
```

# 题目九 变态跳台阶

## 题目描述

一只青蛙一次可以跳上1级台阶，也可以跳上2级……它也可以跳上n级。求该青蛙跳上一个n级的台阶总共有多少种跳法。

## 思路

和斐波那契数列推导一样，这个推一下吧。

用Fib(n)表示青蛙跳上n阶台阶的跳法数，青蛙一次性跳上n阶台阶的跳法数1(n阶跳)，设定Fib(0) = 1；

当n = 1 时，只有一种跳法，即1阶跳：Fib(1) = 1;

当n = 2 时，有两种跳的方式，一阶跳和二阶跳：Fib(2) = Fib(1) + Fib(0) = 2;

当n = 3 时，有三种跳的方式，第一次跳出一阶后，后面还有Fib(3-1)中跳法；第一次跳出二阶后，后面还有Fib(3-2)中跳法；第一次跳出三阶后，后面还有Fib(3-3)中跳法

当n = n 时，共有n种跳的方式，第一次跳出一阶后，后面还有Fib(n-1)中跳法；第一次跳出二阶后，后面还有Fib(n-2)中跳法...第一次跳出n阶后，后面还有 Fib(n-n)中跳法.

Fib(n) = Fib(n-1)+Fib(n-2)+Fib(n-3)+..........+Fib(n-n)=Fib(0)+Fib(1)+Fib(2)+.......+Fib(n-1)
又因为Fib(n-1)=Fib(0)+Fib(1)+Fib(2)+.......+Fib(n-2)

两式相减得：Fib(n) = 2*Fib(n-1)    n >= 2

## AC代码

`c++`版：

```c++
class Solution {
public:
    int jumpFloorII(int number) {
        if(number==0)
            return 0;
        else if(number==1)
            return 1;
        else
            return 2*jumpFloorII(number-1);
    }
};
```

`Java`版：

```java
public class Solution {
    public int JumpFloorII(int target) {
        if(target == 0)
            return 0;
        else if(target == 1)
            return 1;
        else
            return 2*JumpFloorII(target-1);
    }
}
```

# 题目十 矩形覆盖

## 题目描述

我们可以用2\*1的小矩形横着或者竖着去覆盖更大的矩形。请问用n个2\*1的小矩形无重叠地覆盖一个2\*n的大矩形，总共有多少种方法？

## 思路

n=1 ：只有横放一个矩形一种解决办法 
n=2 ：有横放一个矩形，竖放两个矩形两种解决办法 
n=3 ：n=2的基础上加1个横向，n=1的基础上加2个竖向 
n=4 ：n=3的基础上加1个横向，n=2的基础上加2个竖向 

...

n=n ：n = f(n-1) + f(n-2)

## AC代码

`c++`版：

```c++
class Solution {
public:
    int jumpFloorII(int number) {
        if(number==0)
            return 0;
        else if(number==1)
            return 1;
        else
            return 2*jumpFloorII(number-1);
    }
};
```

`Java`版：

```java
public class Solution {
    public int JumpFloorII(int target) {
        if(target == 0)
            return 0;
        else if(target == 1)
            return 1;
        else
            return 2*JumpFloorII(target-1);
    }
}
```

# 题目十一 二进制中1的个数

## 题目描述

输入一个整数，输出该数二进制表示中1的个数。其中负数用补码表示。

## 思路

把一个整数减去1，再和原整数做与运算，会把该整数最右边的一个1变成0.那么一个整数的二进制表示中有多少个1，就可以进行多少次运算

## AC代码

`c++`版：

```c++
class Solution {
public:
     int  NumberOf1(int n) {
         int ans=0;
         while(n){
             n&=(n-1);
             ans++;
         }
         return ans;
     }
};
```

`Java`版：

```java
public class Solution {
    public int NumberOf1(int n) {
        int ans=0;
        while(n!=0){
             n&=(n-1);
             ans++;
         }
         return ans;
    }
}
```

# 题目十二 数值的整数次方

## 题目描述

给定一个double类型的浮点数base和int类型的整数exponent。求base的exponent次方。

## 思路

就是简单地整数次方，但是要考虑正负数和0。

## AC代码

`c++`版：

```c++
class Solution {
public:
    double Power(double a, int b) {
        double ans = 1.0;
        if (b > 0) {
            for (int i = 1; i <= b; i++) {
                ans = ans * a;
            }
        } else if (b < 0) {
            b = -b;
            for (int i = 1; i <= b; i++) {
                ans = ans / a;
            }
        } else {
            return 1.0;
        }
        return ans;
    }
};
```

`Java`版：

```java
public class Solution {
    public double Power(double a, int b) {
        double ans = 1.0;
        if (b > 0) {
            for (int i = 1; i <= b; i++) {
                System.out.println(ans);
                ans = ans * a;
            }
        } else if (b < 0) {
            b = -b;
            for (int i = 1; i <= b; i++) {
                ans = ans / a;
                //System.out.println(ans);
            }
        } else {
            return 1.0;
        }
        return ans;
    }
}
```

# 题目十三 调整数组顺序使奇数位于偶数前面

## 题目描述

输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有的奇数位于数组的前半部分，所有的偶数位于数组的后半部分，并保证奇数和奇数，偶数和偶数之间的相对位置不变。

## 思路

两种做法：

`c++`用空间换时间，直接把所有的值存到两个数组里面然后重新按顺序填一下就好了很简单。

`Java`就是用两个变量标注奇数和偶数的位置`i`标示奇数，`j`表示偶数，当出现`i`和`j`不同的时候就把所有的奇数往前移保证奇数都在前面然后把这个偶数放在这个位置，然后继续往前找。可以画图理解一下。

## AC代码

`c++`版：

```c++
class Solution {
public:
    void reOrderArray(vector<int> &array) {
        vector<int> t1,t2;
        int i,j;
        for(i=0;i<array.size();i++){
            if(array[i]%2!=0){
                t1.push_back(array[i]);
            }else{
                t2.push_back(array[i]);
            }
        }
        for(i=0;i<t1.size();i++){
            array[i]=t1[i];
        }
        for(j=0;j<t2.size()&&i<array.size();j++,i++){
            array[i]=t2[j];
        }
    }
};
```

`Java`版：

```java
public class Solution {
    public void reOrderArray(int[] array) {
        int j = 0;
        for (int i = 0; i < array.length; i++) {
            if (array[i] % 2 == 1) {
                //如果是奇数的话
                if (i != j) {
                    int temp = array[i];
                    int k = i;
                    for (k = i; k > j; k--) {
                        array[k] = array[k - 1];
                    }
                    array[k] = temp;

                }
                j++;
            }
        }
    }
}
```

























































