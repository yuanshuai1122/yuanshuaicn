---
title: SpringMVC之常用注解
date: 2021-11-26 21:32:26.899
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/0820590.png'
theme: 'light'
tags: 
- Java
- SpringMVC
---

# 常用注解

## 1、RequestParam

### 1.1、使用说明

作用:

- 把请求中指定名称的参数给控制器中的形参赋值。

属性:

- value:请求参数中的名称。
- required:请求参数中是否必须提供此参数。默认值:true。表示必须提供，如果不提供将报错。

### 1.2、使用示例

jsp 中的代码:

```jsp
<!-- requestParams 注解的使用 -->
<a href="springmvc/useRequestParam?name=test">requestParam注解</a>
```

控制器中的代码:

```java
/**
 * requestParams 注解的使用 * @param username
 *
 * @return
 */
@RequestMapping("/useRequestParam")
public String useRequestParam(@RequestParam("name")String username,
                              @RequestParam(value = "age", required = false)Integer age){
    System.out.println(username+","+age);
        return"success";
}
```

运行结果:

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/19.png)

## 2、RequestBody

### 2.1、使用说明

作用:

- 用于获取请求体内容。直接使用得到是 key=value&key=value...结构的数据。
- get 请求方式不适用。 

属性:

- required:是否必须有请求体。默认值是:true。当取值为 true 时,get 请求方式会报错。如果取值 为 false，get 请求得到是null。

### 2.2、使用示例

post 请求 jsp 代码:

```jsp
<!-- request body 注解 -->
<form action="springmvc/useRequestBody" method="post">
	用户名称:<input type="text" name="username" ><br/> 
  用户密码:<input type="password" name="password" ><br/>
	用户年龄:<input type="text" name="age" ><br/>
<input type="submit" value="保存"> 
</form>
```

get 请求 jsp 代码:

```jsp
<a href="springmvc/useRequestBody?body=test">requestBody注解get请求</a>
```

控制器代码:

```java
/**
 * RequestBody注解 * @param user
 *
 * @return
 */
@RequestMapping("/useRequestBody")
public String useRequestBody(@RequestBody(required = false) String body){
        System.out.println(body);
        return"success";
}
```

post 请求运行结果:

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/20.png)

get 请求运行结果:

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/21.png)

## 3、PathVaribale

### 3.1、使用说明

作用:

- 用于绑定 url 中的占位符。例如:请求 url 中 /delete/{id}，这个{id}就是 url 占位符。
- url 支持占位符是 spring3.0 之后加入的。是 springmvc 支持 rest 风格 URL 的一个重要标志。 

属性:

- value:用于指定 url 中占位符名称。
- required:是否必须提供占位符。

### 3.2、使用示例

jsp 代码:

```jsp
<!-- PathVariable 注解 -->
<a href="springmvc/usePathVariable/100">pathVariable 注解</a>
```

控制器代码:

```java
/**
 * PathVariable注解
 *
 * @param user
 * @return
 */

import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;

@RequestMapping("/usePathVariable/{id}")
public String usePathVariable(@PathVariable("id") Integer id){
        System.out.println(id);
        return"success";
}
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/22.png)

### 3.3、REST 风格 URL

什么是 rest:

> REST(英文:Representational State Transfer，简称 REST)描述了一个架构样式的网络系统， 比如 web 应用程序。它首次出现在 2000 年 Roy Fielding 的博士论文中，他是 HTTP 规范的主要编写者之 一。在目前主流的三种 Web 服务交互方案中，REST 相比于 SOAP(Simple Object Access protocol，简单 对象访问协议)以及 XML-RPC 更加简单明了，无论是对 URL 的处理还是对 Payload 的编码，REST 都倾向于用更 加简单轻量的方法设计和实现。值得注意的是 REST 并没有一个明确的标准，而更像是一种设计的风格。
> 它本身并没有什么实用性，其核心价值在于如何设计出符合 REST 风格的网络接口。

restful 的优点

> 它结构清晰、符合标准、易于理解、扩展方便，所以正得到越来越多网站的采用。

restful 的特性:

> 资源(Resources):网络上的一个实体，或者说是网络上的一个具体信息。 
>
> - 它可以是一段文本、一张图片、一首歌曲、一种服务，总之就是一个具体的存在。可以用一个 URI(统一资源定位符)指向它，每种资源对应一个特定的 URI 。要获取这个资源，访问它的 URI 就可以，因此 URI 即为每一个资源的独一无二的识别符。
>
> 表现层(Representation):把资源具体呈现出来的形式，叫做它的表现层 (Representation)。
>
> - 比如，文本可以用 txt 格式表现，也可以用 HTML 格式、XML 格式、JSON 格式表现，甚至可以采用二进制格式。
>
> 状态转化(State Transfer):每发出一个请求，就代表了客户端和服务器的一次交互过程。
>
> - HTTP 协议，是一个无状态协议，即所有的状态都保存在服务器端。因此，如果客户端想要操作服务器，必须通过某种手段，让服务器端发生“状态转化”(State Transfer)。而这种转化是建立在表现层之上的，所以 就是 “表现层状态转化”。具体说，就是 HTTP 协议里面，四个表示操作方式的动词:GET、POST、PUT、 DELETE。它们分别对应四种基本操作:GET 用来获取资源，POST 用来新建资源，PUT 用来更新资源，DELETE 用来删除资源。

restful 的示例:

> /account/1      HTTP GET :          得到 id = 1 的 account
>
> /account/1      HTTP DELETE:     删除 id = 1 的 account
>
> /account/1      HTTP PUT:           更新 id = 1 的 account
>
> /account/1      HTTP POST:         新增 account

### 3.4、基于 HiddentHttpMethodFilter 的示例

作用:

- 由于浏览器 form 表单只支持 GET 与 POST 请求，而DELETE、PUT 等 method 并不支持，Spring3.0 添 加了一个过滤器，可以将浏览器请求改为指定的请求方式，发送给我们的控制器方法，使得支持 GET、POST、PUT与 DELETE 请求。

使用方法:

- 第一步:在 web.xml 中配置该过滤器。
- 第二步:请求方式必须使用 post 请求。
- 第三步:按照要求提供_method 请求参数，该参数的取值就是我们需要的请求方式

源码分析:

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/23.png)

jsp 中示例代码:

```jsp
<!-- 保存 -->
<form action="springmvc/testRestPOST" method="post">
   用户名称:<input type="text" name="username"><br/>
