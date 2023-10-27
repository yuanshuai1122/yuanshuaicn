---
title: 死磕juc（五）volatile与Java内存模型
date: 2022-08-29 12:35:28.948
author:
  name: "yuanshuai"
  link: "https://cloud.tencent.com/developer/user/8180692"
  email: "shuaiyuan1122@gmail.com"
  avatar: "https://aabb-2023.oss-cn-beijing.aliyuncs.com/hjscijg3uw.png"
tags: 
- juc
---

# volatile与Java内存模型

## 一、被volatile修改的变量有2大特点

### 1.1 特点

- 可见性
- 有序性

### 1.2 volatile的内存语义

- 当写一个volatile变量时，JMM会把该线程对应的本地内存中的共享变量值立即刷新回主内存中。
- 当读一个volatile变量时，JMM会把该线程对应的本地内存设置为无效，直接从主内存中读取共享变量
- 所以volatile的写内存语义是直接刷新到主内存中，读的内存语义是直接从主内存中读取。

## 二、内存屏障

### 2.1 是什么

内存屏障（也称内存栅栏，内存栅障，屏障指令等，是一类同步屏障指令，是CPU或编译器在对内存随机访问的操作中的一个同步点，使得此点之前的所有读写操作都执行后才可以开始执行此点之后的操作），避免代码重排序。内存屏障其实就是一种JVM指令，Java内存模型的重排规则会要求Java编译器在生成JVM指令时插入特定的内存屏障指令，通过这些内存屏障指令，volatile实现了Java内存模型中的可见性和有序性，但volatile无法保证原子性。

内存屏障之前的所有写操作都要回写到主内存，内存屏障之后的所有读操作都能获得内存屏障之前的所有写操作的最新结果(实现了可见性)。

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291227645.png)

因此重排序时，不允许把内存屏障之后的指令重排序到内存屏障之前。

一句话：对一个 volatile 域的写, happens-before 于任意后续对这个 volatile 域的读，也叫写后读。

### 2.2 volatile凭什么可以保证可见性和有序性？

> 内存屏障 (Memory Barriers / Fences)

### 2.3 JVM中提供了四类内存屏障指令

#### 2.3.1 C++源码分析

IDEA工具里面找Unsafe.class

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291227949.png)

Unsafe.java

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291227097.png)

Unsafe.cpp

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291227001.png)

OrderAccess.hpp

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291227852.png)

orderAccess_linux_x86.inline.hpp

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291227924.png)

#### 2.3.2 四大屏障分别是什么意思

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291227256.png)

#### 2.3.3 happens-before 之 volatile 变量规则

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291227356.png)

| 当第一个操作为volatile读时，不论第二个操作是什么，都不能重排序。这个操作保证了volatile读之后的操作不会被重排到volatile读之前。 |
| ------------------------------------------------------------ |
| 当第二个操作为volatile写时，不论第一个操作是什么，都不能重排序。这个操作保证了volatile写之前的操作不会被重排到volatile写之后。 |
| 当第一个操作为volatile写时，第二个操作为volatile读时，不能重排。 |

#### 2.3.4 JMM 就将内存屏障插⼊策略分为 4 种

> 写

1. 在每个 volatile 写操作的前⾯插⼊⼀个 StoreStore 屏障
2. 在每个 volatile 写操作的后⾯插⼊⼀个 StoreLoad 屏障

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291227599.png)

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291228836.png)

> 读

3. 在每个 volatile 读操作的后⾯插⼊⼀个 LoadLoad 屏障
4. 在每个 volatile 读操作的后⾯插⼊⼀个 LoadStore 屏障

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291228878.png)

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291228597.png)

## 三、volatile特性

### 3.1 保证可见性

#### 3.1.1 说明

保证不同线程对这个变量进行操作时的可见性，即变量一旦改变所有线程立即可见

#### 3.1.2 Code

