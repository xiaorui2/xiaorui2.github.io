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

# 题目四 重建二叉树

## 题目描述

输入某二叉树的前序遍历和中序遍历的结果，请重建出该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。例如输入前序遍历序列`{1,2,4,7,3,5,6,8}`和中序遍历序列`{4,7,2,1,5,3,8,6}`，则重建二叉树并返回。

## 思路

之前写过这样的思路，就是根据前序和中序排列的特性找到`root`，不断进行递归就可以了。`c++`版是直接拿了别人的，个人不建议这么写，可以看`Java`版的会容易理解一点

![](1.png)

## AC代码

`c++`版：

```c++
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    struct TreeNode* reConstructBinaryTree(vector<int> pre,vector<int> in) {
        if (pre.size() == 0 || pre.size() != in.size()) {
            return NULL;
        }
        vector<int> pre_left;
        vector<int> pre_right;
        vector<int> in_left;
        vector<int> in_right;
        TreeNode *root = new TreeNode(pre.at(0));
        // 查找根在中序数组中的位置
        int rootIndex = 0;
        for (int i = 0; i < in.size(); ++i) {
            if (in.at(i) == pre.at(0)) {
                rootIndex = i;
                break;
            }
        }
        // 左子树部分
        for (int i = 0; i < rootIndex; ++i) {
            in_left.push_back(in.at(i));
            
            if (i + 1 < pre.size()) {
                pre_left.push_back(pre.at(i + 1));
            }
        }
        // 右子树部分
        for (int i = rootIndex + 1; i < in.size(); ++i) {
            in_right.push_back(in.at(i));
            pre_right.push_back(pre.at(i));
        }
        root->left = reConstructBinaryTree(pre_left, in_left);
        root->right = reConstructBinaryTree(pre_right, in_right);
        return root;
    }
};
```

`Java`版：

