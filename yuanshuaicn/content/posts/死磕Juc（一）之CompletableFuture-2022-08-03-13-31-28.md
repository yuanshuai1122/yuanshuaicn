---
title: 死磕Juc（一）之CompletableFuture
date: 2022-08-03 13:31:27.592
author:
  name: "yuanshuai"
  link: "https://cloud.tencent.com/developer/user/8180692"
  email: "shuaiyuan1122@gmail.com"
  avatar: "https://aabb-2023.oss-cn-beijing.aliyuncs.com/hjscijg3uw.png"
tags: 
- juc
- 并发
---

# 死磕Juc（一）之CompletableFuture

## 一、Future和Callable接口

Future接口定义了操作`异步任务执行一些方法`，如获取异步任务的执行结果、取消任务的执行、判断任

务是否被取消、判断任务执行是否完毕等。

Callable接口中定义了需要有返回的任务需要实现的方法

```java
@FunctionalInterface
public interface CallAble<v> {
    V call() throws Exception;
}
```

比如主线程让一个子线程去执行任务，子线程可能比较耗时，启动子线程开始执行任务后，

主线程就去做其他事情了，过了一会才去获取子任务的执行结果。

## 二、从FutureTask说开去

### 2.1 Future接口相关架构

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208031329459.png)

### 2.2 Future用法

#### 2.2.1 基本用法

我们模拟FutureTask执行3s，然后在主线程通过get()拿到FutureTask的执行结果

```java
    public static void main(String[] args) throws ExectionException, InterruptedException, TimeoutException
    {
        FutureTask<String> futureTask = new FutureTask<>(() -> {
            System.out.println("-----come in FutureTask");
            try {
                TimeUnit.SECONDS.sleep(3);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return ""+ ThreadLocalRandom.current().nextInt(100);
        });

        Thread t1 = new Thread(futureTask,"t1");
        t1.start();
        System.out.println(Thread.currentThread().getName()+"\t"+futureTask.get());

        System.out.println(Thread.currentThread().getName()+"\t"+" run... here");

    }
```

运行结果

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208031329478.png)

执行发现，当前线程main瞬间执行，到了futureTask.get()，会一直等futureTask返回结果，导致阻塞。

当然，`阻塞在并发中，属于大忌`。

怎么优化？

#### 2.2.2 FutureTask简单优化

我们可以设定一个超时时间，也就是如果futureTask线程在预定时间没有返回值，即抛出异常

```java
    public static void main(String[] args) throws ExecutionException, InterruptedException, TimeoutException
    {
        FutureTask<String> futureTask = new FutureTask<>(() -> {
            System.out.println("-----come in FutureTask");
            try {
                TimeUnit.SECONDS.sleep(3);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return ""+ ThreadLocalRandom.current().nextInt(100);
        });

        Thread t1 = new Thread(futureTask,"t1");
        t1.start();

        //3秒钟后才出来结果，还没有计算你提前来拿(只要一调用get方法，对于结果就是不见不散，会导致阻塞)
        //System.out.println(Thread.currentThread().getName()+"\t"+futureTask.get());

        //3秒钟后才出来结果，我只想等待1秒钟，过时不候
        System.out.println(Thread.currentThread().getName()+"\t"+futureTask.get(1L,TimeUnit.SECONDS));

        System.out.println(Thread.currentThread().getName()+"\t"+" run... here");

    }
```

执行

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208031329965.png)

可以发现，futureTask执行3s，我们只等1s，结果在1s内没等到结果，抛出了异常。

但是还是指标不治本，我们要的是非阻塞，但是现在抛出异常只是停了代码，并未走下去。

继续优化。

#### 2.2.3 FutureTask继续优化

如果想要异步获取结果,通常都会以轮询的方式去获取结果，尽量不要阻塞。

