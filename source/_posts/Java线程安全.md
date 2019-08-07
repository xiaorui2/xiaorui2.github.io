---
title: Java线程安全
date: 2019-08-06 23:41:22
tags: 
categories: Java入门
---

# 什么是线程安全

线程安全可以简单理解为一个方法或者一个实例可以在多线程环境中使用而不会出现问题。多说一点就是线程安全就是多线程访问时，采用了加锁机制，当一个线程访问该类的某个数据时，进行保护，其他线程不能进行访问直到该线程读取完，其他线程才可使用。不会出现数据不一致或者数据污染。

线程不安全就是不提供数据访问保护，有可能出现多个线程先后更改数据造成所得到的数据是“脏”数据。

比方说`ArrayList`是非线程安全的，`Vector`是线程安全的；`HashMap`是非线程安全的，`HashVector`是线程安全的；`StringBuilder`是非线程安全的，`StringBuffer`是线程安全的。

## 产生线程不安全的原因

在同一程序中运行多个线程本身不会导致问题，问题在于多个线程访问了相同的资源。如，同一内存区（变量，数组，或对象）、系统（数据库，`web services`等）或文件。实际上，这些问题只有在一或多个线程向这些资源做了写操作时才有可能发生，只要资源没有发生变化,多个线程读取相同的资源就是安全的。

记住引用不是线程安全的，即使一个对象是线程安全的不可变对象，指向这个对象的引用也可能不是线程安全的。

# Java线程安全的类

- 通过`synchronized` 关键字给方法加上内置锁来实现线程安全 
  `Timer`，`TimerTask`，`Vector`，`Stack`，`HashTable`，`StringBuffer`
- 原子类`Atomicxxx`—包装类的线程安全类 
  如`AtomicLong`，`AtomicInteger`等等
- `BlockingQueue` 和`BlockingDeque` 
- `ThreadPoolExecutor`
  `ThreadPoolExecutor`也是使用了`ReentrantLock`显式加锁同步

# 锁的种类和区别

## 公平锁/非公平锁

公平锁是指多个线程按照申请锁的顺序来获取锁。
非公平锁是指多个线程获取锁的顺序并不是按照申请锁的顺序，有可能后申请的线程比先申请的线程优先获取锁。有可能，会造成优先级反转或者饥饿现象。

对于`Java ReentrantLock`而言，通过构造函数指定该锁是否是公平锁，默认是非公平锁。非公平锁的优点在于吞吐量比公平锁大。
对于`Synchronized`而言，也是一种非公平锁。由于其并不像`ReentrantLock`是通过`AQS`的来实现线程调度，所以并没有任何办法使其变成公平锁。

## 可重入锁

可重入锁又名递归锁，是指在同一个线程在外层方法获取锁的时候，在进入内层方法会自动获取锁。

`Java ReentrantLock`以及`Synchronized`都是可重入锁

## 独享锁/共享锁

独享锁是指该锁一次只能被一个线程所持有。共享锁是指该锁可被多个线程所持有。

对于`Java ReentrantLock`而言，其是独享锁。但是对于`Lock`的另一个实现类`ReadWriteLock`，其读锁是共享锁，其写锁是独享锁。`Synchronized`是独享锁。

## 乐观锁/悲观锁

乐观锁与悲观锁不是指具体的什么类型的锁，而是指看待并发同步的角度。

悲观锁认为对于同一个数据的并发操作，一定是会发生修改的，哪怕没有修改，也会认为修改。因此对于同一个数据的并发操作，悲观锁采取加锁的形式。悲观的认为，不加锁的并发操作一定会出问题。

乐观锁则认为对于同一个数据的并发操作，是不会发生修改的。在更新数据的时候，会采用尝试更新，不断重新的方式更新数据。乐观的认为，不加锁的并发操作是没有事情的。

从上面的描述我们可以看出，悲观锁适合写操作非常多的场景，乐观锁适合读操作非常多的场景，不加锁会带来大量的性能提升。悲观锁在`Java`中的使用，就是利用各种锁。乐观锁在`Java`中的使用，是无锁编程，常常采用的是`CAS`算法，典型的例子就是原子类，通过`CAS`自旋实现原子操作的更新。

## 偏向锁/轻量级锁/重量级锁

这三种锁是指锁的状态，并且是针对`Synchronized`。在`Java 5`通过引入锁升级的机制来实现高效`Synchronized`。这三种锁的状态是通过对象监视器在对象头中的字段来表明的。

偏向锁是指一段同步代码一直被一个线程所访问，那么该线程会自动获取锁。降低获取锁的代价。

