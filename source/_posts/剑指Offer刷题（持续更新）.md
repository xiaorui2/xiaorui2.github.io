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







































































