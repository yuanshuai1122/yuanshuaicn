---
title: JVM内存与垃圾回收篇第2章类加载子系统
date: 2022-03-19 18:12:20.377
author:
  name: "yuanshuai"
  link: "https://cloud.tencent.com/developer/user/8180692"
  email: "shuaiyuan1122@gmail.com"
tags: 
- Java
- JVM
---

# 第 2 章 类加载子系统

## 1、内存结构概述

如果自己想手写一个Java虚拟机的话，主要考虑哪些结构呢？

1. 类加载器
2. 执行引擎

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/20.png)

**完整框图：**

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/21.png)

## 2、类加载子系统

> **类加载器子系统作用**

1. **类加载器子系统负责从文件系统或者网络中加载Class文件**，class文件在文件开头有特定的文件标识。
2. ClassLoader只负责class文件的加载，至于它是否可以运行，则由Execution Engine决定。
3. **加载的类信息存放于一块称为方法区的内存空间**。除了类的信息外，方法区中还会存放运行时常量池信息，可能还包括字符串字面量和数字常量（这部分常量信息是Class文件中常量池部分的内存映射）

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/22.png)

> **class --> Java.lang.Class**

1. class file存在于本地硬盘上，可以理解为设计师画在纸上的模板，而最终这个模板在执行的时候是要加载到JVM当中来根据这个文件实例化出n个一模一样的实例。
2. **class file加载到JVM中，被称为DNA元数据模板，放在方法区**。
3. 在.class文件–>JVM–>最终成为元数据模板，此过程就要一个运输工具（类装载器Class Loader），扮演一个快递员的角色。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/23.png)

## 3、类加载过程

### 3.1、类加载过程概述

- 看代码

```java
public class HelloLoader {
    public static void main(String[] args) {
        System.out.println("谢谢ClassLoader加载我....");
        System.out.println("你的大恩大德，我下辈子再报！");
    }
}
```

- 它的加载过程是怎么样的呢?
  - 执行 main() 方法（静态方法）就需要先加载承载类 HelloLoader
  - 加载成功，则进行链接、初始化等操作，完成后调用 HelloLoader 类中的静态方法 main
  - 加载失败则抛出异常

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/24.png)

- 完整的流程图如下所示：**加载 --> 链接（验证 --> 准备 --> 解析） --> 初始化**

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/25.png)

### 3.2、加载阶段

> **加载流程**

1. **通过一个类的全限定名获取定义此类的二进制字节流**
2. 将这个字节流所代表的静态存储结构转化为**方法区的运行时数据结构**
3. **在内存中生成一个代表这个类的java.lang.Class对象**，作为方法区这个类的各种数据的访问入口

> **加载class文件的方式**

1. 从本地系统中直接加载
2. 通过网络获取，典型场景：Web Applet
3. 从zip压缩包中读取，成为日后jar、war格式的基础
4. 运行时计算生成，使用最多的是：动态代理技术
5. 由其他文件生成，典型场景：JSP应用从专有数据库中提取.class文件，比较少见
6. 从加密文件中获取，典型的防Class文件被反编译的保护措施

### 3.3、链接阶段

- **链接分为三个子阶段：验证 --> 准备 --> 解析**

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/26.png)

#### 3.3.1、验证(Verify)

> **验证**

1. **目的在于确保Class文件的字节流中包含信息符合当前虚拟机要求，保证被加载类的正确性，不会危害虚拟机自身安全**
2. 主要包括四种验证，文件格式验证，元数据验证，字节码验证，符号引用验证。

> **举例**

- 使用 BinaryViewer 查看字节码文件，其开头均为 CAFE BABE ，如果出现不合法的字节码文件，那么将会验证不通过

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/27.png)

#### 3.3.2、准备(Prepare)

> **准备**

1. **为类变量分配内存并且设置该类变量的默认初始值，即零值**
2. 这里不包含用final修饰的static，因为final在编译的时候就会分配好了默认值，准备阶段会显式初始化
3. 注意：这里不会为实例变量分配初始化，类变量会分配在方法区中，而实例变量是会随着对象一起分配到Java堆中

> **举例**

- 代码：变量a在准备阶段会赋初始值，但不是1，而是0，在初始化阶段会被赋值为 1