```java
    public static void main(String[] args) throws ExecutionException, InterruptedException, TimeoutException
    {
        FutureTask<Integer> futureTask = new FutureTask<>(() -> {
            System.out.println(Thread.currentThread().getName() + "\t" + "---come in");
            try {
                TimeUnit.SECONDS.sleep(3);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return 1024;
        });

        new Thread(futureTask,"t1").start();

        //不要阻塞，尽量用轮询替代
        while(true)
        {
            if(futureTask.isDone())
            {
                System.out.println("----result: "+futureTask.get());
                break;
            }else{
                System.out.println("还在计算中，别催，越催越慢，再催熄火");
            }
        }

    }
```

执行

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208031329914.png)

可以看到，程序在3s内一直打印日志，等待3s后，拿到了futureTask的返回值。

但是，轮询的方式会耗费无谓的CPU资源，而且也不见得能及时地得到计算结果.

其实也不是我们理想中的非阻塞状态，只是阻塞状态后的一点点优化。

> 待解决的问题（或者需求）
>
> 想完成一些复杂的任务
>
> - 应对Future的完成时间，完成了可以告诉我，也就是我们的回调通知
>
> - 将两个异步计算合成一个异步计算，这两个异步计算互相独立，同时第二个又依赖第一个的结果。
>
> - 当Future集合中某个任务最快结束时，返回结果。
>
> - 等待Future结合中的所有任务都完成。

所以我们需要对Future进行改进。

### 2.3 对Future的改进

#### 2.3.1 CompletableFuture和CompletionStage

类架构说明：

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208031329891.png)

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208031329633.png)

CompleteFuture实现了CompletionStage和Future，说明他既有CompletionStage的功能，也有Future的功能。

##### 2.3.1.1 接口CompletionStage是什么

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208031329378.png)

代表异步计算过程中的某一个阶段，一个阶段完成以后可能会触发另外一个阶段，有些类似Linux系统的管道分隔符传参数。

##### 2.3.1.2 类CompletableFuture是什么

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208031330892.png)

#### 2.3.2 CompletableFuture的核心的四个静态方法

##### 2.3.2.1 runAsync()和supplyAsync()

- public static CompletableFuture<Void> runAsync(Runnable runnable)
- public static CompletableFuture<Void> runAsync(Runnable runnable,Executor executor)
- public static  CompletableFuture supplyAsync(Supplier supplier)
- public static  CompletableFuture supplyAsync(Supplier supplier,Executor executor)

`runAsync()无返回值,supplyAsync()`有返回值，看名字，就知道都是异步的方法。

```java
    public static void main(String[] args)throws Exception {
        ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(1,
                20,
                1L,
                TimeUnit.SECONDS,
                new LinkedBlockingQueue<>(50), Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.AbortPolicy()
        );

        // runAsync可以直接执行，无返回值
        CompletableFuture<Void> future1 = CompletableFuture.runAsync(() -> {
            System.out.println(Thread.currentThread().getName() + "\t" + "-------come in");
        });

        // runAsync可以添加到线程池，无返回值
        CompletableFuture<Void> future2 = CompletableFuture.runAsync(() -> {
            System.out.println(Thread.currentThread().getName() + "\t" + "-------come in");
        }, threadPoolExecutor);

        // supplyAsync 可以直接执行，有返回值
        CompletableFuture<Integer> future3 = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread().getName() + "\t" + "-------come in");
            return 11;
        });

        // supplyAsync 可以添加到线程池，有返回值
        CompletableFuture<Integer> future4 = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread().getName() + "\t" + "-------come in");
            return 11;
        },threadPoolExecutor);

        // get他们的返回值
        System.out.println(future1.get() + "------" + future2.get());
        System.out.println(future3.get() + "------" + future4.get());

        threadPoolExecutor.shutdown();
    }
```

执行一下

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208031330402.png)

可以看到，没加线程池默认都是ForkJoinPool池，并且runAsync()返回值为空，因为`CompletableFuture<Void>`,而supplyAsync()有返回值。

