---
title: 深入拆解Tomcat和Jetty之必备基础
date: 2021-03-19 18:12:20.377
author:
  name: "yuanshuai"
  link: "https://cloud.tencent.com/developer/user/8180692"
  email: "shuaiyuan1122@gmail.com"
  avatar: "https://aabb-2023.oss-cn-beijing.aliyuncs.com/hjscijg3uw.png"
tags: 
- Java
- Tomcat
---

> 本系列文章转自极客时间大佬李号双的专栏《深入拆解Tomcat & Jetty》
>
> 链接：https://time.geekbang.org/column/intro/100027701
>
> 仅供自己学习，联系我，侵删。

# 01 | Web容器学习路径

你好，我是李号双。在开篇词里我提到要成长为一名高级程序员或者架构师，我们需要提高自己知识的广度和深度。你可以先突破深度，再以点带面拓展广度，因此我建议通过深入学习一些优秀的开源系统来达到突破深度的目的。

我会跟你一起在这个专栏里深入学习 Web 容器 Tomcat 和 Jetty，而作为专栏更新的第 1 篇文章，我想和你谈谈什么是 Web 容器，以及怎么学习 Web 容器。根据我的经验，在学习一门技术之前，想一想这两个问题，往往可以达到事半功倍的效果。

## Web 容器是什么？

让我们先来简单回顾一下 Web 技术的发展历史，可以帮助你理解 Web 容器的由来。

早期的 Web 应用主要用于浏览新闻等静态页面，HTTP 服务器（比如 Apache、Nginx）向浏览器返回静态 HTML，浏览器负责解析 HTML，将结果呈现给用户。

随着互联网的发展，我们已经不满足于仅仅浏览静态页面，还希望通过一些交互操作，来获取动态结果，因此也就需要一些扩展机制能够让 HTTP 服务器调用服务端程序。

于是 Sun 公司推出了 Servlet 技术。你可以把 Servlet 简单理解为运行在服务端的 Java 小程序，但是 Servlet 没有 main 方法，不能独立运行，因此必须把它部署到 Servlet 容器中，由容器来实例化并调用 Servlet。

而 Tomcat 和 Jetty 就是一个 Servlet 容器。为了方便使用，它们也具有 HTTP 服务器的功能，因此**Tomcat 或者 Jetty 就是一个“HTTP 服务器 + Servlet 容器”，我们也叫它们 Web 容器。**

其他应用服务器比如 JBoss 和 WebLogic，它们不仅仅有 Servlet 容器的功能，也包含 EJB 容器，是完整的 Java EE 应用服务器。从这个角度看，Tomcat 和 Jetty 算是一个轻量级的应用服务器。

在微服务架构日渐流行的今天，开发人员更喜欢稳定的、轻量级的应用服务器，并且应用程序用内嵌的方式来运行 Servlet 容器也逐渐流行起来。之所以选择轻量级，是因为在微服务架构下，我们把一个大而全的单体应用，拆分成一个个功能单一的微服务，在这个过程中，服务的数量必然要增加，但为了减少资源的消耗，并且降低部署的成本，我们希望运行服务的 Web 容器也是轻量级的，Web 容器本身应该消耗较少的内存和 CPU 资源，并且由应用本身来启动一个嵌入式的 Web 容器，而不是通过 Web 容器来部署和启动应用，这样可以降低应用部署的复杂度。

因此轻量级的 Tomcat 和 Jetty 就是一个很好的选择，并且 Tomcat 它本身也是 Spring Boot 默认的嵌入式 Servlet 容器。最新版本 Tomcat 和 Jetty 都支持 Servlet 4.0 规范。

读到这里，我想你应该对 Web 容器有了基本的认识，可以结合平时工作再去细细体会一下。如果你对 HTTP 协议和 Servlet 依然是一头雾水，不用担心，在预习模块中我还会和你聊聊你应该掌握的 HTTP 协议和 Servlet 的相关知识，帮你打好学习的基础。

## Web 容器该怎么学？

Java Web 技术发展日新月异，各种框架也是百花齐放。在从事 Java Web 开发相关的工作时，面对这些眼花缭乱的技术时你是否会感到一丝迷茫？可能有些初学者不知道从哪里开始，我身边还有些已经进入了这个行业，并且有了一定 Java 基础的人，对于系统设计的体会可能还不够深刻，编程的时候还停留在完成功能的层次。这样不仅业务上难有突破，对于个人成长也很不利。

为了打破这个瓶颈，就需要我们在深度上多下功夫，找准一个点，深挖下去，彻底理解它的原理和设计精髓。并且在深入学习 Tomcat 和 Jetty 这样的 Web 容器之前，你还需要掌握一定的基础知识，这样才能达到事半功倍的效果。

下面我列举一些在学习 Web 容器之前需要掌握的关键点，我建议你在学习专栏的同时，再去复习一下这些基础知识。你可以把这些基础知识当作成为架构师的必经之路，在专栏以外也要花时间深入进去。当然为了让你更好地理解专栏每期所讲的内容，重点的基础知识我也会在文章里帮你再梳理一遍。