轻量级锁是指当锁是偏向锁的时候，被另一个线程所访问，偏向锁就会升级为轻量级锁，其他线程会通过自旋的形式尝试获取锁，不会阻塞，提高性能。

重量级锁是指当锁为轻量级锁的时候，另一个线程虽然是自旋，但自旋不会一直持续下去，当自旋一定次数的时候，还没有获取到锁，就会进入阻塞，该锁膨胀为重量级锁。重量级锁会让其他申请的线程进入阻塞，性能降低。

## 自旋锁

在`Java`中，自旋锁是指尝试获取锁的线程不会立即阻塞，而是采用循环的方式去尝试获取锁，这样的好处是减少线程上下文切换的消耗，缺点是循环会消耗`CPU`。

# synchronized

## 	三种应用方式

- 修饰实例方法，作用于当前实例加锁，进入同步代码前要获得当前实例的锁
- 修饰静态方法，作用于当前类对象加锁，进入同步代码前要获得当前类对象的锁
- 修饰代码块，指定加锁对象，对给定对象加锁，进入同步代码库前要获得给定对象的锁。

作用于实例方法的时候，会出现`new`了两个不同的实例对象，这也就意味着存在着两个不同的实例对象锁，这个时候各自的进程使用的就是不同的锁就不能保证线程的安全，这个时候就是用`Synchronized`作用于静态方法，因为锁对象是当前类的`class`对象。

`Java` 虚拟机中的同步(`Synchronization`)基于进入和退出管程(`Monitor`)对象（存在于每个`Java`对象的对象头）实现， 无论是显式同步(有明确的 `monitorenter `和 `monitorexit `指令,即同步代码块)还是隐式同步都是如此。

## synchronized代码块底层原理

同步语句块的实现使用的是`monitorenter` 和 `monitorexit `指令，其中`monitorenter`指令指向同步代码块的开始位置，`monitorexit`指令则指明同步代码块的结束位置，当执行`monitorenter`指令时，当前线程将试图获取 `objectref`(即对象锁) 所对应的 `monitor `的持有权，当 `objectref `的`monitor `的进入计数器为 0，那线程可以成功取得` monitor`，并将计数器值设置为 1，取锁成功。如果当前线程已经拥有 `objectref` 的 `monitor `的持有权，那它可以重入这个 `monitor` (关于重入性稍后会分析)，重入时计数器的值也会加 1。倘若其他线程已经拥有 `objectref` 的 `monitor` 的所有权，那当前线程将被阻塞，直到正在执行线程执行完毕，即`monitorexit`指令被执行，执行线程将释放 `monitor`(锁)并设置计数器值为0 ，其他线程将有机会持有` monitor` 。

## synchronized方法底层原理
方法级的同步是隐式，即无需通过字节码指令来控制的，它实现在方法调用和返回操作之中。`JVM`可以从方法常量池中的方法表结构(`method_info Structure`) 中的` ACC_SYNCHRONIZED` 访问标志区分一个方法是否同步方法。当方法调用时，调用指令将会 检查方法的` ACC_SYNCHRONIZED `访问标志是否被设置，如果设置了，执行线程将先持有`monitor`（虚拟机规范中用的是管程一词）， 然后再执行方法，最后再方法完成(无论是正常完成还是非正常完成)时释放`monitor`。在方法执行期间，执行线程持有了`monitor`，其他任何线程都无法再获得同一个`monitor`。如果一个同步方法执行期间抛 出了异常，并且在方法内部无法处理此异常，那这个同步方法所持有的`monitor`将在异常抛到同步方法之外时自动释放。

## Java虚拟机对synchronized的优化

锁的状态总共有四种，无锁状态、偏向锁、轻量级锁和重量级锁。随着锁的竞争，锁可以从偏向锁升级到轻量级锁，再升级的重量级锁，但是锁的升级是单向的，也就是说只能从低到高升级，不会出现锁的降级，把我们刚才看的锁的进一步看一下在`synchronized`是怎么应用的。

### 偏向锁
偏向锁是`Java 6`之后加入的新锁，它是一种针对加锁操作的优化手段，经过研究发现，在大多数情况下，锁不仅不存在多线程竞争，而且总是由同一线程多次获得，因此为了减少同一线程获取锁(会涉及到一些`CAS`操作,耗时)的代价而引入偏向锁。偏向锁的核心思想是，如果一个线程获得了锁，那么锁就进入偏向模式，此时`Mark Word` 的结构也变为偏向锁结构，当这个线程再次请求锁时，无需再做任何同步操作，即获取锁的过程，这样就省去了大量有关锁申请的操作，从而也就提供程序的性能。

### 轻量级锁

