---
title: Redis之复制
date: 2021-09-24 18:38:23.971
author:
  name: "yuanshuai"
  link: "https://cloud.tencent.com/developer/user/8180692"
  email: "shuaiyuan1122@gmail.com"
  avatar: "https://aabb-2023.oss-cn-beijing.aliyuncs.com/hjscijg3uw.png"
tags: 
- 数据库
- Redis
---

## **1、是什么**

行话：也就是我们所说的主从复制，主机数据更新后根据配置和策略，自动同步到备机的master/slaver机制，Master以写为主，Slave以读为主

## 2、**能干嘛**

读写分离

容灾恢复

## 3、**怎么玩**

### 3.1、配从(库)不配主(库)

### 3.2、从库配置：slaveof 主库IP 主库端口

- 每次与master断开之后，都需要重新连接，除非你配置进redis.conf文件

- Info replication

### 3.3、修改配置文件细节操作

- 拷贝多个redis.conf文件

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/107.png)

- 开启daemonize yes

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/108.png)

- Pid文件名字
- 指定端口
- Log文件名字

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/109.png)

- Dump.rdb名字

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/200.png)

### 3.4、常用3招

#### 3.4.1、一主二仆

- Init

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/201.png)

- 一个Master两个Slave

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/202.png)

- 日志查看

主机日志

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/203.png)

备机日志

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/204.png)

info replication

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/205.png)

#### 3.4.2、薪火相传

- 上一个Slave可以是下一个slave的Master，Slave同样可以接收其他Slaves的连接和同步请求，那么该slave作为了链条中下一个的master,可以有效减轻master的写压力

- 中途变更转向:会清除之前的数据，重新建立拷贝最新的

- Slaveof 新主库IP 新主库端口

#### 3.4.3、反客为主

SLAVEOF no one：使当前数据库停止与其他数据库的同步，转成主数据库

## 4、**复制原理**

- Slave启动成功连接到master后会发送一个sync命令
- Master接到命令启动后台的存盘进程，同时收集所有接收到的用于修改数据集命令，在后台进程执行完毕之后，master将传送整个数据文件到slave,以完成一次完全同步
- 全量复制：而slave服务在接收到数据库文件数据后，将其存盘并加载到内存中。
- 增量复制：Master继续将新的所有收集到的修改命令依次传给slave,完成同步
- 但是只要是重新连接master,一次完全同步（全量复制)将被自动执行

## 5、**哨兵模式**(sentinel)

### 5.1、是什么

反客为主的自动版，能够后台监控主机是否故障，如果故障了根据投票数自动将从库转换为主库

### 5.2、怎么玩(使用步骤)

#### 5.2.1、调整结构，6379带着80、81

#### 5.2.2、自定义的/myredis目录下新建sentinel.conf文件，名字绝不能错

### 5.2.3、配置哨兵,填写内容

- sentinel monitor 被监控数据库名字(自己起名字) 127.0.0.1 6379 1

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/206.png)

- 上面最后一个数字1，表示主机挂掉后salve投票看让谁接替成为主机，得票数多少后成为主机

### 5.2.4、启动哨兵

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/207.png)

- Redis-sentinel /myredis/sentinel.conf 
- 上述目录依照各自的实际情况配置，可能目录不同

### 5.2.5、原有的master挂了

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/208.png)

### 5.2.6、投票新选

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/209.png)

### 5.2.7、重新主从继续开工,info replication查查看

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/210.png)

### 5.2.8、问题：如果之前的master重启回来，会不会双master冲突？

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/211.png)

## 5.3、一组sentinel能同时监控多个Master

## 6、复制的缺点

- 复制延时

由于所有的写操作都是先在Master上操作，然后同步更新到Slave上，所以从Master同步到Slave机器有一定的延迟，当系统很繁忙的时候，延迟问题会更加严重，Slave机器数量的增加也会使这个问题更加严重。