```java
public class HelloApp {
    private static int a = 1;   //prepare：a = 0 ---> initial : a = 1

    public static void main(String[] args) {
        System.out.println(a);
    }
}
```

#### 3.3.3、解析(Resolve)

> **解析**

1. **将常量池内的符号引用转换为直接引用的过程**
2. 事实上，解析操作往往会伴随着JVM在执行完初始化之后再执行
3. 符号引用就是一组符号来描述所引用的目标。符号引用的字面量形式明确定义在《java虚拟机规范》的class文件格式中。直接引用就是直接指向目标的指针、相对偏移量或一个间接定位到目标的句柄
4. 解析动作主要针对类或接口、字段、类方法、接口方法、方法类型等。对应常量池中的CONSTANT Class info、CONSTANT Fieldref info、CONSTANT Methodref info等

> **符号引用**

- 反编译 class 文件后可以查看符号引用

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/28.png)

### 3.4、初始化阶段

> **初始化阶段**

1. **初始化阶段就是执行类构造器方法`<clinit>()`的过程**
2. 此方法不需定义，是javac编译器自动收集类中的所有类变量的赋值动作和静态代码块中的语句合并而来。也就是说，**当我们代码中包含static变量的时候，就会有clinit方法**
3. **`<clinit>()`方法中的指令按语句在源文件中出现的顺序执行**
4. `<clinit>()`不同于类的构造器。（关联：构造器是虚拟机视角下的`<init>()`）
5. **若该类具有父类，JVM会保证子类的`<clinit>()`执行前，父类的`<clinit>()`已经执行完毕**
6. **虚拟机必须保证一个类的`<clinit>()`方法在多线程下被同步加锁**

> **IDEA 中安装 JClassLib 插件**

在 IDEA 中安装 JClassLib 插件后，重启 IDEA 生效

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/29.png)

- 选中对应的 Java 类文件，注意：不是字节码文件~！
- 点击【View --> Show Bytecode With jclasslib】即可查看反编译后的代码

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/30.png)

> **当我们代码中包含static变量的时候，就会有clinit方法**

**示例 1：无 static 变量**

```java
public class ClinitTest {
    private int a = 1;

    public static void main(String[] args) {
        int b = 2;
    }
}
```

- 并没有生成 clinit 方法

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/31.png)

**示例 2：有 static 变量**

```java
public class ClinitTest {
    //任何一个类声明以后，内部至少存在一个类的构造器
    private int a = 1;
    private static int c = 3;
    
    public static void main(String[] args) {
        int b = 2;
    }

}
```

- 在 clinit 方法中初始化静态变量的值为 3

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/32.png)

> **构造器方法中指令按语句在源文件中出现的顺序执行**

**示例 1**

- 代码：

```java
public class ClassInitTest {
    private static int num = 1;
    private static int number = 10;      //linking之prepare: number = 0 --> initial: 10 --> 20

    static {
        num = 2;
        number = 20;
        System.out.println(num);
        //System.out.println(number);    //报错：非法的前向引用（可以赋值，但不能调用）
    }

    public static void main(String[] args) {
        System.out.println(ClassInitTest.num);//2
        System.out.println(ClassInitTest.number);//10
    }
}
```

- 静态变量 number 的值变化过程如下
  - 准备阶段时：0
  - 执行静态变量初始化：10
  - 执行静态代码块：20

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/33.png)

**示例 2**

- 代码

```java
public class ClassInitTest {
   private static int num = 1;

   static{
       num = 2;
       number = 20;
       System.out.println(num);
       //System.out.println(number);    //报错：非法的前向引用（可以赋值，但不能调用）
   }

   private static int number = 10;      //linking之prepare: number = 0 --> initial: 20 --> 10

    public static void main(String[] args) {
        System.out.println(ClassInitTest.num);		//2
        System.out.println(ClassInitTest.number);	//10
    }
}
```

- 静态变量 number 的值变化过程如下
  - 准备阶段时：0
  - 执行静态代码块：20
  - 执行静态变量初始化：10

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/34.png)

> **构造器是虚拟机视角下的`<init>()`**

- 代码

```java
public class ClinitTest {
    //任何一个类声明以后，内部至少存在一个类的构造器
    private int a = 1;
    private static int c = 3;

    public static void main(String[] args) {
        int b = 2;
    }

    public ClinitTest(){
        a = 10;
        int d = 20;
    }

}
```

