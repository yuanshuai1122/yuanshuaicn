---
title: Spring5之新功能Webflux
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

# **Webflux**

## **1**、**SpringWebflux** 介绍

(1)是 Spring5 添加新的模块，用于 web 开发的，功能和 SpringMVC 类似的，Webflux 使用 当前一种比较流程响应式编程出现的框架。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/spring5/43.png)

(2)使用传统 web 框架，比如 SpringMVC，这些基于 Servlet 容器，Webflux 是一种异步非阻 塞的框架，异步非阻塞的框架在 Servlet3.1 

以后才支持，核心是基于 Reactor 的相关 API 实现 的。

(3)解释什么是异步非阻塞

- 异步和同步
- 非阻塞和阻塞
  - 上面都是针对对象不一样

- 异步和同步针对调用者，调用者发送请求，如果等着对方回应之后才去做其他事情就是同步，如果发送请求之后不等着对方回应就去做其他事情就是异步

- 阻塞和非阻塞针对被调用者，被调用者受到请求之后，做完请求任务之后才给出反馈就是阻塞，受到请求之后马上给出反馈然后再去做事情就是非阻塞

(4)Webflux 特点:

 第一 非阻塞式:在有限资源下，提高系统吞吐量和伸缩性，以 Reactor 为基础实现响应式编程 第二 函数式编程:Spring5 框架基于 java8，

Webflux 使用 Java8 函数式编程方式实现路由请求

(5)比较 SpringMVC

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/spring5/44.png)

第一 两个框架都可以使用注解方式，都运行在 Tomcat 等容器中

第二 SpringMVC 采用命令式编程，Webflux 采用异步响应式编程

## **2**、响应式编程(**Java** 实现) 

(1)什么是响应式编程

响应式编程是一种面向数据流和变化传播的编程范式。这意味着可以在编程语言中很方便地表达静态或动态的数据流，而相关的计算模型会自动将变化的值通过数据流进行传播。

电子表格程序就是响应式编程的一个例子。单元格可以包含字面值或类似"=B1+C1"的公式，而包含公式的单元格的值会依据其他单元格的值的变化而变化。

(2)Java8 及其之前版本

- 提供的观察者模式两个类 Observer 和 Observable

```java
public class ObserverDemo extends Observable {
	public static void main(String[] args) { 
    ObserverDemo observer = new ObserverDemo(); 
    //添加观察者 
    observer.addObserver((o,arg)->{
			System.out.println("发生变化"); 
    });
		observer.addObserver((o,arg)->{ 
      System.out.println("手动被观察者通知，准备改变");
		});
			observer.setChanged(); //数据变化 
    observer.notifyObservers(); //通知
} 
}
```

## **3**、响应式编程(**Reactor** 实现)

 (**1**)响应式编程操作中，**Reactor** 是满足 **Reactive** 规范框架

 (**2**)**Reactor** 有两个核心类，**Mono** 和 **Flux**，这两个类实现接口 **Publisher**，提供丰富操作 符。**Flux** 对象实现发布者，返回 **N** 个元

素;**Mono** 实现发布者，返回 **0** 或者 **1** 个元素

(3)Flux 和 Mono 都是数据流的发布者，使用 Flux 和 Mono 都可以发出三种数据信号: 元素值，错误信号，完成信号，错误信号和完成信

号都代表终止信号，终止信号用于告诉 订阅者数据流结束了，错误信号终止数据流同时把错误信息传递给订阅者

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/spring5/45.png)

(**4**)代码演示 **Flux** 和 **Mono**

第一步 引入依赖

```xml
				<dependency>
            <groupId>io.projectreactor</groupId>
            <artifactId>reactor-core</artifactId>
            <version>3.1.5.RELEASE</version>
        </dependency>
```

第二步 编程代码

```java
public class TestReactor {

    public static void main(String[] args) {

        //just方法直接声明
        Flux.just(1,2,3,4);
        Mono.just(1);

        //其他的方法
//        Integer[] array = {1,2,3,4};
//        Flux.fromArray(array);
//
//        List<Integer> list = Arrays.asList(array);
//        Flux.fromIterable(list);
//
//        Stream<Integer> stream = list.stream();
//        Flux.fromStream(stream);

    }
}

```

(5)三种信号特点

- 错误信号和完成信号都是终止信号，不能共存的
- 如果没有发送任何元素值，而是直接发送错误或者完成信号，表示是空数据流
- 如果没有错误信号，没有完成信号，表示是无限数据流

