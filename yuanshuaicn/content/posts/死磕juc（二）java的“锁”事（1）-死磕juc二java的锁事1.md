---
title: 死磕juc（二）java的“锁”事（1）
date: 2022-08-09 20:51:10.388
author: 'yuanshuai'
cover: 'https://aabb-2023.oss-cn-beijing.aliyuncs.com/hqdefault.jpg'
theme: 'light'
tags: 
- juc
- 并发
---

# java的”锁“事（1）

## 一、乐观锁和悲观锁

### 1.1 悲观锁

>认为自己在使用数据的时候一定有别的线程来修改数据，因此在获取数据的时候会先加锁，确保数据不会被别的线程修改。
>
>synchronized关键字和Lock的实现类都是悲观锁


适合写操作多的场景，先加锁可以保证写操作时数据正确。

显式的锁定之后再操作同步资源

```java
//=============悲观锁的调用方式
public synchronized void m1()
{
    //加锁后的业务逻辑......
}

// 保证多个线程使用的是同一个lock对象的前提下
ReentrantLock lock = new ReentrantLock();
public void m2() {
    lock.lock();
    try {
        // 操作同步资源
    }finally {
        lock.unlock();
    }
}

//=============乐观锁的调用方式
// 保证多个线程使用的是同一个AtomicInteger
private AtomicInteger atomicInteger = new AtomicInteger();
atomicInteger.incrementAndGet();
```

### 1.2 乐观锁

> 乐观锁认为自己在使用数据时不会有别的线程修改数据，所以不会添加锁，只是在更新数据的时候去判断之前有没有别的线程更新了这个数据。
>
> 如果这个数据没有被更新，当前线程将自己修改的数据成功写入。如果数据已经被其他线程更新，则根据不同的实现方式执行不同的操作
>
> 乐观锁在Java中是通过使用无锁编程来实现，最常采用的是CAS算法，Java原子类中的递增操作就通过CAS自旋实现的。

适合读操作多的场景，不加锁的特点能够使其读操作的性能大幅提升。

乐观锁则直接去操作同步资源，是一种无锁算法，得之我幸不得我命，再抢

乐观锁一般有两种实现方式：

- 采用版本号机制
- CAS（Compare-and-Swap，即比较并替换）算法实现

## 二、经典8锁

> 1    标准访问有ab两个线程，请问先打印邮件还是短信?

```java
class Phone //资源类
{
    public synchronized void sendEmail() {
        System.out.println("-------sendEmail");
    }

    public synchronized void sendSMS() {
        System.out.println("-------sendSMS");
    }

}

public class Lock8Demo
{
    public static void main(String[] args)//一切程序的入口，主线程
    {
        Phone phone = new Phone();

        new Thread(() -> {
            phone.sendEmail();
        },"a").start();

        //暂停毫秒
        try { TimeUnit.MILLISECONDS.sleep(300); } catch (InterruptedException e) { e.printStackTrace(); }

        new Thread(() -> {
            phone.sendSMS();
        },"b").start();

    }
}
```

运行下：

```
-------sendEmail
-------sendSMS
```

> 2    sendEmail方法暂停3秒钟，请问先打印邮件还是短信

```java
class Phone //资源类
{
    public synchronized void sendEmail() {
        try { TimeUnit.SECONDS.sleep(3); } catch (InterruptedException e) { e.printStackTrace(); }
        System.out.println("-------sendEmail");
    }

    public synchronized void sendSMS() {
        System.out.println("-------sendSMS");
    }

}

public class Lock8Demo
{
    public static void main(String[] args)//一切程序的入口，主线程
    {
        Phone phone = new Phone();

        new Thread(() -> {
            phone.sendEmail();
        },"a").start();

        //暂停毫秒
        try { TimeUnit.MILLISECONDS.sleep(300); } catch (InterruptedException e) { e.printStackTrace(); }

        new Thread(() -> {
            phone.sendSMS();
        },"b").start();

    }
}
```

运行下：

```
-------sendEmail
-------sendSMS
```

原因：

> ```
> * *  1-2
> *  *  *  一个对象里面如果有多个synchronized方法，某一个时刻内，只要一个线程去调用其中的一个synchronized方法了，
> *  *  *  其它的线程都只能等待，换句话说，某一个时刻内，只能有唯一的一个线程去访问这些synchronized方法
> *  *  *  锁的是当前对象this，被锁定后，其它的线程都不能进入到当前对象的其它的synchronized方法
> ```

> 3    新增一个普通的hello方法，请问先打印邮件还是hello