```java
public class VolatileSeeDemo
{
    static          boolean flag = true;       //不加volatile，没有可见性
    //static volatile boolean flag = true;       //加了volatile，保证可见性

    public static void main(String[] args)
    {
        new Thread(() -> {
            System.out.println(Thread.currentThread().getName()+"\t come in");
            while (flag)
            {

            }
            System.out.println(Thread.currentThread().getName()+"\t flag被修改为false,退出.....");
        },"t1").start();

        //暂停2秒钟后让main线程修改flag值
        try { TimeUnit.SECONDS.sleep(2); } catch (InterruptedException e) { e.printStackTrace(); }

        flag = false;

        System.out.println("main线程修改完成");
    }
}

```

不加volatile，没有可见性，程序无法停止

加了volatile，保证可见性，程序可以停止

#### 3.1.3 上述代码原理解释

线程t1中为何看不到被主线程main修改为false的flag的值？

问题可能:

1. 主线程修改了flag之后没有将其刷新到主内存，所以t1线程看不到。
2. 主线程将flag刷新到了主内存，但是t1一直读取的是自己工作内存中flag的值，没有去主内存中更新获取flag最新的值。

我们的诉求：
1.线程中修改了工作内存中的副本之后，立即将其刷新到主内存；
2.工作内存中每次读取共享变量时，都去主内存中重新读取，然后拷贝到工作内存。

解决：
使用volatile修饰共享变量，就可以达到上面的效果，被volatile修改的变量有以下特点：

1. 线程中读取的时候，每次读取都会去主内存中读取共享变量最新的值，然后将其复制到工作内存
2. 线程中修改了工作内存中变量的副本，修改之后会立即刷新到主内存

#### 3.1.4 volatile变量的读写过程

Java内存模型中定义的8种工作内存与主内存之间的原子操作

read(读取)→load(加载)→use(使用)→assign(赋值)→store(存储)→write(写入)→lock(锁定)→unlock(解锁)

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291228532.png)

read: 作用于主内存，将变量的值从主内存传输到工作内存，主内存到工作内存

load: 作用于工作内存，将read从主内存传输的变量值放入工作内存变量副本中，即数据加载

use: 作用于工作内存，将工作内存变量副本的值传递给执行引擎，每当JVM遇到需要该变量的字节码指令时会执行该操作

assign: 作用于工作内存，将从执行引擎接收到的值赋值给工作内存变量，每当JVM遇到一个给变量赋值字节码指令时会执行该操作

store: 作用于工作内存，将赋值完毕的工作变量的值写回给主内存

write: 作用于主内存，将store传输过来的变量值赋值给主内存中的变量

由于上述只能保证单条指令的原子性，针对多条指令的组合性原子保证，没有大面积加锁，所以，JVM提供了另外两个原子指令：

lock: 作用于主内存，将一个变量标记为一个线程独占的状态，只是写时候加锁，就只是锁了写变量的过程。

unlock: 作用于主内存，把一个处于锁定状态的变量释放，然后才能被其他线程占用

### 3.2 没有原子性

#### 3.2.1 volatile变量的复合操作(如i++)不具有原子性

```java
class MyNumber
{
    volatile int number = 0;

    public void addPlusPlus()
    {
        number++;
    }
}

public class VolatileNoAtomicDemo
{
    public static void main(String[] args) throws InterruptedException
    {
        MyNumber myNumber = new MyNumber();

        for (int i = 1; i <=10; i++) {
            new Thread(() -> {
                for (int j = 1; j <= 1000; j++) {
                    myNumber.addPlusPlus();
                }
            },String.valueOf(i)).start();
        }
        
        //暂停几秒钟线程
        try { TimeUnit.SECONDS.sleep(3); } catch (InterruptedException e) { e.printStackTrace(); }
        System.out.println(Thread.currentThread().getName() + "\t" + myNumber.number);
    }
}

```