**操作系统基础**

Java 语言其实是对操作系统 API 的封装，上层应用包括 Web 容器都是通过操作系统来工作的，因此掌握相关的操作系统原理是我们深刻理解 Web 容器的基础。

对于 Web 容器来说，操作系统方面你应该掌握它的工作原理，比如什么是进程、什么是内核、什么是内核空间和用户空间、进程间通信的方式、进程和线程的区别、线程同步的方式、什么是虚拟内存、内存分配的过程、什么是 I/O、什么是 I/O 模型、阻塞与非阻塞的区别、同步与异步的区别、网络通信的原理、OSI 七层网络模型以及 TCP/IP、UDP 和 HTTP 协议。

总之一句话，基础扎实了，你学什么都快。关于操作系统的学习，我推荐你读一读《UNIX 环境高级编程》这本经典书籍。

**Java 语言基础**

Java 的基础知识包括 Java 基本语法、面向对象设计的概念（封装、继承、多态、接口、抽象类等）、Java 集合的使用、Java I/O 体系、异常处理、基本的多线程并发编程（包括线程同步、原子类、线程池、并发容器的使用和原理）、Java 网络编程（I/O 模型 BIO、NIO、AIO 的原理和相应的 Java API）、Java 注解以及 Java 反射的原理等。

此外你还需要了解一些 JVM 的基本知识，比如 JVM 的类加载机制、JVM 内存模型、JVM 内存空间分布、JVM 内存和本地内存的区别以及 JVM GC 的原理等。

这方面我推荐的经典书籍有[《Java 核心技术》](time://mall?url=http%3A%2F%2Fh5.youzan.com%2Fv2%2Fgoods%2F2fnx3ed6fpk3c)、[《Java 编程思想》](time://mall?url=http%3A%2F%2Fh5.youzan.com%2Fv2%2Fgoods%2F3f0ddticdedfc)、[《Java 并发编程实战》](time://mall?url=http%3A%2F%2Fh5.youzan.com%2Fv2%2Fgoods%2F2758xqdzr6uuw)和[《深入理解 Java 虚拟机：JVM 高级特性与最佳实践》](time://mall?url=http%3A%2F%2Fh5.youzan.com%2Fv2%2Fgoods%2F36a92yq65q4x4)等。

**Java Web 开发基础**

具备了一定的操作系统和 Java 基础，接下来就可以开始学习 Java Web 开发，你可以开始学习一些通用的设计原则和设计模式。这个阶段的核心任务就是了解 Web 的工作原理，**同时提高你的设计能力**，注重代码的质量。我的建议是可以从学习 Servlet 和 Servlet 容器开始。我见过不少同学跳过这个阶段直接学 Web 框架，这样做的话结果会事倍功半。

为什么这么说呢？Web 框架的本质是，开发者在使用某种语言编写 Web 应用时，总结出的一些经验和设计思路。很多 Web 框架都是从实际的 Web 项目抽取出来的，其目的是用于简化 Web 应用程序开发。

我以 Spring 框架为例，给你讲讲 Web 框架是怎么产生的。Web 应用程序的开发主要是完成两方面的工作。

- 设计并实现类，包括定义类与类之间的关系，以及实现类的方法，方法对数据的操作就是具体的业务逻辑。
- 类设计好之后，需要创建这些类的实例并根据类与类的关系把它们组装在一起，这样类的实例才能一起协作完成业务功能。

就好比制造一辆汽车，汽车是由零件组装而成的。第一步是画出各种零件的图纸，以及定义零件之间的接口。第二步把把图纸交给工厂去生产零件并组装在一起。因此对于 Web 应用开发来说，第一步工作是具体业务逻辑的实现，每个应用都不一样。而第二步工作，相对来说比较通用和标准化，工厂拿到零件的图纸，就知道怎么生产零件并按照零件之间的接口把它们组装起来，因此这个工作就被抽取出来交给 Spring 框架来做。

Spring 又是用容器来完成这个工作的的，容器负责创建、组装和销毁这些类的实例，而应用只需要通过配置文件或者注解来告诉 Spring 类与类之间的关系。但是容器的概念不是 Spring 发明的，最开始来源于 Servlet 容器，并且 Servlet 容器也是通过配置文件来加载 Servlet 的。你会发现它们的“元神”是相似的，在 Web 应用的开发中，有一些本质的东西是不变的，而很多“元神”就藏在“老祖宗”那里，藏在 Servlet 容器的设计里。

Spring 框架就是对 Servlet 的封装，Spring 应用本身就是一个 Servlet，而 Servlet 容器是管理和运行 Servlet 的，因此我们需要先理解 Servlet 和 Servlet 容器是怎样工作的，才能更好地理解 Spring。

## 本期精华

今天我谈了什么是 Web 容器，以及该如何学习 Web 容器。在深入学习之前，你需要掌握一些操作系统、Java 和 Web 的基础知识。我希望你在学习专栏的过程中多温习一下这些基础知识，有扎实的基础，再结合专栏深入学习 Web 容器就比较容易了。

等你深刻理解了 Web 容器的工作原理和设计精髓以后，你就可以把学到的知识扩展到其他领域，你会发现它们的本质都是相通的，这个时候你可以站在更高的角度来学习和审视各种 Web 框架。虽然 Web 框架的更新比较快，但是抓住了框架的本质，在学习的过程中，往往会更得心应手。

不知道你有没有遇到过这样的场景，当你在看一个框架的技术细节时，会突然恍然大悟：对啊，就是应该这么设计！如果你有这种感觉，说明你的知识储备起到了作用，你对框架的运用也会更加自如。

# 02 | HTTP协议必知必会

在开始学习 Web 容器之前，我想先问你一个问题：HTTP 和 HTML 有什么区别？

为什么我会问这个问题？你可以把它当作一个入门测试，检测一下自己的对 HTTP 协议的理解。因为 Tomcat 和 Jetty 本身就是一个“HTTP 服务器 + Servlet 容器”，如果你想深入理解 Tomcat 和 Jetty 的工作原理，我认为理解 HTTP 协议的工作原理是学习的基础。

如果你对这个问题还稍有迟疑，那么请跟我一起来回顾一下 HTTP 协议吧。

## HTTP 的本质

HTTP 协议是浏览器与服务器之间的数据传送协议。作为应用层协议，HTTP 是基于 TCP/IP 协议来传递数据的（HTML 文件、图片、查询结果等），HTTP 协议不涉及数据包（Packet）传输，主要规定了客户端和服务器之间的通信格式。

下面我通过一个例子来告诉你 HTTP 的本质是什么。

假如浏览器需要从远程 HTTP 服务器获取一个 HTML 文本，在这个过程中，浏览器实际上要做两件事情。

- 与服务器建立 Socket 连接。
- 生成**请求数据**并通过 Socket 发送出去。

第一步比较容易理解，浏览器从地址栏获取用户输入的网址和端口，去连接远端的服务器，这样就能通信了。

我们重点来看第二步，这个请求数据到底长什么样呢？都请求些什么内容呢？或者换句话说，浏览器需要告诉服务端什么信息呢？

首先最基本的是，你要让服务端知道你的意图，你是想获取内容还是提交内容；其次你需要告诉服务端你想要哪个内容。那么要把这些信息以一种什么样的格式放到请求里去呢？这就是 HTTP 协议要解决的问题。也就是说，**HTTP 协议的本质就是一种浏览器与服务器之间约定好的通信格式**。那浏览器与服务器之间具体是怎么工作的呢？

## HTTP 工作原理

请你来看下面这张图，我们过一遍一次 HTTP 的请求过程。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/tomcat_jetty/1.png)

