---
title: 理解ThreadLocal
date: 2019-08-15 23:15:38
tags: 
categories: Java入门
---

# ThreadLocal介绍

ThreadLocal提供了线程的局部变量，每个线程都可以通过 set() 和 get() 来对这个局部变量进行操作，但不会和其他线程的局部变量进行冲突，实现了线程的数据隔离。简单的来说往 ThreadLocal 中填充的变量是属于当前线程的。设计的目的就是为了能够在当前线程中有属于自己的变量，并不是为了解决并发或者共享变量的问题

# 实现的原理

先看 ThreadLocal 的 set() 方法，因为我们一般使用都是 new 完对象，就往里边 set 对象了

```java
public void set(T value) {
    // 得到当前线程对象
    Thread t = Thread.currentThread();
    // 这里获取ThreadLocalMap
    ThreadLocalMap map = getMap(t);
    // 如果map存在，则将当前线程对象t作为key，要存储的对象作为value存到map里面去
    if (map != null)
      map.set(this, value);
    else
        createMap(t, value);
}
// 看下ThreadLocalMap是啥

static class ThreadLocalMap {
    /**
         * The entries in this hash map extend WeakReference, using
         * its main ref field as the key (which is always a
         * ThreadLocal object).  Note that null keys (i.e. entry.get()
         * == null) mean that the key is no longer referenced, so the
         * entry can be expunged from table.  Such entries are referred to
         * as "stale entries" in the code that follows.
         */
    static class Entry extends WeakReference<ThreadLocal<?>> {
            /** The value associated with this ThreadLocal. */
            Object value;

            Entry(ThreadLocal<?> k, Object v) {
                super(k);
                value = v;
            }
    }
    //....很长
}
// 通过上面我们可以发现的是ThreadLocalMap是ThreadLocal的一个内部类。用Entry类来进行存储我们的值都是存储到这个Map上的，key是当前ThreadLocal对象！

// 如果该Map不存在，则初始化一个,存在的话则从Thread中获取
void createMap(Thread t, T firstValue) {
     	t.threadLocals = new ThreadLocalMap(this, firstValue);
}
ThreadLocalMap getMap(Thread t) {
    return t.threadLocals;
}

// 而Thread又维护了ThreadLocalMap变量
ThreadLocal.ThreadLocalMap threadLocals = null
// ThreadLocalMap是在ThreadLocal中使用内部类来编写的，但对象的引用是在Thread中！于是我们可以总结出：Thread为每个线程维护了ThreadLocalMap这么一个Map，而ThreadLocalMap的key是LocalThread对象本身，value则是要存储的对象

```

那么我们再看 get() 方法

```java
public T get() {
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null) {
        ThreadLocalMap.Entry e = map.getEntry(this);
        if (e != null) {
            @SuppressWarnings("unchecked")
            T result = (T)e.value;
            return result;
        }
    }
    return setInitialValue();
}
```

原理整理：

- 每个 Thread 维护着一个 ThreadLocalMap 的引用
- ThreadLocalMap 是 ThreadLocal 的内部类，用 Entry 来进行存储
- 调用 ThreadLocal 的 set() 方法时，实际上就是往 ThreadLocalMap 设置值，key 是 ThreadLocal 对象，值是传递进来的对象
- 调用 ThreadLocal 的 get() 方法时，实际上就是往 ThreadLocalMap 获取值，key 是 ThreadLocal 对象
- ThreadLocal 本身并不存储值，它只是作为一个 key 来让线程从 ThreadLocalMap 获取 value。

# 对象存放在哪里

我们知道在 Java 中，栈内存归属于单个线程，每个线程都会有一个栈内存，其存储的变量只能在其所属线程中可见，即栈内存可以理解成线程的私有内存。而堆内存中的对象对所有线程可见。堆内存中的对象可以被所有线程访问。

但是ThreadLocal 的实例以及其值不是存放在栈上而是存放在堆上，只是通过一些技巧处理使得修改了可见性。

# ThreadLocal 内存泄漏

由于 ThreadLocalMap 的生命周期跟 Thread 一样长，如果没有手动删除对应 key 就会导致内存泄漏，而不是因为弱引用。想要避免内存泄露就要手动 remove() 掉！

# 使用的点

管理数据库 Connection ，为了方便操作写了一个简单数据库连接池，需要数据库连接池的理由也很简单，频繁创建和关闭 Connection 是一件非常耗费资源的操作，因此需要创建数据库连接池～

数据库连接池的连接怎么管理呢？我们交由 ThreadLocal 来进行管理。为什么交给它来管理呢？ThreadLocal能够实现当前线程的操作都是用同一个Connection，保证了事务！

# ThreadLocal是如何做到为每一个线程维护变量的副本的呢

在ThreadLocal类中有一个Map，用于存储每一个线程的变量副本，Map中元素的键为线程对象，而值对应线程的变量副本。

# ThreadLocal 和同步机制的区别

在同步机制中，通过对象的锁机制保证同一时间只有一个线程访问变量。这时该变量是多个线程共享的，使用同步机制要求程序缜密地分析什么时候对变量进行读写，什么时候需要锁定某个对象，什么时候释放对象锁等繁杂的问题，程序设计和编写难度相对较大。 

而 ThreadLocal 则从另一个角度来解决多线程的并发访问。ThreadLocal 为每一个线程提供一个独立的变量副本，从而隔离了多个线程对访问数据的冲突。因为每一个线程都拥有自己的变量副本，从而也就没有必要对该变量进行同步了。ThreadLocal 提供了线程安全的对象封装，在编写多线程代码时，可以把不安全的变量封装进ThreadLocal。 

由于 ThreadLocal 中可以持有任何类型的对象，低版本 JDK 所提供的 get( ) 返回的是 Object 对象，需要强制类型转换。但 JDK 5.0 通过泛型很好的解决了这个问题，在一定程度上简化 ThreadLocal 的使用，代码清单9-2就使用了 JDK 5.0 新的 ThreadLocal<T> 版本

synchronized 关键字也用来解决多线程环境下访问变量的问题，这两者的区别在于 ThreadLocal 是用空间换取时间，synchronized 关键字是用时间换空间。

