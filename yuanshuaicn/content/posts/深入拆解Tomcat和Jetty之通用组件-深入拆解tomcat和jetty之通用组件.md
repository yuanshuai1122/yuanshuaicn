---
title: 深入拆解Tomcat和Jetty之通用组件
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

# 31 | Logger组件：Tomcat的日志框架及实战

每一个系统都有一些通用的模块，比如日志模块、异常处理模块、工具类等，对于 Tomcat 来说，比较重要的通用模块有日志、Session 管理和集群管理。从今天开始我会分三期来介绍通用模块，今天这一期先来讲日志模块。

日志模块作为一个通用的功能，在系统里通常会使用第三方的日志框架。Java 的日志框架有很多，比如：JUL（Java Util Logging）、Log4j、Logback、Log4j2、Tinylog 等。除此之外，还有 JCL（Apache Commons Logging）和 SLF4J 这样的“门面日志”。下面是 SLF4J 与日志框架 Logback、Log4j 的关系图：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/tomcat_jetty/69.png)

我先来解释一下什么是“门面日志”。“门面日志”利用了设计模式中的门面模式思想，对外提供一套通用的日志记录的 API，而不提供具体的日志输出服务，如果要实现日志输出，需要集成其他的日志框架，比如 Log4j、Logback、Log4j2 等。

这种门面模式的好处在于，记录日志的 API 和日志输出的服务分离开，代码里面只需要关注记录日志的 API，通过 SLF4J 指定的接口记录日志；而日志输出通过引入 JAR 包的方式即可指定其他的日志框架。当我们需要改变系统的日志输出服务时，不用修改代码，只需要改变引入日志输出框架 JAR 包。

今天我们就来看看 Tomcat 的日志模块是如何实现的。默认情况下，Tomcat 使用自身的 JULI 作为 Tomcat 内部的日志处理系统。JULI 的日志门面采用了 JCL；而 JULI 的具体实现是构建在 Java 原生的日志系统`java.util.logging`之上的，所以在看 JULI 的日志系统之前，我先简单介绍一下 Java 的日志系统。

## Java 日志系统

Java 的日志包在`java.util.logging`路径下，包含了几个比较重要的组件，我们通过一张图来理解一下：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/tomcat_jetty/70.png)

从图上我们看到这样几个重要的组件：

- Logger：用来记录日志的类。
- Handler：规定了日志的输出方式，如控制台输出、写入文件。
- Level：定义了日志的不同等级。
- Formatter：将日志信息格式化，比如纯文本、XML。

我们可以通过下面的代码来使用这些组件：

```java
public static void main(String[] args) {
  Logger logger = Logger.getLogger("com.mycompany.myapp");
  logger.setLevel(Level.FINE);
  logger.setUseParentHandlers(false);
  Handler hd = new ConsoleHandler();
  hd.setLevel(Level.FINE);
  logger.addHandler(hd);
  logger.info("start log"); 
}
```

## JULI

JULI 对日志的处理方式与 Java 自带的基本一致，但是 Tomcat 中可以包含多个应用，而每个应用的日志系统应该相互独立。Java 的原生日志系统是每个 JVM 有一份日志的配置文件，这不符合 Tomcat 多应用的场景，所以 JULI 重新实现了一些日志接口。

**DirectJDKLog**

Log 的基础实现类是 DirectJDKLog，这个类相对简单，就包装了一下 Java 的 Logger 类。但是它也在原来的基础上进行了一些修改，比如修改默认的格式化方式。

**LogFactory**

Log 使用了工厂模式来向外提供实例，LogFactory 是一个单例，可以通过 SeviceLoader 为 Log 提供自定义的实现版本，如果没有配置，就默认使用 DirectJDKLog。

```java
private LogFactory() {
    // 通过 ServiceLoader 尝试加载 Log 的实现类
    ServiceLoader<Log> logLoader = ServiceLoader.load(Log.class);
    Constructor<? extends Log> m=null;
    
    for (Log log: logLoader) {
        Class<? extends Log> c=log.getClass();
        try {
            m=c.getConstructor(String.class);
            break;
        }
        catch (NoSuchMethodException | SecurityException e) {
            throw new Error(e);
        }
    }
    
    // 如何没有定义 Log 的实现类，discoveredLogConstructor 为 null
    discoveredLogConstructor = m;
}
```

下面的代码是 LogFactory 的 getInstance 方法：

```java
public Log getInstance(String name) throws LogConfigurationException {
    // 如果 discoveredLogConstructor 为 null，也就没有定义 Log 类，默认用 DirectJDKLog
    if (discoveredLogConstructor == null) {
        return DirectJDKLog.getInstance(name);
    }
 
    try {
        return discoveredLogConstructor.newInstance(name);
    } catch (ReflectiveOperationException | IllegalArgumentException e) {
        throw new LogConfigurationException(e);
    }
}
```

**Handler**

在 JULI 中就自定义了两个 Handler：FileHandler 和 AsyncFileHandler。FileHandler 可以简单地理解为一个在特定位置写文件的工具类，有一些写操作常用的方法，如 open、write(publish)、close、flush 等，使用了读写锁。其中的日志信息通过 Formatter 来格式化。

AsyncFileHandler 继承自 FileHandler，实现了异步的写操作。其中缓存存储是通过阻塞双端队列 LinkedBlockingDeque 来实现的。当应用要通过这个 Handler 来记录一条消息时，消息会先被存储到队列中，而在后台会有一个专门的线程来处理队列中的消息，取出的消息会通过父类的 publish 方法写入相应文件内。这样就可以在大量日志需要写入的时候起到缓冲作用，防止都阻塞在写日志这个动作上。需要注意的是，我们可以为阻塞双端队列设置不同的模式，在不同模式下，对新进入的消息有不同的处理方式，有些模式下会直接丢弃一些日志：

