---
title: 死磕juc（三）LockSupport与线程中断
date: 2022-08-15 14:02:41.606
author:
  name: "yuanshuai"
  link: "https://cloud.tencent.com/developer/user/8180692"
  email: "shuaiyuan1122@gmail.com"
  avatar: "https://aabb-2023.oss-cn-beijing.aliyuncs.com/hjscijg3uw.png"
tags: 
- juc
- LockSupport
---

# LockSupport与线程中断

## 一、线程中断机制

### 1.1 什么是中断

首先

> 一个线程不应该由其他线程来强制中断或停止，而是应该由线程自己自行停止。
>
> 所以，Thread.stop, Thread.suspend, Thread.resume 都已经被废弃了。

其次

> 在Java中没有办法立即停止一条线程，然而停止线程却显得尤为重要，如取消一个耗时操作。
>
> 因此，Java提供了一种用于停止线程的机制——中断。
>
> 中断只是一种协作机制，Java没有给中断增加任何语法，中断的过程完全需要程序员自己实现。
>
> 若要中断一个线程，你需要手动调用该线程的interrupt方法，该方法也仅仅是将线程对象的中断标识设成true；
>
> 接着你需要自己写代码不断地检测当前线程的标识位，如果为true，表示别的线程要求这条线程中断，
>
> 此时究竟该做什么需要你自己写代码实现。
>
> 每个线程对象中都有一个标识，用于表示线程是否被中断；该标识位为true表示中断，为false表示未中断；
>
> 通过调用线程对象的interrupt方法将该线程的标识位设为true；可以在别的线程中调用，也可以在自己的线程中调用。

### 1.2 中断的相关API方法

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151359635.png)

- public void interrupt()	

> 实例方法，
>
> 实例方法interrupt()仅仅是设置线程的中断状态为true，不会停止线程

- public static boolean interrupted()	

> 静态方法，Thread.interrupted();  
>
> 判断线程是否被中断，并清除当前中断状态
>
> 这个方法做了两件事：
>
> 1 返回当前线程的中断状态
>
> 2 将当前线程的中断状态设为false
>
> 这个方法有点不好理解，因为连续调用两次的结果可能不一样。

- public boolean isInterrupted()	

> 实例方法，
>
> 判断当前线程是否被中断（通过检查中断标志位）

### 1.3 如何使用中断标识停止线程？

在需要中断的线程中不断监听中断状态，一旦发生中断，就执行相应的中断处理业务逻辑。

- 修改状态
- 停止程序的运行

#### 1.3.1 方法

- 通过一个volatile变量实现

```java
public class InterruptDemo{
private static volatile boolean isStop = false;

public static void main(String[] args){
    new Thread(() -> {
        while(true){
            if(isStop){
                System.out.println(Thread.currentThread().getName()+"线程------isStop = true,自己退出了");
                break;
            }
            System.out.println("-------hello interrupt");
        }
    },"t1").start();

    //暂停几秒钟线程
    try { TimeUnit.SECONDS.sleep(1); } catch (InterruptedException e) { e.printStackTrace(); }
    isStop = true;
}

}
```

运行下

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151359143.png)

- 通过AtomicBoolean

```java
public class StopThreadDemo
{
    private final static AtomicBoolean atomicBoolean = new AtomicBoolean(true);

    public static void main(String[] args){
        Thread t1 = new Thread(() -> {
            while(atomicBoolean.get()){
                try { TimeUnit.MILLISECONDS.sleep(500); } catch (InterruptedException e) { e.printStackTrace(); }
                System.out.println("-----hello");
            }
        }, "t1");
        t1.start();

        try { TimeUnit.SECONDS.sleep(3); } catch (InterruptedException e) { e.printStackTrace(); }

        atomicBoolean.set(false);
    }
}
```

运行下

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151400613.png)

- 通过Thread类自带的中断api方法实现

> 实例方法interrupt()，没有返回值

`调用interrupt()方法仅仅是在当前线程中打了一个停止的标记，并不是真正立刻停止线程。`

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151400511.png)

看一眼源码

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151400111.png)