- 在构造器中：
  - 先将类变量 a 赋值为 10
  - 再将局部变量赋值为 20

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/35.png)

> **若该类具有父类，JVM会保证子类的`<clinit>()`执行前，父类的`<clinit>()`已经执行完毕**

- 代码

```java
public class ClinitTest1 {
    static class Father{
        public static int A = 1;
        static{
            A = 2;
        }
    }

    static class Son extends Father{
        public static int B = A;
    }

    public static void main(String[] args) {
        //加载Father类，其次加载Son类。
        System.out.println(Son.B);//2
    }
}
```

- 如上代码，加载流程如下：
  - 首先，执行 main() 方法需要加载 ClinitTest1 类
  - 获取 Son.B 静态变量，需要加载 Son 类
  - Son 类的父类是 Father 类，所以需要先执行 Father 类的加载，再执行 Son 类的加载

> **虚拟机必须保证一个类的`<clinit>()`方法在多线程下被同步加锁**

- 代码

```java
public class DeadThreadTest {    public static void main(String[] args) {        Runnable r = () -> {            System.out.println(Thread.currentThread().getName() + "开始");            DeadThread dead = new DeadThread();            System.out.println(Thread.currentThread().getName() + "结束");        };        Thread t1 = new Thread(r, "线程1");        Thread t2 = new Thread(r, "线程2");        t1.start();        t2.start();    }}class DeadThread {    static {        if (true) {            System.out.println(Thread.currentThread().getName() + "初始化当前类");            while (true) {            }        }    }}
```

- 程序卡死，分析原因：
  - 两个线程同时去加载 DeadThread 类，而 DeadThread 类中静态代码块中有一处死循环
  - 先加载 DeadThread 类的线程抢到了同步锁，然后在类的静态代码块中执行死循环，而另一个线程在等待同步锁的释放
  - 所以无论哪个线程先执行 DeadThread 类的加载，另外一个类也不会继续执行

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/36.png)

## 4、类加载器的分类

### 4.1、类加载器概述

> **类加载器的分类**

1. **JVM支持两种类型的类加载器 。分别为引导类加载器（Bootstrap ClassLoader）和自定义类加载器（User-Defined ClassLoader）**
2. 从概念上来讲，自定义类加载器一般指的是程序中由开发人员自定义的一类类加载器，但是Java虚拟机规范却没有这么定义，而是**将所有派生于抽象类ClassLoader的类加载器都划分为自定义类加载器**
3. 无论类加载器的类型如何划分，在程序中我们最常见的类加载器始终只有3个，如下所示
4. 这里的四者之间是包含关系，不是上层和下层，也不是子父类的继承关系。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/37.png)

> **为什么 ExtClassLoader 和 AppClassLoader 都属于自定义加载器**

- **规范定义：所有派生于抽象类ClassLoader的类加载器都划分为自定义类加载器**
- ExtClassLoader 继承树

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/38.png)

- AppClassLoader 继承树

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/39.png)

- 代码：
  - 我们尝试获取引导类加载器，获取到的值为 null ，这并不代表引导类加载器不存在，**因为引导类加载器是C/C++ 语言，我们获取不到**
  - 两次获取系统类加载器的值都相同：sun.misc.Launcher$AppClassLoader@18b4aac2 ，这说明**系统类加载器是全局唯一的**

```java
public class ClassLoaderTest {    public static void main(String[] args) {        //获取系统类加载器        ClassLoader systemClassLoader = ClassLoader.getSystemClassLoader();        System.out.println(systemClassLoader);//sun.misc.Launcher$AppClassLoader@18b4aac2        //获取其上层：扩展类加载器        ClassLoader extClassLoader = systemClassLoader.getParent();        System.out.println(extClassLoader);//sun.misc.Launcher$ExtClassLoader@1540e19d        //获取其上层：获取不到引导类加载器        ClassLoader bootstrapClassLoader = extClassLoader.getParent();        System.out.println(bootstrapClassLoader);//null        //对于用户自定义类来说：默认使用系统类加载器进行加载        ClassLoader classLoader = ClassLoaderTest.class.getClassLoader();        System.out.println(classLoader);//sun.misc.Launcher$AppClassLoader@18b4aac2        //String类使用引导类加载器进行加载的。---> Java的核心类库都是使用引导类加载器进行加载的。        ClassLoader classLoader1 = String.class.getClassLoader();        System.out.println(classLoader1);//null    }}
```

