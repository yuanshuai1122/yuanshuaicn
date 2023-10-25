---
title: Redis之数据类型
date: 2021-posts-26 21:30:42.31
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/1498119653712274.png'
theme: 'light'
tags: 
- 数据库
- Redis
---

>当前库就没有了，被移除了
- expire key 秒钟：为给定的key设置过期时间
- ttl key 查看还有多少秒过期，-1表示永不过期，-2表示已过期
- type key 查看你的key是什么类型

# 2、**Redis**字符串(String)

## 2.1、常用

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/37.png)

## 2.2、单值单value

案例

- set/get/del/append/strlen

- Incr/decr/incrby/decrby,一定要是数字才能进行加减

- getrange/setrange

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/38.png)

- setex(set with expire)键秒值/setnx(set if not exist)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/39.png)

- mset/mget/msetnx

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/40.png)

- getset(先get再set)

getset:将给定 key 的值设为 value ，并返回 key 的旧值(old value)。 

简单一句话，先get然后立即set 

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/41.png)

# **5、Redis**列表(List)

## 4.1、常用

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/42.png)

## 4.2、单值多value

案例

- lpush/rpush/lrange

- lpop/rpop

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/43.png)

- lindex，按照索引下标获得元素(从上到下)

通过索引获取列表中的元素  lindex key index 

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/44.png)

- llen

-  lrem key 删N个value

 \* 从left往right删除2个值等于v1的元素，返回的值为实际删除的数量 

 \* LREM list3 0 值，表示删除全部给定的值。 零个就是全部值 

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/45.png)

- trim key 开始index 结束index，截取指定范围的值后再赋值给key

ltrim：截取指定索引区间的元素，格式是ltrim list的key 起始索引 结束索引 

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/46.png)

-  rpoplpush 源列表 目的列表

移除列表的最后一个元素，并将该元素添加到另一个列表并返回 

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/47.png)

- lset key index value

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/48.png)

- linsert key before/after 值1 值2

在list某个已有值的前后再添加具体值 

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/49.png)

- 性能总结
  - 它是一个字符串链表，left、right都可以插入添加； 
  - 如果键不存在，创建新的链表； 
  - 如果键已存在，新增内容； 
  - 如果值全移除，对应的键也就消失了。 
  - 链表的操作无论是头和尾效率都极高，但假如是对中间元素进行操作，效率就很惨淡了。 

# **6、Redis**集合(Set)

## 6.1、常用

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/51.png)

## 6.2、单值多value

## 6.3、案例

- sadd/smembers/sismember

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/52.png)

- scard，获取集合里面的元素个数

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/53.png)

-  srem key value 删除集合中元素

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/54.png)

- srandmember key 某个整数(随机出几个数)

 \*  从set集合里面随机取出2个 

 \*  如果超过最大数量就全部取出， 

 \*  如果写的值是负数，比如-3 ，表示需要取出3个，但是可能会有重复值。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/55.png)

- spop key 随机出栈

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/56.png)

-  smove key1 key2 在key1里某个值   作用是将key1里的某个值赋给key2

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/57.png)

- 数学集合类
  - 差集：sdiff

在第一个set里面而不在后面任何一个set里面的项 

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/58.png)

- 
  - 交集：sinter

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/59.png)

- 
  - 并集：sunion

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/60.png)

# 7、**Redis**哈希(Hash)

## 7.1、常用

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/61.png)

## 7.2、KV模式不变，但V是一个键值对

## 7.3、案例

- hset/hget/hmset/hmget/hgetall/hdel

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/62.png)

- hlen

- hexists key 在key里面的某个值的key
- hkeys/hvals

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/63.png)

- hincrby/hincrbyfloat

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/64.png)

- hsetnx

不存在赋值，存在了无效。 

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/65.png)

# 8、**Redis**有序集合Zset(sorted set)

在set基础上，加一个score值。

之前set是k1 v1 v2 v3，

现在zset是k1 score1 v1 score2 v2

## 8.1、常用

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/66.png)

## 8.2、案例

- zadd/zrange

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/67.png)

- zrangebyscore key 开始score 结束score

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/68.png)

- zrem key 某score下对应的value值，作用是删除元素

删除元素，格式是zrem zset的key 项的值，项的值可以是多个 

zrem key score某个对应值，可以是多个值 

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/69.png)

- zcard/zcount key score区间/zrank key values值，作用是获得下标值/zscore key 对应值,获得分数

  - zcard ：获取集合中元素个数 

  ![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/70.png)
  - zcount ：获取分数区间内元素个数，zcount key 开始分数区间 结束分数区间
  - ![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/71.png)
  - zrank： 获取value在zset中的下标位置 

  ![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/72.png)
  
  - zscore：按照值获得对应的分数 

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/73.png)

- zrevrank key values值，作用是逆序获得下标值

正序、逆序获得下标索引值 

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/74.png)

- zrevrange

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/75.png)

- zrevrangebyscore key 结束score 开始score

zrevrangebyscore zset1 90 60 withscores  分数是反着来的 

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/redis/76.png)