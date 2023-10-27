---
title: Redis之NoSql入门和概述
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

# **NoSql**入门和概述

## 1、**入门概述**

### 1.1、互联网时代背景下大机遇，为什么用nosql

#### 1.1.1、单机MySQL的美好年代

在90年代，一个网站的访问量一般都不大，用单个数据库完全可以轻松应付。 

在那个时候，更多的都是静态网页，动态交互类型的网站不多。 

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/1.png)

上述架构下，我们来看看数据存储的瓶颈是什么？ 

1.数据量的总大小 一个机器放不下时 

2.数据的索引（B+ Tree）一个机器的内存放不下时 

3.访问量(读写混合)一个实例不能承受时

​       如果满足了上述1 or 3个，进化...... 

#### 1.1.2、Memcached(缓存)+MySQL+垂直拆分

后来，随着访问量的上升，几乎大部分使用MySQL架构的网站在数据库上都开始出现了性能问题，web程序不再仅仅专注在功能上，同时也在追求性能。程序员们开始大量的使用缓存技术来缓解数据库的压力，优化数据库的结构和索引。开始比较流行的是通过文件缓存来缓解数据库压力，但是当访问量继续增大的时候，多台web机器通过文件缓存不能共享，大量的小文件缓存也带了了比较高的IO压力。在这个时候， Memcached就自然的成为一个非常时尚的技术产品。 

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/2.png)

Memcached作为一个独立的分布式的缓存服务器，为多个web服务器提供了一个共享的高性能缓存服务，在Memcached服务器上，又发展了根据hash算法来进行多台Memcached缓存服务的扩展，然后又出现了一致性hash来解决增加或减少缓存服务器导致重新hash带来的大量缓存失效的弊端 

#### 1.1.3、Mysql主从读写分离

由于数据库的写入压力增加，Memcached只能缓解数据库的读取压力。读写集中在一个数据库上让数据库不堪重负，大部分网站开始使用主从复制技术来达到读写分离，以提高读写性能和读库的可扩展性。Mysql的master-slave模式成为这个时候的网站标配了。 

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/3.png)

#### 1.1.4、分表分库+水平拆分+mysql集群

在Memcached的高速缓存，MySQL的主从复制，读写分离的基础之上，这时MySQL主库的写压力开始出现瓶颈，而数据量的持续猛增，由于MyISAM使用表锁，在高并发下会出现严重的锁问题，大量的高并发MySQL应用开始使用InnoDB引擎代替MyISAM。 

同时，开始流行使用分表分库来缓解写压力和数据增长的扩展问题。这个时候，分表分库成了一个热门技术，是面试的热门问题也是业界讨论的热门技术问题。也就在这个时候，MySQL推出了还不太稳定的表分区，这也给技术实力一般的公司带来了希望。虽然MySQL推出了MySQL Cluster集群，但性能也不能很好满足互联网的要求，只是在高可靠性上提供了非常大的保证。 

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/4.png)

#### 1.1.5、MySQL的扩展性瓶颈

MySQL数据库也经常存储一些大文本字段，导致数据库表非常的大，在做数据库恢复的时候就导致非常的慢，不容易快速恢复数据库。比如1000万4KB大小的文本就接近40GB的大小，如果能把这些数据从MySQL省去，MySQL将变得非常的小。关系数据库很强大，但是它并不能很好的应付所有的应用场景。MySQL的扩展性差（需要复杂的技术来实现），大数据下IO压力大，表结构更改困难，正是当前使用MySQL的开发人员面临的问题。 

#### 1.1.6、今天是什么样子？

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/5.png)

#### 1.1.7、为什么用NoSQL

为什么使用NoSQL ? 

今天我们可以通过第三方平台（如：Google,Facebook等）可以很容易的访问和抓取数据。用户的个人信息，社交网络，地理位置，用户生成的数据和用户操作日志已经成倍的增加。 我们如果要对这些用户数据进行挖掘，那SQL数据库已经不适合这些应用了 , NoSQL数据库的发展也却能很好的处理这些大的数据。 

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/6.png)

### 1.2、NoSQL是什么

NoSQL(NoSQL = Not Only SQL )，意即“不仅仅是SQL”， 