```java
OVERFLOW_DROP_LAST：丢弃栈顶的元素 
OVERFLOW_DROP_FIRSH：丢弃栈底的元素 
OVERFLOW_DROP_FLUSH：等待一定时间并重试，不会丢失元素 
OVERFLOW_DROP_CURRENT：丢弃放入的元素
```

**Formatter**

Formatter 通过一个 format 方法将日志记录 LogRecord 转化成格式化的字符串，JULI 提供了三个新的 Formatter。

- OnlineFormatter：基本与 Java 自带的 SimpleFormatter 格式相同，不过把所有内容都写到了一行中。
- VerbatimFormatter：只记录了日志信息，没有任何额外的信息。
- JdkLoggerFormatter：格式化了一个轻量级的日志信息。

**日志配置**

Tomcat 的日志配置文件为 Tomcat 文件夹下`conf/logging.properties`。我来拆解一下这个配置文件，首先可以看到各种 Handler 的配置：

```java
handlers = 1catalina.org.apache.juli.AsyncFileHandler, 2localhost.org.apache.juli.AsyncFileHandler, 3manager.org.apache.juli.AsyncFileHandler, 4host-manager.org.apache.juli.AsyncFileHandler, java.util.logging.ConsoleHandler
 
.handlers = 1catalina.org.apache.juli.AsyncFileHandler, java.util.logging.ConsoleHandler
```

以`1catalina.org.apache.juli.AsyncFileHandler`为例，数字是为了区分同一个类的不同实例；catalina、localhost、manager 和 host-manager 是 Tomcat 用来区分不同系统日志的标志；后面的字符串表示了 Handler 具体类型，如果要添加 Tomcat 服务器的自定义 Handler，需要在字符串里添加。

接下来是每个 Handler 设置日志等级、目录和文件前缀，自定义的 Handler 也要在这里配置详细信息:

```java
1catalina.org.apache.juli.AsyncFileHandler.level = FINE
1catalina.org.apache.juli.AsyncFileHandler.directory = ${catalina.base}/logs
1catalina.org.apache.juli.AsyncFileHandler.prefix = catalina.
1catalina.org.apache.juli.AsyncFileHandler.maxDays = 90
1catalina.org.apache.juli.AsyncFileHandler.encoding = UTF-8
```

## Tomcat + SLF4J + Logback

在今天文章开头我提到，SLF4J 和 JCL 都是日志门面，那它们有什么区别呢？它们的区别主要体现在日志服务类的绑定机制上。JCL 采用运行时动态绑定的机制，在运行时动态寻找和加载日志框架实现。

