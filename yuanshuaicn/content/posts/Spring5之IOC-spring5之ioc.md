---
title: Spring5之IOC
date: 2021-11-26 21:31:35.513
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/spring2220202.png'
theme: 'light'
tags: 
- Spring5
- Java
---

# 一、概念和原理

## 1、什么是 IOC

 (1)控制反转，把对象创建和对象之间的调用过程，交给 Spring 进行管理

 (2)使用 IOC 目的:为了耦合度降低

 (3)做入门案例就是 IOC 实现

## 2、IOC 底层原理

 (1)xml 解析、工厂模式、反射

## 3、画图讲解 IOC 底层原理

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/spring5/16.png)

# **二、BeanFactory** 接口

### 1、IOC 思想基于 IOC 容器完成，IOC 容器底层就是对象工厂

### 2、Spring 提供 IOC 容器实现两种方式:(两个接口)

 (1)BeanFactory:IOC 容器基本实现，是 Spring 内部的使用接口，不提供开发人员进行使用 

* 加载配置文件时候不会创建对象，在获取对象(使用)才去创建对象

(2)ApplicationContext:BeanFactory 接口的子接口，提供更多更强大的功能，一般由开发人 员进行使用

- 加载配置文件时候就会把在配置文件对象进行创建

### 3、ApplicationContext 接口有实现类

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/spring5/17.png)