```java
/**
 * Definition for binary tree
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
public class Solution {
    public TreeNode reConstructBinaryTree(int [] pre,int [] in) {
        TreeNode root = funre(pre,0,pre.length-1,in,0,in.length-1);
        return root;
    }
    
    public TreeNode funre(int[] pre,int startPre,int endPre,int[] in,int startIn,int endIn){
        if(startPre>endPre||startIn>endIn){
            return null;
        }
        TreeNode root = new TreeNode(pre[startPre]);
        for(int i=startIn;i<=endIn;i++){
            if(in[i] == pre[startPre]){
                root.left = funre(pre,startPre+1,startPre+i-startIn,in,startIn,i-1);
                root.right = funre(pre,startPre+i-startIn+1,endPre,in,i+1,endIn);
            }
        }
        return root;
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

把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。 输入一个非减排序的数组的一个旋转，输出旋转数组的最小元素。 例如数组`{3,4,5,1,2}`为`{1,2,3,4,5}`的一个旋转，该数组的最小值为1。 `NOTE`：给出的所有元素都大于0，若数组大小为0，请返回0。

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

`n=1` ：只有横放一个矩形一种解决办法 
`n=2` ：有横放一个矩形，竖放两个矩形两种解决办法 
`n=3` ：`n=2`的基础上加1个横向，n=1的基础上加2个竖向 
`n=4`：`n=3`的基础上加1个横向，n=2的基础上加2个竖向 

`...`

`n=n` ：`n = f(n-1) + f(n-2)`

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

给定一个`double`类型的浮点数`base`和`int`类型的整数`exponent`。求`base`的`exponent`次方。

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

# 题目十四 链表中倒数第k个结点

## 题目描述

输入一个链表，输出该链表中倒数第`k`个结点。

## 思路

解题思路就和给你一个有环的链表让你找入口，我们选择用两个指针，两个一个走快一点一个走慢一点然后它必会重合就是那个入口，这个也一样，这里使用两个指针实现一次遍历，第一个指针先走`k-1`步，第二个指针一直不动；然后两个指针同时移动，知道第一个指针遍历完成。因为两个指针间隔为`k-1`，所以第二个指针指向的节点即为倒数第`k`个节点。

同样的题还有求链表的中间节点，我们也可以定义两个指针，同时从链表的头节点出发，一个指针一次走一步，另一个指针一次走两步。当走得快的指针走到链表的末尾时，走得慢的指针正好在链表的中间。

## AC代码

`c++`版：

```c++
/*
struct ListNode {
	int val;
	struct ListNode *next;
	ListNode(int x) :
			val(x), next(NULL) {
	}
};*/
class Solution {
public:
    ListNode* FindKthToTail(ListNode* pListHead, unsigned int k) {
    ListNode* p=pListHead;
    ListNode* q=pListHead;
    int i=0;
    for(;p!=NULL;i++)
    {
        if(i>=k)
            q=q->next;
        p=p->next;
    }
    if(i<k)
        return NULL;
    else
        return q;
    }
};
```

`Java`版：

```java
/*
public class ListNode {
    int val;
    ListNode next = null;

    ListNode(int val) {
        this.val = val;
    }
}*/
public class Solution {
    public ListNode FindKthToTail(ListNode head,int k) {
        ListNode p=head;
        ListNode q=head;
        int i=0;
        for(;p!=null;i++)
        {
            if(i>=k)
                q=q.next;
            p=p.next;
        }
        if(i<k)
            return null;
        else
            return q;
    }
}
```

# 题目十五 反转链表

## 题目描述

输入一个链表，反转链表后，输出新链表的表头。

## 思路

两种思路一个是递归，一个是遍历。分别用`c++`和`Java`写的。

递归：
我们使其先走到链表的末尾，确保每次回溯时都返回最后一个节点的指针。同时从倒数第二个结点开始反序。
`head.next.next = head;`是指使当前节点的下一个节点指向自己
`head.next = null`断开与下一个节点的联系，完成真正的反序操作。

遍历：

一遍遍历，保存三个指针，`pre`，`now`，`aft`，用`aft`保存下一个节点，防止链表断开后，无法继续后移然后每次`now`的`next`指向`pre`实现反序，然后`now`和`pre`同时后移一步即可，直到`now`指向空为止，说明链表已完成反序操作。

## AC代码

`c++`版：

```c++
/*
struct ListNode {
	int val;
	struct ListNode *next;
	ListNode(int x) :
			val(x), next(NULL) {
	}
};*/
class Solution {
public:
    ListNode* ReverseList(ListNode* pHead) {
        if(pHead == NULL || pHead->next == NULL){
            return pHead;
        }
        ListNode *reverseHead = ReverseList(pHead->next);
        pHead->next->next = pHead;
        pHead->next = NULL;
        return reverseHead;
    }
};
```

`Java`版：

```java
/*
public class ListNode {
    int val;
    ListNode next = null;

    ListNode(int val) {
        this.val = val;
    }
}*/
public class Solution {
    public ListNode ReverseList(ListNode head) {
        ListNode pre = null;
        ListNode now = head;
        ListNode aft = null;
        while(now != null){
            aft = now.next;
            now.next=pre;
            pre = now;
            now = aft;
        }
        return pre;
    }
}
```

# 题目十六 合并两个排序的链表

## 题目描述

输入两个单调递增的链表，输出两个链表合成后的链表，当然我们需要合成后的链表满足单调不减规则。

## 思路

同上两种思路一个是递归，一个是遍历。分别用`Java`和`c++`写的。

递归：
新建个链表，每次两个链表当中选一个小值复制过去，那么现在是不是就是合并两个链表（其中有一个已经赋值过去了，就是之前的`.next`，然后递归就可以了。

遍历：

新建一个链表，我们先把第一个值复制过去，然后当两个链表都存在的时候，谁值小就把谁链接到新链表后面然后后移一个，直到两个当中某一个为空了，最后把两个链表剩余的直接链接上去就可以了。

## AC代码

`c++`版：

```c++
/*
struct ListNode {
	int val;
	struct ListNode *next;
	ListNode(int x) :
			val(x), next(NULL) {
	}
};*/
class Solution {
public:
    ListNode* Merge(ListNode* p1, ListNode* p2){
        if(p1 == NULL)
            return p2;
        if(p2 == NULL)
            return p1;
        ListNode *ans = new ListNode(0);
        ListNode *temp = NULL;
        if(p1->val <= p2->val){
            ans->next = p1;
            p1 = p1->next;
        }else{
            ans->next = p2;
            p2 = p2->next;
        }
        temp = ans->next;
        while(p1 != NULL && p2 != NULL){
            if(p1->val <= p2->val){
                temp->next = p1;
                temp = p1;
                p1 = p1->next;
            }else{
                temp->next = p2;
                temp = p2;
                p2 = p2->next;
            }
        }
        if(p1 != NULL){
            temp->next = p1;
        }
        if(p2 != NULL){
            temp->next = p2;
        }
        return ans->next;
    }
};
```

`Java`版：

```java
/*
public class ListNode {
    int val;
    ListNode next = null;

    ListNode(int val) {
        this.val = val;
    }
}*/
public class Solution {
    public ListNode Merge(ListNode l1,ListNode l2) {
        if (l1 == null) return l2;
        if (l2 == null) return l1;
 
        ListNode head = null;
        if (l1.val <= l2.val){
            head = l1;
            head.next = Merge(l1.next, l2);
        } else {
            head = l2;
            head.next = Merge(l1, l2.next);
        }
        return head;
    }
}
```

# 题目十八 二叉树的镜像

## 题目描述

操作给定的二叉树，将其变换为源二叉树的镜像

## 思路

递归。

先把特殊情况去了，然后对于每个节点，把左右节点调换一下，然后子节点递归调用即可。

递归：
新建个链表，每次两个链表当中选一个小值复制过去，那么现在是不是就是合并两个链表（其中有一个已经赋值过去了，就是之前的`.next`，然后递归就可以了。

遍历：

新建一个链表，我们先把第一个值复制过去，然后当两个链表都存在的时候，谁值小就把谁链接到新链表后面然后后移一个，直到两个当中某一个为空了，最后把两个链表剩余的直接链接上去就可以了。

## AC代码

`c++`版：

```c++
/*
struct TreeNode {
	int val;
	struct TreeNode *left;
	struct TreeNode *right;
	TreeNode(int x) :
			val(x), left(NULL), right(NULL) {
	}
};*/
class Solution {
public:
    void Mirror(TreeNode *pRoot) {
        if((pRoot==NULL) || (pRoot->left == NULL && pRoot->right == NULL)){
            return ;
        }
        TreeNode *temp = NULL;
		temp = pRoot->right;
		pRoot->right = pRoot->left;
		pRoot->left = temp;
        if(pRoot->left){
            Mirror(pRoot->left);
        }
        if(pRoot->right){
            Mirror(pRoot->right);
        }
    }
};
```

`Java`版：

```java
/**
public class TreeNode {
    int val = 0;
    TreeNode left = null;
    TreeNode right = null;

    public TreeNode(int val) {
        this.val = val;

    }

}
*/
public class Solution {
    public void Mirror(TreeNode pRoot) {
        if((pRoot == null) || (pRoot.left == null && pRoot.right == null)){
            return ;
        }
        TreeNode temp = null;
		temp = pRoot.right;
		pRoot.right = pRoot.left;
		pRoot.left = temp;
        if(pRoot.left != null){
            Mirror(pRoot.left);
        }
        if(pRoot.right != null){
            Mirror(pRoot.right);
        }
    }
}
```

# 题目十九 顺时针打印矩阵

## 题目描述

输入一个矩阵，按照从外向里以顺时针的顺序依次打印出每一个数字，例如，如果输入如下4 X 4矩阵： 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 则依次打印出数字1,2,3,4,8,12,16,15,14,13,9,5,6,7,11,10.

## 思路

画下图就知道，因为每次起点都是在`（a,a）`点，所以保证每次`n > flag\*2 && m > flag\*2`，然后就是四次循环跑就可以了。

## AC代码

`c++`版：

```c++
class Solution {
public:
    vector<int> printMatrix(vector<vector<int> > matrix) {
        int n = matrix.size();
        vector<int> ans;
        if(n == 0) {
            return ans;
        }
        int m = matrix[0].size();
        int flag = 0;
        while(n > flag*2 && m > flag*2) {
            int endX = m-flag-1;
			int endY = n-flag-1;
            for(int i=flag;i<=endX;i++) {
                ans.push_back(matrix[flag][i]);
            }
            if(flag < endY) {
				for (int i=flag+1;i<=endY;i++) {
					ans.push_back(matrix[i][endX]);
				}
			}
			if(flag < endX && flag < endY) {
				for (int i=endX-1;i>=flag;i--) {
					ans.push_back(matrix[endY][i]);
				}
			}
			if(flag < endX && flag < endY-1) {
				for (int i=endY-1;i>=flag+1;i--) {
					ans.push_back(matrix[i][flag]);
				}
			}
			flag++;
        }
        return ans;
    }
};
```

`Java`版：

```java
import java.util.ArrayList;
public class Solution {
    public ArrayList<Integer> printMatrix(int [][] matrix) {
        int n = matrix.length;
        ArrayList<Integer> ans =  new ArrayList<Integer>();
        if(n == 0) {
            return ans;
        }
        int m = matrix[0].length;
        int flag = 0;
        while(n > flag*2 && m > flag*2) {
            int endX = m-flag-1;
			int endY = n-flag-1;
            for(int i=flag;i<=endX;i++) {
                ans.add(matrix[flag][i]);
            }
            if(flag < endY) {
				for (int i=flag+1;i<=endY;i++) {
					ans.add(matrix[i][endX]);
				}
			}
			if(flag < endX && flag < endY) {
				for (int i=endX-1;i>=flag;i--) {
					ans.add(matrix[endY][i]);
				}
			}
			if(flag < endX && flag < endY-1) {
				for (int i=endY-1;i>=flag+1;i--) {
					ans.add(matrix[i][flag]);
				}
			}
			flag++;
        }
        return ans;
    }
}
```

# 题目二十 包含min函数的栈

## 题目描述

定义栈的数据结构，请在该类型中实现一个能够得到栈中所含最小元素的`min`函数（时间复杂度应为`O（1）`）。

## 思路

两个栈维护，一个维护正常的栈元素，一个维护`min`值栈，

那什么时候放到`min`值栈呢？

第一个是`min`值栈为空必须要放

第二个就是我现在放进来的值比我所有的元素值也就是`min`值栈的首元素。

当然了在`pop`的时候如果把最小值`pop`了，`min`值栈也要进行对应的`pop`操作

先把特殊情况去了，然后对于每个节点，把左右节点调换一下，然后子节点递归调用即可。

## AC代码

`c++`版：

```c++
class Solution {
public:
    stack<int>s1;//存储元素
    stack<int>s2;//存储最小值
    void push(int value) {
        s1.push(value);
        if(s2.empty())
            s2.push(value);
        else if(value < s2.top())
            s2.push(value);
    }
    void pop() {
        if(s1.top() == s2.top())
            s2.pop();
        s1.pop();
    }
    int top() {
        return s1.top();
    }
    int min() {
        return s2.top();
    }
};
```

`Java`版：

```java
import java.util.Stack;