SLF4J 日志输出服务绑定则相对简单很多，在编译时就静态绑定日志框架，只需要提前引入需要的日志框架。另外 Logback 可以说 Log4j 的进化版，在性能和可用性方面都有所提升。你可以参考官网上这篇[文章](https://logback.qos.ch/reasonsToSwitch.html)来了解 Logback 的优势。

基于此我们来实战一下如何将 Tomcat 默认的日志框架切换成为“SLF4J + Logback”。具体的步骤是：

1. 根据你的 Tomcat 版本，从[这里](https://github.com/tomcat-slf4j-logback/tomcat-slf4j-logback/releases)下载所需要文件。解压后你会看到一个类似于 Tomcat 目录结构的文件夹。
2. 替换或拷贝下列这些文件到 Tomcat 的安装目录：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/tomcat_jetty/71.jpg)

3. 删除`<Tomcat>/conf/logging.properties`
4. 启动 Tomcat

## 本期精华

今天我们谈了日志框架与日志门面的区别，以及 Tomcat 的日志模块是如何实现的。默认情况下，Tomcat 的日志模板叫作 JULI，JULI 的日志门面采用了 JCL，而具体实现是基于 Java 默认的日志框架 Java Util Logging，Tomcat 在 Java Util Logging 基础上进行了改造，使得它自身的日志框架不会影响 Web 应用，并且可以分模板配置日志的输出文件和格式。最后我分享了如何将 Tomcat 的日志模块切换到时下流行的“SLF4J + Logback”，希望对你有所帮助。

# 32 | Manager组件：Tomcat的Session管理机制解析

我们可以通过 Request 对象的 getSession 方法来获取 Session，并通过 Session 对象来读取和写入属性值。而 Session 的管理是由 Web 容器来完成的，主要是对 Session 的创建和销毁，除此之外 Web 容器还需要将 Session 状态的变化通知给监听者。

当然 Session 管理还可以交给 Spring 来做，好处是与特定的 Web 容器解耦，Spring Session 的核心原理是通过 Filter 拦截 Servlet 请求，将标准的 ServletRequest 包装一下，换成 Spring 的 Request 对象，这样当我们调用 Request 对象的 getSession 方法时，Spring 在背后为我们创建和管理 Session。

那么 Tomcat 的 Session 管理机制我们还需要了解吗？我觉得还是有必要，因为只有了解这些原理，我们才能更好的理解 Spring Session，以及 Spring Session 为什么设计成这样。今天我们就从 Session 的创建、Session 的清理以及 Session 的事件通知这几个方面来了解 Tomcat 的 Session 管理机制。

## Session 的创建

Tomcat 中主要由每个 Context 容器内的一个 Manager 对象来管理 Session。默认实现类为 StandardManager。下面我们通过它的接口来了解一下 StandardManager 的功能：

```java
public interface Manager {
    public Context getContext();
    public void setContext(Context context);
    public SessionIdGenerator getSessionIdGenerator();
    public void setSessionIdGenerator(SessionIdGenerator sessionIdGenerator);
    public long getSessionCounter();
    public void setSessionCounter(long sessionCounter);
    public int getMaxActive();
    public void setMaxActive(int maxActive);
    public int getActiveSessions();
    public long getExpiredSessions();
    public void setExpiredSessions(long expiredSessions);
    public int getRejectedSessions();
    public int getSessionMaxAliveTime();
    public void setSessionMaxAliveTime(int sessionMaxAliveTime);
    public int getSessionAverageAliveTime();
    public int getSessionCreateRate();
    public int getSessionExpireRate();
    public void add(Session session);
    public void changeSessionId(Session session);
    public void changeSessionId(Session session, String newId);
    public Session createEmptySession();
    public Session createSession(String sessionId);
    public Session findSession(String id) throws IOException;
    public Session[] findSessions();
    public void load() throws ClassNotFoundException, IOException;
    public void remove(Session session);
    public void remove(Session session, boolean update);
    public void addPropertyChangeListener(PropertyChangeListener listener)
    public void removePropertyChangeListener(PropertyChangeListener listener);
    public void unload() throws IOException;
    public void backgroundProcess();
    public boolean willAttributeDistribute(String name, Object value);
}
```

不出意外我们在接口中看到了添加和删除 Session 的方法；另外还有 load 和 unload 方法，它们的作用是分别是将 Session 持久化到存储介质和从存储介质加载 Session。

当我们调用`HttpServletRequest.getSession(true)`时，这个参数 true 的意思是“如果当前请求还没有 Session，就创建一个新的”。那 Tomcat 在背后为我们做了些什么呢？

HttpServletRequest 是一个接口，Tomcat 实现了这个接口，具体实现类是：`org.apache.catalina.connector.Request`。

但这并不是我们拿到的 Request，Tomcat 为了避免把一些实现细节暴露出来，还有基于安全上的考虑，定义了 Request 的包装类，叫作 RequestFacade，我们可以通过代码来理解一下：

```java
public class Request implements HttpServletRequest {}
复制代码
public class RequestFacade implements HttpServletRequest {
  protected Request request = null;
  
  public HttpSession getSession(boolean create) {
     return request.getSession(create);
  }
}
```

因此我们拿到的 Request 类其实是 RequestFacade，RequestFacade 的 getSession 方法调用的是 Request 类的 getSession 方法，我们继续来看 Session 具体是如何创建的：

```java
Context context = getContext();
if (context == null) {
    return null;
}
 
Manager manager = context.getManager();
if (manager == null) {
    return null;      
}
 
session = manager.createSession(sessionId);
session.access();
```

从上面的代码可以看出，Request 对象中持有 Context 容器对象，而 Context 容器持有 Session 管理器 Manager，这样通过 Context 组件就能拿到 Manager 组件，最后由 Manager 组件来创建 Session。

因此最后还是到了 StandardManager，StandardManager 的父类叫 ManagerBase，这个 createSession 方法定义在 ManagerBase 中，StandardManager 直接重用这个方法。

接着我们来看 ManagerBase 的 createSession 是如何实现的：

```java
@Override
public Session createSession(String sessionId) {
    // 首先判断 Session 数量是不是到了最大值，最大 Session 数可以通过参数设置
    if ((maxActiveSessions >= 0) &&
            (getActiveSessions() >= maxActiveSessions)) {
        rejectedSessions++;
        throw new TooManyActiveSessionsException(
                sm.getString("managerBase.createSession.ise"),
                maxActiveSessions);
    }
 
    // 重用或者创建一个新的 Session 对象，请注意在 Tomcat 中就是 StandardSession
    // 它是 HttpSession 的具体实现类，而 HttpSession 是 Servlet 规范中定义的接口
    Session session = createEmptySession();
 
 
    // 初始化新 Session 的值
    session.setNew(true);
    session.setValid(true);
    session.setCreationTime(System.currentTimeMillis());
    session.setMaxInactiveInterval(getContext().getSessionTimeout() * 60);
    String id = sessionId;
    if (id == null) {
        id = generateSessionId();
    }
    session.setId(id);// 这里会将 Session 添加到 ConcurrentHashMap 中
    sessionCounter++;
    
    // 将创建时间添加到 LinkedList 中，并且把最先添加的时间移除
    // 主要还是方便清理过期 Session
    SessionTiming timing = new SessionTiming(session.getCreationTime(), 0);
    synchronized (sessionCreationTiming) {
        sessionCreationTiming.add(timing);
        sessionCreationTiming.poll();
    }
    return session
}
```

到此我们明白了 Session 是如何创建出来的，创建出来后 Session 会被保存到一个 ConcurrentHashMap 中：

```java
protected Map<String, Session> sessions = new ConcurrentHashMap<>();
```

请注意 Session 的具体实现类是 StandardSession，StandardSession 同时实现了`javax.servlet.http.HttpSession`和`org.apache.catalina.Session`接口，并且对程序员暴露的是 StandardSessionFacade 外观类，保证了 StandardSession 的安全，避免了程序员调用其内部方法进行不当操作。StandardSession 的核心成员变量如下：

```java
public class StandardSession implements HttpSession, Session, Serializable {
    protected ConcurrentMap<String, Object> attributes = new ConcurrentHashMap<>();
    protected long creationTime = 0L;
    protected transient volatile boolean expiring = false;
    protected transient StandardSessionFacade facade = null;
    protected String id = null;
    protected volatile long lastAccessedTime = creationTime;
    protected transient ArrayList<SessionListener> listeners = new ArrayList<>();
    protected transient Manager manager = null;
    protected volatile int maxInactiveInterval = -1;
    protected volatile boolean isNew = false;
    protected volatile boolean isValid = false;
    protected transient Map<String, Object> notes = new Hashtable<>();
    protected transient Principal principal = null;
}
```

## Session 的清理

我们再来看看 Tomcat 是如何清理过期的 Session。在 Tomcat[热加载和热部署](https://time.geekbang.org/column/article/104423)的文章里，我讲到容器组件会开启一个 ContainerBackgroundProcessor 后台线程，调用自己以及子容器的 backgroundProcess 进行一些后台逻辑的处理，和 Lifecycle 一样，这个动作也是具有传递性的，也就是说子容器还会把这个动作传递给自己的子容器。你可以参考下图来理解这个过程。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/tomcat_jetty/72.jpg)

其中父容器会遍历所有的子容器并调用其 backgroundProcess 方法，而 StandardContext 重写了该方法，它会调用 StandardManager 的 backgroundProcess 进而完成 Session 的清理工作，下面是 StandardManager 的 backgroundProcess 方法的代码：

```java
public void backgroundProcess() {
    // processExpiresFrequency 默认值为 6，而 backgroundProcess 默认每隔 10s 调用一次，也就是说除了任务执行的耗时，每隔 60s 执行一次
    count = (count + 1) % processExpiresFrequency;
    if (count == 0) // 默认每隔 60s 执行一次 Session 清理
        processExpires();
}
 
/**
 * 单线程处理，不存在线程安全问题
 */
public void processExpires() {
 
    // 获取所有的 Session
    Session sessions[] = findSessions();   
    int expireHere = 0 ;
    for (int i = 0; i < sessions.length; i++) {
        // Session 的过期是在 isValid() 方法里处理的
        if (sessions[i]!=null && !sessions[i].isValid()) {
            expireHere++;
        }
    }
}
```

backgroundProcess 由 Tomcat 后台线程调用，默认是每隔 10 秒调用一次，但是 Session 的清理动作不能太频繁，因为需要遍历 Session 列表，会耗费 CPU 资源，所以在 backgroundProcess 方法中做了取模处理，backgroundProcess 调用 6 次，才执行一次 Session 清理，也就是说 Session 清理每 60 秒执行一次。

## Session 事件通知

按照 Servlet 规范，在 Session 的生命周期过程中，要将事件通知监听者，Servlet 规范定义了 Session 的监听器接口：

```java
public interface HttpSessionListener extends EventListener {
    //Session 创建时调用
    public default void sessionCreated(HttpSessionEvent se) {
    }
    
    //Session 销毁时调用
    public default void sessionDestroyed(HttpSessionEvent se) {
    }
}
```

注意到这两个方法的参数都是 HttpSessionEvent，所以 Tomcat 需要先创建 HttpSessionEvent 对象，然后遍历 Context 内部的 LifecycleListener，并且判断是否为 HttpSessionListener 实例，如果是的话则调用 HttpSessionListener 的 sessionCreated 方法进行事件通知。这些事情都是在 Session 的 setId 方法中完成的：

```java
session.setId(id);
 
@Override
public void setId(String id, boolean notify) {
    // 如果这个 id 已经存在，先从 Manager 中删除
    if ((this.id != null) && (manager != null))
        manager.remove(this);
 
    this.id = id;
 
    // 添加新的 Session
    if (manager != null)
        manager.add(this);
 
    // 这里面完成了 HttpSessionListener 事件通知
    if (notify) {
        tellNew();
    }
}
```

从代码我们看到 setId 方法调用了 tellNew 方法，那 tellNew 又是如何实现的呢？

```java
public void tellNew() {
 
    // 通知 org.apache.catalina.SessionListener
    fireSessionEvent(Session.SESSION_CREATED_EVENT, null);
 
    // 获取 Context 内部的 LifecycleListener 并判断是否为 HttpSessionListener
    Context context = manager.getContext();
    Object listeners[] = context.getApplicationLifecycleListeners();
    if (listeners != null && listeners.length > 0) {
    
        // 创建 HttpSessionEvent
        HttpSessionEvent event = new HttpSessionEvent(getSession());
        for (int i = 0; i < listeners.length; i++) {
            // 判断是否是 HttpSessionListener
            if (!(listeners[i] instanceof HttpSessionListener))
                continue;
                
            HttpSessionListener listener = (HttpSessionListener) listeners[i];
            // 注意这是容器内部事件
            context.fireContainerEvent("beforeSessionCreated", listener);   
            // 触发 Session Created 事件
            listener.sessionCreated(event);
            
            // 注意这也是容器内部事件
            context.fireContainerEvent("afterSessionCreated", listener);
            
        }
    }
}
```

上面代码的逻辑是，先通过 StandardContext 将 HttpSessionListener 类型的 Listener 取出，然后依次调用它们的 sessionCreated 方法。

## 本期精华

今天我们从 Request 谈到了 Session 的创建、销毁和事件通知，里面涉及不少相关的类，下面我画了一张图帮你理解和消化一下这些类的关系：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/tomcat_jetty/73.jpg)

Servlet 规范中定义了 HttpServletRequest 和 HttpSession 接口，Tomcat 实现了这些接口，但具体实现细节并没有暴露给开发者，因此定义了两个包装类，RequestFacade 和 StandardSessionFacade。

Tomcat 是通过 Manager 来管理 Session 的，默认实现是 StandardManager。StandardContext 持有 StandardManager 的实例，并存放了 HttpSessionListener 集合，Session 在创建和销毁时，会通知监听器。

# 33 | Cluster组件：Tomcat的集群通信原理

为了支持水平扩展和高可用，Tomcat 提供了集群部署的能力，但与此同时也带来了分布式系统的一个通用问题，那就是如何在集群中的多个节点之间保持数据的一致性，比如会话（Session）信息。

要实现这一点，基本上有两种方式，一种是把所有 Session 数据放到一台服务器或者一个数据库中，集群中的所有节点通过访问这台 Session 服务器来获取数据。另一种方式就是在集群中的节点间进行 Session 数据的同步拷贝，这里又分为两种策略：第一种是将一个节点的 Session 拷贝到集群中其他所有节点；第二种是只将一个节点上的 Session 数据拷贝到另一个备份节点。

对于 Tomcat 的 Session 管理来说，这两种方式都支持。今天我们就来看看第二种方式的实现原理，也就是 Tomcat 集群通信的原理和配置方法，最后通过官网上的一个例子来了解下 Tomcat 集群到底是如何工作的。

## 集群通信原理

要实现集群通信，首先要知道集群中都有哪些成员。Tomcat 是通过**组播**（Multicast）来实现的。那什么是组播呢？为了理解组播，我先来说说什么是“单播”。网络节点之间的通信就好像是人们之间的对话一样，一个人对另外一个人说话，此时信息的接收和传递只在两个节点之间进行，比如你在收发电子邮件、浏览网页时，使用的就是单播，也就是我们熟悉的“点对点通信”。

如果一台主机需要将同一个消息发送多个主机逐个传输，效率就会比较低，于是就出现组播技术。组播是**一台主机向指定的一组主机发送数据报包**，组播通信的过程是这样的：每一个 Tomcat 节点在启动时和运行时都会周期性（默认 500 毫秒）发送组播心跳包，同一个集群内的节点都在相同的**组播地址**和**端口**监听这些信息；在一定的时间内（默认 3 秒）不发送**组播报文**的节点就会被认为已经崩溃了，会从集群中删去。因此通过组播，集群中每个成员都能维护一个集群成员列表。

## 集群通信配置

有了集群成员的列表，集群中的节点就能通过 TCP 连接向其他节点传输 Session 数据。Tomcat 通过 SimpleTcpCluster 类来进行会话复制（In-Memory Replication）。要开启集群功能，只需要将`server.xml`里的这一行的注释去掉就行：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/tomcat_jetty/74.png)