### 4.2、虚拟机自带的加载器

#### 4.2.1、启动类加载器

> **启动类加载器（引导类加载器，Bootstrap ClassLoader）**

1. **这个类加载使用C/C++语言实现的，嵌套在JVM内部**
2. **它用来加载Java的核心库（JAVA_HOME/jre/lib/rt.jar、resources.jar或sun.boot.class.path路径下的内容），用于提供JVM自身需要的类**
3. **并不继承自java.lang.ClassLoader，没有父加载器**
4. **加载扩展类和应用程序类加载器，并作为他们的父类加载器（当他俩的爹）**
5. **出于安全考虑，Bootstrap启动类加载器只加载包名为java、javax、sun等开头的类**

#### 4.2.2、扩展类加载器

> **扩展类加载器（Extension ClassLoader）**

1. **Java语言编写，由sun.misc.Launcher$ExtClassLoader实现**
2. **派生于ClassLoader类**
3. **父类加载器为启动类加载器**
4. **从java.ext.dirs系统属性所指定的目录中加载类库，或从JDK的安装目录的jre/lib/ext子目录（扩展目录）下加载类库。如果用户创建的JAR放在此目录下，也会自动由扩展类加载器加载**

#### 4.2.3、系统类加载器

> **应用程序类加载器（系统类加载器，AppClassLoader）**

1. **Java语言编写，由sun.misc.LaunchersAppClassLoader实现**
2. **派生于ClassLoader类**
3. **父类加载器为扩展类加载器**
4. **它负责加载环境变量classpath或系统属性java.class.path指定路径下的类库**
5. **该类加载是程序中默认的类加载器，一般来说，Java应用的类都是由它来完成加载**
6. **通过classLoader.getSystemclassLoader()方法可以获取到该类加载器**

> **代码举例说明**

- 代码

```java
public class ClassLoaderTest1 {    public static void main(String[] args) {        System.out.println("**********启动类加载器**************");        //获取BootstrapClassLoader能够加载的api的路径        URL[] urLs = sun.misc.Launcher.getBootstrapClassPath().getURLs();        for (URL element : urLs) {            System.out.println(element.toExternalForm());        }        //从上面的路径中随意选择一个类,来看看他的类加载器是什么:引导类加载器        ClassLoader classLoader = Provider.class.getClassLoader();        System.out.println(classLoader);//null        System.out.println("***********扩展类加载器*************");        String extDirs = System.getProperty("java.ext.dirs");        for (String path : extDirs.split(";")) {            System.out.println(path);        }        //从上面的路径中随意选择一个类,来看看他的类加载器是什么:扩展类加载器        ClassLoader classLoader1 = CurveDB.class.getClassLoader();        System.out.println(classLoader1);//sun.misc.Launcher$ExtClassLoader@1540e19d    }}
```

- System.out.println(classLoader); 输出 null ，再次证明我们无法获取到启动类加载器

```java
**********启动类加载器**************file:/C:/Program%20Files/Java/jdk1.8.0_144/jre/lib/resources.jarfile:/C:/Program%20Files/Java/jdk1.8.0_144/jre/lib/rt.jarfile:/C:/Program%20Files/Java/jdk1.8.0_144/jre/lib/sunrsasign.jarfile:/C:/Program%20Files/Java/jdk1.8.0_144/jre/lib/jsse.jarfile:/C:/Program%20Files/Java/jdk1.8.0_144/jre/lib/jce.jarfile:/C:/Program%20Files/Java/jdk1.8.0_144/jre/lib/charsets.jarfile:/C:/Program%20Files/Java/jdk1.8.0_144/jre/lib/jfr.jarfile:/C:/Program%20Files/Java/jdk1.8.0_144/jre/classesnull***********扩展类加载器*************C:\Program Files\Java\jdk1.8.0_144\jre\lib\extC:\WINDOWS\Sun\Java\lib\extsun.misc.Launcher$ExtClassLoader@7ea987ac
```

### 4.3、用户自定义类加载器

> **为什么需要自定义类加载器？**

在Java的日常应用程序开发中，类的加载几乎是由上述3种类加载器相互配合执行的，在必要时，我们还可以自定义类加载器，来定制类的加载方式。那为什么还需要自定义类加载器？

