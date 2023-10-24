---
title: 死磕juc（二）java的“锁”事（2）
date: 2022-08-15 12:43:29.035
author: 'yuanshuai'
cover: 'https://aabb-2023.oss-cn-beijing.aliyuncs.com/hqdefault.jpg'
theme: 'light'
tags: 
- juc
- 并发
---

# java的”锁“事（2）

## 一、公平锁和非公平锁

### 1.1 从ReentrantLock卖票编码演示公平和非公平现象

```java
class Ticket
{
    private int number = 30;
    ReentrantLock lock = new ReentrantLock(); // 默认 ReentrantLock lock = new ReentrantLock(false); 为非公平锁（抢占式）

    public void sale()
    {
        lock.lock();
        try
        {
            if(number > 0)
            {
                System.out.println(Thread.currentThread().getName()+"卖出第：\t"+(number--)+"\t 还剩下:"+number);
            }
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }
}


public class SaleTicketDemo
{
    public static void main(String[] args)
    {
        Ticket ticket = new Ticket();

        new Thread(() -> { for (int i = 0; i <35; i++)  ticket.sale(); },"a").start();
        new Thread(() -> { for (int i = 0; i <35; i++)  ticket.sale(); },"b").start();
        new Thread(() -> { for (int i = 0; i <35; i++)  ticket.sale(); },"c").start();
    }
}
```

运行下

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151240702.png)

> ReentrantLock lock = new ReentrantLock(); // 默认 ReentrantLock lock = new ReentrantLock(false); 为非公平锁（抢占式）

可以看到三个线程a,b,c，同时卖票，线程默认抢占式的，哪个线程比较猛（上图c线程比较猛），就可以获得更多资源。

- 但是可能会导致其他抢不到的线程饥饿

我们改为ReentrantLock lock = new ReentrantLock(true);，进行测试，（为了明显，我们设置5个线程abcde）

```java
class Ticket
{
    private int number = 50;

    private Lock lock = new ReentrantLock(true); //默认用的是非公平锁，分配的平均一点，=--》公平一点
    public void sale()
    {
        lock.lock();
        try
        {
            if(number > 0)
            {
                System.out.println(Thread.currentThread().getName()+"\t 卖出第: "+(number--)+"\t 还剩下: "+number);
            }
        }finally {
            lock.unlock();
        }
    }

}

public class SaleTicketDemo
{
    public static void main(String[] args)
    {
        Ticket ticket = new Ticket();

        new Thread(() -> { for (int i = 1; i <=55; i++) ticket.sale(); },"a").start();
        new Thread(() -> { for (int i = 1; i <=55; i++) ticket.sale(); },"b").start();
        new Thread(() -> { for (int i = 1; i <=55; i++) ticket.sale(); },"c").start();
        new Thread(() -> { for (int i = 1; i <=55; i++) ticket.sale(); },"d").start();
        new Thread(() -> { for (int i = 1; i <=55; i++) ticket.sale(); },"e").start();
    }
}
```

运行下：

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151241338.png)

可以看到，这次就分配比较均匀点，达到雨露均沾的效果。

### 1.2 何为公平锁/非公平锁?

> 生活中，排队讲求先来后到视为公平。程序中的公平性也是符合请求锁的绝对时间的，其实就是 FIFO，否则视为不公平

#### 1.2.1 看一眼源码

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151241659.png)

> 公平锁维护了一个队列

按序排队公平锁，就是判断同步队列是否还有先驱节点的存在(我前面还有人吗?)，如果没有先驱节点才能获取锁；

先占先得非公平锁，是不管这个事的，只要能抢获到同步状态就可以

#### 1.2.2 为什么会有公平锁/非公平锁的设计为什么默认非公平？

> 恢复挂起的线程到真正锁的获取还是有时间差的，从开发人员来看这个时间微乎其微，但是从CPU的角度来看，这个时间差存在的还是很明显的。所以非公平
>
> 锁能更充分的利用CPU 的时间片，尽量减少 CPU 空闲状态时间。
>
> 使用多线程很重要的考量点是线程切换的开销，当采用非公平锁时，当1个线程请求锁获取同步状态，然后释放同步状态，因为不需要考虑是否还有前驱节
>
> 点，所以刚释放锁的线程在此刻再次获取同步状态的概率就变得非常大，所以就减少了线程的开销。

#### 1.2.3 使⽤公平锁会有什么问题

> 公平锁保证了排队的公平性，非公平锁霸气的忽视这个规则，所以就有可能导致排队的长时间在排队，也没有机会获取到锁，
>
> 这就是传说中的 “锁饥饿”

#### 1.2.4 什么时候用公平？什么时候用非公平？

> 如果为了更高的吞吐量，很显然非公平锁是比较合适的，因为节省很多线程切换时间，吞吐量自然就上去了；
>
> 否则那就用公平锁，大家公平使用。

### 1.3 看一眼AQS

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151241434.png)

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151241191.png)

## 二、可重入锁(又名递归锁)

### 2.1 什么是可重入锁

> 可重入锁又名递归锁
>
> 是指在同一个线程在外层方法获取锁的时候，再进入该线程的内层方法会自动获取锁(前提，锁对象得是同一个对象)，不会因为之前已经获取过还没释放而阻
>
> 塞。
>
> 如果是1个有 synchronized 修饰的递归调用方法，程序第2次进入被自己阻塞了岂不是天大的笑话，出现了作茧自缚。
>
> 所以Java中ReentrantLock和synchronized都是可重入锁，可重入锁的一个优点是可一定程度避免死锁。
>
> 进入同步域（即同步代码块/方法或显式锁锁定的代码）
>
> 一个线程中的多个流程可以获取同一把锁，持有这把同步锁可以再次进入。
>
> 自己可以获取自己的内部锁