变成这样：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/tomcat_jetty/75.png)

虽然只是简单的一行配置，但这一行配置等同于下面这样的配置，也就是说 Tomcat 给我们设置了很多默认参数，这些参数都跟集群通信有关。

```xml
<!-- 
  SimpleTcpCluster 是用来复制 Session 的组件。复制 Session 有同步和异步两种方式：
  同步模式下，向浏览器的发送响应数据前，需要先将 Session 拷贝到其他节点完；
  异步模式下，无需等待 Session 拷贝完成就可响应。异步模式更高效，但是同步模式
  可靠性更高。
  同步异步模式由 channelSendOptions 参数控制，默认值是 8，为异步模式；4 是同步模式。
  在异步模式下，可以通过加上 " 拷贝确认 "（Acknowledge）来提高可靠性，此时
  channelSendOptions 设为 10
-->
<Cluster className="org.apache.catalina.ha.tcp.SimpleTcpCluster"
                 channelSendOptions="8">
   <!--
    Manager 决定如何管理集群的 Session 信息。
    Tomcat 提供了两种 Manager：BackupManager 和 DeltaManager。
    BackupManager－集群下的某一节点的 Session，将复制到一个备份节点。
    DeltaManager－ 集群下某一节点的 Session，将复制到所有其他节点。
    DeltaManager 是 Tomcat 默认的集群 Manager。
    
    expireSessionsOnShutdown－设置为 true 时，一个节点关闭时，
    将导致集群下的所有 Session 失效
    notifyListenersOnReplication－集群下节点间的 Session 复制、
    删除操作，是否通知 session listeners
    
    maxInactiveInterval－集群下 Session 的有效时间 (单位:s)。
    maxInactiveInterval 内未活动的 Session，将被 Tomcat 回收。
    默认值为 1800(30min)
  -->
  <Manager className="org.apache.catalina.ha.session.DeltaManager"
                   expireSessionsOnShutdown="false"
                   notifyListenersOnReplication="true"/>
 
   <!--
    Channel 是 Tomcat 节点之间进行通讯的工具。
    Channel 包括 5 个组件：Membership、Receiver、Sender、
    Transport、Interceptor
   -->
  <Channel className="org.apache.catalina.tribes.group.GroupChannel">
     <!--
      Membership 维护集群的可用节点列表。它可以检查到新增的节点，
      也可以检查没有心跳的节点
      className－指定 Membership 使用的类
      address－组播地址
      port－组播端口
      frequency－发送心跳 (向组播地址发送 UDP 数据包) 的时间间隔 (单位:ms)。
      dropTime－Membership 在 dropTime(单位:ms) 内未收到某一节点的心跳，
      则将该节点从可用节点列表删除。默认值为 3000。
     -->
     <Membership  className="org.apache.catalina.tribes.membership.
         McastService"
         address="228.0.0.4"
         port="45564"
         frequency="500"
         dropTime="3000"/>
     
     <!--
       Receiver 用于各个节点接收其他节点发送的数据。
       接收器分为两种：BioReceiver(阻塞式)、NioReceiver(非阻塞式)
 
       className－指定 Receiver 使用的类
       address－接收消息的地址
       port－接收消息的端口
       autoBind－端口的变化区间，如果 port 为 4000，autoBind 为 100，
                 接收器将在 4000-4099 间取一个端口进行监听。
       selectorTimeout－NioReceiver 内 Selector 轮询的超时时间
       maxThreads－线程池的最大线程数
     -->
     <Receiver className="org.apache.catalina.tribes.transport.nio.
         NioReceiver"
         address="auto"
         port="4000"
         autoBind="100"
         selectorTimeout="5000"
         maxThreads="6"/>
 
      <!--
         Sender 用于向其他节点发送数据，Sender 内嵌了 Transport 组件，
         Transport 真正负责发送消息。
      -->
      <Sender className="org.apache.catalina.tribes.transport.
          ReplicationTransmitter">
          <!--
            Transport 分为两种：bio.PooledMultiSender(阻塞式)
            和 nio.PooledParallelSender(非阻塞式)，PooledParallelSender
            是从 tcp 连接池中获取连接，可以实现并行发送，即集群中的节点可以
            同时向其他所有节点发送数据而互不影响。
           -->
          <Transport className="org.apache.catalina.tribes.
          transport.nio.PooledParallelSender"/>     
       </Sender>
       
       <!--
         Interceptor : Cluster 的拦截器
         TcpFailureDetector－TcpFailureDetector 可以拦截到某个节点关闭
         的信息，并尝试通过 TCP 连接到此节点，以确保此节点真正关闭，从而更新集
         群可用节点列表                 
        -->
       <Interceptor className="org.apache.catalina.tribes.group.
       interceptors.TcpFailureDetector"/>
       
       <!--
         MessageDispatchInterceptor－查看 Cluster 组件发送消息的
         方式是否设置为 Channel.SEND_OPTIONS_ASYNCHRONOUS，如果是，
         MessageDispatchInterceptor 先将等待发送的消息进行排队，
         然后将排好队的消息转给 Sender。
        -->
       <Interceptor className="org.apache.catalina.tribes.group.
       interceptors.MessageDispatchInterceptor"/>
  </Channel>
 
  <!--
    Valve : Tomcat 的拦截器，
    ReplicationValve－在处理请求前后打日志；过滤不涉及 Session 变化的请求。                 
    -->
  <Valve className="org.apache.catalina.ha.tcp.ReplicationValve"
    filter=""/>
  <Valve className="org.apache.catalina.ha.session.
  JvmRouteBinderValve"/>
 
  <!--
    Deployer 用于集群的 farm 功能，监控应用中文件的更新，以保证集群中所有节点
    应用的一致性，如某个用户上传文件到集群中某个节点的应用程序目录下，Deployer
    会监测到这一操作并把文件拷贝到集群中其他节点相同应用的对应目录下以保持
    所有应用的一致，这是一个相当强大的功能。
  -->
  <Deployer className="org.apache.catalina.ha.deploy.FarmWarDeployer"
     tempDir="/tmp/war-temp/"
     deployDir="/tmp/war-deploy/"
     watchDir="/tmp/war-listen/"
     watchEnabled="false"/>
 
  <!--
    ClusterListener : 监听器，监听 Cluster 组件接收的消息
    使用 DeltaManager 时，Cluster 接收的信息通过 ClusterSessionListener
    传递给 DeltaManager，从而更新自己的 Session 列表。
    -->
  <ClusterListener className="org.apache.catalina.ha.session.
  ClusterSessionListener"/>
  
</Cluster>
```

