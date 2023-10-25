---
title: Redis之事务
date: 2021-posts-26 21:30:51.891
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/1498119653712274.png'
theme: 'light'
tags: 
- 数据库
- Redis
---

## 1、是什么

可以一次执行多个命令，本质是一组命令的集合。一个事务中的所有命令都会序列化，按顺序地串行化执行而不会被其它命令插入，不许加塞

## 2、能干嘛

一个队列中，一次性、顺序性、排他性的执行一系列命令

## 3、怎么玩

### 3.1、常用命令

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/96.png)

### 3.2、Case1：正常执行

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/97.png)

### 3.3、Case2：放弃事务

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/98.png)

### 3.4、Case3：全体连坐

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/99.png)

### 3.5、Case4：冤头债主

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/100.png)

### 3.6、Case5：watch监控

#### 3.6.1、悲观锁/乐观锁/CAS(Check And Set)

- 悲观锁

悲观锁(Pessimistic Lock), 顾名思义，就是很悲观，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会锁，这样别人想拿这个数据就会block直到它拿到锁。传统的关系型数据库里边就用到了很多这种锁机制，比如行锁，表锁等，读锁，写锁等，都是在做操作之前先上锁 

- 乐观锁

乐观锁(Optimistic Lock), 顾名思义，就是很乐观，每次去拿数据的时候都认为别人不会修改， 所以不会上锁 ，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号等机制。乐观锁适用于多读的应用类型，这样可以提高吞吐量，

乐观锁策略:提交版本必须大于记录当前版本才能执行更新 

- CAS

#### 3.6.2、初始化信用卡可用余额和欠额

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/101.png)

#### 3.6.3、无加塞篡改，先监控再开启multi，保证两笔金额变动在同一个事务内

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/102.png)

#### 3.6.4、有加塞篡改

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/103.png)

监控了key，如果key被修改了，后面一个事务的执行失效

#### 3.6.5、unwatch

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/104.png)

#### 3.6.6、一旦执行了exec之前加的监控锁都会被取消掉了

#### 3.6.7、小结

- Watch指令，类似乐观锁，事务提交时，如果Key的值已被别的客户端改变，比如某个list已被别的客户端push/pop过了，整个事务队列都不会被执行

- EXEC命令执行的事务都将被放弃，同时返回Nullmulti-bulk应答以通知调用者事务执行失败

## 4、**3**个阶段

- 开启：以MULTI开始一个事务
- 入队：将多个命令入队到事务中，接到这些命令并不会立即执行，而是放到等待执行的事务队列里面
- 执行：由EXEC命令触发事务

## 5、3个特性

- 单独的隔离操作：事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。
- 没有隔离级别的概念：队列中的命令没有提交之前都不会实际的被执行，因为事务提交前任何指令都不会被实际执行，也就不存在”事务内的查询要看到事务里的更新，在事务外查询不能看到”这个让人万分头痛的问题
- 不保证原子性：redis同一个事务中如果有一条命令执行失败，其后的命令仍然会被执行，没有回滚