从i++的字节码角度说明

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291228582.png)

原子性指的是一个操作是不可中断的，即使是在多线程环境下，一个操作一旦开始就不会被其他线程影响。
public void add(){
        i++; //不具备原子性，该操作是先读取值，然后写回一个新值，相当于原来的值加上1，分3步完成
 }
如果第二个线程在第一个线程读取旧值和写回新值期间读取i的域值，那么第二个线程就会与第一个线程一起看到同一个值，并执行相同值的加1操作，这也就造成了线程安全失败，因此对于add方法必须使用synchronized修饰，以便保证线程安全.

读取赋值一个普通变量的情况

当线程1对主内存对象发起read操作到write操作第一套流程的时间里，线程2随时都有可能对这个主内存对象发起第二套操作

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291228758.png)

#### 3.2.2 既然一修改就是可见，为什么还不能保证原子性？

volatile主要是对其中部分指令做了处理

> 要use(使用)一个变量的时候必需load(载入），要载入的时候必需从主内存read(读取）这样就解决了读的可见性。 
>
> 写操作是把assign和store做了关联(在assign(赋值)后必需store(存储))。store(存储)后write(写入)。
>
> 也就是做到了给一个变量赋值的时候一串关联指令直接把变量值写到主内存。
>
> 就这样通过用的时候直接从主内存取，在赋值到直接写回主内存做到了内存可见性。注意蓝色框框的间隙。。。。。。o(╥﹏╥)o

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291228155.png)

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291228916.png)

#### 3.2.3 结论

读取赋值一个volatile变量的情况

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291228152.png)

> read-load-use 和 assign-store-write 成为了两个不可分割的原子操作，但是在use和assign之间依然有极小的一段真空期，有可能变量会被其他线程读取，导致写丢失一次...o(╥﹏╥)o
> 但是无论在哪一个时间点主内存的变量和任一工作内存的变量的值都是相等的。这个特性就导致了volatile变量不适合参与到依赖当前值的运算，如i = i + 1; i++;之类的那么依靠可见性的特点volatile可以用在哪些地方呢？ 通常volatile用做保存某个状态的boolean值or int值。

《深入理解Java虚拟机》提到：

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291229332.png)

JVM的字节码，i++分成三步，间隙期不同步非原子操作(i++)

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291229405.png)

### 3.3 指令禁重排

#### 3.3.1 说明与案例

重排序 

重排序是指编译器和处理器为了优化程序性能而对指令序列进行重新排序的一种手段，有时候会改变程序语句的先后顺序 

- 不存在数据依赖关系，可以重排序； 

- 存在数据依赖关系，禁止重排序 ；

但重排后的指令绝对不能改变原有的串行语义！ 这点在并发设计中必须要重点考虑！ 

重排序的分类和执行流程 

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291229566.png)

- 编译器优化的重排序 ： 编译器在不改变单线程串行语义的前提下，可以重新调整指令的执行顺序 

- 指令级并行的重排序 ： 处理器使用指令级并行技术来讲多条指令重叠执行，若不存在数据依赖性，处理器可以改变语句对应机器指令的执行顺序 

- 内存系统的重排序 ： 由于处理器使用缓存和读/写缓冲区，这使得加载和存储操作看上去可能是乱序执行 

**数据依赖性** ： 若两个操作访问同一变量，且这两个操作中有一个为写操作，此时两操作间就存在数据依赖性。 

案例 ： 

**不存在** 数据依赖关系，可以重排序===> 重排序OK  。

| 重排前                                                   | 重排后                                           |
| -------------------------------------------------------- | ------------------------------------------------ |
| int a = 1; //1 int b = 20; //2 int c = a + b; //3        | int b = 20; //1int a = 1; //2 int c = a + b; //3 |
| 结论：编译器调整了语句的顺序，但是不影响程序的最终结果。 | 重排序OK                                         |