从上面的的参数列表可以看到，默认情况下 Session 管理组件 DeltaManager 会在节点之间拷贝 Session，DeltaManager 采用的一种 all-to-all 的工作方式，即集群中的节点会把 Session 数据向所有其他节点拷贝，而不管其他节点是否部署了当前应用。当集群节点数比较少时，比如少于 4 个，这种 all-to-all 的方式是不错的选择；但是当集群中的节点数量比较多时，数据拷贝的开销成指数级增长，这种情况下可以考虑 BackupManager，BackupManager 只向一个备份节点拷贝数据。

在大体了解了 Tomcat 集群实现模型后，就可以对集群作出更优化的配置了。Tomcat 推荐了一套配置，使用了比 DeltaManager 更高效的 BackupManager，并且通过 ReplicationValve 设置了请求过滤。

这里还请注意在一台服务器部署多个节点时需要修改 Receiver 的侦听端口，另外为了在节点间高效地拷贝数据，所有 Tomcat 节点最好采用相同的配置，具体配置如下：

```xml
<Cluster className="org.apache.catalina.ha.tcp.SimpleTcpCluster"
                 channelSendOptions="6">
 
    <Manager className="org.apache.catalina.ha.session.BackupManager"
                   expireSessionsOnShutdown="false"
                   notifyListenersOnReplication="true"
                   mapSendOptions="6"/>
         
     <Channel className="org.apache.catalina.tribes.group.
     GroupChannel">
     
     <Membership className="org.apache.catalina.tribes.membership.
     McastService"
       address="228.0.0.4"
       port="45564"
       frequency="500"
       dropTime="3000"/>
       
     <Receiver className="org.apache.catalina.tribes.transport.nio.
     NioReceiver"
       address="auto"
       port="5000"
       selectorTimeout="100"
       maxThreads="6"/>
 
     <Sender className="org.apache.catalina.tribes.transport.
     ReplicationTransmitter">
          <Transport className="org.apache.catalina.tribes.transport.
          nio.PooledParallelSender"/>
     </Sender>
     
     <Interceptor className="org.apache.catalina.tribes.group.
     interceptors.TcpFailureDetector"/>
     
     <Interceptor className="org.apache.catalina.tribes.group.
     interceptors.MessageDispatchInterceptor"/>
     
     <Interceptor className="org.apache.catalina.tribes.group.
     interceptors.ThroughputInterceptor"/>
   </Channel>
 
   <Valve className="org.apache.catalina.ha.tcp.ReplicationValve"
       filter=".*\.gif|.*\.js|.*\.jpeg|.*\.jpg|.*\.png|.*\
               .htm|.*\.html|.*\.css|.*\.txt"/>
 
   <Deployer className="org.apache.catalina.ha.deploy.FarmWarDeployer"
       tempDir="/tmp/war-temp/"
       deployDir="/tmp/war-deploy/"
       watchDir="/tmp/war-listen/"
       watchEnabled="false"/>
 
    <ClusterListener className="org.apache.catalina.ha.session.
    ClusterSessionListener"/>
</Cluster>
```