(6)调用 just 或者其他方法只是声明数据流，数据流并没有发出，只有进行订阅之后才会触 发数据流，不订阅什么都不会发生的

```java
				//just方法直接声明
        Flux.just(1,2,3,4).subscribe(System.out::print);
        Mono.just(1).subscribe(System.out::print);
```

(**7**)操作符

对数据流进行一道道操作，成为操作符，比如工厂流水线

- 第一 map 元素映射为新元素

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/spring5/46.png)

- 第二 flatMap 元素映射为流
  - 把每个元素转换流，把转换之后多个流合并大的流

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/spring5/47.png)

## **4**、**SpringWebflux** 执行流程和核心 **API

 SpringWebflux 基于 Reactor，默认使用容器是 Netty，Netty 是高性能的 NIO 框架，异步非阻塞的框架

 (1)Netty(blocking I/O)： 同步并阻塞

- BIO

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/spring5/48.png)

- NIO (non-blocking I/O)： 同步非阻塞

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/spring5/49.png)

(2)SpringWebflux 执行过程和 SpringMVC 相似的

- SpringWebflux 核心控制器 DispatchHandler，实现接口 WebHandler
- 接口 WebHandler 有一个方法

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/spring5/50.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/spring5/51.png)

(3)SpringWebflux 里面 DispatcherHandler，负责请求的处理

- HandlerMapping:请求查询到处理的方法

- HandlerAdapter:真正负责请求处理
- HandlerResultHandler:响应结果处理

(4)SpringWebflux 实现函数式编程，两个接口:RouterFunction(路由处理) 和 HandlerFunction(处理函数)

## **5**、**SpringWebflux**(基于注解编程模型)

 SpringWebflux 实现方式有两种:注解编程模型和函数式编程模型 使用注解编程模型方式，和之前 SpringMVC 使用相似的，只需要把相

关依赖配置到项目中， SpringBoot 自动配置相关运行容器，默认情况下使用 Netty 服务器

第一步 创建 SpringBoot 工程，引入 Webflux 依赖

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/spring5/52.png)

```xml
				<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-webflux</artifactId>
        </dependency>
```

第二步 配置启动端口号

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/spring5/53.png)

第三步 创建包和相关类

- 实体类

```java
//实体类
public class User {
    private String name;
    private String gender;
    private Integer age;

    public User() {
    }

    public User(String name, String gender, Integer age) {
        this.name = name;
        this.gender = gender;
        this.age = age;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setGender(String gender) {
        this.gender = gender;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public String getGender() {
        return gender;
    }

    public Integer getAge() {
        return age;
    }
}

```

- 创建接口定义操作的方法

```java
//用户操作接口
public interface UserService {
    //根据id查询用户
    Mono<User> getUserById(int id);

    //查询所有用户
    Flux<User> getAllUser();

    //添加用户
    Mono<Void> saveUserInfo(Mono<User> user);
}
```

- 接口实现类

```java
@Repository
public class UserServiceImpl implements UserService {

    //创建map集合存储数据
    private final Map<Integer,User> users = new HashMap<>();

    public UserServiceImpl() {
        this.users.put(1,new User("lucy","nan",20));
        this.users.put(2,new User("mary","nv",30));
        this.users.put(3,new User("jack","nv",50));
    }

    //根据id查询
    @Override
    public Mono<User> getUserById(int id) {
        return Mono.justOrEmpty(this.users.get(id));
    }

    //查询多个用户
    @Override
    public Flux<User> getAllUser() {
        return Flux.fromIterable(this.users.values());
    }

    //添加用户
    @Override
    public Mono<Void> saveUserInfo(Mono<User> userMono) {
        return userMono.doOnNext(person -> {
            //向map集合里面放值
            int id = users.size()+1;
            users.put(id,person);
        }).thenEmpty(Mono.empty());
    }
}

```

- 创建controller

```java
@RestController
public class UserController { 
  //注入 service
	@Autowired
	private UserService userService;
	//id 查询
	@GetMapping("/user/{id}")
	public Mono<User> geetUserId(@PathVariable int id) {
		return userService.getUserById(id); 
  }
	//查询所有
	@GetMapping("/user")
	public Flux<User> getUsers() {
		return userService.getAllUser(); 
  }
	//添加
	@PostMapping("/saveuser")
	public Mono<Void> saveUser(@RequestBody User user) {
		Mono<User> userMono = Mono.just(user);
		return userService.saveUserInfo(userMono); 
  }
}
```