**存在** 数据依赖关系 ，禁止重排序 ===> 重排序发生，会导致程序运行结果不同。 

编译器和处理器在重排序时，会遵守数据依赖性，不会改变存在依赖关系的两个操作的执行,但不同处理器和不同线程之间的数据性不会被编译器和处理器考虑，其只会作用于单处理器和单线程环境，下面三种情况，只要重排序两个操作的执行顺序，程序的执行结果就会被改变。 

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291229014.png)

#### 3.3.2 volatile的底层实现是通过内存屏障

volatile有关的禁止指令重排的行为

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291229587.png)

- 当第一个操作为volatile读时，不论第二个操作是什么，都不能重排序。这个操作保证了volatile读之后的操作不会被重排到volatile读之前。
- 当第二个操作为volatile写时，不论第一个操作是什么，都不能重排序。这个操作保证了volatile写之前的操作不会被重排到volatile写之后。
- 当第一个操作为volatile写时，第二个操作为volatile读时，不能重排。

四大屏障的插入情况

- 在每一个volatile写操作前面插入一个StoreStore屏障
  - StoreStore屏障可以保证在volatile写之前，其前面的所有普通写操作都已经刷新到主内存中。
- 在每一个volatile写操作后面插入一个StoreLoad屏障
  - StoreLoad屏障的作用是避免volatile写与后面可能有的volatile读/写操作重排序
- 在每一个volatile读操作后面插入一个LoadLoad屏障
  - LoadLoad屏障用来禁止处理器把上面的volatile读与下面的普通读重排序。
- 在每一个volatile读操作后面插入一个LoadStore屏障
  - LoadStore屏障用来禁止处理器把上面的volatile读与下面的普通写重排序。

Code说明

```java
//模拟一个单线程，什么顺序读？什么顺序写？
public class VolatileTest {
    int i = 0;
    volatile boolean flag = false;
    public void write(){
        i = 2;
        flag = true;
    }
    public void read(){
        if(flag){
            System.out.println("---i = " + i);
        }
    }
}
```

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291229540.png)

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291229771.png)

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291229260.png)

## 四、如何正确使用volatile

### 4.1 单一赋值可以，but含复合运算赋值不可以(i++之类)

- volatile int a = 10
- volatile boolean flag = false

### 4.2 状态标志，判断业务是否结束

```java
/**
 * 使用：作为一个布尔状态标志，用于指示发生了一个重要的一次性事件，例如完成初始化或任务结束
 * 理由：状态标志并不依赖于程序内任何其他状态，且通常只有一种状态转换
 * 例子：判断业务是否结束
 */
public class UseVolatileDemo
{
    private volatile static boolean flag = true;

    public static void main(String[] args)
    {
        new Thread(() -> {
            while(flag) {
                //do something......
            }
        },"t1").start();

        //暂停几秒钟线程
        try { TimeUnit.SECONDS.sleep(2L); } catch (InterruptedException e) { e.printStackTrace(); }

        new Thread(() -> {
            flag = false;
        },"t2").start();
    }
}
```

### 4.3 开销较低的读，写锁策略

```java
public class UseVolatileDemo
{
    /**
     * 使用：当读远多于写，结合使用内部锁和 volatile 变量来减少同步的开销
     * 理由：利用volatile保证读取操作的可见性；利用synchronized保证复合操作的原子性
     */
    public class Counter
    {
        private volatile int value;

        public int getValue()
        {
            return value;   //利用volatile保证读取操作的可见性
              }
        public synchronized int increment()
        {
            return value++; //利用synchronized保证复合操作的原子性
               }
    }
}
```

### 4.4 DCL双端锁的发布

#### 4.4.1 问题

单线程看问题代码

