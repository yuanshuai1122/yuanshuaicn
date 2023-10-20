---
title: SpringMVC之入门
date: 2021-11-26 21:32:18.801
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/0820590.png'
theme: 'light'
tags: 
- Java
- SpringMVC
---

# SpringMVC 的基本概念

## 1、关于三层架构和 MVC

### 1.1、三层架构

我们的开发架构一般都是基于两种形式，一种是 C/S 架构，也就是客户端/服务器，另一种是 B/S 架构，也就是浏览器服务器。

在 JavaEE 开发中，几乎全都是基于 B/S 架构的开发。那么在 B/S 架构中，系统标准的三层架构包括:

- 表现层

- 业务层

- 持久层

三层架构在我们 的实际开发中使用的非常多，所以我们的案例也都是 基于三层架构设计的。

三层架构中，每一层各司其职，接下来我们就说说每层都负责哪些方面:

#### 表现层:

> 也就是我们常说的 web 层。它负责接收客户端请求，向客户端响应结果，通常客户端使用 http 协议请求 web 层，web 需要接收 http 请求，完成 http 响应。
>
> - 表现层包括展示层和控制层:控制层负责接收请求，展示层负责结果的展示。
>
> - 表现层依赖业务层，接收到客户端请求一般会调用业务层进行业务处理，并将处理结果响应给客户端。
>
> - 表现层的设计一般都使用 MVC 模型。(MVC 是表现层的设计模型，和其他层没有关系)

#### 业务层:

> 也就是我们常说的 service 层。它负责业务逻辑处理，和我们开发项目的需求息息相关。web 层依赖业 务层，但是业务层不依赖 web 层。
>
> 业务层在业务处理时可能会依赖持久层，如果要对数据持久化需要保证事务一致性。(也就是我们说的， 事务应该放到业务层来控制)

#### 持久层:

> 也就是我们是常说的 dao 层。负责数据持久化，包括数据层即数据库和数据访问层，数据库是对数据进 行持久化的载体，数据访问层是业务层和持久层交互的接口，业务层需要通过数据访问层将数据持久化到数据库中。通俗的讲，持久层就是和数据库交互，对数据库表进行曾删改查的。

### 1.2、MVC模型

MVC 全名是 Model View Controller，是模型(model)-视图(view)-控制器(controller)的缩写， 是一种用于设计创建 Web 应用程序表现层的模式。MVC 中每个部分各司其职:

Model(模型):

> 通常指的就是我们的数据模型。作用一般情况下用于封装数据。

View(视图):

> 通常指的就是我们的 jsp 或者 html。作用一般就是展示数据的。 
>
> 通常视图是依据模型数据创建的。

Controller(控制器):

> 是应用程序中处理用户交互的部分。作用一般就是处理程序逻辑的。 
>
> 它相对于前两个不是很好理解，这里举个例子:
>
> 例如:
>
> 我们要保存一个用户的信息，该用户信息中包含了姓名，性别，年龄等等。
>
> 这时候表单输入要求年龄必须是 1~100 之间的整数。姓名和性别不能为空。并且把数据填充到模型之中。
>
> 此时除了 js 的校验之外，服务器端也应该有数据准确性的校验，那么校验就是控制器的该做的。
>
> 当校验失败后，由控制器负责把错误页面展示给使用者。
>
> 如果校验成功，也是控制器负责把数据填充到模型，并且调用业务层实现完整的业务需求。

## 2、SpringMVC 概述

### 2.1、SpringMVC是什么

> ​	SpringMVC 是一种基于 Java 的实现 MVC 设计模型的请求驱动类型的轻量级 Web 框架，属于 SpringFrameWork 的后续产品，已经融合在 Spring Web Flow 里面。Spring 框架提供了构建 Web 应用程序的全功能 MVC 模块。使用 Spring 可插入的 MVC 架构，从而在使用Spring进行WEB开发时，可以选择使用 Spring的 Spring MVC 框架或集成其他 MVC 开发框架，如 Struts1(现在一般不用)，Struts2 等。
>
> ​	SpringMVC 已经成为目前最主流的 MVC 框架之一，并且随着 Spring3.0 的发布，全面超越 Struts2，成 为最优秀的 MVC 框架。
>
> ​	它通过一套注解，让一个简单的 Java 类成为处理请求的控制器，而无须实现任何接口。同时它还支持 RESTful 编程风格的请求。