## 集群工作过程

Tomcat 的官网给出了一个例子，来说明 Tomcat 集群模式下是如何工作的，以及 Tomcat 集群是如何实现高可用的。比如集群由 Tomcat A 和 Tomcat B 两个 Tomcat 实例组成，按照时间先后顺序发生了如下事件：

**1. Tomcat A 启动**

Tomcat A 启动过程中，当 Host 对象被创建时，一个 Cluster 组件（默认是 SimpleTcpCluster）被关联到这个 Host 对象。当某个应用在`web.xml`中设置了 Distributable 时，Tomcat 将为此应用的上下文环境创建一个 DeltaManager。SimpleTcpCluster 启动 Membership 服务和 Replication 服务。

**2. Tomcat B 启动（在 Tomcat A 之后启动）**

首先 Tomcat B 会执行和 Tomcat A 一样的操作，然后 SimpleTcpCluster 会建立一个由 Tomcat A 和 Tomcat B 组成的 Membership。接着 Tomcat B 向集群中的 Tomcat A 请求 Session 数据，如果 Tomcat A 没有响应 Tomcat B 的拷贝请求，Tomcat B 会在 60 秒后 time out。在 Session 数据拷贝完成之前 Tomcat B 不会接收浏览器的请求。

**3. Tomcat A 接收 HTTP 请求，创建 Session 1**

