---
title: Spring5之事务
date: 2021-08-24 18:38:23.971
author:
  name: "yuanshuai"
  link: "https://cloud.tencent.com/developer/user/8180692"
  email: "shuaiyuan1122@gmail.com"
  avatar: "https://aabb-2023.oss-cn-beijing.aliyuncs.com/hjscijg3uw.png"
tags: 
- Spring5
- Java
---

# 一、事务概念

## **1**、什么是事务

 (1)事务是数据库操作最基本单元，逻辑上一组操作，要么都成功，如果有一个失败所有操 作都失败

 (2)典型场景:银行转账

- lucy转账100元给mary
- lucy 少 100，mary 多 100

## **2**、事务四个特性(**ACID**) 

(1)原子性

(2)一致性

(3)隔离性

(4)持久性

# 二、搭建事务操作环境

![31](img/31.png)

### **1**、创建数据库表，添加记录

![32](img/32.png)

### **2**、创建 **service**，搭建 **dao**，完成对象创建和注入关系

(1)service 注入 dao，在 dao 注入 JdbcTemplate，在 JdbcTemplate 注入 DataSource

```java
@Service
public class UserService { 
  	//注入 dao
		@Autowired
    private UserDao userDao;
}
```

```java
@Repository
public class UserDaoImpl implements UserDao { 
  @Autowired
  private JdbcTemplate jdbcTemplate;
}
```

### **3**、在 **dao** 创建两个方法:多钱和少钱的方法，在 **service** 创建方法(转账的方法)

```java
@Repository
public class UserDaoImpl implements UserDao { 
  @Autowired
  private JdbcTemplate jdbcTemplate;
	//lucy 转账 100 给 mary 
  //少钱
	@Override
	public void reduceMoney() {
		String sql = "update t_account set money=money-? where username=?"; 
    jdbcTemplate.update(sql,100,"lucy");
}
	//多钱
	@Override
	public void addMoney() {
		String sql = "update t_account set money=money+? where username=?"; 
    jdbcTemplate.update(sql,100,"mary");
	} 
}
```

```java
@Service
public class UserService { 
  //注入 dao
	@Autowired
  private UserDao userDao;
	//转账的方法
	public void accountMoney() { 
    //lucy 少 100
		userDao.reduceMoney();
		//mary 多 100
    userDao.addMoney(); 
  }
}
```

### **4**、上面代码，如果正常执行没有问题的，但是如果代码执行过程中出现异常，有问题

```java
@Service
public class UserService { 
  //注入 dao
	@Autowired
  private UserDao userDao;
	//转账的方法
	public void accountMoney() { 
    //lucy 少 100
		userDao.reduceMoney();
    
    //模拟异常
    int i = 10/0;
    
		//mary 多 100
    userDao.addMoney(); 
  }
}
```

#### (1)上面问题如何解决呢? 

- 使用事务进行解决

#### (2)事务操作过程

```java
    //转账的方法
    public void accountMoney() {
        try {
            //第一步 开启事务

            //第二步 进行业务操作
            //lucy少100
            userDao.reduceMoney();

            //模拟异常
            int i = 10/0;

            //mary多100
            userDao.addMoney();

            //第三步 没有发生异常，提交事务
        }catch(Exception e) {
            //第四步 出现异常，事务回滚
        }
    }
```

# 三、**Spring** 事务管理介绍

## **1**、事务添加到 **JavaEE** 三层结构里面 **Service** 层(业务逻辑层) 

## **2**、在 **Spring** 进行事务管理操作

### (**1**)有两种方式:编程式事务管理和声明式事务管理(使用)

## **3**、声明式事务管理 

### (**1**)基于注解方式(使用) 

### (2)基于 xml 配置文件方式

## **4**、在 **Spring** 进行声明式事务管理，底层使用 **AOP** 原理

## **5**、**Spring** 事务管理 **API** 

### (1)提供一个接口，代表事务管理器，这个接口针对不同的框架提供不同的实现类

![33](img/33.png)

# 四、注解声明式事务管理

## **1**、在 **spring** 配置文件配置事务管理器

```xml
<!--创建事务管理器-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <!--注入数据源-->
        <property name="dataSource" ref="dataSource"></property>
    </bean>
```

## 2、在 **spring** 配置文件，开启事务注解 

(1)在 spring 配置文件引入名称空间 tx

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
                        http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd
                        http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">

```

(2)开启事务注解

```xml
<!--开启事务注解-->
    <tx:annotation-driven transaction-manager="transactionManager"></tx:annotation-driven>