### 2.2、SpringMVC在三层架构的位置

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/1.png)

### 2.3、SpringMVC的优势

1、清晰的角色划分: 

- 前端控制器(DispatcherServlet)
- 请求到处理器映射(HandlerMapping) 
- 处理器适配器(HandlerAdapter)
- 视图解析器(ViewResolver) 
- 处理器或页面控制器(Controller) 
- 验证器( Validator)
- 命令对象(Command 请求参数绑定到的对象就叫命令对象)
- 表单对象(Form Object 提供给表单展示和提交到的对象就叫表单对象)。 

2、分工明确，而且扩展点相当灵活，可以很容易扩展，虽然几乎不需要。 

3、由于命令对象就是一个 POJO，无需继承框架特定 API，可以使用命令对象直接作为业务对象。

4、和 Spring 其他框架无缝集成，是其它 Web 框架所不具备的。 

5、可适配，通过 HandlerAdapter 可以支持任意的类作为处理器。 

6、可定制性，HandlerMapping、ViewResolver 等能够非常简单的定制。

7、功能强大的数据验证、格式化、绑定机制。

8、利用 Spring 提供的 Mock 对象能够非常简单的进行 Web 层单元测试。

9、本地化、主题的解析的支持，使我们更容易进行国际化和主题的切换。

10、强大的 JSP 标签库，使 JSP 编写更容易。

..................还有比如 RESTful 风格的支持、简单的文件上传、约定大于配置的契约式编程支持、基于注解的零配置支持等等。

### 2.4、SpringMVC 和 Struts2 的优略分析

共同点:

- 它们都是表现层框架，都是基于 MVC 模型编写的。 

- 它们的底层都离不开原始 ServletAPI。 

- 它们处理请求的机制都是一个核心控制器。

区别:

- Spring MVC 的入口是 Servlet, 而 Struts2 是 Filter

- Spring MVC 是基于方法设计的，而 Struts2 是基于类，Struts2 每次执行都会创建一个动作类。所以 Spring MVC 会稍微比 Struts2 快些。

- Spring MVC 使用更加简洁,同时还支持 JSR303, 处理 ajax 的请求更方便

  (JSR303 是一套 JavaBean 参数校验的标准，它定义了很多常用的校验注解，我们可以直接将这些注
  解加在我们 JavaBean 的属性上面，就可以在需要校验的时候进行校验了。)

- Struts2 的 OGNL 表达式使页面的开发效率相比 Spring MVC 更高些，但执行效率并没有比 JSTL 提升，尤其是 struts2 的表单标签，远没有 html 执行效率高。

# SpringMVC 的入门

## 1、SpringMVC 的入门案例

### 1.1、前期准备

下载开发包: https://spring.io/projects

其实spring mvc的jar包就在之前我们的spring框架开发包中。

**创建一个 javaweb 工程**

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/2.png)

**创建一个 jsp 用于发送请求**

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/3.png)

jsp 中的内容:

```jsp
<a href="${pageContext.request.contextPath}/hello">SpringMVC入门案例</a> <br/>
<a href="hello">SpringMVC入门案例</a>
```

### 1.2、拷贝jar包

spring mvc的jar包就在

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/4.png)

除了上面两个 jar 包之外，还需要拷贝 spring 的注解 ioc 所需 jar 包(包括一个 aop 的 jar 包)。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/5.png)

### 1.3、配置核心控制器-一个 Servlet

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
																																http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
         id="WebApp_ID" version="2.5">

    <!-- 配置 spring mvc 的核心控制器 -->
    <servlet>
        <servlet-name>SpringMVCDispatcherServlet</servlet-name>
        <servlet-class>
            org.springframework.web.servlet.DispatcherServlet
        </servlet-class>
        <!-- 配置初始化参数，用于读取 SpringMVC 的配置文件 -->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:SpringMVC.xml</param-value>
        </init-param>
        <!-- 配置 servlet 的对象的创建时间点:应用加载时创建。 
                    取值只能是非 0 正整数，表示启动顺序 -->
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>SpringMVCDispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
```

### 1.4、创建 spring mvc 的配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd
http://www.springframework.org/schema/context
http://www.springframework.org/schema/context/spring-context.xsd"> <!-- 配置创建 spring 容器要扫描的包 -->
    <context:component-scan base-package="com.itheima"></context:component-scan>
    <!-- 配置视图解析器 --> bean
    class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix"
              value="/WEB-INF/pages/"></property>
    <property name="suffix" value=".jsp"></property>
</bean>
        </beans>
```