从图上你可以看到，这个过程是：

1. 用户通过浏览器进行了一个操作，比如输入网址并回车，或者是点击链接，接着浏览器获取了这个事件。

2. 浏览器向服务端发出 TCP 连接请求。

3. 服务程序接受浏览器的连接请求，并经过 TCP 三次握手建立连接。

4. 浏览器将请求数据打包成一个 HTTP 协议格式的数据包。

5. 浏览器将该数据包推入网络，数据包经过网络传输，最终达到端服务程序。

6. 服务端程序拿到这个数据包后，同样以 HTTP 协议格式解包，获取到客户端的意图。

7. 得知客户端意图后进行处理，比如提供静态文件或者调用服务端程序获得动态结果。

8. 服务器将响应结果（可能是 HTML 或者图片等）按照 HTTP 协议格式打包。

9. 服务器将响应数据包推入网络，数据包经过网络传输最终达到到浏览器。

10. 浏览器拿到数据包后，以 HTTP 协议的格式解包，然后解析数据，假设这里的数据是 HTML。

11. 浏览器将 HTML 文件展示在页面上。

那我们想要探究的 Tomcat 和 Jetty 作为一个 HTTP 服务器，在这个过程中都做了些什么事情呢？主要是接受连接、解析请求数据、处理请求和发送响应这几个步骤。这里请你注意，可能有成千上万的浏览器同时请求同一个 HTTP 服务器，因此 Tomcat 和 Jetty 为了提高服务的能力和并发度，往往会将自己要做的几个事情并行化，具体来说就是使用多线程的技术。这也是专栏所关注的一个重点，我在后面会进行专门讲解。

## HTTP 请求响应实例

你有没有注意到，在浏览器和 HTTP 服务器之间通信的过程中，首先要将数据打包成 HTTP 协议的格式，那 HTTP 协议的数据包具体长什么样呢？这里我以极客时间的登陆请求为例，用户在登陆页面输入用户名和密码，点击登陆后，浏览器发出了这样的 HTTP 请求：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/tomcat_jetty/2.png)

你可以看到，HTTP 请求数据由三部分组成，分别是**请求行、请求报头、请求正文**。当这个 HTTP 请求数据到达 Tomcat 后，Tomcat 会把 HTTP 请求数据字节流解析成一个 Request 对象，这个 Request 对象封装了 HTTP 所有的请求信息。接着 Tomcat 把这个 Request 对象交给 Web 应用去处理，处理完后得到一个 Response 对象，Tomcat 会把这个 Response 对象转成 HTTP 格式的响应数据并发送给浏览器。