泛指非关系型的数据库 。随着互联网web2.0网站的兴起，传统的关系数据库在应付web2.0网站，特别是超大规模和高并发的SNS类型的web2.0纯动态网站已经显得力不从心，暴露了很多难以克服的问题，而非关系型的数据库则由于其本身的特点得到了非常迅速的发展。NoSQL数据库的产生就是为了解决大规模数据集合多重数据种类带来的挑战，尤其是大数据应用难题，包括超大规模数据的存储。 

（例如谷歌或Facebook每天为他们的用户收集万亿比特的数据）。 这些类型的数据存储不需要固定的模式，无需多余操作就可以横向扩展。 

### 1.3、NoSQL能干嘛

#### 1.3.1、易扩展

NoSQL数据库种类繁多，但是一个共同的特点都是去掉关系数据库的关系型特性。 

数据之间无关系，这样就非常容易扩展。也无形之间，在架构的层面上带来了可扩展的能力。 

#### 1.3.2、大数据量高性能

NoSQL数据库都具有非常高的读写性能，尤其在大数据量下，同样表现优秀。 

这得益于它的无关系性，数据库的结构简单。 

一般MySQL使用Query Cache，每次表的更新Cache就失效，是一种大粒度的Cache， 

在针对web2.0的交互频繁的应用，Cache性能不高。而NoSQL的Cache是记录级的， 

是一种细粒度的Cache，所以NoSQL在这个层面上来说就要性能高很多了 

#### 1.3.3、多样灵活的数据模型

NoSQL无需事先为要存储的数据建立字段，随时可以存储自定义的数据格式。而在关系数据库里， 

增删字段是一件非常麻烦的事情。如果是非常大数据量的表，增加字段简直就是一个噩梦 

#### 1.3.4、传统RDBMS VS NOSQL

**RDBMS vs NoSQL** 

RDBMS 

- 高度组织化结构化数据 
- 结构化查询语言（SQL） 
- 数据和关系都存储在单独的表中。 
- 数据操纵语言，数据定义语言 
- 严格的一致性 
- 基础事务 



NoSQL 

- 代表着不仅仅是SQL 
- 没有声明性查询语言 
- 没有预定义的模式 
- 键 - 值对存储，列存储，文档存储，图形数据库 
- 最终一致性，而非ACID属性 
- 非结构化和不可预知的数据 
- CAP定理 
- 高性能，高可用性和可伸缩性 

### 1.4、NoSQL去哪下

Redis： **Redis**是一个使用[ANSI C](https://zh.wikipedia.org/wiki/ANSI_C)编写的[开源](https://zh.wikipedia.org/wiki/开源)、支持[网络](https://zh.wikipedia.org/wiki/电脑网络)、基于[内存](https://zh.wikipedia.org/wiki/内存)、可选[持久性](https://zh.wikipedia.org/w/index.php?title=持久性_(数据库)&action=edit&redlink=1)的[键值对存储数据库](https://zh.wikipedia.org/wiki/键值-值数据库)。从2015年6月开始，Redis的开发由[Redis Labs](https://zh.wikipedia.org/w/index.php?title=Redis_Labs&action=edit&redlink=1)赞助，而2013年5月至2015年6月期间，其开发由[Pivotal](https://zh.wikipedia.org/wiki/Pivotal)赞助。[[3\]](https://zh.wikipedia.org/wiki/Redis#cite_note-3)在2013年5月之前，其开发由[VMware](https://zh.wikipedia.org/wiki/VMware)赞助。[[4\]](https://zh.wikipedia.org/wiki/Redis#cite_note-4)[[5\]](https://zh.wikipedia.org/wiki/Redis#cite_note-5)根据月度排行网站DB-Engines.com的数据，Redis是最流行的键值对存储数据库。[[6\]](https://zh.wikipedia.org/wiki/Redis#cite_note-6)

Memcache： **memcache**是一套[分布式](https://baike.baidu.com/item/分布式/19276232)的高速缓存系统，由[LiveJournal](https://baike.baidu.com/item/LiveJournal)的Brad Fitzpatrick开发，但目前被许多网站使用以提升网站的访问速度，尤其对于一些大型的、需要频繁访问[数据库](https://baike.baidu.com/item/数据库/103728)的网站访问速度提升效果十分显著 [1] 。这是一套[开放源代码](https://baike.baidu.com/item/开放源代码)[软件](https://baike.baidu.com/item/软件)，以BSD license授权发布。

Mongdb：MongoDB是一个基于分布式文件存储 [1] 的数据库。由[C++](https://baike.baidu.com/item/C%2B%2B)语言编写。旨在为WEB应用提供可扩展的高性能数据存储解决方案。MongoDB是一个介于[关系数据库](https://baike.baidu.com/item/关系数据库)和非关系数据库之间的产品，是非关系数据库当中功能最丰富，最像关系数据库的。它支持的数据结构非常松散，是类似[json](https://baike.baidu.com/item/json)的[bson](https://baike.baidu.com/item/bson)格式，因此可以存储比较复杂的数据类型。Mongo最大的特点是它支持的查询语言非常强大，其语法有点类似于面向对象的查询语言，几乎可以实现类似关系数据库单表查询的绝大部分功能，而且还支持对数据建立[索引](https://baike.baidu.com/item/索引)。

### 1.5、NoSQL怎么玩

KV、Cache、Persistence...

## 2、3V+3高

### 2.1、大数据时代的3V

- 海量Volume

- 多样Variety

- 实时Velocity

### 2.2、互联网需求的3高

- 高并发
- 高可扩
- 高性能

## 3、当下的NoSQL经典应用

### 3.1、当下的应用是sql和nosql一起使用

并不是NoSQL就可以完全取代SQL...

### 3.2、阿里巴巴中文站商品信息如何存放

#### 看看阿里巴巴中文网站首页(以女装/女包包为例)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/7.png)

#### 架构发展历程

演变过程

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/8.png)

#### 第5代

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/9.png)

#### 第5代架构使命

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/10.png)

