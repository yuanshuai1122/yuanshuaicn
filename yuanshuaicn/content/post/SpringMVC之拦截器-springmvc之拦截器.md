---
title: SpringMVC之拦截器
date: 2021-post-26 21:32:36.285
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/0820590.png'
theme: 'light'
tags: 
- Java
- SpringMVC
---

# SpringMVC 中的拦截器

## 1、拦截器的作用

Spring MVC 的处理器拦截器类似于 Servlet 开发中的过滤器 Filter，用于对处理器进行预处理和后处理。 

用户可以自己定义一些拦截器来实现特定的功能。 

谈到拦截器，还要向大家提一个词——拦截器链(Interceptor Chain)。

拦截器链就是将拦截器按一定的顺序联结成一条链。

在访问被拦截的方法或字段时，拦截器链中的拦截器就会按其之前定义的顺序被调用。 

说到这里，可能大家脑海中有了一个疑问，这不是我们之前学的过滤器吗?是的它和过滤器是有几分相似，但是也有区别，接下来我们就来说说他们的区别:

- 过滤器是servlet规范中的一部分，任何java web工程都可以使用。
- 拦截器是 SpringMVC 框架自己的，只有使用了 SpringMVC 框架的工程才能用。
- 过滤器在 url-pattern 中配置了/*之后，可以对所有要访问的资源拦截。 
- 拦截器它是只会拦截访问的控制器方法，如果访问的是 jsp，html,css,image 或者 js 是不会进行拦截的。

它也是 AOP 思想的具体应用。

我们要想自定义拦截器， 要求必须实现:HandlerInterceptor 接口。

## 2、自定义拦截器的步骤

### 2.1、第一步:编写一个普通类实现 HandlerInterceptor 接口

```java
/**
 * 自定义拦截器
 */
public class HandlerInterceptorDemo1 implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("preHandle 拦截器拦截了");
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("postHandle 方法执行了");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("afterCompletion 方法执行了");
    }
}
```

### 2.2、第二步:配置拦截器

```xml
<!-- 配置拦截器 -->
<mvc:interceptors>
    <mvc:interceptor>
        <mvc:mapping path="/**"/>
        <bean id="handlerInterceptorDemo1" class="com.itheima.web.interceptor.HandlerInterceptorDemo1"></bean>
    </mvc:interceptor>
</mvc:interceptors>
```

### 2.3、测试运行结果:

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/44.png)

## 3、拦截器的细节

### 3.1、拦截器的放行

放行的含义是指，如果有下一个拦截器就执行下一个，如果该拦截器处于拦截器链的最后一个，则执行控制器中的方法。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/45.png)

### 3.2、拦截器中方法的说明

```java
public interface HandlerInterceptor {
    /**
     * 如何调用:
     *  按拦截器定义顺序调用
     * 何时调用:
     *  只要配置了都会调用
     * 有什么用:
     *  如果程序员决定该拦截器对请求进行拦截处理后还要调用其他的拦截器，或者是业务处理器去进行处理，则返回 true。
     *  如果程序员决定不需要再调用其他的组件去处理请求，则返回 false。
     */
    default boolean preHandle(HttpServletRequest request, HttpServletResponse
            response, Object handler) throws Exception {
        return true;
    }
    /**
     * 如何调用:
     *  按拦截器定义逆序调用
     * 何时调用:
     *  在拦截器链内所有拦截器返成功调用
     * 有什么用:
     * 在业务处理器处理完请求后，但是 DispatcherServlet 向客户端返回响应前被调用， 在该方法中对用户请求 request 进行处理。
     */
    
    default void postHandle(HttpServletRequest request, HttpServletResponse
            response, Object handler, @Nullable ModelAndView modelAndView) throws Exception {
    }
    /**
     * 如何调用:
     *  按拦截器定义逆序调用
     * 何时调用:
     *  只有 preHandle 返回 true 才调用
     * 有什么用:
     *  在 DispatcherServlet 完全处理完请求后被调用，
     *  可以在该方法中进行一些资源清理的操作。 */
    default void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable Exception ex) throws Exception {
    }
    }
```

思考:

如果有多个拦截器，这时拦截器 1 的 preHandle 方法返回 true，但是拦截器 2 的 preHandle 方法返 回 false，而此时拦截器 1 的 

afterCompletion 方法是否执行?

### 3.3、拦截器的作用路径

作用路径可以通过在配置文件中配置。

```xml
<!-- 配置拦截器的作用范围 -->
<mvc:interceptors>
    <mvc:interceptor>
        <mvc:mapping path="/**"/><!-- 用于指定对拦截的 url -->
        <mvc:exclude-mapping path=""/><!-- 用于指定排除的 url-->
        <bean id="handlerInterceptorDemo1"
              class="com.itheima.web.interceptor.HandlerInterceptorDemo1"></bean>
    </mvc:interceptor>
