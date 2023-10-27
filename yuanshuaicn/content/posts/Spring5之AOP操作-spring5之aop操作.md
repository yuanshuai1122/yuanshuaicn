---
title: Spring5之AOP操作
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

--------- | ---------------------------------------- |
| **后置通知** | **记录日志(方法已经成功调用)**           |
| **异常通知** | **异常处理 控制事务**                    |
| **最终通知** | **记录日志(方法已经调用，但不一定成功)** |

### **5**、相同的切入点抽取

```java
 //相同切入点抽取
    @Pointcut(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
    public void pointdemo() {

    }
```

那么通知表达式就直接可以使用pointdemo()

```java
 //前置通知
    //@Before注解表示作为前置通知
    @Before(value = "pointdemo()")
    public void before() {
        System.out.println("before.........");
    }
```

#### **6**、有多个增强类多同一个方法进行增强，设置增强类优先级 

(1)在增强类上面添加注解 @Order(数字类型值)，数字类型值越小优先级越高

```java
@Component
@Aspect
@Order(1)
public class PersonProxy{
  
}

```

#### 7、完全使用注解开发

(1)创建配置类，不需要创建 xml 配置文件

```java
@Configuration
@ComponentScan(basePackages = {"com.atguigu"}) 
@EnableAspectJAutoProxy(proxyTargetClass = true) 
public class ConfigAop {
}
 
```

# **三、AspectJ** 配置文件

#### **1**、创建两个类，增强类和被增强类，创建方法

#### **2**、在 **spring** 配置文件中创建两个类对象

```xml
<!--创建对象-->
<bean id="book" class="com.atguigu.spring5.aopxml.Book"></bean>
<bean id="bookProxy" class="com.atguigu.spring5.aopxml.BookProxy"></bean>
```

#### **3**、在 **spring** 配置文件中配置切入点

```java
<aop:config> 
  <!--切入点-->
 <aop:pointcut id="p" expression="execution(*com.atguigu.spring5.aopxml.Book.buy(..))"/>
<!--配置切面-->
<aop:aspect ref="bookProxy"> 
  <!--增强作用在具体的方法上-->
  <aop:before method="before" pointcut-ref="p"/>
 </aop:aspect>
</aop:config>
```