```java
public class SafeDoubleCheckSingleton
{
    private static SafeDoubleCheckSingleton singleton;
    //私有化构造方法
    private SafeDoubleCheckSingleton(){
    }
    //双重锁设计
    public static SafeDoubleCheckSingleton getInstance(){
        if (singleton == null){
            //1.多线程并发创建对象时，会通过加锁保证只有一个线程能创建对象
            synchronized (SafeDoubleCheckSingleton.class){
                if (singleton == null){
                    //隐患：多线程环境下，由于重排序，该对象可能还未完成初始化就被其他线程读取
                    singleton = new SafeDoubleCheckSingleton();
                }
            }
        }
        //2.对象创建完毕，执行getInstance()将不需要获取锁，直接返回创建对象
        return singleton;
    }
}
```

单线程看问题代码

- 单线程环境下(或者说正常情况下)，在"问题代码处"，会执行如下操作，保证能获取到已完成初始化的实例

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291229476.png)

由于存在指令重排序......

多线程看问题代码

- 隐患：多线程环境下，在"问题代码处"，会执行如下操作，由于重排序导致2,3乱序，后果就是其他线程得到的是null而不是完成初始化的对象

right

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291229388.png)

problem

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291230900.png)

#### 4.4.2 解决01

加volatile修饰

```java
public class SafeDoubleCheckSingleton
{
    //通过volatile声明，实现线程安全的延迟初始化。
    private volatile static SafeDoubleCheckSingleton singleton;
    //私有化构造方法
    private SafeDoubleCheckSingleton(){
    }
    //双重锁设计
    public static SafeDoubleCheckSingleton getInstance(){
        if (singleton == null){
            //1.多线程并发创建对象时，会通过加锁保证只有一个线程能创建对象
            synchronized (SafeDoubleCheckSingleton.class){
                if (singleton == null){
                    //隐患：多线程环境下，由于重排序，该对象可能还未完成初始化就被其他线程读取
                                      //原理:利用volatile，禁止 "初始化对象"(2) 和 "设置singleton指向内存空间"(3) 的重排序
                    singleton = new SafeDoubleCheckSingleton();
                }
            }
        }
        //2.对象创建完毕，执行getInstance()将不需要获取锁，直接返回创建对象
        return singleton;
    }
}
```

#### 4.4.3 解决02

采用静态内部类的方式实现

```java
//现在比较好的做法就是采用静态内部内的方式实现
 
public class SingletonDemo
{
    private SingletonDemo() { }

    private static class SingletonDemoHandler
    {
        private static SingletonDemo instance = new SingletonDemo();
    }

    public static SingletonDemo getInstance()
    {
        return SingletonDemoHandler.instance;
    }
}
```

## 五、最后的小总结

### 5.1 内存屏障是什么

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291230045.png)

### 5.2 内存屏障能干嘛

- 阻止屏障两边的指令重排序
- 写数据时加入屏障，强制将线程私有工作内存的数据刷回主物理内存
- 读数据时加入屏障，线程私有工作内存的数据失效，重新到主物理内存中获取最新数据

### 5.3 内存屏障四大指令

- 在每一个volatile写操作前面插入一个StoreStore屏障

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291230309.png)

- 在每一个volatile写操作后面插入一个StoreLoad屏障

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291231722.png)

- 在每一个volatile读操作后面插入一个LoadLoad屏障

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291231900.png)

- 在每一个volatile读操作后面插入一个LoadStore屏障

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291231409.png)

### 5.4 凭什么我们java写了一个volatile关键字 系统底层加入内存屏障？两者关系怎么勾搭上的?

字节码层面

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291231294.png)

关键字

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291231556.png)

### 5.5 volatile可见性

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291231293.png)

### 5.6 volatile禁重排

写指令

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291231696.png)

读指令

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291231206.png)

### 5.7 对比java.util.concurrent.locks.Lock来理解

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208291231060.png)

### 5.8 一句话总结

- volatile写之前的操作，都禁止重排序到volatile之后
- volatile读之后的操作，都禁止重排序到volatile之前
- volatile写之后volatile读，禁止重排序