public class Solution {

    Stack<Integer> s1 = new Stack<Integer>();
    Stack<Integer> s2 = new Stack<Integer>();
    public void push(int node) {
        s1.push(node);
        if(s2.empty())
            s2.push(node);
        else if(node < s2.peek())
            s2.push(node);
    }

    public void pop() {
        if(s1.peek() == s2.peek())
            s2.pop();
        s1.pop();
    }

    public int top() {
        return s1.peek();
    }

    public int min() {
        return s2.peek();
    }
}
```

# 题目二十一 栈的压入、弹出序列

## 题目描述

输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否可能为该栈的弹出顺序。假设压入栈的所有数字均不相等。例如序列`1,2,3,4,5`是某栈的压入顺序，序列`4,5,3,2,1`是该压栈序列对应的一个弹出序列，但`4,3,5,1,2`就不可能是该压栈序列的弹出序列。（注意：这两个序列的长度是相等的）

## 思路

这个比较简单，就是模拟一次就行，按放进的顺序往里放元素，然后碰到出栈元素就执行`pop`，注意这里一个`while`循环，因为可能`pop`了下一个还是出栈元素 ，到最后看这个栈是不是空的就行了。

那什么时候放到`min`值栈呢？

第一个是`min`值栈为空必须要放

第二个就是我现在放进来的值比我所有的元素值也就是`min`值栈的首元素。

当然了在`pop`的时候如果把最小值`pop`了，`min`值栈也要进行对应的`pop`操作

先把特殊情况去了，然后对于每个节点，把左右节点调换一下，然后子节点递归调用即可。

## AC代码

`c++`版：

```c++
class Solution {
public:
    bool IsPopOrder(vector<int> pushV,vector<int> popV) {
        stack<int> s;
        int len1 = pushV.size();
        int len2 = popV.size();
        int i = 0;
        int j = 0;
        while(i < len1 && j < len2) {
            s.push(pushV[i]);
            i++;
            while(!s.empty() && s.top() == popV[j]) {
                s.pop();
                j++;
            }
        }
        if(s.empty()) {
            return true;
        } else {
            return false;
        }
    }
};
```

`Java`版：

```java
import java.util.Stack;

