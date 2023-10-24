---
title: Java强化之线程池
date: 2021-12-03 18:17:42.498
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/v2-2c82c1cbb0b5af2f8bab6cd772541a8a_250x0.png'
theme: 'light'
tags: 
- Java
---

# Java强化之线程池

## 一、什么是线程池

线程池其实就是一种多线程处理形式，处理过程中可以将任务添加到队列中，然后在创建线程后自动启动这些任务。这里的线程就是我们

前面学过的线程,这里的任务就是我们前面学过的实现了Runnable或Callable接口的实例对象;

## 二、为什么使用线程池

使用线程池最大的原因就是可以根据系统的需求和硬件环境灵活的控制线程的数量,且可以对所有线程进行统一的管理和控制,从而提高系

统的运行效率,降低系统运行压力;当然了,使用线程池的原因不仅仅只有这些,我们可以从线程池自身的优点上来进一步了解线程池的好处;

## 三、使用线程池有哪些优势

线程和任务分离,提升线程重用性;

控制线程并发数量,降低服务器压力,统一管理所有线程;

提升系统响应速度,假如创建线程用的时间为T1，执行任务用的时间为T2,销毁线程用的时间为T3，那么使用线程池就免去了T1和T3的时间；

## 四、线程池应用场景

1.网购商品秒杀

2.云盘文件上传和下载

3.12306网上购票系统等

只要有并发的地方、任务数量大或小、每个任务执行时间长或短的都可以使用线程池;只不过在使用线程池的时候,注意一下设置合理的线

程池大小即可;

## 五、Java内置线程池原理剖析

我们要想自定义线程池,必须先了解线程池的工作原理,才能自己定义线程池；

这里我们通过观察java中ThreadPoolExecutor的源码来学习线程池的原理;

### 1.`ThreadPoolExecutor` 构造方法:

```java
public ThreadPoolExecutor(int corePoolSize, //核心线程数量
	 int maximumPoolSize,//     最大线程数
	  long keepAliveTime, //       最大空闲时间
	  TimeUnit unit,         //        时间单位
	  BlockingQueue<Runnable> workQueue,   //   任务队列
	  ThreadFactory threadFactory,    // 线程工厂
	  RejectedExecutionHandler handler  //  饱和处理机制
		) 
	{ ... }
```

### 2.构造方法中的四个重要参数:

#### (1)核心线程数(`corePoolSize`)

核心线程数的设计需要依据任务的处理时间和每秒产生的任务数量来确定,例如:执行一个任务需要0.1秒,系统百分之80的时间每秒都会产生100个任务,那么要想在1秒内处理完这100个任务,就需要10个线程,此时我们就可以设计核心线程数为10;当然实际情况不可能这么平均,所以我们一般按照8020原则设计即可,既按照百分之80的情况设计核心线程数,剩下的百分之20可以利用最大线程数处理;

#### (2)任务队列长度(`workQueue`)

任务队列长度一般设计为:核心线程数/单个任务执行时间*2即可;例如上面的场景中,核心线程数设计为10,单个任务执行时间为0.1秒,则队列长度可以设计为200;

#### (3)最大线程数(`maximumPoolSize`)

最大线程数的设计除了需要参照核心线程数的条件外,还需要参照系统每秒产生的最大任务数决定:例如:上述环境中,如果系统每秒最大产生的任务是1000个,那么,最大线程数=(最大任务数-任务队列长度)*单个任务执行时间;既: 最大线程数=(1000-200)*0.1=80个;

#### (4)最大空闲时间(`keepAliveTime`)

这个参数的设计完全参考系统运行环境和硬件压力设定,没有固定的参考值,用户可以根据经验和系统产生任务的时间间隔合理设置一个值即可;

### 3.Java内置线程池-ExecutorService介绍:

#### 1) 获取线程池对象`ExecutorService`：

- **每提交一个任务就创建一个线程**
  `static ExecutorService newCachedThreadPool()` 创建一个默认的线程池对象,里面的线程可重用,且在第一次使用时才创建

  `static ExecutorService newCachedThreadPool(ThreadFactorythreadFactory) `线程池中的所有线程都使用`ThreadFactory`来创建,这样的线程无需手动启动,自动执行;

- **可以创建固定数量的线程池**
  `static ExecutorService newFixedThreadPool(int nThreads)`创建一个可重用固定线程数的线程池

  `static ExecutorService newFixedThreadPool(int nThreads, ThreadFactory threadFactory)`创建一个可重用固定线程数的线程池且线程池中的所有线程都使用`ThreadFactory`来创建。

- **b整个线程池只有一个线程，任务需要排队来进行处理**
  `static ExecutorService newSingleThreadExecutor()` 创建一个使用单个 worker 线程的 Executor，以无界队列方式来运行该线程。

  `static ExecutorService newSingleThreadExecutor(ThreadFactory threadFactory)`创建一个使用单个 worker 线程的 Executor，且线程池中的所有线程都使用ThreadFactory来创建。

#### 2`)ExecutorService`线程对象的方法：

- `void shutdown() `启动一次顺序关闭，执行以前提交的任务，但不接受新任务。
- `List<Runnable> shutdownNow()` 停止所有正在执行的任务，暂停处理正在等待的任务，并返回等待执行的任务列表。
- `<T> Future<T> submit(Callable<T> task) `执行带返回值的任务，返回一个Future对象。

- `Future<?> submit(Runnable task)` 执行 Runnable 任务，并返回一个表示该任务的 Future。

- `<T> Future<T> submit(Runnable task, T result)` 执行 Runnable任务，并返回一个表示该任务的 Future。