在这个方法中，如果被wait()、等等方法阻塞，就会清除当前线程的阻塞状态，然后抛出一个异常。

（我猜就是怕死锁，宁愿抛出异常也要走下去）

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151400311.png)

而interrupt()调用放入是native方法（原子性）

`实例方法isInterrupted，返回布尔值`

> 获取中断标志位的当前值是什么，判断当前线程是否被中断（通过检查中断标志位），默认是false

看一眼源码

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151400952.png)

也是个native

code

```java
public class InterruptDemo
{
    public static void main(String[] args)
    {
        Thread t1 = new Thread(() -> {
            while(true)
            {
                if(Thread.currentThread().isInterrupted())
                {
                    System.out.println("-----t1 线程被中断了，break，程序结束");
                    break;
                }
                System.out.println("-----hello");
            }
        }, "t1");
        t1.start();

        System.out.println("**************"+t1.isInterrupted());
        //暂停5毫秒
        try { TimeUnit.MILLISECONDS.sleep(5); } catch (InterruptedException e) { e.printStackTrace(); }
        t1.interrupt();
        System.out.println("**************"+t1.isInterrupted());
    }
}
```

运行下

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151400593.png)

#### 1.3.2 当前线程的中断标识为true，是不是就立刻停止？

> 具体来说，当对一个线程，调用 interrupt() 时：
>
> ①  如果线程处于正常活动状态，那么会将该线程的中断标志设置为 true，仅此而已。
>
> 被设置中断标志的线程将继续正常运行，不受影响。所以， interrupt() 并不能真正的中断线程，需要被调用的线程自己进行配合才行。
>
> ②  如果线程处于被阻塞状态（例如处于sleep, wait, join 等状态），在别的线程中调用当前线程对象的interrupt方法，那么线程将立即退出被阻塞状态，并抛出一个InterruptedException异常。

```java
public class InterruptDemo2
{
    public static void main(String[] args) throws InterruptedException
    {
        Thread t1 = new Thread(() -> {
            for (int i=0;i<300;i++) {
                System.out.println("-------"+i);
            }
            System.out.println("after t1.interrupt()--第2次---: "+Thread.currentThread().isInterrupted());
        },"t1");
        t1.start();

        System.out.println("before t1.interrupt()----: "+t1.isInterrupted());
        //实例方法interrupt()仅仅是设置线程的中断状态位设置为true，不会停止线程
        t1.interrupt();
        //活动状态,t1线程还在执行中
        try { TimeUnit.MILLISECONDS.sleep(3); } catch (InterruptedException e) { e.printStackTrace(); }
        System.out.println("after t1.interrupt()--第1次---: "+t1.isInterrupted());
        //非活动状态,t1线程不在执行中，已经结束执行了。
        try { TimeUnit.MILLISECONDS.sleep(3000); } catch (InterruptedException e) { e.printStackTrace(); }
        System.out.println("after t1.interrupt()--第3次---: "+t1.isInterrupted());
    }
}
```

运行下

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151400158.png)

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151400158.png)

结论

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151400604.png)

中断只是一种协同机制，修改中断标识位仅此而已，不是立刻stop打断

#### 1.3.3 静态方法Thread.interrupted()

code

```java
public class InterruptDemo
{

    public static void main(String[] args) throws InterruptedException
    {
        System.out.println(Thread.currentThread().getName()+"---"+Thread.interrupted());
        System.out.println(Thread.currentThread().getName()+"---"+Thread.interrupted());
        System.out.println("111111");
        Thread.currentThread().interrupt();
        System.out.println("222222");
        System.out.println(Thread.currentThread().getName()+"---"+Thread.interrupted());
        System.out.println(Thread.currentThread().getName()+"---"+Thread.interrupted());
    }
}
```

静态方法，Thread.interrupted();  

> 判断线程是否被中断，并清除当前中断状态，类似i++
>
> 这个方法做了两件事：
>
> 1 返回当前线程的中断状态
>
> 2 将当前线程的中断状态设为false
>
> 这个方法有点不好理解，因为连续调用两次的结果可能不一样。

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151401436.png)