我们再来看看 HTTP 响应的格式，HTTP 的响应也是由三部分组成，分别是**状态行、响应报头、报文主体**。同样，我还以极客时间登陆请求的响应为例。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/tomcat_jetty/3.png)

具体的 HTTP 协议格式，你可以去网上搜索，我就不再赘述了。为了更好地帮助你理解 HTTP 服务器（比如 Tomcat）的工作原理，接下来我想谈一谈 Cookie 跟 Session 的原理。

## Cookie 和 Session

我们知道，HTTP 协议有个特点是无状态，请求与请求之间是没有关系的。这样会出现一个很尴尬的问题：Web 应用不知道你是谁。比如你登陆淘宝后，在购物车中添加了三件商品，刷新一下网页，这时系统提示你仍然处于未登录的状态，购物车也空了，很显然这种情况是不可接受的。因此 HTTP 协议需要一种技术让请求与请求之间建立起联系，并且服务器需要知道这个请求来自哪个用户，于是 Cookie 技术出现了。

**1. Cookie 技术**

Cookie 是 HTTP 报文的一个请求头，Web 应用可以将用户的标识信息或者其他一些信息（用户名等）存储在 Cookie 中。用户经过验证之后，每次 HTTP 请求报文中都包含 Cookie，这样服务器读取这个 Cookie 请求头就知道用户是谁了。**Cookie 本质上就是一份存储在用户本地的文件，里面包含了每次请求中都需要传递的信息**。

**2. Session 技术**

由于 Cookie 以明文的方式存储在本地，而 Cookie 中往往带有用户信息，这样就造成了非常大的安全隐患。而 Session 的出现解决了这个问题，**Session 可以理解为\**\*\*服务器\*\**\*端开辟的存储空间，里面保存了用户的状态**，用户信息以 Session 的形式存储在服务端。当用户请求到来时，服务端可以把用户的请求和用户的 Session 对应起来。那么 Session 是怎么和请求对应起来的呢？答案是通过 Cookie，浏览器在 Cookie 中填充了一个 Session ID 之类的字段用来标识请求。

具体工作过程是这样的：服务器在创建 Session 的同时，会为该 Session 生成唯一的 Session ID，当浏览器再次发送请求的时候，会将这个 Session ID 带上，服务器接受到请求之后就会依据 Session ID 找到相应的 Session，找到 Session 后，就可以在 Session 中获取或者添加内容了。而这些内容只会保存在服务器中，发到客户端的只有 Session ID，这样相对安全，也节省了网络流量，因为不需要在 Cookie 中存储大量用户信息。

**3. Session 创建与存储**

那么 Session 在何时何地创建呢？当然还是在服务器端程序运行的过程中创建的，不同语言实现的应用程序有不同的创建 Session 的方法。在 Java 中，是 Web 应用程序在调用 HttpServletRequest 的 getSession 方法时，由 Web 容器（比如 Tomcat）创建的。那 HttpServletRequest 又是什么呢？别着急，我们下一期再聊。

Tomcat 的 Session 管理器提供了多种持久化方案来存储 Session，通常会采用高性能的存储方式，比如 Redis，并且通过集群部署的方式，防止单点故障，从而提升高可用。同时，Session 有过期时间，因此 Tomcat 会开启后台线程定期的轮询，如果 Session 过期了就将 Session 失效。

## 本期精华

HTTP 协议和其他应用层协议一样，本质上是一种通信格式。回到文章开头我问你的问题，其实答案很简单：HTTP 是通信的方式，HTML 才是通信的目的，就好比 HTTP 是信封，信封里面的信（HTML）才是内容；但是没有信封，信也没办法寄出去。HTTP 协议就是浏览器与服务器之间的沟通语言，具体交互过程是请求、处理和响应。

由于 HTTP 是无状态的协议，为了识别请求是哪个用户发过来的，出现了 Cookie 和 Session 技术。Cookie 本质上就是一份存储在用户本地的文件，里面包含了每次请求中都需要传递的信息；Session 可以理解为服务器端开辟的存储空间，里面保存的信息用于保持状态。作为 Web 容器，Tomcat 负责创建和管理 Session，并提供了多种持久化方案来存储 Session。

# 03 | 你应该知道的Servlet规范和Servlet容器

通过专栏上一期的学习我们知道，浏览器发给服务端的是一个 HTTP 格式的请求，HTTP 服务器收到这个请求后，需要调用服务端程序来处理，所谓的服务端程序就是你写的 Java 类，一般来说不同的请求需要由不同的 Java 类来处理。

那么问题来了，HTTP 服务器怎么知道要调用哪个 Java 类的哪个方法呢。最直接的做法是在 HTTP 服务器代码里写一大堆 if else 逻辑判断：如果是 A 请求就调 X 类的 M1 方法，如果是 B 请求就调 Y 类的 M2 方法。但这样做明显有问题，因为 HTTP 服务器的代码跟业务逻辑耦合在一起了，如果新加一个业务方法还要改 HTTP 服务器的代码。

