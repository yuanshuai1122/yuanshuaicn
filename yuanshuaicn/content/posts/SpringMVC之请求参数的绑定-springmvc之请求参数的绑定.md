---
title: SpringMVC之请求参数的绑定
date: 2021-07-21 18:38:23.971
author:
  name: "yuanshuai"
  link: "https://cloud.tencent.com/developer/user/8180692"
  email: "shuaiyuan1122@gmail.com"
  avatar: "https://aabb-2023.oss-cn-beijing.aliyuncs.com/hjscijg3uw.png"
tags: 
- Java
- SpringMVC
---

# 请求参数的绑定

## 1、绑定说明

### 1.1 绑定的机制

我们都知道，表单中请求参数都是基于 key=value 的。

SpringMVC 绑定请求参数的过程是通过把表单提交请求参数，作为控制器中方法参数进行绑定的。 

例如:

```jsp
<a href="account/findAccount?accountId=10">查询账户</a>
```

中请求参数是:

```jsp
accountId=10
```

```java
/**
 * 查询账户 * @return
 */
@RequestMapping("/findAccount")
public String findAccount(Integer accountId){
        System.out.println("查询了账户。。。。"+accountId);return"success";
        }
```

### 1.2 支持的数据类型:

基本类型参数:

- 包括基本类型和 String 类型 

POJO 类型参数:

- 包括实体类，以及关联的实体类

数组和集合类型参数 :

- 包括 List 结构和 Map 结构的集合(包括数组)

**SpringMVC 绑定请求参数是自动实现的，但是要想使用，必须遵循使用要求。**

### 1.3 使用要求

如果是基本类型或者 String 类型: 

- 要求我们的参数名称必须和控制器中方法的形参名称保持一致。(严格区分大小写)

如果是 POJO 类型，或者它的关联对象:

- 要求表单中参数名称和 POJO 类的属性名称保持一致。并且控制器方法的参数类型是 POJO 类型。

如果是集合类型 ,有两种方式:

- 第一种:
  - 要求集合类型的请求参数必须在 POJO 中。在表单中请求参数名称要和 POJO 中集合属性名称相同。 
  - 给 List 集合中的元素赋值，使用下标。
  - 给 Map 集合中的元素赋值，使用键值对。

- 第二种:
  - 接收的请求参数是 json 格式数据。需要借助一个注解实现。

注意: 它还可以实现一些数据类型自动转换。内置转换器全都在:org.springframework.core.convert.support 包下。

有:

> java.lang.Boolean -> java.lang.String : ObjectToStringConverter
>
> java.lang.Character -> java.lang.Number : CharacterToNumberFactory 
>
> java.lang.Character -> java.lang.String : ObjectToStringConverter 
>
> java.lang.Enum -> java.lang.String : EnumToStringConverter 
>
> java.lang.Number -> java.lang.Character : NumberToCharacterConverter
>
> java.lang.Number -> java.lang.Number : NumberToNumberConverterFactory 
>
> java.lang.Number -> java.lang.String : ObjectToStringConverter 
>
> java.lang.String -> java.lang.Boolean : StringToBooleanConverter 
>
> java.lang.String -> java.lang.Character : StringToCharacterConverter 
>
> java.lang.String -> java.lang.Enum : StringToEnumConverterFactory
>
> java.lang.String -> java.lang.Number : StringToNumberConverterFactory 
>
> java.lang.String -> java.util.Locale : StringToLocaleConverter 
>
> java.lang.String -> java.util.Properties : StringToPropertiesConverter 
>
> java.lang.String -> java.util.UUID : StringToUUIDConverter
>
> java.util.Locale -> java.lang.String : ObjectToStringConverter
>
>  java.util.Properties -> java.lang.String : PropertiesToStringConverter 
>
> java.util.UUID -> java.lang.String : ObjectToStringConverter
> ......
>
> 如遇特殊类型转换要求，需要我们自己编写自定义类型转换器。

### 1.4 使用示例

#### 1.4.1 基本类型和 String 类型作为参数

jsp 代码:

```jsp
<!-- 基本类型示例 -->
<a href="account/findAccount?accountId=10&accountName=zhangsan">查询账户</a>
```

控制器代码:

```java
/**
 * 查询账户 * @return
 */
@RequestMapping("/findAccount")
public String findAccount(Integer accountId,String accountName) {
        System.out.println("查询了账户。。。。"+accountId+","+ accountName );
        return "success"; 
}
```

