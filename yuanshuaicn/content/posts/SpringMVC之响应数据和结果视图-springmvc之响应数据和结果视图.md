---
title: SpringMVC之响应数据和结果视图
date: 2021-posts-26 21:32:27.913
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/0820590.png'
theme: 'light'
tags: 
- Java
- SpringMVC
---

# 响应数据和结果视图

## 1、返回值分类

### 1.1、字符串

controller 方法返回字符串可以指定逻辑视图名，通过视图解析器解析为物理视图地址。

```java
//指定逻辑视图名，经过视图解析器解析为 jsp 物理路径:/WEB-INF/pages/success.jsp
@RequestMapping("/testReturnString")
public String testReturnString(){
        System.out.println("AccountController 的 testReturnString 方法执行了。。。。");
        return"success";
        }
```

运行结果:

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/30.png)

## 1.2、void

Servlet 原始 API 可以作为控制器中方法的参数:

```java
@RequestMapping("/testReturnVoid")
public void testReturnVoid(HttpServletRequest request,HttpServletResponse response) throws Exception {
}
```

在 controller 方法形参上可以定义 request 和 response，使用 request 或 response 指定响应结果:

1、使用 request 转向页面，如下:

```java
request.getRequestDispatcher("/WEB-INF/pages/success.jsp").forward(request, response);
```

2、也可以通过 response 页面重定向:

```java
response.sendRedirect("testRetrunString")
```

3、也可以通过 response 指定响应结果，例如响应 json 数据:

```java
response.setCharacterEncoding("utf-8"); 
response.setContentType("application/json;charset=utf-8"); 
response.getWriter().write("json 串");
```

## 1.3、ModelAndView

ModelAndView 是 SpringMVC 为我们提供的一个对象，该对象也可以用作控制器方法的返回值。 

该对象中有两个方法:

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/31.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/32.png)

示例代码:

```java
/**
 * 返回 ModeAndView
 * @return
 */
@RequestMapping("/testReturnModelAndView") 
public ModelAndView testReturnModelAndView(){
        ModelAndView mv=new ModelAndView();
        mv.addObject("username","张三"); 
        mv.setViewName("success");
        return mv; 
}
```

响应的 jsp 代码:

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>执行成功</title></head>
<body>
执行成功!
${requestScope.username}
</body>
</html>
```

输出结果:

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/33.png)

注意:

​	我们在页面上上获取使用的是 requestScope.username 取的，所以返回 ModelAndView 类型时，浏览器跳转只能是请求转发。

## 2、转发和重定向

### 2.1、forward转发

controller 方法在提供了 String 类型的返回值之后，默认就是请求转发。我们也可以写成:

```java
/**
 * 转发
 *
 * @return
 */

import org.springframework.web.bind.annotation.RequestMapping;

@RequestMapping("/testForward")
public String testForward(){
        System.out.println("AccountController 的 testForward 方法执行了。。。。");
        return"forward:/WEB-INF/pages/success.jsp";
        }
```

### 2.2、Redirect重定向

contrller 方法提供了一个 String 类型返回值之后，它需要在返回值里使用:redirect:

```java
/**
 * 重定向
 *
 * @return
 */

import org.springframework.web.bind.annotation.RequestMapping;

@RequestMapping("/testRedirect")
public String testRedirect(){
        System.out.println("AccountController 的 testRedirect 方法执行了。。。。");
        return"redirect:testReturnModelAndView";
        }
```

它相当于“response.sendRedirect(url)”。需要注意的是，如果是重定向到 jsp 页面，则 jsp 页面不 能写在 WEB-INF 目录中，否则无法找到。

## 3、ResponseBody 响应 json 数据

### 3.1、使用说明

作用:

该注解用于将 Controller 的方法返回的对象，通过 HttpMessageConverter 接口转换为指定格式的 数据如:json,xml 等，通过 Response 响应给客户端

### 3.2、使用示例

需求:

​	使用@ResponseBody 注解实现将 controller 方法返回对象转换为 json 响应给客户端。 

前置知识点:

Springmvc 默认用 MappingJacksonHttpMessageConverter 对 json 数据进行转换，需要加入 jackson 的包。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/34.png)

注意:2.7.0 以下的版本用不了

jsp 中的代码:

```jsp
<script type="text/javascript" src="${pageContext.request.contextPath}/js/jquery.min.js"></script>
<script type="text/javascript"> $(function () {
    $("#testJson").click(function () {
        $.ajax({
            type: "post",
            url: "${pageContext.request.contextPath}/testResponseJson",
            contentType: "application/json;charset=utf-8",
            data: '{"id":1,"name":"test","money":999.0}',
            dataType: "json",
            success: function (data) {
                alert(data);
            }
        });
    });
})
</script>
<!-- 测试异步请求 -->
<input type="button" value="测试ajax请求json和响应json" id="testJson"/>
```

控制器中的代码:

```java
/**
 * 响应 json 数据的控制器
 *
 */
@Controller("jsonController")
public class JsonController {
    /**
     * 测试响应 json 数据
     */
    @RequestMapping("/testResponseJson")
    public @ResponseBody
    Account testResponseJson(@RequestBody Account account) {
        System.out.println("异步请求:" + account);
        return account;
    }
}
```

运行结果:

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/35.png)