```

## **3**、在 **service** 类上面(或者 **service** 类里面方法上面)添加事务注解 

(1)@Transactional，这个注解添加到类上面，也可以添加方法上面 

(2)如果把这个注解添加类上面，这个类里面所有的方法都添加事务 

(3)如果把这个注解添加方法上面，为这个方法添加事务

```java
@Service
@Transactional
public class UserService {
 ....
}
```

# 五、声明式事务管理参数配置

### **1**、在 **service** 类上面添加注解**@Transactional**，在这个注解里面可以配置事务相关参数

![34](img/34.png)

### **2**、**propagation**:事务传播行为 

(**1**)多事务方法直接进行调用，这个过程中事务是如何进行管理的

![35](img/35.png)

![事务传播行为](img/事务传播行为.bmp)

我们只需要掌握前两种常用的行为即可！

(2)添加方法

```java
@Service
@Transactional(propagation = Propagation.REQUIRED)
public class UserService {
  ...
}
```

### **3**、**ioslation**:事务隔离级别 

#### (1)事务有特性成为隔离性，多事务操作之间不会产生影响。不考虑隔离性产生很多问题 

#### (2)有三个读问题:脏读、不可重复读、虚(幻)读 

#### (3)脏读:一个未提交事务读取到另一个未提交事务的数据

![36](img/36.png)

东方不败和岳不群分别开启了事务A、B，岳不群将数值5000 update 到60000，此时东方不败select数值为60000，接着岳不群并未commit，而是rollback，数值又回到了5000，可是东方不败读取的数值却为60000（数据库中的实际数值仍为5000），这就是脏读。

#### (4)不可重复读:一个未提交事务读取到另一提交事务修改数据

![37](img/37.png)

东方不败和岳不群分别开启了事务A、B，东方不败和岳不群同时读取到数据5000，接着岳不群将5000 update到900 ，并且立刻commit（此时数据库中的数值已经成了900），此时东方不败又读取数据，却拿到900，两次读取结果不一样，这就是不可重复读。

#### (5)虚读:一个未提交事务读取到另一提交事务添加数据

和脏读类似。

#### (6)解决:通过设置事务隔离级别，解决读问题

![事务隔离级别](img/事务隔离级别.bmp)

#### (7)添加事务隔离的方法

```java
@Service
@Transactional(isolation = Isolation.REPEATABLE_READ)
public class UserService {
  ...
}
```

### **4**、**timeout**:超时时间 

(1)事务需要在一定时间内进行提交，如果不提交进行回滚 

(2)默认值是 -1 ，设置时间以秒单位进行计算

(3)添加方法

```java
@Service
@Transactional(timeout = -1)
public class UserService {
  ...
}
```

### **5**、**readOnly**:是否只读 

(1)读:查询操作，写:添加修改删除操作

(2)readOnly 默认值 false，表示可以查询，可以添加修改删除操作 

(3)设置 readOnly 值是 true，设置成 true 之后，只能查询

```java
@Service
@Transactional(readOnly = false)
public class UserService {
  ...
}
```

### **6**、**rollbackFor**:回滚 

(1)设置出现哪些异常进行事务回滚

### **7**、**noRollbackFor**:不回滚

(1)设置出现哪些异常不进行事务回滚

# 六、**XML** 声明式事务管理

**1**、在 **spring** 配置文件中进行配置

第一步 配置事务管理器 

第二步 配置通知

第三步 配置切入点和切面

```xml
<!--1 创建事务管理器-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <!--注入数据源-->
        <property name="dataSource" ref="dataSource"></property>
    </bean>

    <!--2 配置通知-->
    <tx:advice id="txadvice">
        <!--配置事务参数-->
        <tx:attributes>
            <!--指定哪种规则的方法上面添加事务-->
            <tx:method name="accountMoney" propagation="REQUIRED"/>
            <!--<tx:method name="account*"/>-->
        </tx:attributes>
    </tx:advice>

    <!--3 配置切入点和切面-->
    <aop:config>
        <!--配置切入点-->
        <aop:pointcut id="pt" expression="execution(* com.atguigu.spring5.service.UserService.*(..))"/>
        <!--配置切面-->
        <aop:advisor advice-ref="txadvice" pointcut-ref="pt"/>
    </aop:config>
```

# 七、完全注解声明式事务管理

**1**、创建配置类，使用配置类替代 **xml** 配置文件

```java
@Configuration //配置类
@ComponentScan(basePackages = "com.atguigu") //组件扫描
@EnableTransactionManagement //开启事务
public class TxConfig {

    //创建数据库连接池
    @Bean
    public DruidDataSource getDruidDataSource() {
        DruidDataSource dataSource = new DruidDataSource();
        dataSource.setDriverClassName("com.mysql.jdbc.Driver");
        dataSource.setUrl("jdbc:mysql:///user_db");
        dataSource.setUsername("root");
        dataSource.setPassword("root");
        return dataSource;
    }

    //创建JdbcTemplate对象
    @Bean
    public JdbcTemplate getJdbcTemplate(DataSource dataSource) {
        //到ioc容器中根据类型找到dataSource
        JdbcTemplate jdbcTemplate = new JdbcTemplate();
        //注入dataSource
        jdbcTemplate.setDataSource(dataSource);
        return jdbcTemplate;
    }

    //创建事务管理器
    @Bean
    public DataSourceTransactionManager getDataSourceTransactionManager(DataSource dataSource) {
        DataSourceTransactionManager transactionManager = new DataSourceTransactionManager();
        transactionManager.setDataSource(dataSource);
        return transactionManager;
    }
}
```