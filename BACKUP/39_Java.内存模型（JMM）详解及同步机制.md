# [Java 内存模型（JMM）详解及同步机制](https://github.com/humyna/gitblog/issues/39)

## 一、Java 内存模型（JMM）概述

Java 内存模型（Java Memory Model, JMM）是 Java 虚拟机规范的一部分，它定义了多线程程序中变量的访问规则，即在一个线程中对共享变量的写入何时对另一个线程可见。JMM 主要解决三个问题：**原子性**、**可见性**和**有序性**。

### 1. 原子性
原子性指的是一个操作是不可分割的，不能被中断，要么全部执行成功，要么全部执行失败。Java 提供了一些基本数据类型的读写操作具有原子性，如 `int`、`boolean` 等。

### 2. 可见性
可见性指的是当一个线程修改了某个共享变量的值，其他线程能够立即看到这个修改。JMM 通过 **volatile** 关键字、锁机制等来保证可见性。

### 3. 有序性
有序性指的是程序执行的顺序按照代码编写的顺序进行。在单线程环境下，所有操作都是有序的；但在多线程环境下，由于编译器优化和 CPU 指令重排序，可能会导致代码执行顺序与预期不一致。JMM 提供了 **happens-before** 原则来保证有序性。

## 二、同步工具及其实现机制

### 1. volatile
`volatile` 是一种轻量级的同步机制，用于修饰变量。当一个变量被声明为 `volatile` 时，它保证：
- 对该变量的写操作对所有线程立即可见。
- 禁止指令重排序优化。

#### 实现 happens-before 原则
- 对一个 `volatile` 变量的写操作 happens-before 后续对同一变量的读操作。这意味着，如果一个线程写入了 `volatile` 变量，那么其他任何读取这个 `volatile` 变量的线程都能看到这个最新值。

```java
public class VolatileExample {
    private volatile boolean flag = false;

    public void writer() {
        flag = true; // 写操作，对flag赋值
    }

    public void reader() {
        if (flag) { // 读操作，读取flag值
            // do something
        }
    }
}
```

在上述例子中，当 `writer()` 方法中的 `flag = true;` 执行后，任何调用 `reader()` 方法并检查 `flag` 的线程都将看到最新值，这符合 happens-before 原则。

### 2. synchronized
`synchronized` 是一种重量级锁机制，用于修饰方法或代码块。它保证：
- 同一时刻只有一个线程可以进入 `synchronized` 修饰的方法或代码块。
- 保证进入 `synchronized` 方法或代码块之前对共享变量所做的修改对后续进入该方法或代码块的其他线程可见。

#### 实现 happens-before 原则
- 对一个锁对象解锁 unlock 操作 happens-before 随后的加锁 lock 操作。这意味着如果一个线程释放了锁，那么接下来获取这把锁的任何其他线程都能看到前面持有这把锁期间所做的一切更改。

```java
public class SynchronizedExample {
    private int count = 0;

    public synchronized void increment() { // 加锁 lock 操作
        count++;
    }

    public synchronized int getCount() { // 加锁 lock 操作
        return count;
    }
}
```

在上述例子中，当一个线程调用 `increment()` 方法并退出时，对共享变量 `count` 的修改对于随后调用 `getCount()` 方法并获取相同锁对象（即实例对象）的任何其他线程都是可见的，这符合 happens-before 原则。

### 3. Lock 和 ReentrantLock
`Lock` 接口提供了比 `synchronized` 更加灵活和高级的锁机制，其中最常用的是 `ReentrantLock`。它提供了显式加锁和解锁的方法，并且支持公平锁和非公平锁等特性。

#### 实现 happens-before 原则
- 与 synchronized 类似，对 Lock 对象 unlock 操作 happens-before 随后的 lock 操作。这意味着如果一个线程释放了 Lock，那么接下来获取这把 Lock 的任何其他线程都能看到前面持有这把 Lock 时所做的一切更改。

```java
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class LockExample {
    private final Lock lock = new ReentrantLock();
    private int count = 0;

    public void increment() {
        lock.lock(); // 加锁 lock 操作 
        try {
            count++;
        } finally {
            lock.unlock(); // 解锁 unlock 操作 
        }
    }

    public int getCount() {
        lock.lock(); // 加锁 lock 操作 
        try {
            return count;
        } finally {
            lock.unlock(); // 解锁 unlock 操作 
        }
    }
}
```

在上述例子中，当一个线程调用 `increment()` 方法并退出时，对共享变量 `count` 的修改对于随后调用并获取相同 Lock 对象（即实例对象）的任何其他线程都是可见的，这符合 happens-before 原则。

### 4. Atomic 类
Java 提供了一些原子类，如 `AtomicInteger`, `AtomicLong`, 和 `AtomicReference`, 它们通过 CAS（Compare-And-Swap）操作实现无锁算法，保证高效地进行原子操作，同时也满足 JMM 的要求。

#### 实现 happens-before 原则

- 在使用 Atomic 类进行更新时，每次更新都会发生内存屏障，从而确保每次更新后的结果对于所有读取者都是最新且一致的。
  
```java
import java.util.concurrent.atomic.AtomicInteger;

public class AtomicExample {
    private final AtomicInteger count = new AtomicInteger(0);

    public void increment() {
        count.incrementAndGet(); // CAS 更新操作 
    }

    public int getCount() {
        return count.get(); // 获取最新值 
    }
}
```

在上述例子中，每次调用 `.incrementAndGet()` 都会确保对共享变量的新值是立即对所有读取者可见且一致，这符合 JMM 中关于原子的要求以及 happen-before 原则。


## 三、happens-before 原则

happens-before 是 JMM 中定义的一种偏序关系，用于描述两个操作之间内存可见性的关系。如果一个操作 happens-before 另一个操作，那么第一个操作产生结果对于第二个操作是可见，并且第一个排在第二个之前执行。以下是几种常见规则：

1. 程序次序规则：在同一条线内，按照程序代码顺序，前面的 happen before 后面的。
2. 锁定规则：unlock 必须 happen before 相应lock。
3. volatile 域规则：对 volatile 域写入必须 happen before 随后读取。
4. start 和 join：start 必须 happen before 新启动 run 中动作；join 成功返回前必须 wait 所有动作完成。
5. Transitivity: If A hb B and B hb C, then A hb C.

## 四、总结

Java 内存模型（JMM）通过定义一系列规范来确保多环境下的数据一致正确，同时提供一些同步工具如 volatile, synchronized, Lock, 和 atomic classes 帮助开发者实现安全高效地并发编程。此外，通过理解应用好 JMM 的 happen原则，可以设计出健壮可靠并发程序。这些知识构建高性能、多应用至关重要。

by AI