运行结果:

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/12.png)

#### 1.4.2 POJO 类型作为参数

实体类代码:

```java
/**
 * 账户信息
 */
public class Account implements Serializable {

    private Integer id;
    private String name;
    private Float money;
    private Address address; //getters and setters
}

/**
 * 地址的实体类
 */
public class Address implements Serializable {

    private String provinceName;
    private String cityName; //getters and setters
}
```

jsp 代码:

```jsp
<!-- pojo 类型演示 -->
<form action="account/saveAccount" method="post">
	账户名称:<input type="text" name="name" ><br/>
  账户金额:<input type="text" name="money" ><br/>
	账户省份:<input type="text" name="address.provinceName" ><br/> 
  账户城市:<input type="text" name="address.cityName" ><br/> 
  <input type="submit" value="保存">
</form>
```

控制器代码:

```java
/**
 * 保存账户
 * @param account 
 * * @return
 */
@RequestMapping("/saveAccount")
public String saveAccount(Account account){
        System.out.println("保存了账户。。。。"+account);
        return"success";
}
```

运行结果:

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/13.png)

#### 1.4.3 POJO 类中包含集合类型参数

实体类代码:

```java
/**
 * 用户实体类
 */
public class User implements Serializable {
    private String username;
    private String password;
    private Integer age;
    private List<Account> accounts;
    private Map<String, Account> accountMap;

    //getters and setters
    @Override
    public String toString() {
        return "User [username=" + username + ", password=" + password + ", age=" + age + ",\n accounts=" + accounts
                + ",\n accountMap=" + accountMap + "]";
    }
}
```

jsp 代码:

```jsp
<!-- POJO 类包含集合类型演示 -->
<form action="account/updateAccount" method="post">
	用户名称:<input type="text" name="username" ><br/> 
  用户密码:<input type="password" name="password" ><br/> 
  用户年龄:<input type="text" name="age" ><br/> 
  账户1名称:<input type="text" name="accounts[0].name" ><br/> 
  账户1金额:<input type="text" name="accounts[0].money" ><br/>
	账户2名称:<input type="text" name="accounts[1].name" ><br/> 
  账户2金额:<input type="text" name="accounts[1].money" ><br/> 
  账户3名称:<input type="text" name="accountMap['one'].name" ><br/> 
  账户3金额:<input type="text" name="accountMap['one'].money" ><br/>
	账户4名称:<input type="text" name="accountMap['two'].name" ><br/> 
  账户4金额:<input type="text" name="accountMap['two'].money" ><br/> 
  <input type="submit" value="保存">
</form>
```

控制器代码:

```java
/**
 * 更新账户 
 * @return
 */
@RequestMapping("/updateAccount")
public String updateAccount(User user){
        System.out.println("更新了账户。。。。"+user);
        return"success";
        }
```

运行结果:

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/14.png)

#### 1.4.4 请求参数乱码问题

post 请求方式:
在 web.xml 中配置一个过滤器

```xml
<!-- 配置 springMVC 编码过滤器 -->
<filter>
    <filter-name>CharacterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter
    </filter-class>
    <!-- 设置过滤器中的属性值 -->
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
    <!-- 启动过滤器 -->
    <init-param>
        <param-name>forceEncoding</param-name>
        <param-value>true</param-value>
    </init-param>
</filter>
        <!-- 过滤所有请求 --> 
<filter-mapping>
    <filter-name>CharacterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

在 springmvc 的配置文件中可以配置，静态资源不过滤:

```xml
<!-- location表示路径，mapping表示文件，**表示该目录下的文件以及子目录的文件 --> 
<mvc:resources location="/css/" mapping="/css/**"/>
<mvc:resources location="/images/" mapping="/images/**"/> 
<mvc:resources location="/scripts/" mapping="/javascript/**"/>
```

get 请求方式:
	tomacat 对 GET 和 POST 请求处理方式是不同的，GET 请求的编码问题，要改 tomcat 的 server.xml 配置文件，如下:

```xml
<Connector connectionTimeout="20000" port="8080" protocol="HTTP/1.1" redirectPort="8443"/>
```

改为:

```xml
<Connector connectionTimeout="20000" port="8080" 
           protocol="HTTP/1.1" redirectPort="8443"
					 useBodyEncodingForURI="true"/>