> 没有指定Executor的方法，直接使用默认的ForkJoinPool.commonPool() 作为它的线程池执行异步代码。
>
> 如果指定线程池，则使用我们自定义的或者特别指定的线程池执行异步代码

##### 2.3.2.2 解决Future的痛点

> 待解决的问题（或者需求）
>
> 想完成一些复杂的任务
>
> - 应对Future的完成时间，完成了可以告诉我，也就是我们的回调通知
>
> - 将两个异步计算合成一个异步计算，这两个异步计算互相独立，同时第二个又依赖第一个的结果。
>
> - 当Future集合中某个任务最快结束时，返回结果。
>
> - 等待Future结合中的所有任务都完成。

从Java8开始引入了CompletableFuture，它是Future的功能增强版，可以传入回调对象，当异步任务完成或者发生异常时，自动调用回调对象的回调方法

demo

```java
public static void main(String[] args)throws Exception {
        ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(1,
                20,
                1L,
                TimeUnit.SECONDS,
                new LinkedBlockingQueue<>(50), Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.AbortPolicy()
        );

        CompletableFuture.supplyAsync(()-> {
            // 暂停几秒钟线程
            try {
                TimeUnit.SECONDS.sleep(2);
            }catch (InterruptedException e) {
                e.printStackTrace();
            }
            return 1;
        },threadPoolExecutor).thenApply(f -> {
            return f + 2;
        }).whenComplete((v,e) -> {
            if (e == null) {
                System.out.println("----result is ok: " + v );
            }
        }).exceptionally( e -> {
            e.printStackTrace();
            return null;
        });

        // 这里就不用future.get()，以为上面线程完成，会自动通知我们
        System.out.println("---- main is over");

        // 主线程不要立即结束，否则CompletableFuture默认使用的线程池会立刻关闭，我们暂停3s主线程
        try {
            TimeUnit.SECONDS.sleep(2);
        }catch (InterruptedException e) {
            e.printStackTrace();
        }

        threadPoolExecutor.shutdown();
    }
```

我们执行下

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208031330450.png)

主线程打印出了 main is over 等了2s，CompletableFuture自动打印出了result is ok：3，没有阻塞主线程。

上面的实现方法，类似于前端的.then()~

CompletableFuture的优点

- 异步任务结束时，会自动回调某个对象的方法；
- 异步任务出错时，会自动回调某个对象的方法；
- 异步任务出错时，会自动回调某个对象的方法；

##### 2.3.2.3 总结下几个方法

- Runnable 无参数，无返回值

```java
@FunctionalInterface
public interface Runnable {

    public abstract void run();
}
```

- Function<T, R> 接受一个参数，并且有返回值

```java
@FunctionalInterface
public interface Function<T, R> {

    R apply(T t);
    
    default <V> Function<V, R> compose(Function< ? super V, ? extends T> before) {
        Objects.requireNonNull(before);
        return (V v) -> apply(before.apply(v));
    }
    
    default <V> Function<T, V> andThen(Function< ? super R, ? extends V> after) {
        Objects.requireNonNull(after);
        return (T t) -> after.apply(apply(t));
    }
    
    static <T> Function<T, T> identity() {
        return t -> t;
    }
}
```

- Consumer<T> 接受一个参数，没有返回值

```java
@FunctionalInterface
public interface Consumer<T> {

    void accept(T t);

    default Consumer<T> andThen(Consumer< ? super T> after) {
        Objects.requireNonNull(after);
        return (T t) -> { accept(t); after.accept(t); };
    }
}
```

- Supplier<T> 没有参数，有一个返回值

```java
@FunctionalInterface
public interface Supplier<T> {

    T get();
}
```

- BiConsumer<T, U> 接受两个参数（Bi，英文单词词根，double的意思）

```java
@FunctionalInterface
public interface BiConsumer<T, U> {

    void accept(T t, U u);

    default BiConsumer<T, U> andThen(BiConsumer< ? super T, ? super U> after) {
        Objects.requireNonNull(after);

        return (l, r) -> {
            accept(l, r);
            after.accept(l, r);
        };
    }
}
```