### 4.Java内置线程池-ScheduledExecutorService介绍:

> ScheduledExecutorService是ExecutorService的子接口,具备了延迟运行或定期执行任务的能力

#### 1)获取线程池对象ScheduledExecutorService：

- `static ScheduledExecutorService newScheduledThreadPool(int corePoolSize)`创建一个可重用固定线程数的线程池且允许延迟运行或定期执行任务;

- `static ScheduledExecutorService newScheduledThreadPool(int corePoolSize, ThreadFactory threadFactory)`创建一个可重用固定线程数的线程池且线程池中的所有线程都使用ThreadFactory来创建,且允许延迟运行或定期执行任务;

- `static ScheduledExecutorService newSingleThreadScheduledExecutor()`创建一个单线程执行程序，它允许在给定延迟后运行命令或者定期地执行。

- `static ScheduledExecutorService newSingleThreadScheduledExecutor(ThreadFactory threadFactory)` 创建一个单线程执行程序，它可安排在给定延迟后运行命令或者定期地执行。

#### 2)ScheduledExecutorService线程对象的方法：

- **实现Callable接口的任务，只有延迟**
  `<V> ScheduledFuture<V> schedule(Callable<V> callable, long delay, TimeUnit unit)` 延迟时间单位是unit,数量是delay的时间后执行callable。

- **实现Runnable接口的任务，只有延迟**
  `ScheduledFuture<?> schedule(Runnable command, long delay, TimeUnit unit) `延迟时间单位是unit,数量是delay的时间后执行command。

- **任务的工作时间算在延迟时间里面，延迟+重复执行**
  延迟时间单位是unit,数量是initialDelay的时间后,每间隔period时间重复执行一次command。

```java
ScheduledFuture<?> scheduleAtFixedRate(Runnable command, 
long initialDelay, long period,TimeUnit unit) 
```

- **任务的工作时间不算在延迟时间里面，延迟+重复执行**
  创建并执行一个在给定初始延迟后首次启用的定期操作，随后，在每一次执行终止和下一次执行开始之间都存在给定的延迟。

```java
 ScheduledFuture<?> scheduleWithFixedDelay(Runnable command,
  long initialDelay, long delay, TimeUnit unit) 
```

### 5.Java内置线程池-异步计算结果(Future):

我们刚刚在学习java内置线程池使用时,没有考虑线程计算的结果,但开发中,我们有时需要利用线程进行一些计算,然后获取这些计算的结果,而java中的Future接口就是专门用于描述异步计算结果的,我们可以通过Future 对象获取线程计算的结果;

Future 的常用方法如下:

- `boolean cancel(boolean mayInterruptIfRunning)`试图取消对此任务的执行。
- `V get()` 如有必要，等待计算完成，然后获取其结果。
- `V get(long timeout, TimeUnit unit)`如有必要，最多等待为使计算完成所给定的时间之后，获取其结果（如果结果可用）。
- `boolean isCancelled()` 如果在任务正常完成前将其取消，则返回 true。
- `boolean isDone() `如果任务已完成，则返回 true

> 一般使任务类实现Callable接口，因为实现Runnable接口重写的run()方法没有返回值而Callable接口的call()方法可以指定返回值。其返回值可以使用线程池对象的 Future submit(Callable task)方法的返回值Future的get()方法得到。还可以对任务进行取消和是否完成等操作。

## 六、线程池总结

1):利用`Executors`工厂类的静态方法,创建线程池对象;

2):编写`Runnable`或`Callable`实现类的实例对象;

3):利用`ExecutorService`的`submit`方法或`ScheduledExecutorService`的`schedule`方 法提交并执行线程任务

4):如果有执行结果,则处理异步执行结果(`Future`)

5):调用`shutdown()`方法,关闭线程池

## 七、综合案例

### 1、**秒杀商品**

**案例介绍**:

 假如某网上商城推出活动,新上架10部新手机免费送客户体验,要求所有参与活动的人员在规定的时间同时参与秒杀挣抢,假如有20人同时参与了该活动,请使用线程池模拟这个场景,保证前10人秒杀成功,后10人秒杀失败;

**要求**:

 1:使用线程池创建线程

 2:解决线程安全问题

**思路提示**:

 1:既然商品总数量是10个,那么我们可以在创建线程池的时候初始化线程数是10个及以下,设计线程池最大数量为10个;

 2:当某个线程执行完任务之后,可以让其他秒杀的人继续使用该线程参与秒杀;

 3:使用synchronized控制线程安全,防止出现错误数据;

**代码步骤**:

 1:编写任务类,主要是送出手机给秒杀成功的客户;

 2:编写主程序类,创建20个任务(模拟20个客户);

 3:创建线程池对象并接收20个任务,开始执行任务;

代码演示地址：https://gitee.com/tomcatist/data-support.git 的demo01

## 2、**取款业务**

**案例介绍**:

设计一个程序,使用两个线程模拟在两个地点同时从一个账号中取钱,假如卡中一共有1000元,每个线程取800元,要求演示结果一个线程取款成功,剩余200元,另一个线程取款失败,余额不足;

**要求**:

1:使用线程池创建线程

2:解决线程安全问题

**思路提示:**

1:线程池可以利用Executors工厂类的静态方法,创建线程池对象;

2:解决线程安全问题可以使用synchronized方法控制取钱的操作

3:在取款前,先判断余额是否足够,且保证余额判断和取钱行为的原子性;

代码演示地址：https://gitee.com/tomcatist/data-support.git 的demo02