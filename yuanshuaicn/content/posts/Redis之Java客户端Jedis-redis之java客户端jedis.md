---
title: Redis之Java客户端Jedis
date: 2021-posts-26 21:31:04.109
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/1498119653712274.png'
theme: 'light'
tags: 
- 数据库
- Redis
---

-------------------------------------" ); 
     jedis.mset( "str1" , "v1" , "str2" , "v2" , "str3" , "v3" ); 
     System. out .println(jedis.mget( "str1" , "str2" , "str3" )); 
      //list 
     System. out .println( "----------------------------------------" ); 
      //jedis.lpush(" mylist ","v1","v2","v3","v4","v5"); 
     List<String> list = jedis.lrange( "mylist" ,0,-1); 
      for  (String element : list) { 
       System. out .println(element); 
     } 
      //set 
     jedis.sadd( "orders" , "jd001" ); 
     jedis.sadd( "orders" , "jd002" ); 
     jedis.sadd( "orders" , "jd003" ); 
     Set<String> set1 = jedis.smembers( "orders" ); 
      for  ( Iterator  iterator = set1.iterator(); iterator.hasNext();) { 
       String string = (String) iterator.next(); 
       System. out .println(string); 
     } 
     jedis.srem( "orders" , "jd002" ); 
     System. out .println(jedis.smembers( "orders" ).size()); 
      //hash 
     jedis.hset( "hash1" , "userName" , "lisi" ); 
     System. out .println(jedis.hget( "hash1" , "userName" )); 
     Map<String,String> map =  new  HashMap<String,String>(); 
     map.put( "telphone" , "13811814763" ); 
     map.put( "address" , "atguigu" ); 
     map.put( "email" , "abc@163.com" ); 
     jedis.hmset( "hash2" ,map); 
     List<String> result = jedis.hmget( "hash2" ,  "telphone" , "email" ); 
      for  (String element : result) { 
       System. out .println(element); 
     } 
      // zset 
     jedis.zadd( "zset01" ,60d, "v1" ); 
     jedis.zadd( "zset01" ,70d, "v2" ); 
     jedis.zadd( "zset01" ,80d, "v3" ); 
     jedis.zadd( "zset01" ,90d, "v4" ); 
      
     Set<String> s1 = jedis.zrange( "zset01" ,0,-1); 
      for  ( Iterator  iterator = s1.iterator(); iterator.hasNext();) { 
       String string = (String) iterator.next(); 
       System. out .println(string); 
     } 

           
  } 
} 

```

### 4.3、事务提交

- 日常

```java
package  com.atguigu.redis.test; 
 
import   redis.clients.jedis.Jedis ; 
import  redis.clients.jedis.Response; 
import  redis.clients.jedis.Transaction; 
 
public   class  Test03  
{ 
   public   static   void  main(String[] args)  
  { 
      Jedis  jedis =  new   Jedis ( "127.0.0.1" ,6379); 
      
      //监控key，如果该动了事务就被放弃 
      /*3 
     jedis.watch("serialNum"); 
     jedis.set("serialNum","s#####################"); 
     jedis.unwatch();*/ 
      
     Transaction transaction = jedis.multi(); //被当作一个命令进行执行 
     Response<String> response = transaction.get( "serialNum" ); 
     transaction.set( "serialNum" , "s002" ); 
     response = transaction.get( "serialNum" ); 
     transaction.lpush( "list3" , "a" ); 
     transaction.lpush( "list3" , "b" ); 
     transaction.lpush( "list3" , "c" ); 
      
     transaction.exec(); 
      //2 transaction.discard(); 
     System. out .println( "serialNum***********" +response.get()); 
           
  } 
} 

```

- 加锁

```java
public   class  TestTransaction { 
 
   public   boolean  transMethod() { 
     Jedis jedis =  new  Jedis( "127.0.0.1" , 6379); 
      int  balance; // 可用余额 
      int  debt; // 欠额 
      int  amtToSubtract = 10; // 实刷额度 
 
     jedis.watch( "balance" ); 
      //jedis.set("balance","5");//此句不该出现，讲课方便。模拟其他程序已经修改了该条目 
     balance = Integer. parseInt (jedis.get( "balance" )); 
      if  (balance < amtToSubtract) { 
       jedis.unwatch(); 
       System. out .println( "modify" ); 
        return   false ; 
     }  else  { 
       System. out .println( "***********transaction" ); 
        Transaction  transaction = jedis.multi(); 
       transaction.decrBy( "balance" , amtToSubtract); 
       transaction.incrBy( "debt" , amtToSubtract); 
       transaction.exec(); 
       balance = Integer. parseInt (jedis.get( "balance" )); 
       debt = Integer. parseInt (jedis.get( "debt" )); 
 
       System. out .println( "*******"  + balance); 
       System. out .println( "*******"  + debt); 
        return   true ; 
     } 
  } 
 
   /** 
   * 通俗点讲，watch命令就是标记一个键，如果标记了一个键， 在提交事务前如果该键被别人修改过，那事务就会失败，这种情况通常可以在程序中 
   * 重新再尝试一次。 
   * 首先标记了键balance，然后检查余额是否足够，不足就取消标记，并不做扣减； 足够的话，就启动事务进行更新操作， 
   * 如果在此期间键balance被其它人修改， 那在提交事务（执行 exec ）时就会报错， 程序中通常可以捕获这类错误再重新执行一次，直到成功。 
   */ 
   public   static   void  main(String[] args) { 
     TestTransaction test =  new  TestTransaction(); 
      boolean  retValue = test.transMethod(); 
     System. out .println( "main retValue-------: "  + retValue); 
  } 
} 