都会返回中断状态，两者对比

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151401557.png)

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151401658.png)

方法的注释也清晰的表达了“中断状态将会根据传入的ClearInterrupted参数值确定是否重置”。

所以，

静态方法interrupted将会清除中断状态（传入的参数ClearInterrupted为true），

实例方法isInterrupted则不会（传入的参数ClearInterrupted为false）

### 1.4 总结

线程中断相关的方法：

- interrupt()方法是一个实例方法

> 它通知目标线程中断，也就是设置目标线程的中断标志位为true，中断标志位表示当前线程已经被中断了。

- isInterrupted()方法也是一个实例方法

> 它判断当前线程是否被中断（通过检查中断标志位）并获取中断标志

- Thread类的静态方法interrupted()

> 返回当前线程的中断状态(boolean类型)且将当前线程的中断状态设为false，此方法调用之后会清除当前线程的中断标志位的状态（将中断标志置为false了），返回当前值并清零置false

## 二、LockSupport是什么

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151401338.png)

LockSupport是用来创建锁和其他同步类的基本线程阻塞原语。

LockSupport中的park() 和 unpark() 的作用分别是阻塞线程和解除阻塞线程

## 三、线程等待唤醒机制

### 3.1 三种让线程等待和唤醒的方法

- 方式1：使用Object中的wait()方法让线程等待，使用Object中的notify()方法唤醒线程
- 方式2：使用JUC包中Condition的await()方法让线程等待，使用signal()方法唤醒线程
- 方式3：LockSupport类可以阻塞当前线程以及唤醒指定被阻塞的线程

### 3.2 Object类中的wait和notify方法实现线程等待和唤醒

code（正常）

```java
/**
 * 要求：t1线程等待3秒钟，3秒钟后t2线程唤醒t1线程继续工作
 *
 * 1 正常程序演示
 *
 */
public class LockSupportDemo
{
    public static void main(String[] args)//main方法，主线程一切程序入口
    {
        Object objectLock = new Object(); //同一把锁，类似资源类

        new Thread(() -> {
            synchronized (objectLock) {
                try {
                    objectLock.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            System.out.println(Thread.currentThread().getName()+"\t"+"被唤醒了");
        },"t1").start();

        //暂停几秒钟线程
        try { TimeUnit.SECONDS.sleep(3L); } catch (InterruptedException e) { e.printStackTrace(); }

        new Thread(() -> {
            synchronized (objectLock) {
                objectLock.notify();
            }
        },"t2").start();
    }
}
```

运行下

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151401557.png)

code（异常1）

wait方法和notify方法，两个都去掉同步代码块

```java
/**
 * 要求：t1线程等待3秒钟，3秒钟后t2线程唤醒t1线程继续工作
 * 以下异常情况：
 * 2 wait方法和notify方法，两个都去掉同步代码块后看运行效果
 *   2.1 异常情况
 *   Exception in thread "t1" java.lang.IllegalMonitorStateException at java.lang.Object.wait(Native Method)
 *   Exception in thread "t2" java.lang.IllegalMonitorStateException at java.lang.Object.notify(Native Method)
 *   2.2 结论
 *   Object类中的wait、notify、notifyAll用于线程等待和唤醒的方法，都必须在synchronized内部执行（必须用到关键字synchronized）。
 */
public class LockSupportDemo
{

    public static void main(String[] args)//main方法，主线程一切程序入口
    {
        Object objectLock = new Object(); //同一把锁，类似资源类

        new Thread(() -> {
                try {
                    objectLock.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            System.out.println(Thread.currentThread().getName()+"\t"+"被唤醒了");
        },"t1").start();

        //暂停几秒钟线程
        try { TimeUnit.SECONDS.sleep(3L); } catch (InterruptedException e) { e.printStackTrace(); }

        new Thread(() -> {
            objectLock.notify();
        },"t2").start();
    }
}
```

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151401122.png)

code（异常2）

将notify放在wait方法前面,程序无法执行，无法唤醒