小总结：

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208031330227.png)

## 三、CompletableFuture常用方法

### 3.1 获得结果和触发计算

#### 3.1.1 获取结果

- public T    get()  
  - 不见不散（阻塞）

- public T    get(long timeout, TimeUnit unit) 
  - 过时不候（超时）

- public T    getNow(T valueIfAbsent)
  - 没有计算完成的情况下，给我一个替代结果
  - 立即获取结果不阻塞
    - 计算完，返回计算完成后的结果
    - 没算完，返回设定的valueIfAbsent值

- public T    join()
  - 阻塞 相比于get()不抛异常

#### 3.1.2 主动触发计算

public boolean complete(T value) 

是否打断get方法立即返回括号值

```java
public static void main(String[] args) throws ExecutionException, InterruptedException {
        CompletableFuture<Integer> completableFuture = CompletableFuture.supplyAsync(() -> {
            try { TimeUnit.SECONDS.sleep(1); } catch (InterruptedException e) { e.printStackTrace(); }
            return 533;
        });

        //注释掉暂停线程，get还没有算完只能返回complete方法设置的444；暂停2秒钟线程，异步线程能够计算完成返回get
        try { TimeUnit.SECONDS.sleep(2); } catch (InterruptedException e) { e.printStackTrace(); }

        //当调用CompletableFuture.get()被阻塞的时候,complete方法就是结束阻塞并get()获取设置的complete里面的值.
        System.out.println(completableFuture.complete(444)+"\t"+completableFuture.get());


    }
```

### 3.2 对计算结果进行处理

#### 3.2.1 thenApply

计算结果存在依赖关系，这两个线程串行化

由于存在依赖关系(当前步错，不走下一步)，当前步骤有异常的话就叫停。

```java
public static void main(String[] args) throws ExecutionException, InterruptedException
{
    //当一个线程依赖另一个线程时用 thenApply 方法来把这两个线程串行化,
    CompletableFuture.supplyAsync(() -> {
        //暂停几秒钟线程
        try { TimeUnit.SECONDS.sleep(1); } catch (InterruptedException e) { e.printStackTrace(); }
        System.out.println("111");
        return 1024;
    }).thenApply(f -> {
        System.out.println("222");
        return f + 1;
    }).thenApply(f -> {
        //int age = 10/0; // 异常情况：那步出错就停在那步。
        System.out.println("333");
        return f + 1;
    }).whenCompleteAsync((v,e) -> {
        System.out.println("*****v: "+v);
    }).exceptionally(e -> {
        e.printStackTrace();
        return null;
    });

    System.out.println("-----主线程结束，END");

    // 主线程不要立刻结束，否则CompletableFuture默认使用的线程池会立刻关闭:
    try { TimeUnit.SECONDS.sleep(2); } catch (InterruptedException e) { e.printStackTrace(); }
}

```

#### 3.2.2 handle

有异常也可以往下一步走，根据带的异常参数可以进一步处理

```java
 public static void main(String[] args) throws ExecutionException, InterruptedException
    {
        //当一个线程依赖另一个线程时用 handle 方法来把这两个线程串行化,
        // 异常情况：有异常也可以往下一步走，根据带的异常参数可以进一步处理
        CompletableFuture.supplyAsync(() -> {
            //暂停几秒钟线程
            try { TimeUnit.SECONDS.sleep(1); } catch (InterruptedException e) { e.printStackTrace(); }
            System.out.println("111");
            return 1024;
        }).handle((f,e) -> {
            int age = 10/0;
            System.out.println("222");
            return f + 1;
        }).handle((f,e) -> {
            System.out.println("333");
            return f + 1;
        }).whenCompleteAsync((v,e) -> {
            System.out.println("*****v: "+v);
        }).exceptionally(e -> {
            e.printStackTrace();
            return null;
        });

        System.out.println("-----主线程结束，END");

        // 主线程不要立刻结束，否则CompletableFuture默认使用的线程池会立刻关闭:
        try { TimeUnit.SECONDS.sleep(2); } catch (InterruptedException e) { e.printStackTrace(); }
    }

```