Tomcat A 响应客户请求，在把结果发送回客户端之前，ReplicationValve 会拦截当前请求（如果 Filter 中配置了不需拦截的请求类型，这一步就不会进行，默认配置下拦截所有请求），如果发现当前请求更新了 Session，就调用 Replication 服务建立 TCP 连接将 Session 拷贝到 Membership 列表中的其他节点即 Tomcat B。在拷贝时，所有保存在当前 Session 中的可序列化的对象都会被拷贝，而不仅仅是发生更新的部分。

**4. Tomcat A 崩溃**

当 Tomcat A 崩溃时，Tomcat B 会被告知 Tomcat A 已从集群中退出，然后 Tomcat B 就会把 Tomcat A 从自己的 Membership 列表中删除。并且 Tomcat B 的 Session 更新时不再往 Tomcat A 拷贝，同时负载均衡器会把后续的 HTTP 请求全部转发给 Tomcat B。在此过程中所有的 Session 数据不会丢失。

**5. Tomcat B 接收 Tomcat A 的请求**

Tomcat B 正常响应本应该发往 Tomcat A 的请求，因为 Tomcat B 保存了 Tomcat A 的所有 Session 数据。

**6. Tomcat A 重新启动**

Tomcat A 按步骤 1、2 操作启动，加入集群，并从 Tomcat B 拷贝所有 Session 数据，拷贝完成后开始接收请求。

**7. Tomcat A 接收请求，Session 1 被用户注销**

Tomcat 继续接收发往 Tomcat A 的请求，Session 1 设置为失效。请注意这里的失效并非因为 Tomcat A 处于非活动状态超过设置的时间，而是应用程序执行了注销的操作（比如用户登出）而引起的 Session 失效。这时 Tomcat A 向 Tomcat B 发送一个 Session 1 Expired 的消息，Tomcat B 收到消息后也会把 Session 1 设置为失效。

**8. Tomcat B 接收到一个新请求，创建 Session 2**

同理这个新的 Session 也会被拷贝到 Tomcat A。

**9. Tomcat A 上的 Session 2 过期**

因超时原因引起的 Session 失效 Tomcat A 无需通知 Tomcat B，Tomcat B 同样知道 Session 2 已经超时。因此对于 Tomcat 集群有一点非常重要，**所有节点的操作系统时间必须一致**。不然会出现某个节点 Session 已过期而在另一节点此 Session 仍处于活动状态的现象。

## 本期精华

今天我谈了 Tomcat 的集群工作原理和配置方式，还通过官网上的一个例子说明了 Tomcat 集群的工作过程。Tomcat 集群对 Session 的拷贝支持两种方式：DeltaManager 和 BackupManager。

当集群中节点比较少时，可以采用 DeltaManager，因为 Session 数据在集群中各个节点都有备份，任何一个节点崩溃都不会对整体造成影响，可靠性比较高。

当集群中节点数比较多时，可以采用 BackupManager，这是因为一个节点的 Session 只会拷贝到另一个节点，数据拷贝的开销比较少，同时只要这两个节点不同时崩溃，Session 数据就不会丢失。

# 特别放送 | 如何持续保持对学习的兴趣？

你好，我是李号双。今天我们抛开技术本身的内容，来聊聊专栏或者一门新技术的学习方法，我也分享一下自己是如何啃下 Tomcat 和 Jetty 源码的。

专栏如今已经更新完了五个模块，我们学习了 Tomcat 和 Jetty 的整体架构、连接器、容器和通用组件，这些内容可以说是 Tomcat 和 Jetty 的设计核心。在日常工作的使用中，我们使用到了 Tomcat 和 Jetty 提供的功能，我希望通过学习专栏，还能帮你了解这些功能是如何实现的，以及 Tomcat 和 Jetty 在设计时都考虑了哪些地方。