### 1.5、编写控制器并使用注解配置

```java
@Controller("helloController")
public class HelloController {
    @RequestMapping("/hello")
    public String sayHello() {
        System.out.println("HelloController 的 sayHello 方法执行了。。。。");
        return "success";
    }
}
```

### 1.6、测试

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/6.png)

## 2、入门案例的执行过程及原理分析

### 2.1、案例的执行过程

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/7.png)

1、服务器启动，应用被加载。读取到 web.xml 中的配置创建 spring 容器并且初始化容器中的对象。

从入门案例中可以看到的是:HelloController 和 InternalResourceViewResolver，但是远不止这些。

2、浏览器发送请求，被 DispatherServlet 捕获，该 Servlet 并不处理请求，而是把请求转发出去。转发 的路径是根据请求 URL，匹配

@RequestMapping 中的内容。

3、匹配到了后，执行对应方法。该方法有一个返回值。

4、根据方法的返回值，借助 InternalResourceViewResolver 找到对应的结果视图。

5、渲染结果视图，响应浏览器。

### 2.2、SpringMVC的请求响应流程

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/8.png)

## 3、入门案例中涉及的组件

### 3.1、DispatcherServlet:前端控制器

​	用户请求到达前端控制器，它就相当于 mvc 模式中的 c，dispatcherServlet 是整个流程控制的中心，由它调用其它组件处理用户的请求，dispatcherServlet 的存在降低了组件之间的耦合性。

### 3.2、HandlerMapping:处理器映射器

​	HandlerMapping 负责根据用户请求找到 Handler 即处理器，SpringMVC 提供了不同的映射器实现不同的映射方式，例如:配置文件方式，实现接口方式，注解方式等。

### 3.3、Handler:处理器

​	它就是我们开发中要编写的具体业务控制器。由 DispatcherServlet 把用户请求转发到 Handler。由 Handler 对具体的用户请求进行处理。

### 3.4、HandlAdapter:处理器适配器

通过 HandlerAdapter 对处理器进行执行，这是适配器模式的应用，通过扩展适配器可以对更多类型的处理器进行执行。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/9.png)

### 3.5、View Resolver:视图解析器

​	View Resolver 负责将处理结果生成 View 视图，View Resolver 首先根据逻辑视图名解析成物理视图名，即具体的页面地址，再生成 View 视图对象，最后对 View 进行渲染将处理结果通过页面展示给用户。

### 3.6、View:视图

SpringMVC 框架提供了很多的 View 视图类型的支持，包括:jstlView、freemarkerView、pdfView 等。我们最常用的视图就是 jsp。

一般情况下需要通过页面标签或页面模版技术将模型数据通过页面展示给用户，需要由程序员根据业务需求开 发具体的页面。

### 3.7、< mvc:annotation-driven >说明

在 SpringMVC 的各个组件中，处理器映射器、处理器适配器、视图解析器称为 SpringMVC 的三大组件。

使用< mvc:annotation-driven >自动加载 RequestMappingHandlerMapping(处理映射器)和 RequestMappingHandlerAdapter ( 处 理 

适 配 器 ) ， 可 用 在 SpringMVC.xml 配 置 文 件 中 使 用 < mvc:annotation-driven >替代注解处理器和适配器的配置。

它就相当于在 xml 中配置了:

```xml
<!-- 上面的标签相当于 如下配置-->
<!-- Begin -->
<!-- HandlerMapping -->
<beanclass="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerM apping"></bean>
<bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"></bean>
        <!-- HandlerAdapter -->
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerA dapter"></bean>
<bean class="org.springframework.web.servlet.mvc.HttpRequestHandlerAdapter"></bean>
<bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"></bean>
        <!-- HadnlerExceptionResolvers -->
<bean
class="org.springframework.web.servlet.mvc.method.annotation.ExceptionHandlerExcept ionResolver"></bean>
<bean class="org.springframework.web.servlet.mvc.annotation.ResponseStatusExceptionResolv er"></bean>
<bean class="org.springframework.web.servlet.mvc.support.DefaultHandlerExceptionResolver"></bean>
        <!-- End -->
```