1. 隔离加载类
2. 修改类加载的方式
3. 扩展加载源
4. 防止源码泄漏

> **如何自定义类加载器？**

1. 开发人员可以通过继承抽象类java.lang.ClassLoader类的方式，实现自己的类加载器，以满足一些特殊的需求
2. 在JDK1.2之前，在自定义类加载器时，总会去继承ClassLoader类并重写loadClass()方法，从而实现自定义的类加载类，但是在JDK1.2之后已不再建议用户去覆盖loadClass()方法，而是建议把自定义的类加载逻辑写在findclass()方法中
3. 在编写自定义类加载器时，如果没有太过于复杂的需求，可以直接继承URLClassLoader类，这样就可以避免自己去编写findclass()方法及其获取字节码流的方式，使自定义类加载器编写更加简洁。

**代码示例**

```java
public class CustomClassLoader extends ClassLoader {    @Override    protected Class<?> findClass(String name) throws ClassNotFoundException {        try {            byte[] result = getClassFromCustomPath(name);            if (result == null) {                throw new FileNotFoundException();            } else {                return defineClass(name, result, 0, result.length);            }        } catch (FileNotFoundException e) {            e.printStackTrace();        }        throw new ClassNotFoundException(name);    }    private byte[] getClassFromCustomPath(String name) {        //从自定义路径中加载指定类:细节略        //如果指定路径的字节码文件进行了加密，则需要在此方法中进行解密操作。        return null;    }    public static void main(String[] args) {        CustomClassLoader customClassLoader = new CustomClassLoader();        try {            Class<?> clazz = Class.forName("One", true, customClassLoader);            Object obj = clazz.newInstance();            System.out.println(obj.getClass().getClassLoader());        } catch (Exception e) {            e.printStackTrace();        }    }}
```

### 4.4、关于 ClassLoader

> **ClassLoader 类介绍**

- ClassLoader类，它是一个抽象类，其后所有的类加载器都继承自ClassLoader（不包括启动类加载器）

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/40.png)

- sun.misc.Launcher 它是一个java虚拟机的入口应用

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/41.png)

**获取 ClassLoader 途径**

- 获取途径：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/42.png)

- 代码示例：

```java
public class ClassLoaderTest2 {    public static void main(String[] args) {        try {                        //1.Class.forName().getClassLoader()            ClassLoader classLoader = Class.forName("java.lang.String").getClassLoader();            System.out.println(classLoader); // String 类由启动类加载器加载，我们无法获取            //2.Thread.currentThread().getContextClassLoader()            ClassLoader classLoader1 = Thread.currentThread().getContextClassLoader();            System.out.println(classLoader1);            //3.ClassLoader.getSystemClassLoader().getParent()            ClassLoader classLoader2 = ClassLoader.getSystemClassLoader();            System.out.println(classLoader2);        } catch (ClassNotFoundException e) {            e.printStackTrace();        }    }}// 输出nullsun.misc.Launcher$AppClassLoader@18b4aac2sun.misc.Launcher$AppClassLoader@18b4aac2
```

## 5、双亲委派机制

### 5.1、双亲委派机制原理

> **双亲委派机制的原理**

**Java虚拟机对class文件采用的是按需加载的方式**，也就是说当需要使用该类时才会将它的class文件加载到内存生成class对象。而且**加载某个类的class文件时，Java虚拟机采用的是双亲委派模式，即把请求交由父类处理，它是一种任务委派模式**

1. 如果一个类加载器收到了类加载请求，它并不会自己先去加载，而是把这个请求委托给父类的加载器去执行；
2. 如果父类加载器还存在其父类加载器，则进一步向上委托，依次递归，请求最终将到达顶层的启动类加载器；
3. 如果父类加载器可以完成类加载任务，就成功返回，倘若父类加载器无法完成此加载任务，子加载器才会尝试自己去加载，这就是双亲委派模式。
4. 父类加载器一层一层往下分配任务，如果子类加载器能加载，则加载此类，如果将加载任务分配至系统类加载器也无法加载此类，则抛出异常

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/43.png)

### 5.2、双亲委派机制代码示例

> **代码示例**

**举例 1 ：**

- 代码：我们自己建立一个 java.lang.String 类，写上 static 代码块