倘若偏向锁失败，虚拟机并不会立即升级为重量级锁，它还会尝试使用一种称为轻量级锁的优化手段。

### 自旋锁

轻量级锁失败后，虚拟机为了避免线程真实地在操作系统层面挂起，还会进行一项称为自旋锁的优化手段。

### 锁消除

消除锁是虚拟机另外一种锁的优化，这种优化更彻底，`Java`虚拟机在`JIT`编译时，通过对运行上下文的扫描，去除不可能存在共享资源竞争的锁，通过这种方式消除没有必要的锁，可以节省毫无意义的请求锁时间

# ReentrantLock

`ReentrantLock`是基于`AQS`实现的，`AQS`的基础又是`CAS`。

## CAS

`CAS`设计到三个操作数：`V` 内存地址存放的实际值；`O `预期的值（旧值）；`N `更新的新值。当`V`和`O`相同时，也就是说旧值和内存中实际的值相同表明该值没有被其他线程更改过，即该旧值`O`就是目前来说最新的值了，自然而然可以将新值`N`赋值给`V`。反之，`V`和`O`不相同，表明该值已经被其他线程改过了则该旧值`O`不是最新版本的值了，所以不能将新值`N`赋给`V`，返回`V`即可。所以多个线程同时访问的时候就只会有一个线程成功。

实例理解一下：比方说在内存地址`V`中，存放着值为10的变量，此时此时线程1想要把变量的值增加1。对线程1来说，旧的预期值`A=10`，要修改的新值`B=11`。在线程1要提交更新之前，另一个线程2抢先一步，把内存地址V中的变量值率先更新成了11。线程1开始提交更新，首先进行`A`和地址`V`的实际值比较，发现`A`不等于`V`的实际值，提交失败。线程1重新获取内存地址`V`的当前值，并重新计算想要修改的新值。此时对线程1来说，`A=11`，`B=12`。这个重新尝试的过程被称为自旋。这一次比较幸运，没有其他线程改变地址`V`的值。线程1进行`Compare`，发现`A`和地址`V`的实际值是相等的。线程1进行`SWAP`，把地址`V`的值替换为`B`，也就是12。

## 	实现原理

`AQS`是基于`FIFO`队列的实现，因此必然存在一个个节点，`Node`就是一个节点。Node里面有：

![](1.png)

`AQS`的变量和方法一般重点掌握`tryAcquire(int arg)`和`tryRelease(int arg)`两个方法，因为一般这两个函数是会被重写的。

`ReentrantLock`类中有三个内部类，`Sync`是另外两个类的父类，`ReentrantLock`的公平锁和非公平锁的实现就是通过`Sync`的两个子类`NonfairSync`和`FairSync`来完成的。默认`ReentrantLock`实现的是非公平锁，非公平锁虽然失去了公平但是获得了更好地吞吐量。

### 非公平锁的`lock`方法：

```java
final void lock() {
// 如果锁没有被任何线程锁定且加锁成功则设定当前线程为锁的拥有者
	if (compareAndSetState(0, 1))
		setExclusiveOwnerThread(Thread.currentThread());
	else
// 否则执行acquire方法传入参数为1	
		acquire(1);
}
```

关键的逻辑在`acquire`也就是当有线程发生竞争的时候：

```java
//首先调用tryAcquire方法来再一次尝试获取锁，如果成功则返回，否则执行acquireQueued方法
public final void acquire(int arg) {
if (!tryAcquire(arg) &&
    acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
    selfInterrupt();
}
 
//非公平锁的获取方法
final boolean nonfairTryAcquire(int acquires) {
    //首先获取当前线程
    final Thread current = Thread.currentThread();
    //获取锁的状态
    int c = getState();
    //如果为0则继续通过原子操作设置state，如果成功则设置获取锁的线程为当前线程并返回成功
    if (c == 0) {
	if (compareAndSetState(0, acquires)) {
	    setExclusiveOwnerThread(current);
	    return true;
	}
    }
    //否则锁已经被某个线程获取到，判断是否为当前线程
    else if (current == getExclusiveOwnerThread()) {
	//如果是当前线程则将state+1，可以看出锁的可重入性就体现在这里
	int nextc = c + acquires;
	if (nextc < 0) // overflow
	    throw new Error("Maximum lock count exceeded");
	//设置状态，并返回成功
	setState(nextc);
	return true;
    }
    //否则该锁已经被其他线程占用，因此后面需要考虑阻塞该线程。
    return false;
}
 
//新增等待线程节点
 private Node addWaiter(Node mode) {
	//使用当前线程构造出新的节点
	Node node = new Node(Thread.currentThread(), mode);
	// Try the fast path of enq; backup to full enq on failure
	//第一次tail肯定为空则走enq(node)入队列
	Node pred = tail;
	if (pred != null) {
	    //非第一次竞争
	    node.prev = pred;
	    if (compareAndSetTail(pred, node)) {
		pred.next = node;
		return node;
	    }
	}
	enq(node);
	return node;
}
 
//入队列操作
private Node enq(final Node node) {
	//乐观等待
	for (;;) {
	    //第一次tail仍然为空
	    Node t = tail;
	    if (t == null) { // Must initialize
	        //当第一次产生竞争的时候初始化虚拟头结点，节省空间
		Node h = new Node(); // Dummy header
		//头结点h的下一个节点指向前面新建的节点
		h.next = node;
		//双向链表
		node.prev = h;
		//原子操作设置头结点，如果成功则尾节点为前面新建的节点，否则循环直到成功，如果同一时刻有其他线程set成功，则可能走else分支
		if (compareAndSetHead(h)) {
		    //返回头结点
		    tail = node;
		    return h;
		}
	    }
	    else {
	        //否则
		node.prev = t;
		if (compareAndSetTail(t, node)) {
		    t.next = node;
		    return t;
		}
	    }
	}
}
```