public class Solution {
    public boolean IsPopOrder(int [] pushA,int [] popA) {
        Stack<Integer> s = new Stack();
        int len1 = pushA.length;
        int len2 = popA.length;
        int i = 0;
        int j = 0;
        while(i < len1 && j < len2) {
            s.push(pushA[i]);
            i++;
            while(!s.empty() && s.peek() == popA[j]) {
                s.pop();
                j++;
            }
        }
        if(s.empty()) {
            return true;
        } else {
            return false;
        }
    }
}
```

# 题目二十二 从上往下打印二叉树

## 题目描述

从上往下打印出二叉树的每个节点，同层节点从左至右打印。

## 思路

二叉树的层次遍历

## AC代码

`c++`版：

```c++
/*
struct TreeNode {
	int val;
	struct TreeNode *left;
	struct TreeNode *right;
	TreeNode(int x) :
			val(x), left(NULL), right(NULL) {
	}
};*/
class Solution {
public:
    vector<int> PrintFromTopToBottom(TreeNode* root) {
        vector<int> ans;
        queue<TreeNode*> q;
        if(root ==NULL)
            return ans;
        q.push(root);
        while(!q.empty()){
            TreeNode* tempNode = q.front();
            q.pop();
            ans.push_back(tempNode->val);
            if (tempNode->left!=NULL)
                q.push(tempNode->left);
            if (tempNode->right!=NULL)
                q.push(tempNode->right);
        }
        return ans;
    }
};
```

`Java`版：

```java
import java.util.ArrayList;
import java.util.Queue;
import java.util.LinkedList;
/**
public class TreeNode {
    int val = 0;
    TreeNode left = null;
    TreeNode right = null;

    public TreeNode(int val) {
        this.val = val;

    }

}
*/
public class Solution {
    public ArrayList<Integer> PrintFromTopToBottom(TreeNode root) {
        ArrayList<Integer> ans = new ArrayList<Integer>();
        Queue<TreeNode> myQueue = new LinkedList<>();
        if(root == null)
            return ans;
        myQueue.offer(root);
        while(!myQueue.isEmpty()){
            TreeNode tempNode = myQueue.poll();
            ans.add(tempNode.val);
            if (tempNode.left!=null)
                myQueue.offer(tempNode.left);
            if (tempNode.right!=null)
                myQueue.offer(tempNode.right);
        }
        return ans;
    }
}
```

# 题目二十三 二叉搜索树的后序遍历序列

## 题目描述

输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历的结果。如果是则输出Yes,否则输出No。假设输入的数组的任意两个数字都互不相同。

## 思路

如果一个数组是一个二叉搜索树的后序遍历结果，那么这个数组有以下特征：数组的最后一个元素是（子）树的根节点，前面的部分可以分为两个部分，前一部分的元素都小于最后一个元素值，后一部分都大于数组最后一个元素值，我们首先需要找到这个临界值，在判断前一部分的元素是否都小于数组末尾的值，接着分别对数组前两部分处理，（递归定义）。

## AC代码

`c++`版：

```c++
class Solution {
    bool judge(vector<int> &sequence, int l, int r) {
        if (l >= r) {
            return true;
        }
        int i = r - 1;
        while (i >= l && sequence[i] > sequence[r]) {
            i--;
        }
        for (int j = i; j >= l; j--) {
            if (sequence[j] > sequence[r]) {
                return false;
            }
        }
        return judge(sequence, l, i) && judge(sequence, i + 1, r - 1);
    }
public:
    bool VerifySquenceOfBST(vector<int> sequence) {
        int len = sequence.size();
        if (len == 0) {
            return false;
        } else {
            return judge(sequence, 0, len - 1);
        }
    }
};
```

`Java`版：

```java
public class Solution {
    public boolean VerifySquenceOfBST(int [] sequence) {
        if (sequence == null || sequence.length <= 0) {
            return false;
        }
        return verifySequenceOfBST(sequence, 0, sequence.length - 1);
    }
    public static boolean verifySequenceOfBST(int[] sequence, int start, int end) {
        if (start >= end) {
            return true;
        }
        int index = start;
        while (index < end - 1 && sequence[index] < sequence[end]) {
            index++;
        }
        int right = index;
        while (index < end - 1 && sequence[index] > sequence[end]) {
            index++;
        }
        if (index != end - 1) {
            return false;
        }
        index = right;
        return verifySequenceOfBST(sequence, start, index - 1) && verifySequenceOfBST(sequence, index, end - 1);
    }
}
```

# 题目二十四 二叉树中和为某一值的路径

## 题目描述

输入一颗二叉树的跟节点和一个整数，打印出二叉树中结点值的和为输入整数的所有路径。路径定义为从树的根结点开始往下一直到叶结点所经过的结点形成一条路径。(注意: 在返回值的list中，数组长度大的数组靠前)

## 思路

首先我们知道这题肯定要用`dfs`，当一次遍历完成后，如果输入整数值恰好等于节点值之和，则输出这条路径并且回退一个节点；如果不等于则直接回退一个节点，即回退到当前节点的父节点，如果该父节点有右孩子，则继续遍历，否则继续回退。考虑回退到根节点，此时如果它有右孩子，则继续遍历，否则整个DFS结束。需要注意的是不论路径的值是否等于输入整数值，都要回退，也就是使用remove函数移除路径上的最后一个节点。

## AC代码

`c++`版：

```c++
/*
struct TreeNode {
	int val;
	struct TreeNode *left;
	struct TreeNode *right;
	TreeNode(int x) :
			val(x), left(NULL), right(NULL) {
	}
};*/
class Solution {
public:
    vector<vector<int> >result;
    vector<int> ans;
    void dfs(TreeNode* root,int expectNumber) {
        ans.push_back(root->val);
        if(root->val == expectNumber && root->left == NULL && root->right == NULL) {
            result.push_back(ans);
        }
        else {
            if(root->left)
                dfs(root->left, expectNumber-root->val);
            if(root->right)
                dfs(root->right, expectNumber-root->val);
        }
        ans.pop_back();
    }
    vector<vector<int> > FindPath(TreeNode* root,int expectNumber) {
        if(root != NULL){
            dfs(root, expectNumber);
        }
        return result;
    }
};
```

`Java`版：

```java
import java.util.ArrayList;
/**
public class TreeNode {
    int val = 0;
    TreeNode left = null;
    TreeNode right = null;

    public TreeNode(int val) {
        this.val = val;

    }

}
*/
public class Solution {
    ArrayList<ArrayList<Integer>> result = new ArrayList<ArrayList<Integer>>();;
    ArrayList<Integer> ans = new ArrayList<Integer>();;
    /*public dfs(TreeNode root,int expectNumber) {
        ans.add(root.val);
        if(root.val == expectNumber && root.left == null && root.right == null) {
            result.add(ans);
        }
        else {
            if(root.left != null)
                dfs(root.left, expectNumber-root.val);
            if(root.right != null)
                dfs(root.right, expectNumber-root.val);
        }
        ans.remove(ans.size()-1);
    }*/
    public ArrayList<ArrayList<Integer>> FindPath(TreeNode root,int target) {
        if(root != null){
            ans.add(root.val);
            if(root.val == target && root.left == null && root.right == null) {
                result.add(new ArrayList<Integer>(ans));
            }
            else {
                if(root.left != null)
                    FindPath(root.left, target-root.val);
                if(root.right != null)
                    FindPath(root.right, target-root.val);
            }
            ans.remove(ans.size()-1);
        }
        return result;
    }
}
```

# 

# 题目二十八 数组中出现次数超过一半的数字

## 题目描述

数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字。例如输入一个长度为9的数组{1,2,3,2,2,2,5,4,2}。由于数字2在数组中出现了5次，超过数组长度的一半，因此输出2。如果不存在则输出0。

## 思路

有两种想法，一次遍历找到出现次数最多的那个数字，再遍历一下找到这个数出现的次数看是否满足。

或者一次遍历，用map存储对应的数字出现的次数，然后判断这个值是否满足题意。

## AC代码

`c++`版：

```c++
/*
struct TreeNode {
	int val;
	struct TreeNode *left;
	struct TreeNode *right;
	TreeNode(int x) :
			val(x), left(NULL), right(NULL) {
	}
};*/
class Solution {
public:
    vector<int> PrintFromTopToBottom(TreeNode* root) {
        vector<int> ans;
        queue<TreeNode*> q;
        if(root ==NULL)
            return ans;
        q.push(root);
        while(!q.empty()){
            TreeNode* tempNode = q.front();
            q.pop();
            ans.push_back(tempNode->val);
            if (tempNode->left!=NULL)
                q.push(tempNode->left);
            if (tempNode->right!=NULL)
                q.push(tempNode->right);
        }
        return ans;
    }
};
```

`Java`版：

```java
import java.util.HashMap;