```

### 4.4、主从复制

- 6379,6380启动，先各自先独立

- 主写

- 从读

## 5、JedisPool

### 5.1、获取Jedis实例需要从JedisPool中获取

### 5.2、用完Jedis实例需要返还给JedisPool

### 5.2、如果Jedis在使用过程中出错，则也需要还给JedisPool

### 5.4、例见代码

JedisPoolUtil

```java
package  com.atguigu.redis.test; 
 
import  redis.clients.jedis.Jedis; 
import  redis.clients.jedis.JedisPool; 
import  redis.clients.jedis.JedisPoolConfig; 
 
public   class   JedisPoolUtil  { 
   
  private   static   volatile  JedisPool  jedisPool  =  null ; //被volatile修饰的变量不会被本地线程缓存，对该变量的读写都是直接操作共享内存。 
   
   private  JedisPoolUtil() {} 
   
   public   static  JedisPool getJedisPoolInstance() 
 { 
      if ( null  ==  jedisPool ) 
    { 
        synchronized  ( JedisPoolUtil . class ) 
      { 
           if ( null  ==  jedisPool ) 
         { 
           JedisPoolConfig poolConfig =  new  JedisPoolConfig(); 
           poolConfig.setMaxActive(1000); 
           poolConfig.setMaxIdle(32); 
           poolConfig.setMaxWait(100*1000); 
           poolConfig.setTestOnBorrow( true ); 
             
             jedisPool  =  new  JedisPool(poolConfig, "127.0.0.1" ); 
         } 
      } 
    } 
      return   jedisPool ; 
 } 
   
   public   static   void  release(JedisPool jedisPool,Jedis jedis) 
 { 
      if ( null  != jedis) 
    { 
      jedisPool.returnResourceObject(jedis); 
    } 
 } 
} 
 

```

Demo5:JedisPool.getResource();

```java
package  com.atguigu.redis.test; 
 
import  redis.clients.jedis.Jedis; 
import  redis.clients.jedis.JedisPool; 
 
public   class  Test01 { 
   public   static   void  main(String[] args) { 
     JedisPool jedisPool = JedisPoolUtil. getJedisPoolInstance (); 
     Jedis jedis =  null ; 
      
      try   
     { 
       jedis = jedisPool.getResource(); 
       jedis.set( "k18" , "v183" ); 
        
     }  catch  (Exception e) { 
       e.printStackTrace(); 
     } finally { 
       JedisPoolUtil. release (jedisPool, jedis); 
     } 
  } 
} 
 
```

### 5.5、配置总结all

JedisPool的配置参数大部分是由JedisPoolConfig的对应项来赋值的。 

maxActive ：控制一个pool可分配多少个jedis实例，通过pool.getResource()来获取；如果赋值为-1，则表示不限制；如果pool已经分配了maxActive个jedis实例，则此时pool的状态为exhausted。 

maxIdle ：控制一个pool最多有多少个状态为idle(空闲)的jedis实例； 

whenExhaustedAction：表示当pool中的jedis实例都被allocated完时，pool要采取的操作；默认有三种。 

 WHEN_EXHAUSTED_FAIL --> 表示无jedis实例时，直接抛出NoSuchElementException； 

 WHEN_EXHAUSTED_BLOCK --> 则表示阻塞住，或者达到maxWait时抛出JedisConnectionException； 

 WHEN_EXHAUSTED_GROW --> 则表示新建一个jedis实例，也就说设置的maxActive无用； 

maxWait ：表示当borrow一个jedis实例时，最大的等待时间，如果超过等待时间，则直接抛JedisConnectionException； 

testOnBorrow ：获得一个jedis实例的时候是否检查连接可用性（ping()）；如果为true，则得到的jedis实例均是可用的； 

testOnReturn：return 一个jedis实例给pool时，是否检查连接可用性（ping()）； 

testWhileIdle：如果为true，表示有一个idle object evitor线程对idle object进行扫描，如果validate失败，此object会被从pool中drop掉；这一项只有在timeBetweenEvictionRunsMillis大于0时才有意义； 

timeBetweenEvictionRunsMillis：表示idle object evitor两次扫描之间要sleep的毫秒数； 



numTestsPerEvictionRun：表示idle object evitor每次扫描的最多的对象数； 



minEvictableIdleTimeMillis：表示一个对象至少停留在idle状态的最短时间，然后才能被idle object evitor扫描并驱逐；这一项只有在timeBetweenEvictionRunsMillis大于0时才有意义； 

softMinEvictableIdleTimeMillis：在minEvictableIdleTimeMillis基础上，加入了至少minIdle个对象已经在pool里面了。如果为-1，evicted不会根据idle time驱逐任何对象。如果minEvictableIdleTimeMillis>0，则此项设置无意义，且只有在timeBetweenEvictionRunsMillis大于0时才有意义； 

lifo：borrowObject返回对象时，是采用DEFAULT_LIFO（last in first out，即类似cache的最频繁使用队列），如果为False，则表示FIFO队列； 

其中JedisPoolConfig对一些参数的默认设置如下： 

testWhileIdle=true 

minEvictableIdleTimeMills=60000 

timeBetweenEvictionRunsMillis=30000 

numTestsPerEvictionRun=-1