```java
/**
 * 要求：t1线程等待3秒钟，3秒钟后t2线程唤醒t1线程继续工作
 *
 * 3 将notify放在wait方法前先执行，t1先notify了，3秒钟后t2线程再执行wait方法
 *   3.1 程序一直无法结束
 *   3.2 结论
 *   先wait后notify、notifyall方法，等待中的线程才会被唤醒，否则无法唤醒
 */
public class LockSupportDemo
{

    public static void main(String[] args)//main方法，主线程一切程序入口
    {
        Object objectLock = new Object(); //同一把锁，类似资源类

        new Thread(() -> {
            synchronized (objectLock) {
                objectLock.notify();
            }
            System.out.println(Thread.currentThread().getName()+"\t"+"通知了");
        },"t1").start();

        //t1先notify了，3秒钟后t2线程再执行wait方法
        try { TimeUnit.SECONDS.sleep(3L); } catch (InterruptedException e) { e.printStackTrace(); }

        new Thread(() -> {
            synchronized (objectLock) {
                try {
                    objectLock.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            System.out.println(Thread.currentThread().getName()+"\t"+"被唤醒了");
        },"t2").start();
    }
}
```

运行下

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151401728.png)

程序卡死。

总结：

- wait和notify方法必须要在同步块或者方法里面，且成对出现使用
- 先wait后notify才OK

### 3.3 Condition接口中的await后signal方法实现线程的等待和唤醒

code（正常）

```java
public class LockSupportDemo2
{
    public static void main(String[] args)
    {
        Lock lock = new ReentrantLock();
        Condition condition = lock.newCondition();

        new Thread(() -> {
            lock.lock();
            try
            {
                System.out.println(Thread.currentThread().getName()+"\t"+"start");
                condition.await();
                System.out.println(Thread.currentThread().getName()+"\t"+"被唤醒");
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                lock.unlock();
            }
        },"t1").start();

        //暂停几秒钟线程
        try { TimeUnit.SECONDS.sleep(3L); } catch (InterruptedException e) { e.printStackTrace(); }

        new Thread(() -> {
            lock.lock();
            try
            {
                condition.signal();
            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                lock.unlock();
            }
            System.out.println(Thread.currentThread().getName()+"\t"+"通知了");
        },"t2").start();

    }
}
```

run

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151401997.png)

code(异常1)

去掉lock/unlock

```java
/**
 * 异常：
 * condition.await();和condition.signal();都触发了IllegalMonitorStateException异常
 *
 * 原因：调用condition中线程等待和唤醒的方法的前提是，要在lock和unlock方法中,要有锁才能调用
 */
public class LockSupportDemo2
{
    public static void main(String[] args)
    {
        Lock lock = new ReentrantLock();
        Condition condition = lock.newCondition();

        new Thread(() -> {
            try
            {
                System.out.println(Thread.currentThread().getName()+"\t"+"start");
                condition.await();
                System.out.println(Thread.currentThread().getName()+"\t"+"被唤醒");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },"t1").start();

        //暂停几秒钟线程
        try { TimeUnit.SECONDS.sleep(3L); } catch (InterruptedException e) { e.printStackTrace(); }

        new Thread(() -> {
            try
            {
                condition.signal();
            } catch (Exception e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName()+"\t"+"通知了");
        },"t2").start();

    }
}
```

run

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151401655.png)

condition.await();和 condition.signal();都触发了 IllegalMonitorStateException异常。

结论：

lock、unlock对里面才能正确调用调用condition中线程等待和唤醒的方法

code（异常2）

先signal后await

```java
/**
 * 异常：
 * 程序无法运行
 *
 * 原因：先await()后signal才OK，否则线程无法被唤醒
 */
public class LockSupportDemo2
{
    public static void main(String[] args)
    {
        Lock lock = new ReentrantLock();
        Condition condition = lock.newCondition();

        new Thread(() -> {
            lock.lock();
            try
            {
                condition.signal();
                System.out.println(Thread.currentThread().getName()+"\t"+"signal");
            } catch (Exception e) {
                e.printStackTrace();
            }finally {
                lock.unlock();
            }
        },"t1").start();

        //暂停几秒钟线程
        try { TimeUnit.SECONDS.sleep(3L); } catch (InterruptedException e) { e.printStackTrace(); }

        new Thread(() -> {
            lock.lock();
            try
            {
                System.out.println(Thread.currentThread().getName()+"\t"+"等待被唤醒");
                condition.await();
                System.out.println(Thread.currentThread().getName()+"\t"+"被唤醒");
            } catch (Exception e) {
                e.printStackTrace();
            }finally {
                lock.unlock();
            }
        },"t2").start();

    }
}
```

