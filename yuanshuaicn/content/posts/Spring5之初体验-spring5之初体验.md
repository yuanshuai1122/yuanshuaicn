---
title: Spring5之初体验
date: 2021-11-26 21:31:32.366
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/spring2220202.png'
theme: 'light'
tags: 
- Spring5
- Java
---

# **一、Spring5** 框架概述

## 1、Spring 是轻量级的开源的 JavaEE 框架 

## 2、Spring 可以解决企业应用开发的复杂性

## 3、Spring 有两个核心部分:IOC 和 Aop 

(1)IOC:控制反转，把创建对象过程交给 Spring 进行管理 

(2)Aop:面向切面，不修改源代码进行功能增强

## 4、Spring 特点 

(1)方便解耦，简化开发 

(2)Aop 编程支持 

(3)方便程序测试 

(4)方便和其他框架进行整合 

(5)方便进行事务操作 

(6)降低 API 开发难度

## 5、现在课程中，选取 Spring 版本 5.x

### **Spring5** 入门案例

#### **1**、下载 Spring5

(1)使用 Spring 最新稳定版本 5.2.6

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/spring5/8.png)

(2)下载地址

https://repo.spring.io/release/org/springframework/spring/

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/spring5/9.png)

打开下载的框架，看目录结构

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/spring5/10.png)

#### **2**、打开 **idea** 工具，创建普通 **Java** 工程

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/spring5/11.png)

#### **3**、导入 **Spring5** 相关 **jar** 包

下载jar包复制到lib目录中

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/spring5/12.png)

手动引入

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/spring5/13.png)

#### **4**、创建普通类，在这个类创建普通方法

```java
public class User {
    public void add() {
        System.out.println("add......"); 
    }
}
```

#### 5、创建 Spring 配置文件，在配置文件配置创建的对象

(1)Spring 配置文件使用 xml 格式

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/spring5/14.png)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--    1 配置User对象创建-->
    <bean id="user" class="com.atguigu.spring5.User"></bean>
```

#### **6**、进行测试代码编写

```java
@Test
public void testAdd() {
//1 加载 spring 配置文件
	ApplicationContext context =
		new ClassPathXmlApplicationContext("bean1.xml");
//2 获取配置创建的对象
	User user = context.getBean("user", User.class); 
  System.out.println(user);
	user.add();
}
```

看输出结果：输出了User的地址和add()方法

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/spring5/15.png)