<!-- <input type="hidden" name="_method" value="POST"> --> 
  <input type="submit" value="保存">
</form>
<hr/>
<!-- 更新 -->
<form action="springmvc/testRestPUT/1" method="post">
	用户名称:<input type="text" name="username"><br/>
	<input type="hidden" name="_method" value="PUT">
	<input type="submit" value="更新"> 
</form>
<hr/>
<!-- 删除 -->
<form action="springmvc/testRestDELETE/1" method="post">
	<input type="hidden" name="_method" value="DELETE"> 
  <input type="submit" value="删除">
</form>
<hr/>
<!-- 查询一个 -->
<form action="springmvc/testRestGET/1" method="post">
	<input type="hidden" name="_method" value="GET">
	<input type="submit" value="查询"> 
</form>
```

控制器中示例代码:

```java
/**
 * post请求:保存
 *
 * @param username
 * @return
 */
@RequestMapping(value = "/testRestPOST", method = RequestMethod.POST)
public String testRestfulURLPOST(User user){
    System.out.println("rest post"+user);
    return"success";
        }
/**
 * put请求:更新
 * @param username
 * @return
 */
@RequestMapping(value = "/testRestPUT/{id}", method = RequestMethod.PUT)
public String testRestfulURLPUT(@PathVariable("id")Integer id,User user){
        System.out.println("rest put "+id+","+user);
        return"success";
}
/**
 * post请求:删除
 * @param username * @return
 */
@RequestMapping(value = "/testRestDELETE/{id}", method = RequestMethod.DELETE) 
public String testRestfulURLDELETE(@PathVariable("id")Integer id){
        System.out.println("rest delete "+id);
        return"success";
}
/**
 * post请求:查询
 * @param username
 * @return
 */
@RequestMapping(value = "/testRestGET/{id}", method = RequestMethod.GET)
public String testRestfulURLGET(@PathVariable("id")Integer id){
    System.out.println("rest get "+id);
    return"success";
        }
```

运行结果:

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/24.png)

## 4、RequestHeader

### 4.1、使用说明

作用:

- 用于获取请求消息头。

属性:

- value:提供消息头名称 required:是否必须有此消息头

注:

- 在实际开发中一般不怎么用。

### 4.2、使用示例

jsp 中代码:

```jsp
<!-- RequestHeader 注解 -->
<a href="springmvc/useRequestHeader">获取请求消息头</a>
```

控制器中代码:

```java
/**
 * RequestHeader 注解 * @param user
 *
 * @return
 */
@RequestMapping("/useRequestHeader")
public String useRequestHeader(@RequestHeader(value = "Accept-Language", required = false)String requestHeader){
    System.out.println(requestHeader);
    return"success";
        }
```

运行结果:

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/25.png)

## 5、CookieValue

### 5.1、使用说明

作用:

- 用于把指定 cookie 名称的值传入控制器方法参数。

属性:

- value:指定 cookie 的名称。
- required:是否必须有此 cookie。

### 5.2、使用示例

jsp 中的代码:

```jsp
<!-- CookieValue 注解 -->
<a href="springmvc/useCookieValue">绑定cookie的值</a>
```

控制器中的代码:

```java
/**
 * Cookie注解注解 * @param user
 *
 * @return
 */