```java
class Phone //资源类
{
    public synchronized void sendEmail() {
        try { TimeUnit.SECONDS.sleep(3); } catch (InterruptedException e) { e.printStackTrace(); }
        System.out.println("-------sendEmail");
    }

    public synchronized void sendSMS() {
        System.out.println("-------sendSMS");
    }

    public void hello() {
        System.out.println("-------hello");
    }

}

public class Lock8Demo
{
    public static void main(String[] args)//一切程序的入口，主线程
    {
        Phone phone = new Phone();

        new Thread(() -> {
            phone.sendEmail();
        },"a").start();

        //暂停毫秒
        try { TimeUnit.MILLISECONDS.sleep(300); } catch (InterruptedException e) { e.printStackTrace(); }

        new Thread(() -> {
            phone.hello();
        },"b").start();

    }
}
```

运行：

```
-------hello
-------sendEmail
```

> 4    有两部手机，请问先打印邮件还是短信

```java
class Phone //资源类
{
    public synchronized void sendEmail() {
        try { TimeUnit.SECONDS.sleep(3); } catch (InterruptedException e) { e.printStackTrace(); }
        System.out.println("-------sendEmail");
    }

    public synchronized void sendSMS() {
        System.out.println("-------sendSMS");
    }

    public void hello() {
        System.out.println("-------hello");
    }

}

public class Lock8Demo
{
    public static void main(String[] args)//一切程序的入口，主线程
    {
        Phone phone = new Phone();
        Phone phone2 = new Phone();

        new Thread(() -> {
            phone.sendEmail();
        },"a").start();

        //暂停毫秒
        try { TimeUnit.MILLISECONDS.sleep(300); } catch (InterruptedException e) { e.printStackTrace(); }

        new Thread(() -> {
            phone2.sendSMS();
        },"b").start();

    }
}
```

运行：

```
-------sendSMS
-------sendEmails
```

原因:

```
 *  3-4
 *  *  加个普通方法后发现和同步锁无关,hello
 *  *  换成两个对象后，不是同一把锁了，情况立刻变化。
```

> 5    两个静态同步方法，同1部手机，请问先打印邮件还是短信

```java
class Phone //资源类
{
    public static synchronized void sendEmail() {
        try { TimeUnit.SECONDS.sleep(3); } catch (InterruptedException e) { e.printStackTrace(); }
        System.out.println("-------sendEmail");
    }

    public static synchronized void sendSMS() {
        System.out.println("-------sendSMS");
    }

    public void hello() {
        System.out.println("-------hello");
    }

}

public class Lock8Demo
{
    public static void main(String[] args)//一切程序的入口，主线程
    {
        Phone phone = new Phone();

        new Thread(() -> {
            phone.sendEmail();
        },"a").start();

        //暂停毫秒
        try { TimeUnit.MILLISECONDS.sleep(300); } catch (InterruptedException e) { e.printStackTrace(); }

        new Thread(() -> {
            phone.sendSMS();
        },"b").start();

    }
}
```

运行：

```
-------sendEmail
-------sendSMS
```

> ```
> 6    两个静态同步方法， 2部手机，请问先打印邮件还是短信
> ```

```java
class Phone //资源类
{
    public static synchronized void sendEmail() {
        try { TimeUnit.SECONDS.sleep(3); } catch (InterruptedException e) { e.printStackTrace(); }
        System.out.println("-------sendEmail");
    }

    public static synchronized void sendSMS() {
        System.out.println("-------sendSMS");
    }

    public void hello() {
        System.out.println("-------hello");
    }

}

public class Lock8Demo
{
    public static void main(String[] args)//一切程序的入口，主线程
    {
        Phone phone = new Phone();
        Phone phone2 = new Phone();

        new Thread(() -> {
            phone.sendEmail();
        },"a").start();

        //暂停毫秒
        try { TimeUnit.MILLISECONDS.sleep(300); } catch (InterruptedException e) { e.printStackTrace(); }

        new Thread(() -> {
            phone2.sendSMS();
        },"b").start();

    }
}
```

运行

```
-------sendEmail
-------sendSMS
```

原因

```
 *  *  5-6 都换成静态同步方法后，情况又变化
 *  *  三种 synchronized 锁的内容有一些差别:
 *  * 对于普通同步方法，锁的是当前实例对象，通常指this,具体的一部部手机,所有的普通同步方法用的都是同一把锁——实例对象本身，
 *  * 对于静态同步方法，锁的是当前类的Class对象，如Phone.class唯一的一个模板
 *  * 对于同步方法块，锁的是 synchronized 括号内的对象
```

> 7    1个静态同步方法，1个普通同步方法,同1部手机，请问先打印邮件还是短信

```java
class Phone //资源类
{
    public static synchronized void sendEmail() {
        try { TimeUnit.SECONDS.sleep(3); } catch (InterruptedException e) { e.printStackTrace(); }
        System.out.println("-------sendEmail");
    }

    public synchronized void sendSMS() {
        System.out.println("-------sendSMS");
    }

    public void hello() {
        System.out.println("-------hello");
    }

}

public class Lock8Demo
{
    public static void main(String[] args)//一切程序的入口，主线程
    {
        Phone phone = new Phone();

        new Thread(() -> {
            phone.sendEmail();
        },"a").start();

        //暂停毫秒
        try { TimeUnit.MILLISECONDS.sleep(300); } catch (InterruptedException e) { e.printStackTrace(); }

        new Thread(() -> {
            phone.sendSMS();
        },"b").start();

    }
}
```