run

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151401367.png)

总结

- Condtion中的线程等待和唤醒方法之前，需要先获取锁
- 一定要先await后signal，不要反了

### 3.4 Object和Condition使用的限制条件

- 线程先要获得并持有锁，必须在锁块(synchronized或lock)中
- 必须要先等待后唤醒，线程才能够被唤醒

### 3.5 LockSupport类中的park等待和unpark唤醒

#### 3.5.1 是什么

通过park()和unpark(thread)方法来实现阻塞和唤醒线程的操作

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151402816.png)

LockSupport是用来创建锁和其他同步类的基本线程阻塞原语。

LockSupport类使用了一种名为Permit（许可）的概念来做到阻塞和唤醒线程的功能， 每个线程都有一个许可(permit)，permit只有两个值1和零，默认是零。

可以把许可看成是一种(0,1)信号量（Semaphore），但与 Semaphore 不同的是，许可的累加上限是1

#### 3.5.2 主要方法

##### 3.5.2.1 API

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151402341.png)

##### 3.5.2.2 阻塞

> park() /park(Object blocker) 

调用LockSupport.park()时

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151402631.png)

permit默认是零，所以一开始调用park()方法，当前线程就会阻塞，直到别的线程将当前线程的permit设置为1时，park方法会被唤醒，然后会将permit再次设置为零并返回。

阻塞当前线程/阻塞传入的具体线程

##### 3.5.2.3 唤醒

> unpark(Thread thread) 

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151402662.png)

调用unpark(thread)方法后，就会将thread线程的许可permit设置成1(注意多次调用unpark方法，不会累加，permit值还是1)会自动唤醒thread线程，即之前阻塞中的LockSupport.park()方法会立即返回。

唤醒处于阻塞状态的指定线程

#### 3.5.3 code

##### 3.5.3.1 正常+无锁块要求

```java
public class LockSupportDemo3
{
    public static void main(String[] args)
    {
        //正常使用+不需要锁块
Thread t1 = new Thread(() -> {
    System.out.println(Thread.currentThread().getName()+" "+"1111111111111");
    LockSupport.park();
    System.out.println(Thread.currentThread().getName()+" "+"2222222222222------end被唤醒");
},"t1");
t1.start();

//暂停几秒钟线程
try { TimeUnit.SECONDS.sleep(3); } catch (InterruptedException e) { e.printStackTrace(); }

LockSupport.unpark(t1);
System.out.println(Thread.currentThread().getName()+"   -----LockSupport.unparrk() invoked over");

    }
}
```

run

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151402777.png)

##### 3.5.3.2 之前错误的先唤醒后等待，LockSupport照样支持

```java
public class T1
{
    public static void main(String[] args)
    {
        Thread t1 = new Thread(() -> {
            try { TimeUnit.SECONDS.sleep(3); } catch (InterruptedException e) { e.printStackTrace(); }
            System.out.println(Thread.currentThread().getName()+"\t"+System.currentTimeMillis());
            LockSupport.park();
            System.out.println(Thread.currentThread().getName()+"\t"+System.currentTimeMillis()+"---被叫醒");
        },"t1");
        t1.start();

        try { TimeUnit.SECONDS.sleep(1); } catch (InterruptedException e) { e.printStackTrace(); }

        LockSupport.unpark(t1);
        System.out.println(Thread.currentThread().getName()+"\t"+System.currentTimeMillis()+"---unpark over");
    }
}
```

run

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208151402960.png)

说明：sleep方法3秒后醒来，执行park无效，没有阻塞效果

先执行了unpark(t1)，导致上面的park方法形同虚设无效，时间一样。

##### 3.5.3.3 成双成对要牢记

...