......

#### 和我们相关的，多数据源多数据类型的存储问题

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/11.png)

#### 3.2.1、商品基本信息

- 名称、价格，出厂日期，生产厂商等

- 关系型数据库：mysql/oracle目前淘宝在去O化(也即拿掉Oracle)，**注意，淘宝内部用的Mysql是里面的大牛自己改造过的**！
  - 为什么去IOE
    - ![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/12.png)
    - 2008年，王坚加盟阿里巴巴成为集团首席架构师，即现在的首席技术官。这位前微软亚洲研究院常务副院长被马云定位为：将帮助阿里巴巴集团建立世界级的技术团队，并负责集团技术架构以及基础技术平台搭建。 
    - 在加入阿里后，带着技术基因和学者风范的王坚就在阿里巴巴集团提出了被称为“去IOE”（在IT建设过程中，去除IBM小型机、Oracle数据库及EMC存储设备）的想法，并开始把云计算的本质，植入阿里IT基因。 
    - 王坚这样概括“去IOE”运动和阿里云之间的关系：“去IOE”彻底改变了阿里集团IT架构的基础，是阿里拥抱云计算，产出计算服务的基础。“去IOE”的本质是分布化，让随处可以买到的Commodity PC架构成为可能，使云计算能够落地的首要条件。 

#### 3.2.2、商品描述、详情、评价信息(多文字类)

- 多文字信息描述类，IO读写性能变差
- 文档数据库MongDB中

#### 3.2.3、商品的图片

- 商品图片展现类
- 分布式的文件系统中
  - 淘宝自己的TFS
  - Google的GFS
  - Hadoop的HDFS

#### 3.2.4、商品的关键字

- 搜索引擎，淘宝内用

- ISearch

#### 3.2.5、商品的波段性的热点高频信息

- 内存数据库

- Tair、Redis、Memcache

#### 3.2.6、商品的交易、价格计算、积分累计

- 外部系统，外部第3方支付接口
- 支付宝

#### *总结大型互联网应用(大数据、高并发、多样数据类型)的难点和解决方案

- 难点
  - 数据类型多样性
  - 数据源多样性和变化重构
  - 数据源改造而数据服务平台不需要大面积重构

- 解决办法
  - 阿里、淘宝干了什么？UDSL
    - 是什么
    - ![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/13.png)
    - 什么样
    - ![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/14.png)
    - 映射
    - ![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/15.png)
    - API
    - ![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/16.png)
    - 热点缓存
    - ![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/17.png)
    - ...

## 4、NoSQL数据模型简介

### 4.1、以一个电商客户、订单、订购、地址模型来对比下关系型数据库和非关系型数据库