public class Solution {
    public int MoreThanHalfNum_Solution(int [] array) {
        if (array == null || array.length == 0)
            return 0;
        HashMap<Integer, Integer> map = new HashMap<>();
        int count;
        int len = array.length;
        for (int i = 0;i < len;i++) {
            if (!map.containsKey(array[i])) {
                map.put(array[i], 1);
            } else {
                count = map.get(array[i]);
                map.put(array[i], ++count);
            }
            if (map.get(array[i]) > array.length / 2) {
                return array[i];
            }
        }
        return 0;
    }
}
```

# 题目二十九 最小的K个数

## 题目描述

输入`n`个整数，找出其中最小的`K`个数。例如输入`4,5,1,6,2,7,3,8`这8个数字，则最小的4个数字是`1,2,3,4,`。

## 思路

有两种想法，直接`sort`一遍，取前k个数即可。

或者呢可以用冒泡排序的思想，我么知道冒泡排序是一个`O(n^2)`的算法，它是每次把一个数排序到最前面保证前面数有序，然后一直到整个数组有序，那么我们只需要循环`k`次就行了在这里面，保证前k个数最小的就满足题意了。

## AC代码

`c++`版：

```c++
class Solution {
public:
    vector<int> GetLeastNumbers_Solution(vector<int> input, int k) {
        vector<int> v;
        if(k<=0 || input.size()<=0 || k>input.size())
            return v;
        sort(input.begin(), input.end());
        int i = 0;
        while(i < k) {
            v.push_back(input[i]);
            i++;
        }
        return v;
    }
};
```

`Java`版：

```java
import java.util.ArrayList;
public class Solution {
    public ArrayList<Integer> GetLeastNumbers_Solution(int [] input, int k) {
        if(input == null)
            return null;
        ArrayList<Integer> ans = new ArrayList<Integer>();
        if(k > input.length)
            return ans;
        int temp;
        for(int i = 0; i < k; i++){
            for(int j = i + 1; j < input.length; j++){
                if(input[i] > input[j]){
                    temp = input[i];
                    input[i] = input[j];
                    input[j] = temp;
                }
            }
            ans.add(input[i]);
        }
        return ans;
    }
}
```

# 题目三十 连续子数组的最大和

## 题目描述

HZ偶尔会拿些专业问题来忽悠那些非计算机专业的同学。今天测试组开完会后,他又发话了:在古老的一维模式识别中,常常需要计算连续子向量的最大和,当向量全为正数的时候,问题很好解决。但是,如果向量中包含负数,是否应该包含某个负数,并期望旁边的正数会弥补它呢？例如:{6,-3,-2,7,-15,1,2,2},连续子向量的最大和为8(从第0个开始,到第3个为止)。给一个数组，返回它的最大连续子序列的和，你会不会被他忽悠住？(子向量的长度至少是1)

## 思路

可以用递归去做，可以看我的LeetCode字节跳动专题有一样的。

这个是`O(n)`的解法，思路是对于一个数组中的一个数x，若是x的左边的数加起来非负，那么加上x能使得值变大，这样我们认为x之前的数的和对整体和是有贡献的。如果前几项加起来是负数，则认为有害于总和。
我们用cur记录当前值, 用max记录最大值，如果cur<0,则舍弃之前的数，让cur等于当前的数字，否则，cur = cur+当前的数字。若cur和大于max更新max。

## AC代码

`c++`版：

```c++
class Solution {
public:
    int FindGreatestSumOfSubArray(vector<int> array) {
        if(array.size() == 0)
            return 0;
        int cur = array[0], max = array[0];
        for(int i=1; i<array.size(); i++){
            cur = cur > 0 ? cur + array[i] : array[i];
            if(max < cur)
                max = cur;
        }
        return max;
    }
};
```

`Java`版：

```java
public class Solution {
    public int FindGreatestSumOfSubArray(int[] array) {
        if(array.length == 0)
            return 0;
        int cur = array[0], max = array[0];
        for(int i=1; i<array.length; i++){
            cur = cur > 0 ? cur + array[i] : array[i];
            if(max < cur)
                max = cur;
        }
        return max;
    }
}
```

# 题目三十一 整数中1出现的次数（从1到n整数中1出现的次数）

## 题目描述

求出1~13的整数中1出现的次数,并算出100~1300的整数中1出现的次数？为此他特别数了一下1~13中包含1的数字有1、10、11、12、13因此共出现6次,但是对于后面问题他就没辙了。ACMer希望你们帮帮他,并把问题更加普遍化,可以很快的求出任意非负整数区间中1出现的次数（从1 到 n 中1出现的次数）。

## 思路

暴力循环一下，对于每个数都判断一下，应该有更好的解法，暂时没想到。

## AC代码

`c++`版：

```c++
class Solution {
public:
    int NumberOf1Between1AndN_Solution(int n){
        int count = 0;
        for(int i=0; i<=n; i++){
            int temp = i;
            while(temp != 0){
                if(temp%10 == 1){
                    count++;
                }
                temp /= 10;
            }
        }
        return count;
    }
};
```

`Java`版：

```java
public class Solution {
    public int NumberOf1Between1AndN_Solution(int n) {
        int count = 0;
        for(int i=0; i<=n; i++){
            int temp = i;
            while(temp != 0){
                if(temp%10 == 1){
                    count++;
                }
                temp /= 10;
            }
        }
        return count;
    }
}
```

# 题目三十三 丑数

## 题目描述

把只包含质因子2、3和5的数称作丑数（Ugly Number）。例如6、8都是丑数，但14不是，因为它包含质因子7。 习惯上我们把1当做是第一个丑数。求按从小到大的顺序的第N个丑数。

## 思路

某个丑数肯定是前面丑数的`2,3,5`倍数。只需要从前往后生成即可。

## AC代码

`c++`版：

```c++
class Solution {
public:
    int min(int a,int b,int c){
        int minn=(a<b)?a:b;
        return (minn<c)?minn:c;
    }
    int GetUglyNumber_Solution(int index) {
        if(index<=0)
            return 0;
        int a[index];
        a[0]=1;
        int cnt1=0;
        int cnt2=0;
        int cnt3=0;
        for(int i=1;i<index;i++){
            int minn=min(a[cnt1]*2,a[cnt2]*3,a[cnt3]*5);
            a[i]=minn;
            while(a[cnt1]*2<=minn)
                cnt1++;
            while(a[cnt2]*3<=minn)
                cnt2++;
            while(a[cnt3]*5<=minn)
                cnt3++;
        }
        return a[index-1];
    }
};
```

`Java`版：

```java
public class Solution {
    public int min(int a,int b,int c){
        int min=(a<b)?a:b;
        return (min<c)?min:c;
    }
    public int GetUglyNumber_Solution(int index) {
        if(index<=0)
            return 0;
        int[] a=new int[index];
        a[0]=1;
        int cnt1=0;
        int cnt2=0;
        int cnt3=0;
        for(int i=1;i<index;i++){
            int min=min(a[cnt1]*2,a[cnt2]*3,a[cnt3]*5);
            a[i]=min;
            while(a[cnt1]*2<=min)
                cnt1++;
            while(a[cnt2]*3<=min)
                cnt2++;
            while(a[cnt3]*5<=min)
                cnt3++;
        }
        return a[index-1];
    }
}
```

# 题目三十四 第一个只出现一次的字符

## 题目描述

在一个字符串(0<=字符串长度<=10000，全部由字母组成)中找到第一个只出现一次的字符,并返回它的位置, 如果没有则返回 -1（需要区分大小写）.

## 思路

设立一个hashMap，将每个字母出现的次数进行统计。若要找出第一个，就要对string从头开始再次遍历一遍找到其在hashMap中的value值为1，则返回其下标

## AC代码

`c++`版：

```c++