### 2.2 可重入锁种类

#### 2.2.1 隐式锁

>（即synchronized关键字使用的锁）默认是可重入锁
>
>指的是可重复可递归调用的锁，在外层使用锁之后，在内层仍然可以使用，并且不发生死锁，这样的锁就叫做可重入锁。
>
>简单的来说就是：在一个synchronized修饰的方法或代码块的内部调用本类的其他synchronized修饰的方法或代码块时，是永远可以得到锁的
>
>与可重入锁相反，不可重入锁不可递归调用，递归调用就发生死锁。

- 同步块

```java
public class ReEntryLockDemo
{
    public static void main(String[] args)
    {
        final Object objectLockA = new Object();

        new Thread(() -> {
            synchronized (objectLockA)
            {
                System.out.println("-----外层调用");
                synchronized (objectLockA)
                {
                    System.out.println("-----中层调用");
                    synchronized (objectLockA)
                    {
                        System.out.println("-----内层调用");
                    }
                }
            }
        },"a").start();
    }
}
```

执行下

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151241821.png)

- 同步方法

```java
/**
 * 在一个Synchronized修饰的方法或代码块的内部调用本类的其他Synchronized修饰的方法或代码块时，是永远可以得到锁的
 */
public class ReEntryLockDemo
{
    public synchronized void m1()
    {
        System.out.println("-----m1");
        m2();
    }
    public synchronized void m2()
    {
        System.out.println("-----m2");
        m3();
    }
    public synchronized void m3()
    {
        System.out.println("-----m3");
    }

    public static void main(String[] args)
    {
        ReEntryLockDemo reEntryLockDemo = new ReEntryLockDemo();

        reEntryLockDemo.m1();
    }
}
```

执行下

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151241931.png)

> 可以看到，无论是同步块还是同步方法，同一把锁，畅通无阻。

##### 2.2.1.1 Synchronized的重入的实现机理

> 每个锁对象拥有一个锁计数器和一个指向持有该锁的线程的指针。
>
> 当执行monitorenter时，如果目标锁对象的计数器为零，那么说明它没有被其他线程所持有，Java虚拟机会将该锁对象的持有线程设置为当前线程，并且将
>
> 其计数器加1。
>
> 在目标锁对象的计数器不为零的情况下，如果锁对象的持有线程是当前线程，那么 Java 虚拟机可以将其计数器加1，否则需要等待，直至持有线程释放该锁。
>
> 当执行monitorexit时，Java虚拟机则需将锁对象的计数器减1。计数器为零代表锁已被释放。

#### 2.2.2 显式锁

> （即Lock）也有ReentrantLock这样的可重入锁。

```java
public class ReEntryLockDemo
{
    static Lock lock = new ReentrantLock();

    public static void main(String[] args)
    {
        new Thread(() -> {
            lock.lock();
            try
            {
                System.out.println("----外层调用lock");
                lock.lock();
                try
                {
                    System.out.println("----内层调用lock");
                }finally {
                    // 这里故意注释，实现加锁次数和释放次数不一样
                    // 由于加锁次数和释放次数不一样，第二个线程始终无法获取到锁，导致一直在等待。
                    lock.unlock(); // 正常情况，加锁几次就要解锁几次
                }
            }finally {
                lock.unlock();
            }
        },"a").start();

        new Thread(() -> {
            lock.lock();
            try
            {
                System.out.println("b thread----外层调用lock");
            }finally {
                lock.unlock();
            }
        },"b").start();

    }
}
```

执行下

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151241106.png)

## 三、死锁及排查

### 3.1 什么是死锁

> 死锁是指两个或两个以上的线程在执行过程中,因争夺资源而造成的一种`互相等待的现象`,若无外力干涉那它们都将无法推进下去，如果系统资源充足，进程的资
>
> 源请求都能够得到满足，死锁出现的可能性就很低，否则就会因争夺有限的资源而陷入死锁。

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151242867.png)

### 3.2 产生死锁的原因

- 系统资源不足
- 进程运行推进的顺序不合适
- 资源分配不当

### 3.3 一个死锁case

```java
public class DeadLockDemo
{
    public static void main(String[] args)
    {
        final Object objectLockA = new Object();
        final Object objectLockB = new Object();

        new Thread(() -> {
            synchronized (objectLockA)
            {
                System.out.println(Thread.currentThread().getName()+"\t"+"自己持有A，希望获得B");
                //暂停几秒钟线程
                try { TimeUnit.SECONDS.sleep(1); } catch (InterruptedException e) { e.printStackTrace(); }
                synchronized (objectLockB)
                {
                    System.out.println(Thread.currentThread().getName()+"\t"+"A-------已经获得B");
                }
            }
        },"A").start();

        new Thread(() -> {
            synchronized (objectLockB)
            {
                System.out.println(Thread.currentThread().getName()+"\t"+"自己持有B，希望获得A");
                //暂停几秒钟线程
                try { TimeUnit.SECONDS.sleep(1); } catch (InterruptedException e) { e.printStackTrace(); }
                synchronized (objectLockA)
                {
                    System.out.println(Thread.currentThread().getName()+"\t"+"B-------已经获得A");
                }
            }
        },"B").start();

    }
}
```

运行下

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151242609.png)

可以看到，两个线程全部阻塞，互相等待，产生死锁。

### 3.4 如何排查死锁

- 纯命令
  - jps -l
  - jstack 进程编号
- 图形化
  - jconsole

这里没case，=.= 

