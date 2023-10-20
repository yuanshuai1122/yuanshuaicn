---
title: Redis之发布订阅
date: 2021-11-26 21:30:55.42
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/1498119653712274.png'
theme: 'light'
tags: 
- 数据库
- Redis
---

## 1、是什么

- 进程间的一种消息通信模式：发送者(pub)发送消息，订阅者(sub)接收消息。

- 订阅/发布消息图

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/105.png)

## 2、**命令**

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/106.png)

## 3、**案列**

先订阅后发布 后才能收到消息， 

1 可以一次性订阅多个，SUBSCRIBE c1 c2 c3 

2 消息发布，PUBLISH c2 hello-redis 

3 订阅多个，通配符*， PSUBSCRIBE new* 

4 收取消息， PUBLISH new1 redis2015