所以在学习专栏时，你不妨思考这样一个问题，假如让你来设计并实现一个 Web 容器，你会怎么做呢？如何合理设计顶层模块？如何考虑方方面面的需求，比如最基本的功能需求是加载和运行 Web 程序，最重要的非功能需求是高性能、高并发。你可以顺着这两条线先思考下你会怎么做，然后再回过头来看看 Tomcat 和 Jetty 是如何做到的。这样的学习方法其实就在有意识地训练自己独立设计一个系统的能力，不管是对于学习这个专栏还是其他技术，带着问题再去学习都会有所帮助。

说完关于专栏的学习方法，下面我必须要鼓励一下坚持学习到现在的你。专栏从第三模块开始，开始讲解连接器、容器和通用组件的设计和原理，有些内容可能比较偏向底层，确实难度比较大，如果对底层源码不熟悉或者不感兴趣，学习起来会有些痛苦。但是，我之所以设计了这部分内容，就是希望能够揭开 Tomcat 和 Jetty 的内部细节，因为任何一个优秀的中间件之所以可以让用户使用比较容易，其内部一定都是很复杂的。这也从侧面传递出一个信号：美好的东西都是有代价的，需要也值得我们去付出时间和精力。

我和你一样我们都身处 IT 行业，这个行业技术更新迭代非常快，因此我们需要以一个开放的心态持续学习。而学习恰恰又是一个反人性的过程，甚至是比较痛苦的，尤其是有些技术框架本身比较庞大，设计得非常复杂，我们在学习初期很容易遇到“挫折感”，一些技术点怎么想也想不明白，往往也会有放弃的想法。我同样经历过这个过程，**我的经验是找到适合自己的学习方法非常重要，同样关键的是要保持学习的兴趣和动力**。

举个我学习 Spring 框架的例子，记得当时我在接触 Spring 框架的时候，一开始就钻进一个模块开始啃起了源代码。由于 Spring 框架本身比较庞杂，分很多模块，当时给我最直观的感受就是看不懂，我不明白代码为什么要这么写，为什么设计得这么“绕”。这里面的问题是，首先**我还没弄清楚森林长什么样子，就盯着树叶看**，很可能是盲人摸象，看不到全貌和整体的设计思路。第二个问题是我还没学会用 Spring，就开始研究它是如何设计的，结果可想而知，也遇到了挫折。后来我逐渐总结出一些学习新技术的小经验：在学习一门技术的时候，一定要先看清它的全貌，我推荐先看官方文档，看看都有哪些模块、整体上是如何设计的。接着我们先不要直接看源码，而是要动手跑一跑官网上的例子，或者用这个框架实现一个小系统，关键是要学会怎么使用。只有在这个基础上，才能深入到特定模块，去研究设计思路，或者深入到某一模块源码之中。这样在学习的过程中，按照一定的顺序一步一步来，就能够即时获得**成就感**，有了成就感你才会更加专注，才会愿意花更多时间和精力去深入研究。因此要保持学习的兴趣，我觉得有两个方面比较重要：

第一个是我们需要**带着明确的目标去学习**。比如某些知识点是面试的热点，那学习目标就是彻底理解和掌握它，当被问到相关问题时，你的回答能够使得面试官对你刮目相看，有时候往往凭着某一个亮点就能影响最后的录用结果。再比如你想掌握一门新技术来解决工作上的问题，那你的学习目标应该是不但要掌握清楚原理，还要能真正的将新技术合理运用到实际工作中，解决实际问题，产生实际效果。我们学习了 Tomcat 和 Jetty 的责任链模式，是不是在实际项目中的一些场景下就可以用到这种设计呢？再比如学习了调优方法，是不是可以在生产环境里解决性能问题呢？总之技术需要变现才有学习动力。

第二个是**一定要动手实践**。只有动手实践才会让我们对技术有最直观的感受。有时候我们听别人讲经验和理论，感觉似乎懂了，但是过一段时间便又忘记了。如果我们动手实践了，特别是在这个过程中碰到了一些问题，通过网上查找资料，或者跟同事讨论解决了问题，这便是你积累的宝贵经验，想忘记都难。另外适当的动手实践能够树立起信心，培养起兴趣，这跟玩游戏上瘾有点类似，通过打怪升级，一点点积累起成就感。比如学习了 Tomcat 的线程池实现，我们就可以自己写一个定制版的线程池；学习了 Tomcat 的类加载器，我们也可以自己动手写一个类加载器。

专栏更新到现在，内容最难的部分已经结束，在后面的实战调优模块，我在设计内容时都安排了实战环节。毕竟调优本身就是一个很贴近实际场景的话题，应该基于特定场景，去解决某个性能问题，而不是为了调优而调优。所以这部分内容也更贴近实际工作场景，你可以尝试用我前面讲的方法，带着问题学习后面的专栏。

调优的过程中需要一些知识储备，比如我们需要掌握操作系统、JVM 以及网络通信的原理，这些原理在专栏前面的文章也讲到过。虽然涉及很多原理也很复杂，并不是说要面面俱到，我们也不太容易深入到每个细节，所以最关键的是要弄懂相关参数的含义，比如 JVM 内存的参数、GC 的参数、Linux 内核的相关参数等。

除此之外，调优的过程还需要借助大量的工具，包括性能监控工具、日志分析工具、网络抓包工具和流量压测工具等，熟练使用这些工具也是每一个后端程序员必须掌握的看家本领，因此在实战环节，我也设计了一些场景来带你熟悉这些工具。

说了那么多，就是希望你保持对学习的热情，树立明确的目标，再加上亲自动手实践。专栏学习到现在这个阶段，是时候开始动手实践了，希望你每天都能积累一点，每天都能有所进步。