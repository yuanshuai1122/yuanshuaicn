---
title: Redis之入门介绍
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

# 1、**入门概述**

## 1.1、是什么

- Redis:REmote DIctionary Server(远程字典服务器)
- 是完全开源免费的，用C语言编写的，遵守BSD协议，是一个高性能的(key/value)分布式内存数据库，基于内存运行并支持持久化的NoSQL数据库，是当前最热门的NoSql数据库之一,也被人们称为数据结构服务器
- Redis 与其他 key - value 缓存产品有以下三个特点
  - Redis支持数据的持久化，可以将内存中的数据保持在磁盘中，重启的时候可以再次加载进行使用
  - Redis不仅仅支持简单的key-value类型的数据，同时还提供list，set，zset，hash等数据结构的存储
  - Redis支持数据的备份，即master-slave模式的数据备份

## 1.2、能干嘛

- 内存存储和持久化：redis支持异步将内存中的数据写到硬盘上，同时不影响继续服务
- 取最新N个数据的操作，如：可以将最新的10条评论的ID放在Redis的List集合里面
- 模拟类似于HttpSession这种需要设定过期时间的功能
- 发布、订阅消息系统
- 定时器、计数器

## 1.3、去哪下

- Http://redis.io/

- Http://www.redis.cn/

## 1.4、怎么玩

- 数据类型、基本操作和配置

- 持久化和复制，RDB/AOF

- 事务的控制

- 复制

- ......

# 2、**VMWare+VMTools**千里之行始于足下

## 2.1、VMWare虚拟机的安装

这里不多啰嗦。

## 2.2、CentOS或者RedHad5的安装

- 如何查看自己的linux是32位还是64位

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/24.png)

getconf LONG_BIT  返回是多少就是几位 

- **假如出现了不支持虚拟化的问题**

我的笔记本cpu是64位的，操作系统也是64位的，问题应该如虚拟机右下角提示所说， 

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/25.png)

是“宿主机BIOS设置中的硬件虚拟化被禁用了。” 

需要打开笔记本BIOS中的IVT对虚拟化的支持。 

找到菜单“Security”–“System Security”， 

将Virtualization Technology(VTx)和Virtualization Technology DirectedI/O(VTd)设置为 Enabled。 

保存并退出BIOS设置，重启电脑，

![26](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/26.png)

## 2.3、VMTools的安装

..

## 2.4、设置共享目录

..

## 2.5、上述环境都OK后开始进行Redis的服务器安装配置



# 3、**Redis**的安装

## 3.1、Windows版安装

Window 下安装 

下载地址：https://github.com/dmajkic/redis/downloads 

下载到的Redis支持32bit和64bit。根据自己实际情况选择，将64bit的内容cp到自定义盘符安装目录取名redis。 如 C:\reids 

打开一个cmd窗口 使用cd命令切换目录到 C:\redis 运行 redis-server.exe redis.conf 。 

如果想方便的话，可以把redis的路径加到系统的环境变量里，这样就省得再输路径了，后面的那个redis.conf可以省略， 

如果省略，会启用默认的。输入之后，会显示如下界面： 

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/27.png)

这时候另启一个cmd窗口，原来的不要关闭，不然就无法访问服务端了。 

切换到redis目录下运行 redis-cli.exe -h 127.0.0.1 -p 6379 。 

设置键值对 set myKey abc 

取出键值对 get myKey 

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/28.png)

## **3.2、重要提示：**

由于企业里面做Redis开发，99%都是Linux版的运用和安装，几乎不会涉及到Windows版，上一步的讲解只是为了知识的完整性。Windows版不作为重点，可以下去自己玩，企业实战就认一个版：Linux

## 3.3、Linux版安装

1. #### 下载获得redis-3.0.4.Tar.gz后将它放入我们的Linux目录/opt

2. #### /opt目录下，解压命令:tar -zxvf redis-3.0.4.Tar.gz

3. #### 解压完成后出现文件夹：redis-3.0.4

   ![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/29.png)

4. #### 进入目录:cd redis-3.0.4

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/30.png)

5. #### 在redis-3.0.4目录下执行make命令

##### **5.1、运行make命令时出现的错误解析：**

在linux环境下安装redis，在make编译步骤报如下错误信息：

```shell
[root@centos6 redis-3.2.6]# make

cd src && make all

make[1]: Entering directory `/var/redis-3.2.6/src'

  CC adlist.o