### 非公平锁的`unlock`方法

走`AQS`的`release`

```java
public void unlock() {
    sync.release(1);
}
```

先调用`Sync`的`tryRelease`尝试释放锁：

```java
protected final boolean tryRelease(int releases) {
    int c = getState() - releases;
    //只有持有锁的线程才能进行释放动作
    if (Thread.currentThread() != getExclusiveOwnerThread())
        throw new IllegalMonitorStateException();
    boolean free = false;
    if (c == 0) {
        free = true;
        //锁被释放，独占线程设置为null
        setExclusiveOwnerThread(null);
    }
    setState(c);
    return free;
}
/*
首先，只有当c==0的时候才会让free=true，这和上面一个线程多次调用lock方法累加state是对应的，调用了多少次的lock()方法自然必须调用同样次数的unlock()方法才行，这样才把一个锁给全部解开。
当一条线程对同一个ReentrantLock全部解锁之后，AQS的state自然就是0了，AbstractOwnableSynchronizer的exclusiveOwnerThread将被设置为null，这样就表示没有线程占有锁，方法返回true。代码继续往下走，上面的release方法的第四行，h不为null成立，h的waitStatus为-1，不等于0也成立，所以走第5行的unparkSuccessor方法：
*/
```

唤醒`node`节点的后继，保证整个`FIFO`队列减少一个`Node`（参考https://www.cnblogs.com/xrq730/p/4979021.html）

```java
private void unparkSuccessor(Node node) {
        int ws = node.waitStatus;
        if (ws < 0)
	   //如果当前节点为SIGNAL则修改为0
            compareAndSetWaitStatus(node, ws, 0); 
	 // 若后续节点为空或已被cancel，则从尾部开始找到队列中第一个waitStatus<=0，即未被cancel的节点
        Node s = node.next;
        if (s == null || s.waitStatus > 0) {
            s = null;
            for (Node t = tail; t != null && t != node; t = t.prev)
                if (t.waitStatus <= 0)
                    s = t;
        }
	//如果找到这么一个节点它的状态<=0则唤醒该节点中的线程
        if (s != null)
            LockSupport.unpark(s.thread);
    }
```

# synchronized和ReentrantLock的异同

同：

- 它们都是加锁方式同步，而且都是阻塞式的同步，也就是说当如果一个线程获得了对象锁，进入了同步块，其他访问该同步块的线程都必须阻塞在同步块外面等待，而进行线程阻塞和唤醒的代价是比较高的

异：

- 对于`Synchronized`来说，它是`java`语言的关键字，是原生语法层面的互斥，需要`jvm`实现。而`ReentrantLock`它是`JDK 1.5`之后提供的`API`层面的互斥锁，需要`lock()`和`unlock()`方法配合`try/finally`语句块来完成。
- 使用`Synchronized`。如果`Thread1`不释放，`Thread2`将一直等待，不能被中断；使用`ReentrantLock`。如果`Thread1`不释放，`Thread2`等待了很长时间以后，可以中断等待，转而去做别的事情。
- `Synchronized`的锁是非公平锁，`ReentrantLock`默认情况下也是非公平锁，但可以通过带布尔值的构造函数要求使用公平锁。
- 锁绑定多个条件，一个`ReentrantLock`对象可以同时绑定对个对象。`Synchronized`中，锁对象的`wait()`和`notify()`或`notifyAll()`方法可以实现一个隐含的条件。但如果要和多于一个的条件关联的时候，就不得不额外添加一个锁。