#### 3.2.3 总结

![](https://yyss-blog.oss-cn-beijing.aliyuncs.com/202208031330011.png)

### 3.3 对计算结果进行消费

#### 3.3.1 thenAccept

接收任务的处理结果，并消费处理，无返回结果

```java
public static void main(String[] args) throws ExecutionException, InterruptedException
{
    CompletableFuture.supplyAsync(() -> {
        return 1;
    }).thenApply(f -> {
        return f + 2;
    }).thenApply(f -> {
        return f + 3;
    }).thenApply(f -> {
        return f + 4;
    }).thenAccept(r -> System.out.println(r));
}
```

#### 3.3.2 Code之任务之间的顺序执行

- thenRun
  - thenRun(Runnable runnable)
  - 任务 A 执行完执行 B，并且 B 不需要 A 的结果

- thenAccept
  - thenAccept(Consumer action)
  - 任务 A 执行完执行 B，B 需要 A 的结果，但是任务 B 无返回值

- thenApply
  - thenApply(Function fn)
  - 任务 A 执行完执行 B，B 需要 A 的结果，同时任务 B 有返回值

```java
System.out.println(CompletableFuture.supplyAsync(() -> "resultA").thenRun(() -> {}).join());

System.out.println(CompletableFuture.supplyAsync(() -> "resultA").thenAccept(resultA -> {}).join());

System.out.println(CompletableFuture.supplyAsync(() -> "resultA").thenApply(resultA -> resultA + " resultB").join());
```

### 3.4 对计算速度进行选用

谁快用谁

applyToEither

```java
public static void main(String[] args) throws ExecutionException, InterruptedException
    {
        CompletableFuture<Integer> completableFuture1 = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread().getName() + "\t" + "---come in ");
            //暂停几秒钟线程
            try { TimeUnit.SECONDS.sleep(2); } catch (InterruptedException e) { e.printStackTrace(); }
            return 10;
        });

        CompletableFuture<Integer> completableFuture2 = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread().getName() + "\t" + "---come in ");
            try { TimeUnit.SECONDS.sleep(1); } catch (InterruptedException e) { e.printStackTrace(); }
            return 20;
        });

        CompletableFuture<Integer> thenCombineResult = completableFuture1.applyToEither(completableFuture2,f -> {
            System.out.println(Thread.currentThread().getName() + "\t" + "---come in ");
            return f + 1;
        });

        System.out.println(Thread.currentThread().getName() + "\t" + thenCombineResult.get());
    }

```

### 3.5 对计算结果进行合并

> 两个CompletionStage任务都完成后，最终能把两个任务的结果一起交给thenCombine 来处理
>
> 先完成的先等着，等待其它分支任务

thenCombine

```java
public static void main(String[] args) throws ExecutionException, InterruptedException
    {
        CompletableFuture<Integer> thenCombineResult = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread().getName() + "\t" + "---come in 1");
            return 10;
        }).thenCombine(CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread().getName() + "\t" + "---come in 2");
            return 20;
        }), (x,y) -> {
            System.out.println(Thread.currentThread().getName() + "\t" + "---come in 3");
            return x + y;
        }).thenCombine(CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread().getName() + "\t" + "---come in 4");
            return 30;
        }),(a,b) -> {
            System.out.println(Thread.currentThread().getName() + "\t" + "---come in 5");
            return a + b;
        });
        System.out.println("-----主线程结束，END");
        System.out.println(thenCombineResult.get());


        // 主线程不要立刻结束，否则CompletableFuture默认使用的线程池会立刻关闭:
        try { TimeUnit.SECONDS.sleep(10); } catch (InterruptedException e) { e.printStackTrace(); }
    }

```