#### 4.1.1、传统的关系型数据库你如何设计？

ER图(1:1/1:N/N:N,主外键等常见)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/18.png)

#### 4.1.2、Nosql你如何设计

**什么是BSON**

- BSON（）是一种类json的一种二进制形式的存储格式，简称Binary JSON， 它和JSON一样，支持内嵌的文档对象和数组对象 

**用BSon画出构建的数据模型**

```bson
{ 
 "customer":{ 
   "id":1136, 
   "name":"Z3", 
   "billingAddress":[{"city":"beijing"}], 
   "orders":[ 
    { 
      "id":17, 
      "customerId":1136, 
      "orderItems":[{"productId":27,"price":77.5,"productName":"thinking in java"}], 
      "shippingAddress":[{"city":"beijing"}] 
      "orderPayment":[{"ccinfo":"111-222-333","txnid":"asdfadcd334","billingAddress":{"city":"beijing"}}], 
      } 
    ] 
  } 
} 

```

#### 4.1.3、两者对比，问题和难点

为什么上述的情况可以用聚合模型来处理

- 高并发的操作是不太建议有关联查询的
- 互联网公司用冗余数据来避免关联查询
- 分布式事务是支持不了太多的并发的

想想关系模型数据库你如何查？

如果按照我们新设计的BSon，是不是查询起来很可爱

### 4.2、聚合模型

#### 4.2.1、KV键值

#### 4.2.2、Bson

#### 4.2.3、列族

顾名思义，是按列存储数据的。最大的特点是方便存储结构化和半结构化数据，方便做数据压缩， 

对针对某一列或者某几列的查询有非常大的IO优势。 

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/19.png)

#### 4.2.4、图形

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/20.png)

## 5、**NoSQL**数据库的四大分类

### 5.1、KV键值：典型介绍

- 新浪：BerkeleyDB+redis

- 美团：redis+tair

- 阿里、百度：memcache+redis

### 5.2、文档型数据库(bson格式比较多)：典型介绍

- CouchDB

- MongoDB

  - MongoDB 是一个 基于分布式文件存储的数据库 。由 C++ 语言编写。旨在为 WEB 应用提供可扩展的高性能数据存储解决方案。 

    MongoDB 是一个介于关系数据库和非关系数据库之间的产品，是非关系数据库当中功能最丰富，最像关系数据库的。 

### 5.3、列存储数据库

- Cassandra, HBase
- 分布式文件系统

### 5.4、图关系数据库

- 它不是放图形的，放的是关系比如:朋友圈社交网络、广告推荐系统
- 社交网络，推荐系统等。专注于构建关系图谱
- Neo4J, InfoGrid

### 5.5、四者对比

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/21.png)

## 6、在分布式数据库中CAP原理CAP+BASE

### 6.1、传统的ACID分别是什么

关系型数据库遵循ACID规则 

事务在英文中是transaction，和现实世界中的交易很类似，它有如下四个特性： 

1、A (Atomicity) 原子性 

原子性很容易理解，也就是说事务里的所有操作要么全部做完，要么都不做，事务成功的条件是事务里的所有操作都成功，只要有一个操作失败，整个事务就失败，需要回滚。比如银行转账，从A账户转100元至B账户，分为两个步骤：1）从A账户取100元；2）存入100元至B账户。这两步要么一起完成，要么一起不完成，如果只完成第一步，第二步失败，钱会莫名其妙少了100元。 

2、C (Consistency) 一致性 

一致性也比较容易理解，也就是说数据库要一直处于一致的状态，事务的运行不会改变数据库原本的一致性约束。 

3、I (Isolation) 独立性 

所谓的独立性是指并发的事务之间不会互相影响，如果一个事务要访问的数据正在被另外一个事务修改，只要另外一个事务未提交，它所访问的数据就不受未提交事务的影响。比如现有有个交易是从A账户转100元至B账户，在这个交易还未完成的情况下，如果此时B查询自己的账户，是看不到新增加的100元的 

4、D (Durability) 持久性 

持久性是指一旦事务提交后，它所做的修改将会永久的保存在数据库上，即使出现宕机也不会丢失。 

### 6.2、CAP

1、C:Consistency（强一致性）

​	All nodes see the same data at the same time.