</mvc:interceptors>
```

### 3.4、多个拦截器的执行顺序

多个拦截器是按照配置的顺序决定的。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/46.png)

## 4、正常流程测试

### 4.1、配置文件:

```java
<!-- 配置拦截器的作用范围 -->
<mvc:interceptors>
    <mvc:interceptor>
        <mvc:mapping path="/**"/><!-- 用于指定对拦截的 url -->
        <bean id="handlerInterceptorDemo1" class="com.itheima.web.interceptor.HandlerInterceptorDemo1"></bean>
    </mvc:interceptor>
    <mvc:interceptor>
        <mvc:mapping path="/**"/>
        <bean id="handlerInterceptorDemo2" class="com.itheima.web.interceptor.HandlerInterceptorDemo2"></bean>
    </mvc:interceptor>
</mvc:interceptors>
```

### 4.2、拦截器 1 的代码:

```java
/**
 * 自定义拦截器
 */
public class HandlerInterceptorDemo1 implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("拦截器 1:preHandle 拦截器拦截了");
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("拦截器 1:postHandle 方法执行了");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("拦截器 1:afterCompletion 方法执行了");
    }
}
```

### 4.3、拦截器 2 的代码:

```java
/**
 * 自定义拦截器
 */
public class HandlerInterceptorDemo2 implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("拦截器 2:preHandle 拦截器拦截了");
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("拦截器 2:postHandle 方法执行了");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("拦截器 2:afterCompletion 方法执行了");
    }
}
```

### 4.4、运行结果:

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/47.png)

## 5、中断流程测试

### 5.1、配置文件:

```xml
<!-- 配置拦截器的作用范围 -->
<mvc:interceptors>
    <mvc:interceptor>
        <mvc:mapping path="/**"/><!-- 用于指定对拦截的 url -->
        <bean id="handlerInterceptorDemo1" class="com.itheima.web.interceptor.HandlerInterceptorDemo1"></bean>
    </mvc:interceptor>
    <mvc:interceptor>
        <mvc:mapping path="/**"/>
        <bean id="handlerInterceptorDemo2"
              class="com.itheima.web.interceptor.HandlerInterceptorDemo2"></bean>
    </mvc:interceptor>
</mvc:interceptors>
```

### 5.2、拦截器 1 的代码:

```java
/**
 * 自定义拦截器
 */
public class HandlerInterceptorDemo1 implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("拦截器 1:preHandle 拦截器拦截了");
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("拦截器 1:postHandle 方法执行了");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("拦截器 1:afterCompletion 方法执行了");
    }
}
```

### 5.3、拦截器 2 的代码:

```java
/**
 * 自定义拦截器
 */
public class HandlerInterceptorDemo2 implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("拦截器 2:preHandle 拦截器拦截了"); 
        return false;
    }
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception { 
        System.out.println("拦截器 2:postHandle 方法执行了");
    }
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("拦截器 2:afterCompletion 方法执行了");
    }
}
```

### 5.4、运行结果:

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/48.png)

## 6、拦截器的简单案例(验证用户是否登录）

### 6.1、实现思路

> 1、有一个登录页面，需要写一个 controller 访问页面 
>
> 2、登录页面有一提交表单的动作。需要在 controller 中处理。
>
> ​	2.1、判断用户名密码是否正确 
>
> ​	2.2、如果正确 向 session 中写入用户信息 
>
> ​	2.3、返回登录成功。
>
> 3、拦截用户请求，判断用户是否登录 
>
> ​	3.1、如果用户已经登录。放行
>
> ​	3.2、如果用户未登录，跳转到登录页面

### 6.2、控制器代码

```java
//登陆页面
@RequestMapping("/login")
public String login(Model model)throws Exception{
        return"login";
        }
//登陆提交
//userid:用户账号，pwd:密码

@RequestMapping("/loginsubmit")
public String loginsubmit(HttpSession session,String userid,String pwd)throws Exception{
        //向 session 记录用户身份信息
        session.setAttribute("activeUser",userid);
        return"redirect:/main.jsp";
        }
//退出
@RequestMapping("/logout")
public String logout(HttpSession session)throws Exception{
        //session 过期 
        session.invalidate();
        return"redirect:index.jsp";
        }
```

### 6.3、拦截器代码

```java
public class LoginInterceptor implements HandlerInterceptor {
    @Override
    Public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
//如果是登录页面则放行
        if (request.getRequestURI().indexOf("login.action") >= 0) {
            return true;
        }
        HttpSession session = request.getSession();
//如果用户已登录也放行
        if (session.getAttribute("user") != null) {
            return true;
            response);

        }
    }
//用户没有登录挑战到登录页面
request.getRequestDispatcher("/WEB-INF/jsp/login.jsp").

    forward(request, response);
        return false;
}
}
```