```

`Java`版：

```java
import java.util.HashMap;
    public class Solution {
        public int FirstNotRepeatingChar(String str) {

            int index = -1;
            if (str == null || str == "") {
                return index;
            }
            HashMap<Character, Integer> statisticsMap = new HashMap<Character, Integer>();
            for (int i = 0; i < str.length(); i++) {
                Character tempChar = str.charAt(i);
                if (statisticsMap.get(tempChar) == null) {
                    statisticsMap.put(tempChar, 1);
                } else {
                    int tempCount = statisticsMap.get(tempChar) + 1;
                    statisticsMap.remove(tempChar);
                    statisticsMap.put(tempChar, tempCount);
                }
            }
            for (int i = 0; i < str.length(); i++) {
                Character tempChar1 = str.charAt(i);
                if (statisticsMap.get(tempChar1) == 1) {
                    index = i;
                    break;
                }
            }

            return index;
        }
    }
```

# 题目三十五 数组中的逆序对

## 题目描述

在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组,求出这个数组中的逆序对的总数P。并将P对1000000007取模的结果输出。 即输出P%1000000007

## 思路

利用归并排序或者树状数组，暂时写了个Java的。

## AC代码

`c++`版：

```c++

```

`Java`版：

```java
public class Solution {
    public int InversePairs(int [] array) {
        int len = array.length;
        if(array== null || len <= 0){
            return 0;
        }
        return mergeSort(array, 0, len-1);
    }
    public int mergeSort(int [] array, int start, int end){
        if(start == end)
            return 0;
        int mid = (start + end) / 2;
        int left_count = mergeSort(array, start, mid);
        int right_count = mergeSort(array, mid + 1, end);
        int i = mid, j = end;
        int [] copy = new int[end - start + 1];
        int copy_index = end - start;
        int count = 0;
        while(i >= start && j >= mid + 1){
            if(array[i] > array[j]){
                copy[copy_index--] = array[i--];
                count += j - mid;
                if(count > 1000000007){
                    count %= 1000000007;
                }
            }else{
                copy[copy_index--] = array[j--];
            }
        }
        while(i >= start){
            copy[copy_index--] = array[i--];
        }
        while(j >= mid + 1){
            copy[copy_index--] = array[j--];
        }
        i = 0;
        while(start <= end) {
            array[start++] = copy[i++];
        }
        return (left_count+right_count+count)%1000000007;
    }
}
```

# 题目三十六 两个链表的第一个公共结点

## 题目描述

输入两个链表，找出它们的第一个公共结点。

## 思路

一个`while`循环，当不是共同结点的时候，两个链表都往后面移动

## AC代码

`c++`版：

```c++
/*
struct ListNode {
	int val;
	struct ListNode *next;
	ListNode(int x) :
			val(x), next(NULL) {
	}
};*/
class Solution {
public:
    ListNode* FindFirstCommonNode( ListNode* headA, ListNode* headB) {
        if(headA==NULL || headB==NULL)
            return NULL;
        ListNode *ans1=headA,*ans2=headB;
        while(ans1!=ans2)
        {
            if(ans1==NULL)
                ans1=headB;
            else
                ans1=ans1->next;
            if(ans2==NULL)
                ans2=headA;
            else
                ans2=ans2->next;
        }
        return ans1;
    }
};
```

`Java`版：

```java
/*
public class ListNode {
    int val;
    ListNode next = null;

    ListNode(int val) {
        this.val = val;
    }
}*/
public class Solution {
    public ListNode FindFirstCommonNode(ListNode headA, ListNode headB) {
        if(headA==null || headB==null)
            return null;
        ListNode ans1=headA,ans2=headB;
        while(ans1!=ans2)
        {
            if(ans1==null)
                ans1=headB;
            else
                ans1=ans1.next;
            if(ans2==null)
                ans2=headA;
            else
                ans2=ans2.next;
        }
        return ans1;
    }
}
```

# 题目三十七 数字在排序数组中出现的次数

## 题目描述

统计一个数字在排序数组中出现的次数。

## 思路

一遍循环，因为是有序的数组，所以一直找到第一个大于`k`的数值就跳出循环，`==`的时候记录数量

## AC代码

`c++`版：

```c++
class Solution {
public:
    int GetNumberOfK(vector<int> data ,int k) {
        int len=data.size();
        int ans=0;
        for(int i=0;i<len;i++){
            if(data[i]==k){
                ans++;
            }
            if(data[i]>k)
                break;
        }
        return ans;
    }
};
```

`Java`版：

```java
public class Solution {
    public int GetNumberOfK(int [] array , int k) {
       int len=array.length;
        int ans=0;
        for(int i=0;i<len;i++){
            if(array[i]==k){
                ans++;
            }
            if(array[i]>k)
                break;
        }
        return ans;
    }
}
```

# 题目三十八 二叉树的深度

## 题目描述

输入一棵二叉树，求该树的深度。从根结点到叶结点依次经过的结点（含根、叶结点）形成树的一条路径，最长路径的长度为树的深度。

## 思路

如果一棵树只有一个结点，它的深度为1，如果根节点只有左子树而没有右子树，那么树的深度应该是其左子树的深度+1.同样如果根节点只有右子树而没有左子树，那么树的深度应该是其右子树+1.如果既有左子树又有右子树，那概述的深度就是左、右子树的深度的较大值加1.。

所以我们可以用递归来实现代码

## AC代码

`c++`版：

```c++
/*
struct TreeNode {
	int val;
	struct TreeNode *left;
	struct TreeNode *right;
	TreeNode(int x) :
			val(x), left(NULL), right(NULL) {
	}
};*/
class Solution {
public:
    int ans=0;
    void dfs(TreeNode* root,int height){
        if(root->left){
            dfs(root->left,height+1);
        }
        if(root->right){
            dfs(root->right,height+1);
        }
        if(root->left==NULL && root->right==NULL){
            ans=max(ans,height);
        }
    }
    int TreeDepth(TreeNode* pRoot){
        if(pRoot == NULL)
            return 0;
        dfs(pRoot,1);
        return ans;
    }
};
```

`Java`版：

```java