​	所有节点在同一时刻都能看到相同的数据。

2、A:Availability（可用性）

​	Every request gets a response on success/failure.

​	每个请求都能得到成功或者失败的响应。

3、P:Partition tolerance（分区容错性）

​	System continues to work despite message loss or partial failure.

​	出现消息丢失或者分区错误时系统能够继续运行。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/22.png)

### 6.3、CAP的3进2

CAP理论就是说在分布式存储系统中，最多只能实现上面的两点。 

而由于当前的网络硬件肯定会出现延迟丢包等问题，所以分区容忍性是我们必须需要实现的。 

所以我们只能在 一致性 和 可用性 之间进行权衡， 没有NoSQL系统能同时保证这三点。

=======================================================================================================================

C:强一致性 A：高可用性 P：分布式容忍性

 CA 传统Oracle数据库 

 AP 大多数网站架构的选择 

 CP Redis、Mongodb 

 注意：分布式架构的时候必须做出取舍。 

一致性和可用性之间取一个平衡。多余大多数web应用，其实并不需要强一致性。 

因此牺牲C换取P，这是目前分布式数据库产品的方向 

=======================================================================================================================

一致性与可用性的决择 

对于web2.0网站来说，关系数据库的很多主要特性却往往无用武之地 

数据库事务一致性需求 

　　很多web实时系统并不要求严格的数据库事务，对读一致性的要求很低， 有些场合对写一致性要求并不高。允许实现最终一致性。 

数据库的写实时性和读实时性需求 

　　对关系数据库来说，插入一条数据之后立刻查询，是肯定可以读出来这条数据的，但是对于很多web应用来说，并不要求这么高的实时性，比方说发一条消息之 后，过几秒乃至十几秒之后，我的订阅者才看到这条动态是完全可以接受的。 

对复杂的SQL查询，特别是多表关联查询的需求 

　　任何大数据量的web系统，都非常忌讳多个大表的关联查询，以及复杂的数据分析类型的报表查询，特别是SNS类型的网站，从需求以及产品设计角 度，就避免了这种情况的产生。往往更多的只是单表的主键查询，以及单表的简单条件分页查询，SQL的功能被极大的弱化了。 

### 6.4、经典CAP图

CAP理论的核心是：一个分布式系统不可能同时很好的满足一致性，可用性和分区容错性这三个需求， 最多只能同时较好的满足两个。 

因此，根据 CAP 原理将 NoSQL 数据库分成了满足 CA 原则、满足 CP 原则和满足 AP 原则三 大类： 

CA - 单点集群，满足一致性，可用性的系统，通常在可扩展性上不太强大。 

CP - 满足一致性，分区容忍必的系统，通常性能不是特别高。 

AP - 满足可用性，分区容忍性的系统，通常可能对一致性要求低一些。 

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/23.png)

### 6.5、BASE

BASE就是为了解决关系数据库强一致性引起的问题而引起的可用性降低而提出的解决方案。 



BASE其实是下面三个术语的缩写： 

  基本可用（Basically Available） 

  软状态（Soft state） 

  最终一致（Eventually consistent） 



它的思想是通过让系统放松对某一时刻数据一致性的要求来换取系统整体伸缩性和性能上改观。为什么这么说呢，缘由就在于大型系统往往由于地域分布和极高性能的要求，不可能采用分布式事务来完成这些指标，要想获得这些指标，我们必须采用另外一种方式来完成，这里BASE就是解决这个问题的办法 

### 6.6、分布式+集群简介

分布式系统（distributed system） 

​	由多台计算机和通信的软件组件通过计算机网络连接（本地网络或广域网）组成。分布式系统是建立在网络之上的软件系统。正是因为软件的特性，所以分布式系统具有高度的内聚性和透明性。因此，网络和分布式系统之间的区别更多的在于高层软件（特别是操作系统），而不是硬件。分布式系统可以应用在在不同的平台上如：Pc、工作站、局域网和广域网上等。 

简单来讲： 

1. 分布式：不同的多台服务器上面部署不同的服务模块（工程），他们之间通过Rpc/Rmi之间通信和调用，对外提供服务和组内协作。 

2. 集群：不同的多台服务器上面部署相同的服务模块，通过分布式调度软件进行统一的调度，对外提供服务和访问。