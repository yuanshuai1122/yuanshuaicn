---
title: SpringMVC之异常处理
date: 2021-post-26 21:32:32.965
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/0820590.png'
theme: 'light'
tags: 
- Java
- SpringMVC
---

# SpringMVC 中的异常处理

## 1、异常处理的思路

系统中异常包括两类:预期异常和运行时异常 RuntimeException，前者通过捕获异常从而获取异常信息， 后者主要通过规范代码开发、

测试通过手段减少运行时异常的发生。

系统的 dao、service、controller 出现都通过 throws Exception 向上抛出，最后由 springmvc 前端

控制器交由异常处理器进行异常处理，如下图:

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/42.png)

## 2、实现步骤

### 2.1、编写异常类和错误页面

```java
/**
 * 自定义异常
 */
public class CustomException extends Exception {
    private String message;

    public CustomException(String message) {
        this.message = message;
    }

    public String getMessage() {
        return message;
    }
}

```

jsp 页面:

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>执行失败</title>
</head>
<body>
执行失败!
${message }
</body>
</html>
```

### 2.2、自定义异常处理器

```java
/**
 * 自定义异常处理器
 */
public class CustomExceptionResolver implements HandlerExceptionResolver {
    @Override
    public ModelAndView resolveException(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) {
        ex.printStackTrace();
        CustomException customException = null;
        //如果抛出的是系统自定义异常则直接转换
        if (ex instanceof CustomException) {
            customException = (CustomException) ex;
        } else {
            //如果抛出的不是系统自定义异常则重新构造一个系统错误异常。
            customException = new CustomException("系统错误，请与系统管理 员联系!");
        }
        ModelAndView modelAndView = new ModelAndView();
        modelAndView.addObject("message", customException.getMessage());
        modelAndView.setViewName("error");
        return modelAndView;
    }
}
```

### 2.3、配置异常处理器

```xml
<!-- 配置自定义异常处理器 -->
<bean id="handlerExceptionResolver"
class="com.itheima.exception.CustomExceptionResolver"/>
```

### 2.4、运行结果:

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/43.png)