运行：

```
-------sendSMS
-------sendEmail
```

> 8    1个静态同步方法，1个普通同步方法,2部手机，请问先打印邮件还是短信

```java
class Phone //资源类
{
    public static synchronized void sendEmail() {
        try { TimeUnit.SECONDS.sleep(3); } catch (InterruptedException e) { e.printStackTrace(); }
        System.out.println("-------sendEmail");
    }

    public synchronized void sendSMS() {
        System.out.println("-------sendSMS");
    }

    public void hello() {
        System.out.println("-------hello");
    }

}

public class Lock8Demo
{
    public static void main(String[] args)//一切程序的入口，主线程
    {
        Phone phone = new Phone();
        Phone phone2 = new Phone();

        new Thread(() -> {
            phone.sendEmail();
        },"a").start();

        //暂停毫秒
        try { TimeUnit.MILLISECONDS.sleep(300); } catch (InterruptedException e) { e.printStackTrace(); }

        new Thread(() -> {
            phone2.sendSMS();
        },"b").start();

    }
}
```

运行

```
-------sendSMS
-------sendEmail
```

原因

```
 *   *  7-8
 *  *    当一个线程试图访问同步代码时它首先必须得到锁，退出或抛出异常时必须释放锁。
 *  *  *
 *  *  *  所有的普通同步方法用的都是同一把锁——实例对象本身，就是new出来的具体实例对象本身,本类this
 *  *  *  也就是说如果一个实例对象的普通同步方法获取锁后，该实例对象的其他普通同步方法必须等待获取锁的方法释放锁后才能获取锁。
 *  *  *
 *  *  *  所有的静态同步方法用的也是同一把锁——类对象本身，就是我们说过的唯一模板Class
 *  *  *  具体实例对象this和唯一模板Class，这两把锁是两个不同的对象，所以静态同步方法与普通同步方法之间是不会有竞态条件的
 *  *  *  但是一旦一个静态同步方法获取锁后，其他的静态同步方法都必须等待该方法释放锁后才能获取锁。
```

## 三、8种锁的案例

### 3.1 JDK源码(notify方法)说明举例

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208092048905.png)

### 3.2 作用在三个地方

- 作用于实例方法，当前实例加锁，进入同步代码前要获得当前实例的锁；
- 作用于代码块，对括号里配置的对象加锁。
- 作用于静态方法，当前类加锁，进去同步代码前要获得当前类对象的锁；

## 四、从字节码角度分析synchronized实现

### 4.1 javap -c ***.class文件反编译

> -c   对代码进行反汇编

假如你需要更多信息

> javap -v ***.class文件反编译
>
> -v  -verbose             输出附加信息（包括行号、本地变量表，反汇编等详细信息）

### 4.2 synchronized同步代码块

javap -c ***.class文件反编译

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208092049569.png)

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208092049682.png)

> 实现使用的是monitorenter和monitorexit指令
>
> 多出来的monitorexit 是为了退出异常，必须保证退出，否则会一直有锁。

一定是一个enter两个exit吗？

m1方法里面自己添加一个异常试试

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208092049466.png)

可以看到只剩一个exit了。

### 4.3 synchronized普通同步方法

javap -v ***.class文件反编译

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208092049267.png)

可以看到直接给类的flag加了个标志位~

synchronized普通同步方法

> 调用指令将会检查方法的ACC_SYNCHRONIZED访问标志是否被设置。
> 如果设置了，执行线程会将先持有monitor然后再执行方法，
> 最后在方法完成(无论是正常完成还是非正常完成)时释放 monitor

### 4.4 synchronized静态同步方法

javap -v ***.class文件反编译

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208092049433.png)

synchronized静态同步方法

> ACC_STATIC, ACC_SYNCHRONIZED访问标志区分该方法是否静态同步方法

## 五、什么是管程monitor

> 管程 (英语：Monitors，也称为监视器) 是一种程序结构，结构内的多个子程序（对象或模块）形成的多个工作线程互斥访问共享资源。
> 这些共享资源一般是硬件设备或一群变量。对共享变量能够进行的所有操作集中在一个模块中。（把信号量及其操作原语“封装”在一个对象内部）管程实现了在一个时间点，最多只有一个线程在执行管程的某个子程序。管程提供了一种机制，管程可以看做一个软件模块，它是将共享的变量和对于这些共享变量的操作封装起来，形成一个具有一定接口的功能模块，进程可以调用管程来实现进程级别的并发控制。

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208092049891.png)

在HotSpot虚拟机中，monitor采用ObjectMonitor实现

瞅一眼C++源码

ObjectMonitor.java→ObjectMonitor.cpp→objectMonitor.hpp

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208092049674.png)

每个对象天生都带着一个对象监视器