那该怎么解决这个问题呢？我们知道，面向接口编程是解决耦合问题的法宝，于是有一伙人就定义了一个接口，各种业务类都必须实现这个接口，这个接口就叫 Servlet 接口，有时我们也把实现了 Servlet 接口的业务类叫作 Servlet。

但是这里还有一个问题，对于特定的请求，HTTP 服务器如何知道由哪个 Servlet 来处理呢？Servlet 又是由谁来实例化呢？显然 HTTP 服务器不适合做这个工作，否则又和业务类耦合了。

于是，还是那伙人又发明了 Servlet 容器，Servlet 容器用来加载和管理业务类。HTTP 服务器不直接跟业务类打交道，而是把请求交给 Servlet 容器去处理，Servlet 容器会将请求转发到具体的 Servlet，如果这个 Servlet 还没创建，就加载并实例化这个 Servlet，然后调用这个 Servlet 的接口方法。因此 Servlet 接口其实是**Servlet 容器跟具体业务类之间的接口**。下面我们通过一张图来加深理解。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/tomcat_jetty/4.jpg)

图的左边表示 HTTP 服务器直接调用具体业务类，它们是紧耦合的。再看图的右边，HTTP 服务器不直接调用业务类，而是把请求交给容器来处理，容器通过 Servlet 接口调用业务类。因此 Servlet 接口和 Servlet 容器的出现，达到了 HTTP 服务器与业务类解耦的目的。

而 Servlet 接口和 Servlet 容器这一整套规范叫作 Servlet 规范。Tomcat 和 Jetty 都按照 Servlet 规范的要求实现了 Servlet 容器，同时它们也具有 HTTP 服务器的功能。作为 Java 程序员，如果我们要实现新的业务功能，只需要实现一个 Servlet，并把它注册到 Tomcat（Servlet 容器）中，剩下的事情就由 Tomcat 帮我们处理了。

接下来我们来看看 Servlet 接口具体是怎么定义的，以及 Servlet 规范又有哪些要重点关注的地方呢？

## Servlet 接口

Servlet 接口定义了下面五个方法：

```java
public interface Servlet {
    void init(ServletConfig config) throws ServletException;
    
    ServletConfig getServletConfig();
    
    void service(ServletRequest req, ServletResponse res）throws ServletException, IOException;
    
    String getServletInfo();
    
    void destroy();
}
```

其中最重要是的 service 方法，具体业务类在这个方法里实现处理逻辑。这个方法有两个参数：ServletRequest 和 ServletResponse。ServletRequest 用来封装请求信息，ServletResponse 用来封装响应信息，因此**本质上这两个类是对通信协议的封装。**

比如 HTTP 协议中的请求和响应就是对应了 HttpServletRequest 和 HttpServletResponse 这两个类。你可以通过 HttpServletRequest 来获取所有请求相关的信息，包括请求路径、Cookie、HTTP 头、请求参数等。此外，我在专栏上一期提到过，我们还可以通过 HttpServletRequest 来创建和获取 Session。而 HttpServletResponse 是用来封装 HTTP 响应的。

你可以看到接口中还有两个跟生命周期有关的方法 init 和 destroy，这是一个比较贴心的设计，Servlet 容器在加载 Servlet 类的时候会调用 init 方法，在卸载的时候会调用 destroy 方法。我们可能会在 init 方法里初始化一些资源，并在 destroy 方法里释放这些资源，比如 Spring MVC 中的 DispatcherServlet，就是在 init 方法里创建了自己的 Spring 容器。

你还会注意到 ServletConfig 这个类，ServletConfig 的作用就是封装 Servlet 的初始化参数。你可以在 web.xml 给 Servlet 配置参数，并在程序里通过 getServletConfig 方法拿到这些参数。

我们知道，有接口一般就有抽象类，抽象类用来实现接口和封装通用的逻辑，因此 Servlet 规范提供了 GenericServlet 抽象类，我们可以通过扩展它来实现 Servlet。虽然 Servlet 规范并不在乎通信协议是什么，但是大多数的 Servlet 都是在 HTTP 环境中处理的，因此 Servet 规范还提供了 HttpServlet 来继承 GenericServlet，并且加入了 HTTP 特性。这样我们通过继承 HttpServlet 类来实现自己的 Servlet，只需要重写两个方法：doGet 和 doPost。

## Servlet 容器

我在前面提到，为了解耦，HTTP 服务器不直接调用 Servlet，而是把请求交给 Servlet 容器来处理，那 Servlet 容器又是怎么工作的呢？接下来我会介绍 Servlet 容器大体的工作流程，一起来聊聊我们非常关心的两个话题：**Web 应用的目录格式是什么样的，以及我该怎样扩展和定制化 Servlet 容器的功能**。

**工作流程**

当客户请求某个资源时，HTTP 服务器会用一个 ServletRequest 对象把客户的请求信息封装起来，然后调用 Servlet 容器的 service 方法，Servlet 容器拿到请求后，根据请求的 URL 和 Servlet 的映射关系，找到相应的 Servlet，如果 Servlet 还没有被加载，就用反射机制创建这个 Servlet，并调用 Servlet 的 init 方法来完成初始化，接着调用 Servlet 的 service 方法来处理请求，把 ServletResponse 对象返回给 HTTP 服务器，HTTP 服务器会把响应发送给客户端。同样我通过一张图来帮助你理解。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/tomcat_jetty/5.jpg)