/bin/sh: cc: command not found

make[1]: *** [adlist.o] Error 127

make[1]: Leaving directory `/var/redis-3.2.6/src'

make: *** [all] Error 2

...
```

错误原因： 原来[L](https://www.baidu.com/s?wd=linux系统&tn=44039180_cpr&fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1Y3nWbLuHIWuH9WnA7hmyRs0ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3EnHTLP1DYP1n1)inux系统没有装gcc，而Redis是C实现的，所以需要gcc来进行编译。

- 报错一 安装gcc

gcc是linux下的一个编译程序，是C程序的编译工具。 

GCC(GNU Compiler Collection) 是 GNU(GNU's Not Unix) 计划提供的编译器家族，它能够支持 C, C++, Objective-C, Fortran, Java 和 Ada 等等程序设计语言前端，同时能够运行在 x86, x86-64, IA-64, PowerPC, SPARC 和 Alpha 等等几乎目前所有的硬件平台上。鉴于这些特征，以及 GCC 编译代码的高效性，使得 GCC 成为绝大多数自由软件开发编译的首选工具。虽然对于程序员们来说，编译器只是一个工具，除了开发和维护人员，很少有人关注编译器的发展，但是 GCC 的影响力是如此之大，它的性能提升甚至有望改善所有的自由软件的运行效率，同时它的内部结构的变化也体现出现代编译器发展的新特征。 

安装过程：

```shell
  yum install cpp
  yum install binutils
  yum install glibc
  yum install glibc-kernheaders
  yum install glibc-common
  yum install glibc-devel
  yum install gcc
  yum install make

  yum install tcl
```

注意gcc依赖了很多东西，有些包可能系统已经 装了，有些没有，防止出意外，最好都走一遍

- 报错二，没有tcl8.5, 安装tcl8.5,过程如下

下载地址：http://downloads.sourceforge.net/tcl/tcl8.5.10-src.tar.gz

安装过程：

```shell
tar -zxvf  tcl8.5.tar.gz

./configure

make

make install
```

- 安装redis

make

如果make继续报错，信息如下：error: jemalloc/jemalloc.h: No such file or directory

执行 make MALLOC=libc 就行

注意的是，为了防止出意外，make失败后在make的话，清理一下，执行make clean

6. #### 如果make完成后继续执行make install

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/31.png)

7. #### 查看默认安装目录：usr/local/bin

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/32.png)

- Redis-benchmark:性能测试工具，可以在自己本子运行，看看自己本子性能如何（服务启动起来后执行）

- Redis-check-aof：修复有问题的AOF文件，rdb和aof后面讲

- Redis-check-dump：修复有问题的dump.rdb文件

- Redis-cli：客户端，操作入口

- Redis-sentinel：redis集群使用

- Redis-server：Redis服务器启动命令

8. #### 启动

- 修改redis.conf文件将里面的daemonize no 改成 yes，让服务在后台启动

- 将默认的redis.conf拷贝到自己定义好的一个路径下，比如/myconf

- 启动

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/33.png)

- 连通测试

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/34.png)

- /usr/local/bin目录下运行redis-server，运行拷贝出存放了自定义conf文件目录下的redis.conf文件

- 永远的helloworld

  ![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/35.png)

- 关闭

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/36.png)

# 4、**Redis**启动后杂项基础知识讲解

#### 4.1、单进程

- 单进程模型来处理客户端的请求。对读写等事件的响应是通过对epoll函数的包装来做到的。Redis的实际处理速度完全依靠主进程的执行效率

- Epoll是Linux内核为处理大批量文件描述符而作了改进的epoll，是Linux下多路复用IO接口select/poll的增强版本，它能显著提高程序在大量并发连接中只有少量活跃的情况下的系统CPU利用率。

#### 4.2、默认16个数据库，类似数组下表从零开始，初始默认使用零号库

设置数据库的数量，默认数据库为0，可以使用SELECT < dbid >命令在连接上指定数据库id 

 databases 16 

#### 4.3、Select命令切换数据库

#### 4.4、Dbsize查看当前数据库的key的数量

#### 4.5、Flushdb：清空当前库

#### 4.6、Flushall；通杀全部库

#### 4.7、统一密码管理，16个库都是同样密码，要么都OK要么一个也连接不上

#### 4.8、Redis索引都是从零开始

#### 4.9、为什么默认端口是6379