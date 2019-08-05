---
title: Foreach原理以及Fail-fast机制
date: 2019-08-05 19:02:58
tags: 
categories: Java入门
---

# Fail-fast机制

它是`Java`集合的一种错误检测机制。当多个线程对集合进行结构上的改变的操作时，有可能会产生`fail-fast`机制。记住是有可能，而不是一定。例如：假设存在两个线程（线程1、线程2），线程1通过`Iterator`在遍历集合`A`中的元素，在某个时候线程2修改了集合`A`的结构（是结构上面的修改，而不是简单的修改集合元素的内容），那么这个时候程序就会抛出 `ConcurrentModificationException` 异常，从而产生`fail-fast`机制。

我们通过`ArrayList`源代码来分析，先看下迭代器的源码

```java

private class Itr implements Iterator<E> {
        int cursor;
        int lastRet = -1;
        int expectedModCount = ArrayList.this.modCount;
 
        public boolean hasNext() {
            return (this.cursor != ArrayList.this.size);
        }
 
        public E next() {
            checkForComodification();
            /** 省略此处代码 */
        }
 
        public void remove() {
            if (this.lastRet < 0)
                throw new IllegalStateException();
            checkForComodification();
            /** 省略此处代码 */
        }
 
        final void checkForComodification() {
            if (ArrayList.this.modCount == this.expectedModCount)
                return;
            throw new ConcurrentModificationException();
        }
    }
```

从上面的源代码我们可以看出，迭代器在调用`next()`、`remove()`方法时都是调用`checkForComodification()`方法，该方法主要就是检测`modCount == expectedModCount ?` 若不等则抛出`ConcurrentModificationException` 异常，从而产生`fail-fast`机制。所以我们要确定什么时候`modCount != expectedModCount`，他们的值在什么时候发生改变的。

我们看源码可以知道`expectedModCount` 是在`Itr`中定义的：`int expectedModCount = ArrayList.this.modCount;`所以他的值是不可能会修改的，所以会变的就是`modCount`，`modCount`是在 `AbstractList` 中定义的，为全局变量。

```java
protected transient int modCount = 0;
```

再看它什么时候会改变，

```java
public boolean add(E paramE) {
        ensureCapacityInternal(this.size + 1);
        /** 省略此处代码 */
    }
 
    private void ensureCapacityInternal(int paramInt) {
        if (this.elementData == EMPTY_ELEMENTDATA)
            paramInt = Math.max(10, paramInt);
        ensureExplicitCapacity(paramInt);
    }
    
    private void ensureExplicitCapacity(int paramInt) {
        this.modCount += 1;    //修改modCount
        /** 省略此处代码 */
    }
    
   public boolean remove(Object paramObject) {
        int i;
        if (paramObject == null)
            for (i = 0; i < this.size; ++i) {
                if (this.elementData[i] != null)
                    continue;
                fastRemove(i);
                return true;
            }
        else
            for (i = 0; i < this.size; ++i) {
                if (!(paramObject.equals(this.elementData[i])))
                    continue;
                fastRemove(i);
                return true;
            }
        return false;
    }
 
    private void fastRemove(int paramInt) {
        this.modCount += 1;   //修改modCount
        /** 省略此处代码 */
    }
 
    public void clear() {
        this.modCount += 1;    //修改modCount
        /** 省略此处代码 */
    }
```

从上面的源代码我们可以看出，`ArrayList`中无论`add`、`remove`、`clear`方法只要是涉及了改变`ArrayList`元素的个数的方法都会导致`modCount`的改变。所以我们这里可以初步判断由于`expectedModCount` 得值与`modCount`的改变不同步，导致两者之间不等从而产生`fail-fast`机制。知道产生`fail-fast`产生的根本原因了，我们可以有如下场景：有两个线程（线程`A`，线程`B`），其中线程`A`负责遍历`list`、线程`B`修改`list`。线程`A`在遍历`list`过程的某个时候（此时`expectedModCount = modCount=N`），线程启动，同时线程`B`增加一个元素，这是`modCount`的值发生改变（`modCount + 1 = N + 1`）。线程`A`继续遍历执行`next`方法时，通告`checkForComodification`方法发现`expectedModCount  = N ` ，而`modCount = N + 1`，两者不等，这时就抛出`ConcurrentModificationException` 异常，从而产生`fail-fast`机制。