/**
public class TreeNode {
    int val = 0;
    TreeNode left = null;
    TreeNode right = null;

    public TreeNode(int val) {
        this.val = val;

    }

}
*/
public class Solution {
    public int TreeDepth(TreeNode root) {
        if(root == null)
			return 0;
		int nLeft = TreeDepth(root.left);
		int nRight = TreeDepth(root.right);
		return (nLeft > nRight)?(nLeft+1):(nRight+1);
    }
}
```

# 题目三十九 平衡二叉树

## 题目描述

输入一棵二叉树，判断该二叉树是否是平衡二叉树。

## 思路

首先如果某二叉树中任意结点的左右子树的深度相差不超过1，那么它就是一棵平衡二叉树。

而我们已经做了二叉树的深度，遍历树的每个结点的时候，调用函数TreeDepth得到它的左右子树的深度。如果每个结点的左右子树的深度相差不超过1，按照定义它就是一棵平衡的二叉树

或者呢可以用冒泡排序的思想，我么知道冒泡排序是一个`O(n^2)`的算法，它是每次把一个数排序到最前面保证前面数有序，然后一直到整个数组有序，那么我们只需要循环`k`次就行了在这里面，保证前k个数最小的就满足题意了。

## AC代码

`c++`版：

```c++
class Solution {
public:
    int depth(TreeNode* root){
        if(root==NULL)
            return 0;
        int left=depth(root->left);
        int right=depth(root->right);
        return (left>right)?(left+1):(right+1);
    }
    bool IsBalanced_Solution(TreeNode* root) {
        if(root==NULL)
            return true;
        int left=depth(root->left);
        int right=depth(root->right);
        if(abs(left-right)>1)
            return false;
        bool booleft=IsBalanced_Solution(root->left);
        bool booright=IsBalanced_Solution(root->right);
        return booleft&&booright;
    }
};
```

`Java`版：

```java
public class Solution {
    public boolean IsBalanced_Solution(TreeNode root) {
        if(root ==null)
			return true;
		int left = TreeDepth(root.leftNode);
		int right = TreeDepth(root.rightNode);
		int diff = left - right;
		if(diff > 1 || diff <-1)
			return false;
		return isBalanced(root.leftNode) && isBalanced(root.rightNode);
    }
    public int TreeDepth(TreeNode root) {
        if(root == null)
			return 0;
		int nLeft = TreeDepth(root.left);
		int nRight = TreeDepth(root.right);
		return (nLeft > nRight)?(nLeft+1):(nRight+1);
    }
}
```

# 题目四十 数组中只出现一次的数字

## 题目描述

一个整型数组里除了两个数字之外，其他的数字都出现了两次。请写程序找出这两个只出现一次的数字。

## 思路

首先是异或运算的一个性质：任何一个数字异或它自己都等于 0。也就是说， 如果我们从头到尾依次异或数组中的每一个数字，那么最终的结果刚好是那个只出现一次的数字，因为那些成对出现两次的数字全部在异或中抵消了，同样应用这个思路，分为三步：

- 从头到尾依次异或数组中的每一个数字，那么最终得到的结果就是两个只出现一次的数字的异或结果。因为其他数字都出现了两次，在异或中全部抵消了。由于这两个数字肯定不一样，那么异或的结果肯定不为 0，也就是说在这个结果数字的二进制表示中至少就有一位为 1 。
- 我们在结果数字中找到第一个为 1 的位的位置，记为第 n 位。现在我们以第 n 位是不是 １ 为标准把原数组中的数字分成两个子数组，第一个子数组中每个数字的第 n 位都是 1，而第二个子数组中每个数字的第 n 位都是 0。由于我们分组的标准是数字中的某一位是 1 还是 0 ， 那么出现了两次的数字肯定被分配到同一个子数组。因为两个相同的数字的任意一位都是相同的，我们不可能把两个相同的数字分配到两个子数组中去，于是我们已经把原数组分成了两个子数组。
- 每个子数组都包含一个只出现一次的数字，而其他数字都出现了两次。我们已经知道如何在数组中找出唯一一个只出现一次数字

## AC代码

`c++`版：

```c++
class Solution {
public:
    bool IsBit(int num,int index){
        num=num>>index;
        return (num&1);
    }
    void FindNumsAppearOnce(vector<int> data,int* num1,int *num2) {
        int size=data.size();
        int temp=data[0];
        for(int i=1;i<size;i++)
           temp=temp^data[i];
        if(temp==0)
           return ;
        int index=0;
        while((temp&1)==0){
           temp=temp>>1;
           ++index;
        }
        *num1=*num2=0;
        for(int i=0;i<size;i++){
           if(IsBit(data[i],index))
               *num1^=data[i];
           else
               *num2^=data[i];
        }
    }
};
```

`Java`版：

```java
//num1,num2分别为长度为1的数组。传出参数
//将num1[0],num2[0]设置为返回结果
public class Solution {
    public void FindNumsAppearOnce(int[] array, int num1[], int num2[]) {
        int diff = 0;
        for (int num : array) diff ^= num;
        // 得到最右一位
        diff &= -diff;
        for (int num : array) {
            if ((num & diff) == 0) num1[0] ^= num;
            else num2[0] ^= num;
        }
    }
}
```















































