**Web 应用**

Servlet 容器会实例化和调用 Servlet，那 Servlet 是怎么注册到 Servlet 容器中的呢？一般来说，我们是以 Web 应用程序的方式来部署 Servlet 的，而根据 Servlet 规范，Web 应用程序有一定的目录结构，在这个目录下分别放置了 Servlet 的类文件、配置文件以及静态资源，Servlet 容器通过读取配置文件，就能找到并加载 Servlet。Web 应用的目录结构大概是下面这样的：

```java
| -  MyWebApp
      | -  WEB-INF/web.xml        -- 配置文件，用来配置 Servlet 等
      | -  WEB-INF/lib/           -- 存放 Web 应用所需各种 JAR 包
      | -  WEB-INF/classes/       -- 存放你的应用类，比如 Servlet 类
      | -  META-INF/              -- 目录存放工程的一些信息
```

Servlet 规范里定义了**ServletContext**这个接口来对应一个 Web 应用。Web 应用部署好后，Servlet 容器在启动时会加载 Web 应用，并为每个 Web 应用创建唯一的 ServletContext 对象。你可以把 ServletContext 看成是一个全局对象，一个 Web 应用可能有多个 Servlet，这些 Servlet 可以通过全局的 ServletContext 来共享数据，这些数据包括 Web 应用的初始化参数、Web 应用目录下的文件资源等。由于 ServletContext 持有所有 Servlet 实例，你还可以通过它来实现 Servlet 请求的转发。

**扩展机制**

不知道你有没有发现，引入了 Servlet 规范后，你不需要关心 Socket 网络通信、不需要关心 HTTP 协议，也不需要关心你的业务类是如何被实例化和调用的，因为这些都被 Servlet 规范标准化了，你只要关心怎么实现的你的业务逻辑。这对于程序员来说是件好事，但也有不方便的一面。所谓规范就是说大家都要遵守，就会千篇一律，但是如果这个规范不能满足你的业务的个性化需求，就有问题了，因此设计一个规范或者一个中间件，要充分考虑到可扩展性。Servlet 规范提供了两种扩展机制：**Filter**和**Listener**。

**Filter**是过滤器，这个接口允许你对请求和响应做一些统一的定制化处理，比如你可以根据请求的频率来限制访问，或者根据国家地区的不同来修改响应内容。过滤器的工作原理是这样的：Web 应用部署完成后，Servlet 容器需要实例化 Filter 并把 Filter 链接成一个 FilterChain。当请求进来时，获取第一个 Filter 并调用 doFilter 方法，doFilter 方法负责调用这个 FilterChain 中的下一个 Filter。

**Listener**是监听器，这是另一种扩展机制。当 Web 应用在 Servlet 容器中运行时，Servlet 容器内部会不断的发生各种事件，如 Web 应用的启动和停止、用户请求到达等。 Servlet 容器提供了一些默认的监听器来监听这些事件，当事件发生时，Servlet 容器会负责调用监听器的方法。当然，你可以定义自己的监听器去监听你感兴趣的事件，将监听器配置在 web.xml 中。比如 Spring 就实现了自己的监听器，来监听 ServletContext 的启动事件，目的是当 Servlet 容器启动时，创建并初始化全局的 Spring 容器。

到这里相信你对 Servlet 容器的工作原理有了深入的了解，只有理解了这些原理，我们才能更好的理解 Tomcat 和 Jetty，因为它们都是 Servlet 容器的具体实现。后面我还会详细谈到 Tomcat 和 Jetty 是如何设计和实现 Servlet 容器的，虽然它们的实现方法各有特点，但是都遵守了 Servlet 规范，因此你的 Web 应用可以在这两个 Servlet 容器中方便的切换。

## 本期精华

今天我们学习了什么是 Servlet，回顾一下，Servlet 本质上是一个接口，实现了 Servlet 接口的业务类也叫 Servlet。Servlet 接口其实是 Servlet 容器跟具体 Servlet 业务类之间的接口。Servlet 接口跟 Servlet 容器这一整套规范叫作 Servlet 规范，而 Servlet 规范使得程序员可以专注业务逻辑的开发，同时 Servlet 规范也给开发者提供了扩展的机制 Filter 和 Listener。

最后我给你总结一下 Filter 和 Listener 的本质区别：

- **Filter 是干预过程的**，它是过程的一部分，是基于过程行为的。
- **Listener 是基于状态的**，任何行为改变同一个状态，触发的事件是一致的。

# 04 | 实战：纯手工打造和运行一个Servlet

作为 Java 程序员，我们可能已经习惯了使用 IDE 和 Web 框架进行开发，IDE 帮我们做了编译、打包的工作，而 Spring 框架在背后帮我们实现了 Servlet 接口，并把 Servlet 注册到了 Web 容器，这样我们可能很少有机会接触到一些底层本质的东西，比如怎么开发一个 Servlet？如何编译 Servlet？如何在 Web 容器中跑起来？