```java
package java.lang;public class String {    static{        System.out.println("我是自定义的String类的静态代码块");    }}1234567
```

- 在另外的程序中加载 String 类，看看加载的 String 类是 JDK 自带的 String 类，还是我们自己编写的 String 类

```java
public class StringTest {    public static void main(String[] args) {        java.lang.String str = new java.lang.String();        System.out.println("hello,atguigu.com");        StringTest test = new StringTest();        System.out.println(test.getClass().getClassLoader());    }}12345678910
```

- 程序并没有输出我们静态代码块中的内容，可见仍然加载的是 JDK 自带的 String 类

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/44.png)

**举例 2 ：**

- 代码：在我们自己的 String 类中整个 main() 方法

```java
package java.lang;public class String {    static{        System.out.println("我是自定义的String类的静态代码块");    }    //错误: 在类 java.lang.String 中找不到 main 方法    public static void main(String[] args) {        System.out.println("hello,String");    }}1234567891011
```

- 由于双亲委派机制找到的是 JDK 自带的 String 类，在那个 String 类中并没有 main() 方法

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/45.png)

**举例 3 ：**

- 代码：在 java.lang 包下整个 ShkStart 类

```java
package java.lang;public class ShkStart {    public static void main(String[] args) {        System.out.println("hello!");    }}1234567
```

- 出于保护机制，java.lang 包下不允许我们自定义类

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/46.png)

**举例 4 ：**

当我们加载jdbc.jar 用于实现数据库连接的时候

1. 首先我们需要知道的是 jdbc.jar是基于SPI接口进行实现的
2. 所以在加载的时候，会进行双亲委派，最终从根加载器中加载 SPI核心类，然后再加载SPI接口类
3. 接着在进行反向委托，通过线程上下文类加载器进行实现类 jdbc.jar的加载。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/47.png)

### 5.3、双亲委派机制优势

> **双亲委派机制的优势**

通过上面的例子，我们可以知道，双亲机制可以

1. 避免类的重复加载
2. 保护程序安全，防止核心API被随意篡改
   1. 自定义类：java.lang.String 没有屌用
   2. 自定义类：java.lang.ShkStart（报错：阻止创建 java.lang开头的类）

## 6、沙箱安全机制

1. 自定义String类时：在加载自定义String类的时候会率先使用引导类加载器加载，而引导类加载器在加载的过程中会先加载jdk自带的文件（rt.jar包中java.lang.String.class），报错信息说没有main方法，就是因为加载的是rt.jar包中的String类。
2. 这样可以保证对java核心源代码的保护，这就是沙箱安全机制。

## 7、其他

> **如何判断两个class对象是否相同？**

在JVM中表示两个class对象是否为同一个类存在两个必要条件：

1. **类的完整类名必须一致，包括包名**
2. **加载这个类的ClassLoader（指ClassLoader实例对象）必须相同**
3. 换句话说，在JVM中，即使这两个类对象（class对象）来源同一个Class文件，被同一个虚拟机所加载，但只要加载它们的ClassLoader实例对象不同，那么这两个类对象也是不相等的

> **对类加载器的引用**

1. JVM必须知道一个类型是由启动加载器加载的还是由用户类加载器加载的
2. **如果一个类型是由用户类加载器加载的，那么JVM会将这个类加载器的一个引用作为类型信息的一部分保存在方法区中**
3. 当解析一个类型到另一个类型的引用的时候，JVM需要保证这两个类型的类加载器是相同的

> **类的主动使用和被动使用**

Java程序对类的使用方式分为：主动使用和被动使用。主动使用，又分为七种情况：

1. 创建类的实例
2. 访问某个类或接口的静态变量，或者对该静态变量赋值
3. 调用类的静态方法
4. 反射（比如：Class.forName(“com.atguigu.Test”)）
5. 初始化一个类的子类
6. Java虚拟机启动时被标明为启动类的类
7. JDK7开始提供的动态语言支持：java.lang.invoke.MethodHandle实例的解析结果REF_getStatic、REF putStatic、REF_invokeStatic句柄对应的类没有初始化，则初始化

除了以上七种情况，其他使用Java类的方式都被看作是对类的被动使用，都不会导致类的初始化，即不会执行初始化阶段（不会调用 clinit() 方法和 init() 方法）

