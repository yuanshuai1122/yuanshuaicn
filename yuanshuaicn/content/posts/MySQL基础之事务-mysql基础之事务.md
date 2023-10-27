---
title: MySQL基础之事务
date: 2021-11-24 18:38:23.971
author:
  name: "yuanshuai"
  link: "https://cloud.tencent.com/developer/user/8180692"
  email: "shuaiyuan1122@gmail.com"
  avatar: "https://aabb-2023.oss-cn-beijing.aliyuncs.com/hjscijg3uw.png"
tags: 
- mysql
- 数据库
---

-----------: | :--: | :--------: | :--: |
| read uncommitted |  √   |     √      |  √   |
|  read committed  |  ×   |     √      |  √   |
| repeatable read  |  ×   |     ×      |  √   |
|   serializable   |  ×   |     ×      |  ×   |

mysql中默认 第三个隔离级别 repeatable read
oracle中默认第二个隔离级别 read committed

### **1. 未提交读(Read uncommitted)。 （写加锁，读不加锁）**

写操作加写锁，读操作不加锁。禁止第一类丢失更新，但是会出现所有其他数据并发问题。

**
**

### **2.提交读(Read committed)。（写加锁，读加锁）**

写操作加写锁，读操作加读锁。禁止第一类丢失更新和脏读。

就是你已经开始读了数据，然后一个事物开始写，然后写的事物不提交的话，是不能进行读的事物，避免了脏读。



### **3.可重复读(Read repeatable)。（写加锁，读加锁）**

对于读操作加读锁到事务结束，其他事务的更新操作只能等到事务结束之后进行。和提交 读的区别在于，

提交读的读操作是加读锁到本次读操作结束，可重复读的锁粒度更大。禁止两类丢失更新，禁止脏读和不可 重复度，但是可能出现幻读.

一个事物读的时候，我们把两次读看成整体，在读的过程中，不允许写的操作，这样就可以禁止不可重复读。就是两次读操作不允许其他事物。

**这是大部分关系数据库的默认 隔离级别。**

### **4.序列化(Serializable)。（对表级读 写加锁）**

## 五、如何设置

读操作加表级读锁至事务结束。可以禁止幻读。

查看隔离级别

```mysql
select @@tx_isolation;
```

设置隔离级别

```mysql
set session|global transaction isolation level 隔离级别;
```

```mysql
开启事务的语句;

update 表 set 张三丰的余额=500 where name='张三丰'

update 表 set 郭襄的余额=1500 where name='郭襄' 

结束事务的语句;
```



```mysql
SHOW VARIABLES LIKE 'autocommit';
SHOW ENGINES;
```

### 1.演示事务的使用步骤

```mysql
#开启事务
SET autocommit=0;
START TRANSACTION;
#编写一组事务的语句
UPDATE account SET balance = 1000 WHERE username='张无忌';
UPDATE account SET balance = 1000 WHERE username='赵敏';

#结束事务
ROLLBACK;
#commit;

SELECT * FROM account;
```

### 2.演示事务对于delete和truncate的处理的区别

```mysql
SET autocommit=0;
START TRANSACTION;

DELETE FROM account;
ROLLBACK;
```

### 3.演示savepoint 的使用

```mysql
SET autocommit=0;
START TRANSACTION;
DELETE FROM account WHERE id=25;
SAVEPOINT a;#设置保存点
DELETE FROM account WHERE id=28;
ROLLBACK TO a;#回滚到保存点


SELECT * FROM account;
```