注意:
 一般开发中，我们都需要写上此标签(虽然从入门案例中看，我们不写也行，随着课程的深入，该标签还有具体的使用场景 )。

明确:

**我们只需要编写处理具体业务的控制器以及视图。**

## 4、RequestMapping 注解

### 4.1、使用说明

源码:

```java
@Target({ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME) @Documented
@Mapping
public @interface RequestMapping { 
}
```

作用:

- 用于建立请求 URL 和处理请求方法之间的对应关系。

出现位置:

- 类上:

  - 请求 URL 的第一级访问目录。此处不写的话，就相当于应用的根目录。写的话需要以/开头。 

  - 它出现的目的是为了使我们的 URL 可以按照模块化管理:

    例如:

  - 账户模块:

    - /account/add 
    - /account/update 
    - /account/delete ...

  - 订单模块:

    - /order/add
    - /order/update
    - /order/delete

    /order/的部分就是把 RequsetMappding 写在类上，使我们的 URL 更加精细。 

    方法上:

    请求 URL 的第二级访问目录。

属性：

- value:用于指定请求的 URL。它和 path 属性的作用是一样的。

- method:用于指定请求的方式。

- params:用于指定限制请求参数的条件。它支持简单的表达式。要求请求参数的 key 和 value 必须和 配置的一模一样。

  例如:

  - params = {"accountName"}，表示请求参数必须有 accountName
  - params = {"moeny!100"}，表示请求参数中money不能是100。 headers:用于指定限制请求消息头的条件。

- 注意:

  - 以上四个属性只要出现 2 个或以上时，他们的关系是与的关系。

### 4.2、使用示例

#### 4.2.1、出现位置的示例

控制器代码 :

```java
/**
 * RequestMapping 注解出现的位置
 */
@Controller("accountController")
@RequestMapping("/account")
public class AccountController {
    @RequestMapping("/findAccount")
    public String findAccount() {
        System.out.println("查询了账户。。。。");
        return "success";
    }
}
```

jsp 中的代码:

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
         pageEncoding="UTF-8" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
<html>
"http://www.w3.org/TR/html4/loose.dtd">
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>requestmapping 的使用</title>
</head>
<body>

<!-- 第一种访问方式 -->
<a href="${pageContext.request.contextPath}/account/findAccount">
    查询账户
</a>
<br/>
<!-- 第二种访问方式 -->
<a href="account/findAccount">查询账户</a>
</body>
</html
```

注意:

- 当我们使用此种方式配置时，在 jsp 中第二种写法时，不要在访问 URL 前面加/，否则无法找到资源。

#### 4.2.2、method 属性的示例:

控制器代码:

```java
/**
 * 保存账户
 * @return
 */
@RequestMapping(value = "/saveAccount", method = RequestMethod.POST) public String saveAccount(){
        System.out.println("保存了账户");
        return"success";
        }
```

jsp 代码:

```jsp
<!-- 请求方式的示例 -->
<a href="account/saveAccount">保存账户，get请求</a> 
<br/>
<form action="account/saveAccount" method="post">
    <input type="submit" value="保存账户，post请求">
</form>
```

注意:

当使用 get 请求时，提示错误信息是 405，信息是方法不支持 get 方式请求

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/10.png)

#### 4.2.3、params 属性的示例:

控制器的代码:

```java
/**
 * 删除账户 * @return
 */

import org.springframework.web.bind.annotation.RequestMapping;

@RequestMapping(value = "/removeAccount", params = {"accountName", "money>100"}) 
public String removeAccount(){
        System.out.println("删除了账户");return"success";
        }
```

jsp 中的代码:

```jsp
<!-- 请求参数的示例 -->
<a href="account/removeAccount?accountName=aaa&money>100">删除账户，金额 100</a>
<br/>
<a href="account/removeAccount?accountName=aaa&money>150">删除账户，金额 150</a>
```

注意:

当我们点击第一个超链接时,可以访问成功。 

当我们点击第二个超链接时，无法访问。如下图:

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/11.png)