说明
SpringMVC 方式实现，同步阻塞的方式，基于 SpringMVC+Servlet+Tomcat SpringWebflux 方式实现，异步非阻塞方式，基于 

SpringWebflux+Reactor+Netty

**6**、**SpringWebflux**(基于函数式编程模型) 

(1)在使用函数式编程模型操作时候，需要自己初始化服务器 

(2)基于函数式编程模型时候，有两个核心接口:RouterFunction(实现路由功能，请求转发 给对应的 handler)和 HandlerFunction(处理请求生成响应的函数)。核心任务定义两个函数 式接口的实现并且启动需要的服务器。

 (3)SpringWebflux 请求和响应不再是 ServletRequest 和 ServletResponse，而是 ServerRequest 和 ServerResponse

第一步 把注解编程模型工程复制一份 ，保留 entity 和 service 内容 第二步 创建 Handler(具体实现方法)

```java
public class UserHandler {

    private final UserService userService;
    public UserHandler(UserService userService) {
        this.userService = userService;
    }

    //根据id查询
    public Mono<ServerResponse> getUserById(ServerRequest request) {
        //获取id值
       int userId = Integer.valueOf(request.pathVariable("id"));
       //空值处理
        Mono<ServerResponse> notFound = ServerResponse.notFound().build();

       //调用service方法得到数据
        Mono<User> userMono = this.userService.getUserById(userId);
        //把userMono进行转换返回
        //使用Reactor操作符flatMap
        return
                userMono
                        .flatMap(person -> ServerResponse.ok().contentType(MediaType.APPLICATION_JSON)
                                .body(fromObject(person)))
                                .switchIfEmpty(notFound);
    }

    //查询所有
    public Mono<ServerResponse> getAllUsers(ServerRequest request) {
        //调用service得到结果
        Flux<User> users = this.userService.getAllUser();
        return ServerResponse.ok().contentType(MediaType.APPLICATION_JSON).body(users,User.class);
    }

    //添加
    public Mono<ServerResponse> saveUser(ServerRequest request) {
        //得到user对象
        Mono<User> userMono = request.bodyToMono(User.class);
        return ServerResponse.ok().build(this.userService.saveUserInfo(userMono));
    }

}

```

第三步 初始化服务器，编写 Router

- 创建路由的方法

```java
//1 创建Router路由
    public RouterFunction<ServerResponse> routingFunction() {
        //创建hanler对象
        UserService userService = new UserServiceImpl();
        UserHandler handler = new UserHandler(userService);
        //设置路由
        return RouterFunctions.route(
                GET("/users/{id}").and(accept(APPLICATION_JSON)),handler::getUserById)
                .andRoute(GET("/users").and(accept(APPLICATION_JSON)),handler::getAllUsers);
    }
```

- 创建服务器完成适配

```java
//2 创建服务器完成适配
    public void createReactorServer() {
        //路由和handler适配
        RouterFunction<ServerResponse> route = routingFunction();
        HttpHandler httpHandler = toHttpHandler(route);
        ReactorHttpHandlerAdapter adapter = new ReactorHttpHandlerAdapter(httpHandler);
        //创建服务器
        HttpServer httpServer = HttpServer.create();
        httpServer.handle(adapter).bindNow();
    }
```

- 最终调用

```java
public static void main(String[] args) throws Exception{
        Server server = new Server();
        server.createReactorServer();
        System.out.println("enter to exit");
        System.in.read();
    }
```

(4)使用 WebClient 调用

```java
public class Client {

    public static void main(String[] args) {
        //调用服务器地址
        WebClient webClient = WebClient.create("http://127.0.0.1:5794");

        //根据id查询
        String id = "1";
        User userresult = webClient.get().uri("/users/{id}", id)
                .accept(MediaType.APPLICATION_JSON).retrieve().bodyToMono(User.class)
                .block();
        System.out.println(userresult.getName());

        //查询所有
        Flux<User> results = webClient.get().uri("/users")
                .accept(MediaType.APPLICATION_JSON).retrieve().bodyToFlux(User.class);

        results.map(stu -> stu.getName())
                    .buffer().doOnNext(System.out::println).blockFirst();
    }
}
```