今天我们就抛弃 IDE、拒绝框架，自己纯手工编写一个 Servlet，并在 Tomcat 中运行起来。一方面进一步加深对 Servlet 的理解；另一方面，还可以熟悉一下 Tomcat 的基本功能使用。

主要的步骤有：

\1. 下载并安装 Tomcat。
\2. 编写一个继承 HttpServlet 的 Java 类。
\3. 将 Java 类文件编译成 Class 文件。
\4. 建立 Web 应用的目录结构，并配置 web.xml。
\5. 部署 Web 应用。
\6. 启动 Tomcat。
\7. 浏览器访问验证结果。
\8. 查看 Tomcat 日志。

下面你可以跟我一起一步步操作来完成整个过程。Servlet 3.0 规范支持用注解的方式来部署 Servlet，不需要在 web.xml 里配置，最后我会演示怎么用注解的方式来部署 Servlet。

**1. 下载并安装 Tomcat**

最新版本的 Tomcat 可以直接在[官网](https://tomcat.apache.org/download-90.cgi)上下载，根据你的操作系统下载相应的版本，这里我使用的是 Mac 系统，下载完成后直接解压，解压后的目录结构如下。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/tomcat_jetty/6.png)

下面简单介绍一下这些目录：

/bin：存放 Windows 或 Linux 平台上启动和关闭 Tomcat 的脚本文件。
/conf：存放 Tomcat 的各种全局配置文件，其中最重要的是 server.xml。
/lib：存放 Tomcat 以及所有 Web 应用都可以访问的 JAR 文件。
/logs：存放 Tomcat 执行时产生的日志文件。
/work：存放 JSP 编译后产生的 Class 文件。
/webapps：Tomcat 的 Web 应用目录，默认情况下把 Web 应用放在这个目录下。

**2. 编写一个继承 HttpServlet 的 Java 类**

我在专栏上一期提到，javax.servlet 包提供了实现 Servlet 接口的 GenericServlet 抽象类。这是一个比较方便的类，可以通过扩展它来创建 Servlet。但是大多数的 Servlet 都在 HTTP 环境中处理请求，因此 Serve 规范还提供了 HttpServlet 来扩展 GenericServlet 并且加入了 HTTP 特性。我们通过继承 HttpServlet 类来实现自己的 Servlet 只需要重写两个方法：doGet 和 doPost。

因此今天我们创建一个 Java 类去继承 HttpServlet 类，并重写 doGet 和 doPost 方法。首先新建一个名为 MyServlet.java 的文件，敲入下面这些代码：

```java
import java.io.IOException;
import java.io.PrintWriter;
 
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
 
 
public class MyServlet extends HttpServlet {
 
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
 
        System.out.println("MyServlet 在处理 get（）请求...");
        PrintWriter out = response.getWriter();
        response.setContentType("text/html;charset=utf-8");
        out.println("<strong>My Servlet!</strong><br>");
    }
 
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
 
        System.out.println("MyServlet 在处理 post（）请求...");
        PrintWriter out = response.getWriter();
        response.setContentType("text/html;charset=utf-8");
        out.println("<strong>My Servlet!</strong><br>");
    }
 
}
```

这个 Servlet 完成的功能很简单，分别在 doGet 和 doPost 方法体里返回一段简单的 HTML。

**3. 将 Java 文件编译成 Class 文件**

下一步我们需要把 MyServlet.java 文件编译成 Class 文件。你需要先安装 JDK，这里我使用的是 JDK 10。接着你需要把 Tomcat lib 目录下的 servlet-api.jar 拷贝到当前目录下，这是因为 servlet-api.jar 中定义了 Servlet 接口，而我们的 Servlet 类实现了 Servlet 接口，因此编译 Servlet 类需要这个 JAR 包。接着我们执行编译命令：

```shell
javac -cp ./servlet-api.jar MyServlet.java
```

编译成功后，你会在当前目录下找到一个叫 MyServlet.class 的文件。

**4. 建立 Web 应用的目录结构**

我们在上一期学到，Servlet 是放到 Web 应用部署到 Tomcat 的，而 Web 应用具有一定的目录结构，所有我们按照要求建立 Web 应用文件夹，名字叫 MyWebApp，然后在这个目录下建立子文件夹，像下面这样：

```java
MyWebApp/WEB-INF/web.xml
 
MyWebApp/WEB-INF/classes/MyServlet.class
```

然后在 web.xml 中配置 Servlet，内容如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
  http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
  version="4.0"
  metadata-complete="true">
 
    <description> Servlet Example. </description>
    <display-name> MyServlet Example </display-name>
    <request-character-encoding>UTF-8</request-character-encoding>
 
    <servlet>
      <servlet-name>myServlet</servlet-name>
      <servlet-class>MyServlet</servlet-class>
    </servlet>
 
    <servlet-mapping>
      <servlet-name>myServlet</servlet-name>
      <url-pattern>/myservlet</url-pattern>
    </servlet-mapping>
 
</web-app>
```

你可以看到在 web.xml 配置了 Servlet 的名字和具体的类，以及这个 Servlet 对应的 URL 路径。请你注意，**servlet 和 servlet-mapping 这两个标签里的 servlet-name 要保持一致。**

**5. 部署 Web 应用**

Tomcat 应用的部署非常简单，将这个目录 MyWebApp 拷贝到 Tomcat 的安装目录下的 webapps 目录即可。

**6. 启动 Tomcat**

找到 Tomcat 安装目录下的 bin 目录，根据操作系统的不同，执行相应的启动脚本。如果是 Windows 系统，执行`startup.bat`.；如果是 Linux 系统，则执行`startup.sh`。

**7. 浏览访问验证结果**

在浏览器里访问这个 URL：`http://localhost:8080/MyWebApp/myservlet`，你会看到：

```
My Servlet!
```

这里需要注意，访问 URL 路径中的 MyWebApp 是 Web 应用的名字，myservlet 是在 web.xml 里配置的 Servlet 的路径。

**8. 查看 Tomcat 日志**

打开 Tomcat 的日志目录，也就是 Tomcat 安装目录下的 logs 目录。Tomcat 的日志信息分为两类 ：一是运行日志，它主要记录运行过程中的一些信息，尤其是一些异常错误日志信息 ；二是访问日志，它记录访问的时间、IP 地址、访问的路径等相关信息。

这里简要介绍各个文件的含义。

- `catalina.***.log`

主要是记录 Tomcat 启动过程的信息，在这个文件可以看到启动的 JVM 参数以及操作系统等日志信息。

- `catalina.out`

catalina.out 是 Tomcat 的标准输出（stdout）和标准错误（stderr），这是在 Tomcat 的启动脚本里指定的，如果没有修改的话 stdout 和 stderr 会重定向到这里。所以在这个文件里可以看到我们在 MyServlet.java 程序里打印出来的信息：

> MyServlet 在处理 get() 请求…

- `localhost.**.log`

主要记录 Web 应用在初始化过程中遇到的未处理的异常，会被 Tomcat 捕获而输出这个日志文件。

- `localhost_access_log.**.txt`

存放访问 Tomcat 的请求日志，包括 IP 地址以及请求的路径、时间、请求协议以及状态码等信息。

- `manager.***.log/host-manager.***.log`

存放 Tomcat 自带的 manager 项目的日志信息。

**用注解的方式部署 Servlet**

为了演示用注解的方式来部署 Servlet，我们首先修改 Java 代码，给 Servlet 类加上**@WebServlet**注解，修改后的代码如下。

```java
import java.io.IOException;
import java.io.PrintWriter;
 
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
 
@WebServlet("/myAnnotationServlet")
public class AnnotationServlet extends HttpServlet {
 
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        
   System.out.println("AnnotationServlet 在处理 get（）请求...");
        PrintWriter out = response.getWriter();
        response.setContentType("text/html; charset=utf-8");
        out.println("<strong>Annotation Servlet!</strong><br>");
 
    }
 
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
 
        System.out.println("AnnotationServlet 在处理 post（）请求...");
        PrintWriter out = response.getWriter();
        response.setContentType("text/html; charset=utf-8");
        out.println("<strong>Annotation Servlet!</strong><br>");
 
    }
 
}  
```

这段代码里最关键的就是这个注解，它表明两层意思：第一层意思是 AnnotationServlet 这个 Java 类是一个 Servlet，第二层意思是这个 Servlet 对应的 URL 路径是 myAnnotationServlet。

```java
@WebServlet("/myAnnotationServlet")
```

创建好 Java 类以后，同样经过编译，并放到 MyWebApp 的 class 目录下。这里要注意的是，你**需要删除原来的 web.xml**，因为我们不需要 web.xml 来配置 Servlet 了。然后重启 Tomcat，接下来我们验证一下这个新的 AnnotationServlet 有没有部署成功。在浏览器里输入：`http://localhost:8080/MyWebApp/myAnnotationServlet`，得到结果：

```
Annotation Servlet!
```

这说明我们的 AnnotationServlet 部署成功了。可以通过注解完成 web.xml 所有的配置功能，包括 Servlet 初始化参数以及配置 Filter 和 Listener 等。

## 本期精华

通过今天的学习和实践，相信你掌握了如何通过扩展 HttpServlet 来实现自己的 Servlet，知道了如何编译 Servlet、如何通过 web.xml 来部署 Servlet，同时还练习了如何启动 Tomcat、如何查看 Tomcat 的各种日志，并且还掌握了如何通过注解的方式来部署 Servlet。我相信通过专栏前面文章的学习加上今天的练习实践，一定会加深你对 Servlet 工作原理的理解。之所以我设置今天的实战练习，是希望你知道 IDE 和 Web 框架在背后为我们做了哪些事情，这对于我们排查问题非常重要，因为只有我们明白了 IDE 和框架在背后做的事情，一旦出现问题的时候，我们才能判断它们做得对不对，否则可能开发环境里的一个小问题就会折腾我们半天。