```

如果遇到 ajax 请求仍然乱码，请把:

```xml
useBodyEncodingForURI="true"改为 URIEncoding="UTF-8"
```

即可。

## 2 特殊情况

### 2.1 自定义类型转换器

#### 2.1.1 使用场景:

jsp 代码:

```jsp
<!-- 特殊情况之:类型转换问题 -->
<a href="account/deleteAccount?date=2018-01-01">根据日期删除账户</a>
```

控制器代码:

```java
/**
 * 删除账户
 *
 * @return
 */
@RequestMapping("/deleteAccount")
public String deleteAccount(String date){
        System.out.println("删除了账户。。。。"+date);
        return"success";
}
```

运行结果:

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/15.png)

当我们把控制器中方法参数的类型改为 Date 时:

```java
/**
 * 删除账户 
 * @return
 */

import org.springframework.web.bind.annotation.RequestMapping;

@RequestMapping("/deleteAccount")
public String deleteAccount(Date date){
        System.out.println("删除了账户。。。。"+date);
        return"success";
        }
```

运行结果:

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/16.png)

异常提示:

> Failed to bind request element:
> org.springframework.web.method.annotation.MethodArgumentTypeMismatchExcept ion:
> Failed to convert value of type 'java.lang.String' to required type'java.util.Date'; nested exception is org.springframework.core.convert.ConversionFailedException:
>
> Failed to convert from type [java.lang.String] to type [java.util.Date] for value '2018-01-01'; nested exception is java.lang.IllegalArgumentException

#### 2.1.2 使用步骤

第一步:定义一个类，实现 Converter 接口，该接口有两个泛型。

```java
public interface<S, T> {//S:表示接受的类型，T:表示目标类型 
       /**
        *实现类型转换的方法
        */
   @Nullable
    T convert(S source);
            }

/**
 * 自定义类型转换器
 */
public class StringToDateConverter implements Converter<String, Date> {
    /**
     * 用于把 String 类型转成日期类型
     */
    @Override
    public Date convert(String source) {
        DateFormat format = null;
        try {
            if (StringUtils.isEmpty(source)) {
                throw new NullPointerException("请输入要转换的日期");
            }
            format = new SimpleDateFormat("yyyy-MM-dd");
            Date date = format.parse(source);
            return date;
        } catch (Exception e) {
            throw new RuntimeException("输入日期有误");
        }
    }
}
```

第二步:在 spring 配置文件中配置类型转换器。

spring 配置类型转换器的机制是，将自定义的转换器注册到类型转换服务中去。

```xml
<!-- 配置类型转换器工厂 -->
<bean id="converterService"
      class="org.springframework.context.support.ConversionServiceFactoryBean"> 
    <!-- 给工厂注入一个新的类型转换器 -->
    <property name="converters">
        <array>
            <!-- 配置自定义类型转换器 -->
            <bean class="com.itheima.web.converter.StringToDateConverter"></bean>
        </array>
    </property>
</bean>
```

第三步:在 annotation-driven 标签中引用配置的类型转换服务

```xml
<!-- 引用自定义类型转换器 -->
<mvc:annotation-driven
 conversion-service="converterService"></mvc:annotation-driven>
```

运行结果:

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/17.png)

### 2.2 使用 ServletAPI 对象作为方法参数

SpringMVC 还支持使用原始 ServletAPI 对象作为控制器方法的参数。支持原始 ServletAPI 对象有:

> HttpServletRequest 
>
> HttpServletResponse 
>
> HttpSession 
>
> java.security.Principal 
>
> Locale
>
> InputStream
>
> OutputStream
>
> Reader
>
> Writer

我们可以把上述对象 ，直接写在控制的方法参数中使用。

部分示例代码:

jsp 代码:

```jsp
<!-- 原始 ServletAPI 作为控制器参数 -->
<a href="account/testServletAPI">测试访问ServletAPI</a>
```

控制器中的代码:

```java
/**
 * 测试访问 testServletAPI
 *
 * @return
 */

import org.springframework.web.bind.annotation.RequestMapping;

@RequestMapping("/testServletAPI")
public String testServletAPI(HttpServletRequest request,
        HttpServletResponse response,HttpSession session){
    
        System.out.println(request);
        System.out.println(response);
        System.out.println(session);
        return"success";
        }
```

执行结果:

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/springmvc/18.png)