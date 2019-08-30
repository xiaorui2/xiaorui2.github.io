---
title: PDD提前批二面
date: 2019-08-21 17:03:09
tags: 面经
categories: 面经
---

时长58分钟

说一下多态的底层的原理？说了一下编译时多态和运行时多态以及JVM调用invokestatic方法然后调用动态分派的过程，通过栈帧的信息去找到被调用方法的具体实现，然后使用这个具体实现的直接引用完成方法调用。

然后问我它是怎么找到对象实际类的？我不知道啊说了一下反射？他说反射太慢了，那我就不知道了

ArrayList 和 Linklist 的区别？大致都说了一下，扯到 ArrayList 线程不安全，我是个智障忘了 Linklist 线程安全不安全了。就说我还没注意到这个，跳过。

详细说一下 Hashmap 的 put 过程

插入链表的时候是前插还是后插？ 我还真没注意，就说没注意，猜测是后插。

HashMap 和 TreeMap 的区别

为何要用红黑树？ 说了一下插入删除查询的时间复杂度的原因

那为什么不直接用红黑树？说了一下小于8个时候查询什么的O(n)就很优秀了，实现红黑树又比较复杂。

他说这个跟你没关系啊，实现都是底层的事情

你为什么一直说个人理解？  我说有的问题我也不能确定我的回答就是正确的，只能通过我已经学过的东西和看过的东西来去确定

写道算法题：问我写 Java 的为什么笔试都是 c++ 写的

 给定一个字符串，里面只有数字(0 ~ 9)、字母(a ~ z，A ～ Z)，小数点(.)，在这个字符串中找出一个最大的合法数字连续子串

123.456 -> 456
123.789.456 -> 789.456
123abc789.4mk56.1cde23 -> 789.4

123.456.789->789

二十分钟写完： 

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <cmath>
#include <algorithm>
#include <vector>
#include <queue>
#include <stack>
#include <set>
#include <map>
#include <sstream>
using namespace std;
typedef long long LL;
const int maxn=1005;
int n,m,t;
int a[maxn];
int main(){
    string s;
    cin>>s;
    int len=s.length();
    double ans=0;
    double temp1=0;
    double temp2=0;
    int i=0;
    int flag=0;
    while(i<len){
        if(i-1>=0 && s[i-1] == '.')
            flag=1;
        while(s[i]>='0' && s[i]<='9'){
            temp1=temp1*10+s[i]-'0';
            i++;
        }
        cout<<temp1<<endl;
        if(flag){
            if(temp1 < temp2){
                while(temp1>1)
                    temp1/=10;
                cout<<temp1<<endl;
                ans=max(ans,temp2+temp1);
                temp2=temp1;
                temp1=0;
            }
            else{
                ans=max(ans,temp1);
                temp2=temp1;
                temp1=0;
            }
        }
        else{
            ans=max(ans,temp1);
            temp2=temp1;
            temp1=0;
        }
        i++;

    }
    cout<<ans<<endl;
    return 0;
}
```

写的稍微有点点问题，说了一下时间复杂度我说 O(n)，他说你这个里面不是又套了一重循环吗，为什么不是 O(n^2) 的

本来说今天就到这结束了，看我没说话又问了一点。

问了一下 c++ 的模板？  我心里我没用过啊，然后说了一下

linkhashmap 应用场景 我没用过，因为知道是根据 key 插入有序，说了一下先来后到的场景

深拷贝？ 我不清楚说了一下拷贝的概念

问了一下 Java 是引用传递还是值传递

问了内存泄漏的场景，说得他一脸疑惑

反问：希望给点建议后续继续提升，多了解一下JVM，多敲点代码。就结束了。

凉凉