## fail-fast解决办法

- 在遍历过程中所有涉及到改变`modCount`值得地方全部加上`synchronized`或者直接使用`Collections.synchronizedList`，这样就可以解决。但是不推荐，因为增删造成的同步锁可能会阻塞遍历操作。
- 使用`CopyOnWriteArrayList`来替换`ArrayList`

## CopyOnWriteArrayList

它是`ArrayList` 的一个线程安全的变体，其中所有可变操作（`add`、`set` 等等）都是通过对底层数组进行一次新的复制来实现的。 该类产生的开销比较大，但是在两种情况下，它非常适合使用。

- 在不能或不想进行同步遍历，但又需要从并发线程中排除冲突时。
- 当遍历操作的数量大大超过可变操作的数量时。

`CopyOnWriterArrayList`的无论是从数据结构、定义都和`ArrayList`一样。它和`ArrayList`一样，同样是实现List接口，底层使用数组实现。在方法上也包含`add`、`remove`、`clear`、`iterator`等方法。

`CopyOnWriterArrayList`根本就不会产生`ConcurrentModificationException`异常，也就是它使用迭代器完全不会产生`fail-fast`机制。以`add`方法为例，它是在于`copy`原来的`array`，再在`copy`数组上进行`add`操作，这样就不会影响`COWIterator`中的数据了，修改完成之后改变原有数据的引用即可。同时这样造成的代价就是产生大量的对象，同时数组的`copy`也是相当有损耗的。

# Foreach

我们知道在`JAVA`中，遍历集合和数组一般有以下三种形式：第一种是普通的`for`循环遍历、第二种是使用迭代器进行遍历，第三种我们一般称之为增强`for`循环`（for each）`。

那么`Foreach`底层是怎么做的，使得它能够进行循环。

我们对以下代码进行反编译：

```java
for (Integer i : list) {
	System.out.println(i);
}
```

反编译后：

```java
Integer i;
for(Iterator iterator = list.iterator(); iterator.hasNext(); System.out.println(i)){
	i = (Integer)iterator.next();        
}
/*
Integer i; 定义一个临时变量i
Iterator iterator = list.iterator(); 获取List的迭代器
iterator.hasNext(); 判断迭代器中是否有未遍历过的元素
i = (Integer)iterator.next(); 获取第一个未遍历的元素，赋值给临时变量i
System.out.println(i) 输出临时变量i的值
如此循环往复，直到遍历完List中的所有元素。
*/
```

通过反编译，我们看到，其实`JAVA`中的增强`for`循环底层是通过迭代器模式来实现的。那么必然就会有`Fail-fast`机制，在使用迭代器遍历元素的时候，在对集合进行删除的时候一定要注意，使用不当有可能发生`ConcurrentModificationException`。

`Iterator` 在工作的时候是不允许被迭代的对象被改变的。但你可以使用` Iterator` 本身的方法 `remove()` 来删除对象，`Iterator.remove() `方法会在删除当前迭代对象的同时维护索引的一致性。

```java
for (Student stu : students) {    
    if (stu.getId() == 2)     
        students.remove(stu);    
}
// 会抛出ConcurrentModificationException异常。
Iterator<Student> stuIter = students.iterator();    
while (stuIter.hasNext()) {    
    Student student = stuIter.next();    
    if (student.getId() == 2)    
        stuIter.remove();/*这里要使用Iterator的remove方法移除当前对象，如果使用List的remove方法，则同样会出现ConcurrentModificationException*/   
}
// 不会抛出ConcurrentModificationException异常
```