@RequestMapping("/useCookieValue")
public String useCookieValue(@CookieValue(value = "JSESSIONID", required = false) String cookieValue){
        System.out.println(cookieValue);
        return"success";
        }
```

运行结果:

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/26.png)

## 6、ModelAttribute

### 6.1、使用说明

作用:

- 该注解是 SpringMVC4.3 版本以后新加入的。它可以用于修饰方法和参数。
- 出现在方法上，表示当前方法会在控制器的方法执行之前，先执行。它可以修饰没有返回值的方法，也可 以修饰有具体返回值的方法。
- 出现在参数上，获取指定的数据给参数赋值。

属性:

- value:用于获取数据的 key。key 可以是 POJO 的属性名称，也可以是 map 结构的 key。 

应用场景:

- 当表单提交数据不是完整的实体类数据时，保证没有提交数据的字段使用数据库对象原来的数据。
  例如:
  我们在编辑一个用户时， 用户有一个创建信息字段，该字段的值是不允许被修改的。在提交表单数
  据是肯定没有此字段的内容，一旦更新会把该字段内容置为 null，此时就可以使用此注解解决问题。

### 6.2、使用示例

#### 6.2.1、基于 POJO 属性的基本使用:

jps 代码:

```jsp
<!-- ModelAttribute 注解的基本使用 -->
<a href="springmvc/testModelAttribute?username=test">测试modelattribute</a>
```

控制器代码:

```java
/**
 * 被 ModelAttribute 修饰的方法
 * @param user
 */
@ModelAttribute
public void showModel(User user){
        System.out.println("执行了 showModel 方法"+user.getUsername());
        }
/**
 * 接收请求的方法 
 * @param user
 * @return
 */
@RequestMapping("/testModelAttribute")
public String testModelAttribute(User user){
        System.out.println("执行了控制器的方法"+user.getUsername());
        return"success";
}
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/27.png)

#### 6.2.2、基于 Map 的应用场景示例 :ModelAttribute 修饰方法带返回值

需求:
修改用户信息，要求用户的密码不能修改

jsp 的代码:

```jsp
<!-- 修改用户信息 -->
<form action="springmvc/updateUser" method="post"> 
  用户名称:<input type="text" name="username" ><br/>
	用户年龄:<input type="text" name="age" ><br/>
<input type="submit" value="保存"> 
</form>
```

控制的代码:

```java
/**
 * 查询数据库中用户信息
 *
 * @param user
 */
@ModelAttribute
public User showModel(String username){
        //模拟数据库查询
        User abc=findUserByName(username);
        System.out.println("执行了 showModel 方法"+abc);
        return abc;
        }
/**
 * 模拟修改用户方法 
 * @param user
 * @return
 */
@RequestMapping("/updateUser")
public String testModelAttribute(User user){
        System.out.println("控制器中处理请求的方法:修改用户:"+user);
        return"success";
        }
/**
 * 模拟数据库查询
 * @param username
 * @return
 */
private User findUserByName(String username){
        User user=new User();
        user.setUsername(username);
        user.setAge(19);
        user.setPassword("123456");
        return user;
        }
```

运行结果:

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/28.png)

## 7、SessionAttribute

### 7.1、使用说明

作用:

- 用于多次执行控制器方法间的参数共享。

属性:

- value:用于指定存入的属性名称 
- type:用于指定存入的数据类型。

### 7.2、使用示例

jsp 中的代码:

```jsp
<!-- SessionAttribute 注解的使用 -->
<a href="springmvc/testPut">存入SessionAttribute</a>
<hr/>
<a href="springmvc/testGet">取出SessionAttribute</a> 
<hr/>
<a href="springmvc/testClean">清除SessionAttribute</a>
```

控制器中的代码:

```java
/**
 * SessionAttribute 注解的使用
 */
@Controller("sessionAttributeController")
@RequestMapping("/springmvc")
@SessionAttributes(value = {"username", "password"}, types = {Integer.class})
public class SessionAttributeController {
    /**
     * 把数据存入 SessionAttribute
     * @param model
     * @return 
     * Model 是 spring 提供的一个接口，该接口有一个实现类 ExtendedModelMap
     * 该类继承了 ModelMap，而 ModelMap 就是 LinkedHashMap 子类
     */
    @RequestMapping("/testPut")
    public String testPut(Model model) {
        model.addAttribute("username", "泰斯特");
        model.addAttribute("password", "123456");
        model.addAttribute("age", 31);
        //跳转之前将数据保存到 username、password 和 age 中，因为注解@SessionAttribute中有这几个参数
        return "success";
    }

    @RequestMapping("/testGet")
    public String testGet(ModelMap model) {
        System.out.println(model.get("username") + ";" + model.get("password") + ";" + model.get("a ge"));
        return "success";
    }

    @RequestMapping("/testClean")
    public String complete(SessionStatus sessionStatus) {
        sessionStatus.setComplete();
        return "success";
    }
}
```

运行结果:

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/29.png)