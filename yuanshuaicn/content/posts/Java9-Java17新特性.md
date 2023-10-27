---
title: Java9-Java17新特性
date: 2023-09-09 12:36:07.568
author:
  name: "yuanshuai"
  link: "https://cloud.tencent.com/developer/user/8180692"
  email: "shuaiyuan1122@gmail.com"
tags: ["BTC", "web3.0"]

---
###  Java为什么要变？

​    因为编程语言千千万，他们就像一个生态系统一样，新的语言会出现，旧的语言会被取代，除非它不断地演变，能跟上节奏；同理，Java也是取代了竞争对手语言，且根据编程市场不断演变才能一直存活的。

  Java的天资很好，这个**面向对象语言**一开始就被精心设计：

 （1）有许多有用的库；

 （2）集成了线程和锁的支持，一开始就支持小规模的并发；

 （3）一开始就设计成将java编译成JVM字节码，这种虚拟机代码可以更快更好的被其他浏览器支持。而JVM的更新也在旨在帮助竞争对手语言在JVM上顺利运行，与java交互操作。

​    但是，生态系统中会出现不同的影响因素，比如，随着**市场的变化**，**硬件发生新的变化**，也会出现新的**编程影响因素**。

   所以java要想继续生存下去，不得不做出演变，以适应新市场，所以，演变后的java为我们提供了新的编程工具和编程概念，帮助程序员更快的编写出更简洁、更容易维护的代码和解决现有的或新的的编程问题。

### Java 版本升级

#### Java 版本发布节奏

从 Java 9 开始，Java 改变了之前的以功能特性为导向的发布周期，而是转为固定时间间隔的火车发布模式，也就是release train。固定在每年的3月和9月。例如Java 16 是2021年3月发布的，而 Java 17是2021年9月发布。

![img](https://aabb-2023.oss-cn-beijing.aliyuncs.com/d70dd26169210d0a4f12a7162d507826.jpg)

#### Java LTS 版本

升级优先选择 LTS 版本，LTS表示长期支持版本。Ubuntu 和 NodeJS 都有类似的概念。
目前 Java 8、Java 11、Java 17 是 LTS 版本
下一个 LTS 版本预计是2023年9月发布的Java 21

#### Java版本升级的方向

语法方向：如模块化，var类型，Switch的拓展，文本块语法等

API方向：如字符串的处理，Stream API的更新，HTTP Client重构等

性能方向：GC垃圾回收器的增强，JFR性能分析记录等

站在开发者的角度，我们还是更关心语法和API两个方向的升级，所以本次课程暂时先略过性能方向的更新。

#### Java 各个版本的主要特性

- Java 9：  模块化、默认 G1 回收、JShell（类似 Ruby 的 console）
- Java 10：G1 的并行完全垃圾回收、var 类型、类数据共享、Thread-Local Handshakes
- Java 11：TLS 1.3、HTTP Client重构、ZGC 垃圾回收、可运行源文件、JFR（性能分析记录）
- Java 12：扩展Switch、支持 Unicode11、字符串的transform、indent
- Java 13：增强 ZGC 垃圾回收、SocketAPI 重构
- Java 14：Record 类型（可代替 lombok）- 预览、删除 CMS 垃圾回收器
- Java 15：支持 EdDSA 加密、文本块语法、Hidden Classes
- Java 16：instanceof 模式匹配、Record 类型、ZGC 并发处理支持
- Java 17：密封类、增强的伪随机数生成器

接下来,我们就一起来学习从Java9~Java17各版本中具有代表性的一些新特性吧。

### Java 9 新特性

这一部分，我们将介绍Java 9为我们带来的新特性，Java 9的主要特性有，全新的模块机制、接口的private方法等。

#### 模块化

##### 初体验

在我们之前的开发中，不知道各位有没有发现一个问题，就是当我们导入一个`jar`包作为依赖时（包括JDK官方库），实际上很多功能我们并不会用到，但是由于它们是属于同一个依赖捆绑在一起，这样就会导致我们可能只用到一部分内容，但是需要引用一个完整的类库，实际上我们可以把用不到的类库排除掉，大大降低依赖库的规模。

于是，Java 9引入了模块化机制来对这种情况进行优化

**![image-20230108190855609](https://aabb-2023.oss-cn-beijing.aliyuncs.com/image-20230108190855609.png)**

模块可以由一个或者多个在一起的 Java 包组成，通过将这些包分出不同的模块，我们就可以按照模块的方式进行管理了。这里我们创建一个新的项目，并在`src`目录下，新建`module-info.java`文件表示此项目采用模块管理机制：

```java
//模块名称随便起一个就可以，但是注意必须是唯一的，以及模块内的包名也得是唯一的，即使模块不同
module NewModule {  
    
}
```

接着我们来创建一个主类：

**![image-20230107003128066](https://aabb-2023.oss-cn-beijing.aliyuncs.com/image-20230107003128066.png)**



程序可以正常运行，貌似和之前没啥区别，不过我们发现，JDK为我们提供的某些API不见了：

**![image-20230108120649468](https://aabb-2023.oss-cn-beijing.aliyuncs.com/image-20230108120649468.png)**

之前可以正常使用的java.sql包中的API，现在发现无法正常使用了，如果要访问这些API需要在当前模块中做依赖操作

```java
module NewModule {  
    // 表示建立当前项目和java.sql模块的依赖关系
    requires java.sql;   
}
```

这里我们导入java.sql相关模块后，就可以正常使用该模块下的API了：

**![image-20230108121224610](https://aabb-2023.oss-cn-beijing.aliyuncs.com/image-20230108121224610.png)**

**总结：**全新的模块化机制提供了另一个级别的Java代码可见性、可访问性的控制，public不再意味着`accessible`。不过，模块化机制不仅仅是用来做包分离的，还有其他的作用，我们在后面继续讲解。

##### 模块的分类

- **系统模块：**来自JDK和JRE的模块（官方提供的模块，比如我们上面用的），我们也可以直接在命令行中使用`java --list-modules`命令来列出所有的模块，不同的模块会导出不同的包供我们使用。当然，`java.base`模块不需要导入（编译器自动导入），且java.base所有的包都已导出。

  | java.base                | java.compiler                       | java.datatransfer    | java.desktop        | java.instrument         |
  | :----------------------- | ----------------------------------- | -------------------- | ------------------- | ----------------------- |
  | java.naming              | java.net.http                       | java.prefs           | java.rmi            | java.scripting          |
  | java.smartcardio         | java.sql                            | java.sql.rowset      | java.transaction.xa | java.xml                |
  | jdk.charsets             | jdk.compiler                        | jdk.crypto.cryptoki  | jdk.crypto.ec       | jdk.crypto.mscapi       |
  | jdk.httpserver           | jdk.incubator.foreign               | jdk.incubator.vector | jdk.internal.ed     | jdk.internal.jvmstat    |
  | jdk.internal.vm.compiler | jdk.internal.vm.compiler.management | jdk.jartool          | jdk.javadoc         | jdk.jcmd                |
  | jdk.jdwp.agent           | jdk.jfr                             | jdk.jlink            | jdk.jpackage        | jdk.jshell              |
  | jdk.management           | jdk.management.agent                | jdk.management.jfr   | jdk.naming.dns      | jdk.naming.rmi          |
  | jdk.sctp                 | jdk.security.auth                   | jdk.security.jgss    | jdk.unsupported     | jdk.unsupported.desktop |
  | jdk.xml.dom              | jdk.zipfs                           | jdk.net              | jdk.nio.mapmode     | jdk.random              |
  | jdk.jsobject             | jdk.jstatd                          | jdk.localedata       | java.xml.crypto     | jdk.accessibility       |
  | jdk.jconsole             | jdk.jdeps                           | jdk.jdi              | java.se             | java.security.jgss      |
  | jdk.internal.le          | jdk.internal.opt                    | jdk.internal.vm.ci   | java.security.sasl  | jdk.attach              |
  | jdk.dynalink             | jdk.editpad                         | jdk.hotspot.agent    | java.logging        | java.management         |
  | java.management.rmi      |                                     |                      |                     |                         |

- **应用程序模块：**我们自己写的Java模块项目。

- **自动模块：**可能有些库并不是Java 9以上的模块项目，这种时候就需要做兼容了，默认情况下是直接导出所有的包，可以访问所有其他模块提供的类，不然之前版本的库就用不了了。
- **未命名模块：**我们自己创建的一个Java项目，如果没有创建`module-info.java`，那么会按照未命名模块进行处理，未命名模块同样可以访问所有其他模块提供的类，这样我们之前写的Java 8代码才能正常地在Java 9以及之后的版本下运行。不过，由于没有使用Java 9的模块新特性，未命名模块只能默认暴露给其他未命名的模块和自动模块，应用程序模块无法访问这些类（实际上就是传统Java 8以下的编程模式，因为没有模块只需要导包就行）

##### 应用程序模块-自定义模块

这里我们就来创建两个项目，看看如何使用模块机制，首先我们在项目A中，添加一个User类，一会项目B需要用到：

```java
package cn.wolfcode.test;

public class User {
    String name;
    int age;

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return name+" ("+age+"岁)";
    }
}
```

接着我们编写一下项目A的模块设置：

```java
module A {
    exports cn.wolfcode.test;
}
```

这里我们将`cn.wolfcode.test`包下所有内容都暴露出去，默认情况下所有的包都是私有的，就算其他项目将此项目作为依赖也无法使用。

接着我们现在想要在项目B中使用项目A的User类，我们需要进行导入：

```java
module B {
    requires A;
}
```

现在我们就可以在Main类中使用模块`A`中暴露出来的包内容了：

```java
package cn.wolfcode.main;

// 模块B依赖了模块A，所以这里可以导入模块A中已暴露的包
import cn.wolfcode.test.User;

public class Main {
    public static void main(String[] args) {
        User u = new User("张三", 10);
        System.out.println(u);
    }
}

```

##### 模块和模块描述符

###### 模块的基本规则

在开发任何 Java 9 模块时，您应该记住以下重要的基本规则：

- 每个模块都有一个唯一的名称

  因为模块存在于 JVM 的全局空间中，所以每个模块都应该有一个唯一的名称。 与包和 JAR 文件名一样，您可以使用反向域名模式来定义模块名称。

- 每个模块在源文件中都有一些描述

  模块描述在一个名为 `module-info.java` 的源文件中表示，并且应该完全像这样命名。 每个模块应该有一个且只有一个模块描述符（`module-info.java`）。模块描述符是一个 Java 文件。 它不是 XML、文本或属性文件。

- 模块描述符文件放在顶层目录

  顶层目录是模块的根文件夹。

- 每个模块可以有任意数量的包和类型

  一个模块可以依赖于任意数量的模块

###### 模块描述符

在 Java 9 模块中，模块描述符是包含描述模块的模块元数据的资源。 它不是 XML 或属性文件； 它是一个普通的 Java 文件。

您必须将此文件命名为`module-info.java`并将其放在模块的根文件夹中。 与其他 Java 源文件一样，模块文件使用 `javac` 命令编译成 `module-info.class`。

使用`module`关键字创建模块描述符:

```java
module  A{
  // Module Meta Data goes here.
}
```

###### 模块元数据

一个模块包含以下基本元数据：

- 一个唯一的名字
- exports 子句
- requires 子句

元数据保存在模块描述符中。

```java
module eg.com.taman.mod1 {
   exports eg.com.taman.service;
   requires eg.com.taman.mod1;
}
```

模块描述符的要点

- 模块描述符可以只包含模块名，其他什么都不包含;没有 `exports` 或`requires` 子句。
- 模块描述符可以由一个或多个不带`requires`子句的`exports`子句组成;这意味着它将包导出到其他模块，但不依赖于任何其他模块——它是一个独立的模块。
- 模块描述符可以同时具有`exports`和`requires`子句；这意味着它将包导出到其他模块并使用其他模块的包——因为它依赖于其他模块，所以它不是一个独立的模块。
- 模块描述符可以有0个、1个或多个`require`子句。

###### 模块路径

​		类路径是用户定义和内置的一系列类和包或 JAR。JVM 或 Java 编译器需要类路径来编译应用程序或类。

​		在 Java 9 之前，编译器和运行时通过类路径定位类型：包含已编译 Java 类的文件夹和库归档文件列表，以及提供给 `javac` 和 `java` 命令的选项。因为可以从几个不同的位置加载类型，所以搜索这些位置的顺序会导致应用程序脆弱。

​		模块路径是一系列模块（以文件夹或 JAR 格式提供）。

​		模块和模块描述符提供的可靠配置有助于消除许多此类运行时类路径问题。每个模块都明确声明其依赖关系，这些依赖关系在应用程序启动时解决。

​		模块路径只能包含每个模块中的一个，并且每个包只能在一个模块中定义。**如果两个或多个模块具有相同的名称或导出相同的包，则运行时会在运行程序之前立即终止。**

###### 模块描述符的详解

​		上一节中对模块描述符的定义、位置、组成内容做了简要的说明。接下来详细的说明模块声明指令以及如何创建模块声明以指定模块的依赖项（使用 `requires` 指令）以及模块可用于其他模块的哪些包（使用 `exports` 指令）。

​		模块描述符是在名为`module-info.java`的文件中定义的模块声明的编译版本。每个模块声明都以关键字`module`开头，后跟一个唯一的模块名称和用大括号括起来的模块主体：

```java
module moduleName {
}
```

模块声明的主体可以是空的，也可以包含各种模块指令，包括`requires`、`exports`、`provides...with`、`uses`和`opens`。

​		**![image-20230108220232922](https://aabb-2023.oss-cn-beijing.aliyuncs.com/image-20230108220232922.png)**

##### 模块指令

- **requires指令和模块依赖**

  requires 模块指令指定该模块依赖于另一个模块——这种关系称为模块依赖关系。

  当模块 A 需要模块 B 时，模块 A 被称为读取模块 B，模块 B 被模块 A 读取。

  要指定对另一个模块的依赖，您可以使用 requires，如下所示：

  ```java
  requires modulename;
  ```

  还有一个 requires 静态指令来指示模块在编译时是必需的，但在运行时是可选的。

  ```java
  requires static <modulename>;
  ```

- **export指令与exports…to…指令**

  不管是export 指令还是exports…to…指令，所有导出模块指令都遵循以下的定义：

  导出模块指令指定模块的包之一，其***their nested public and protected types***应该可供所有其他模块中的代码访问。

  严格导出：模块`my.module`下的包`my.package`只给模块`other.module`和`another.module`使用，其他模块无法使用。

  ```java
  module my.module{
    exports my.package to other.module, another.module;
  }
  ```

  需要此功能以避免将内部包暴露给所有模块，同时允许它们仅由选定的友好模块访问（或换句话说，强封装）。

  例如，JDK `java.base`有许多不应该向所有人公开的包。 这是`java.base`模块的相关片段：

  ```java
  module java.base {
      .....
  exports com.sun.security.ntlmtojava.security.sasl;
  exports jdk.internal to jdk.jfr;
  exports jdk.internal.jimage to jdk.jlink;
  exports jdk.internal.jimage.decompressor to jdk.jlink;
  exports jdk.internal.jmod to jdk.compiler, jdk.jlink;
  exports jdk.internal.loader to java.instrument, java.logging;
  exports jdk.internal.logger to java.logging;
  exports jdk.internal.math to java.desktop;
      .....
  }
  ```

  

- **依赖传递**

  现在还有一个问题，如果模块`module.a`依赖于其他模块，那么会不会传递给依赖于模块`module.a`的模块呢？

  ```java
  module module.a {
      exports com.test to module.b;   // 使用exports将com.test包下所有内容暴露出去，这样其他模块才能导入
      requires java.sql;   // 这里添加一个模块的依赖
  }
  ```

  **![image-20230108205355113](https://aabb-2023.oss-cn-beijing.aliyuncs.com/image-20230108205355113.png)**

  可以看到，在模块`module.b`中，并没有进行依赖传递，说明哪个模块导入的依赖只能哪个模块用，但是现在我们希望依赖可以传递，就是哪个模块用了什么依赖，依赖此模块的模块也会自动进行依赖，我们可以通过一个关键字(transitive)解决：

  ```java
  module module.a {
      exports com.test to module.b;   //使用exports将com.test包下所有内容暴露出去，这样其他模块才能导入
      requires transitive java.sql;   //使用transitive来向其他模块传递此依赖
  }
  ```

  现在就可以使用了：

  **![image-20230108215002804](https://aabb-2023.oss-cn-beijing.aliyuncs.com/image-20230108215002804.png)**

- opens指令与opens…to…指令

​		在 Java 9 之前，您可以使用反射来了解包中包含的类型以及特定类型的所有成员，甚至是私有成员，无论您是否希望允许外部人员拥有这种能力，因此没有真正封装。

​		模块系统的一个关键规定是强封装； 因此，模块中的类型不能被其他模块访问，除非它是公共类型并且您导出它的包。 你只公开你想公开的包。

```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence,
               Constable, ConstantDesc {
    @Stable
    private final byte[] value;
```

```java
public static void main(String[] args) throws NoSuchFieldException, IllegalAccessException {
    Class<String> stringClass = String.class;
    Field field = stringClass.getDeclaredField("value"); //我们通过反射来获取String类中的value字段
    field.setAccessible(true);   //由于是private访问权限，所以我们修改其可访问性
    System.out.println(field.get("ABCD"));
}
```

但是我们发现，在程序运行之后，修改操作被阻止了：

![image-20230108200200299](https://aabb-2023.oss-cn-beijing.aliyuncs.com/image-20230108200200299.png)

那么怎么样才可以使用反射呢？我们可以为其他模块开放某些运行使用反射的类：

```java
open module module.a {   //直接添加open关键字开放整个模块的反射权限
    exports com.test to module.b;
}
```

```java
module module.a {
    exports com.test to module.b;
    opens com.test;   //通过使用opens关键字开放当前包的反射权限
  	//也可以指定目标开放反射 opens com.test to module.b;
}
```

我们还可以指定模块需要使用的抽象类或是接口实现：

```java
package com.test;

public interface Test {
}
```

```java
open module module.a {
    exports com.test to module.b;
    uses com.test.Test;  //使用uses指定，Test是一个接口（比如需要的服务等），模块需要使用到
}
```

我们可以在模块B中去实现一下，然后声明我们提供了实现类：

```java
package com.main;

import com.test.Test;

public class TestImpl implements Test {

}
```

```java
module module.b {
    requires module.a;   //导入项目A的模块，此模块暴露了com.test包
    provides com.test.Test with com.main.TestImpl;  //声明此模块提供了Test的实现类
}
```

了解了以上的相关知识后，我们就可以简单地进行模块的使用了。比如现在我们创建了一个新的Maven项目：

##### 项目演练

![image-20230108230552382](https://aabb-2023.oss-cn-beijing.aliyuncs.com/image-20230108230552382.png)

然后我们导入了junit框架的依赖，如果我们不创建`module-info.java`文件，那么就是一个未命名模块，未命名模块默认可以使用其他所有模块提供的类，实际上就是我们之前的开发模式：

```java
package cn.wolfcode.test;

import org.junit.Test;

public class MyTest {
    @Test
    public void test1() {
        System.out.println("test1");
    }
}
```

现在我们希望按照全新的模块化开发模式来进行开发，将我们的项目从未命名模块改进为应用程序模块，所以我们先创建好`module-info.java`文件：

```java
module cn.wolfcode.test {
}
```

可以看到，直接报错了：

**![image-20230108230727368](https://aabb-2023.oss-cn-beijing.aliyuncs.com/image-20230108230727368.png)**

明明导入了junit的依赖，却无法使用，这是因为我们还需要去依赖对应的模块才行：

```java
module cn.wolfcode.test {
    requires junit;
}
```

**![image-20230108231013205](https://aabb-2023.oss-cn-beijing.aliyuncs.com/image-20230108231013205.png)**

这样我们就可以正常使用了，之后为了教程演示方便，咱们还是不用模块。



#### JShell交互式编程

Java 9为我们通过了一种交互式编程工具JShell，你还别说，真有Python那味。

**![image-20230108231427856](https://aabb-2023.oss-cn-beijing.aliyuncs.com/image-20230108231427856.png)**

环境配置完成后，我们只需要输入`jshell`命令即可开启交互式编程了，它支持我们一条一条命令进行操作。

比如我们来做一个简单的计算：

```shell
jshell> int a = 10
a ==> 10

jshell> int b = 20
b ==> 20

jshell> int c = a + b
c ==> 30

jshell>
```

我们一次输入一行（可以不加分号），先定义一个a=10和b=10，然后定义c并得到a+b的结果，可以看到还是非常方便的，但是注意语法还是和Java是一样的。

我们也可以快速创建一个方法供后续的调用。当我们按下Tab键还可以进行自动补全：

```she
jshell> public void test(int a, int b) {
   ...>     System.out.println("a + b = " + (a + b));
   ...> }
|  已创建 方法 test(int,int)

jshell> test(10, 20)
a + b = 30

jshell>
```

除了直接运行我们写进去的代码之外，它还支持使用命令，输入`help`来查看命令列表：

```shell
jshell> /help
|  键入 Java 语言表达式, 语句或声明。
|  或者键入以下命令之一:
|  /list [<名称或 id>|-all|-start]
|       列出您键入的源
|  /edit <名称或 id>
|       编辑源条目
|  /drop <名称或 id>
|       删除源条目
|  /save [-all|-history|-start] <文件>
|       将片段源保存到文件
|  /open <file>
|       打开文件作为源输入
..............................
```

比如我们可以使用`/vars`命令来展示当前定义的变量列表：

```shell
jshell> /vars
|    int a = 10
|    int b = 20
|    int c = 30

jshell>
```

当我们不想使用jshell时，直接输入`/exit`退出即可：

```shell
jshell> /exit
|  再见

C:\Users\Administrator>
```

#### 接口中的private方法

在Java 8中，接口中 的方法支持添加`default`关键字来添加默认实现：

```java
public interface Test {
    default void test(){
        System.out.println("我是test方法默认实现");
    }
}
```

而在Java 9中，接口再次得到强化，现在接口中可以存在私有方法了：

```java
public interface Test {
    default void test(){
        System.out.println("我是test方法默认实现");
        this.inner();   //接口中方法的默认实现可以直接调用接口中的私有方法
    }
    
    private void inner(){   
        System.out.println("我是接口中的私有方法！");
    }
}
```

注意私有方法必须要提供方法体，因为权限为私有的，也只有这里能进行方法的具体实现了，并且此方法只能被接口中的其他私有方法或是默认实现调用。

#### 集合类新增工厂方法

在之前，如果我们想要快速创建一个Map只能：

```java
public static void main(String[] args) {
    Map<String, Integer> map = new HashMap<>();   //要快速使用Map，需要先创建一个Map对象，然后再添加数据
    map.put("name", "张三");
    map.put("age", 20);
    System.out.println(map);
}
```

而在Java 9之后，我们可以直接通过`of`方法来快速创建了：

```java
public static void main(String[] args) {
    Map<String, Integer> map = Map.of("name", "张三", "age", 20);  //直接一句搞定
    System.out.println(map);
}
```

是不是感觉非常方便，of方法还被重载了很多次，分别适用于快速创建包含0~10对键值对的Map：

**![image-20230108234325297](https://aabb-2023.oss-cn-beijing.aliyuncs.com/image-20230108234325297.png)**

但是注意，通过这种方式创建的Map和通过Arrays创建的List比较类似，也是无法进行修改的。

当然，除了Map之外，其他的集合类都有相应的`of`方法：

```java
public static void main(String[] args) {
    Set<String> set = Set.of("BBB", "CCC", "AAA");
    List<String> list = List.of("AAA", "CCC", "BBB");
}
```

#### 改进的 Stream API

还记得我们之前在JavaSE中学习的Stream流吗？当然这里不是指进行IO操作的流，而是JDK1.8新增的Stream API，通过它大大方便了我们的编程。

```java
public static void main(String[] args) {
    Stream
            .of("A", "B", "B", "C")   //这里我们可以直接将一些元素封装到Stream中
            .filter(s -> s.equals("B"))   //过滤出所有的B
            .distinct()   //去重
            .forEach(System.out::println);   //最后打印去重后的结果
}
```

自从有了Stream，我们对于集合的一些操作就大大地简化了，对集合中元素的批量处理，只需要在Stream中一气呵成（具体的详细操作请回顾JavaSE篇）

如此方便的框架，在Java 9得到了进一步的增强：

```java
public static void main(String[] args) {
    Stream
            .of(null)   //如果传入null会报错
            .forEach(System.out::println);

    Stream
            .ofNullable(null) //使用新增的ofNullable方法，这样就不会了，不过这样的话流里面就没东西了
            .forEach(System.out::println);
}
```

还有，我们可以通过迭代快速生成一组数据（实际上Java 8就有了，这里新增的是允许结束迭代的）：

```java
public static void main(String[] args) {
    Stream
        .iterate(0, i -> i + 1)   //Java8只能像这样生成无限的流，第一个参数是种子，就是后面的UnaryOperator的参数i一开始的值，最后会返回一个值作为i的新值，每一轮都会执行UnaryOperator并生成一个新值到流中，这个是源源不断的，如果不加limit()进行限制的话，将无限生成下去。
      	.limit(20)   //这里限制生成20个
        .forEach(System.out::println); 
}
```



```java
public static void main(String[] args) {
    Stream
            //不知道怎么写？参考一下：for (int i = 0;i < 20;i++)
            .iterate(0, i -> i < 20, i -> i + 1)  //快速生成一组0~19的int数据，中间可以添加一个断言，表示什么时候结束生成
            .forEach(System.out::println);
}
```



Stream还新增了对数据的截断操作，比如我们希望在读取到某个元素时截断，不再继续操作后面的元素：

```java
public static void main(String[] args) {
    Stream
            .iterate(0, i -> i + 1)
            .limit(20)
            .takeWhile(i -> i < 10)   //当i小于10时正常通过，一旦大于等于10直接截断
            .forEach(System.out::println);
}
```

```java
public static void main(String[] args) {
    Stream
            .iterate(0, i -> i + 1)
            .limit(20)
            .dropWhile(i -> i < 10)   //和上面相反，上来就是截断状态，只有当满足条件时再开始通过
            .forEach(System.out::println);
}
```



#### 其他更新

Try-with-resource语法现在不需要再完整的声明一个变量了，我们可以直接将现有的变量丢进去：

```java
public static void main(String[] args) throws IOException {
    InputStream inputStream = Files.newInputStream(Paths.get("pom.xml"));
    try (inputStream) {   //单独丢进try中，效果是一样的
        for (int i = 0; i < 100; i++)
            System.out.print((char) inputStream.read());
    }
}
```

在Java 8中引入了Optional类，它很好的解决了判空问题：

```java
public static void main(String[] args) throws IOException {
    test(null);
}

public static void test(String s){
    //比如现在我们想执行 System.out.println(str.toLowerCase())
    //但是由于我们不清楚给进来的str到底是不是null，如果是null的话会引起空指针异常
    //但是去单独进行一次null判断写起来又不太简洁，这时我们可以考虑使用Optional进行包装
    Optional
            .ofNullable(s)
            .ifPresent(str -> System.out.println(str.toLowerCase()));
}
```

在Java 9新增了一些更加方便的操作：

```java
public static void main(String[] args) {
    String str = null;
    Optional.ofNullable(str).ifPresentOrElse(s -> {  //通过使用ifPresentOrElse，我们同时处理两种情况
        System.out.println("被包装的元素为："+s);     //第一种情况和ifPresent是一样的
    }, () -> {
        System.out.println("被包装的元素为null");   //第二种情况是如果为null的情况
    });
}
```



我们也可以使用`or()`方法快速替换为另一个Optional类：

```java
public static void main(String[] args) {
    String str = null;
    Optional.ofNullable(str)
      .or(() -> Optional.of("AAA"))   //如果当前被包装的类不是null，依然返回自己，但是如果是null，那就返回Supplier提供的另一个Optional包装
      .ifPresent(System.out::println);
}
```

当然还支持直接转换为Stream，这里就不多说了。

在Java 8及之前，匿名内部类是没办法使用钻石运算符进行自动类型推断的：

```java
public abstract class Test<T>{   //这里我们写一个泛型类
    public T t;

    public Test(T t) {
        this.t = t;
    }

    public abstract T test();
}
```



```java
public static void main(String[] args) throws IOException {
    // 在低版本这样写是会直接报错的，因为匿名内部类不支持自动类型推断
    // 但是很明显我们这里给的参数是String类型的，可以通过类型推断得到
    // 在Java 9之后，这样的写法终于可以编译通过了
    Test<String> test = new Test<>("AAA") {   
     
        @Override
        public String test() {
            return t;
        }
    };
}
```

当然除了以上的特性之外还有Java 9的多版本JAR包支持、CompletableFuture API的改进等，因为不太常用，这里就不做介绍了。



## Java 10 新特性

Java 10主要带来的是一些内部更新，相比Java 9带来的直观改变不是很多，其中比较突出的就是局部变量类型推断了。



### 局部变量类型推断

在Java中，我们可以使用自动类型推断：

```java
public static void main(String[] args) {
    // String a = "Hello World!";   之前我们定义变量必须指定类型
    var a = "Hello World!";   //现在我们使用var关键字来自动进行类型推断，因为完全可以从后面的值来判断是什么类型
}
```

但是注意，`var`关键字必须位于有初始值设定的变量上，否则鬼知道你要用什么类型。



我们来看看是不是类型也能正常获取：

```java
public static void main(String[] args) {
    var a = "Hello World!";
    System.out.println(a.getClass());
}
```



这里虽然是有了var关键字进行自动类型推断，但是最终还只是一个语法糖，得到的Class也是String类型。但是Java终究不像JS那样进行动态推断，这种类型推断仅仅发生在编译期间，到最后编译完成后还是会变成具体类型的：

```java
public static void main(String[] args) {
    String a = "Hello World!";
    System.out.println(a.getClass());
}
```



并且`var`关键字仅适用于局部变量，我们是没办法在其他地方使用的，比如类的成员变量：

**![image-20230109000545959](https://aabb-2023.oss-cn-beijing.aliyuncs.com/image-20230109000545959.png)**

有关Java 10新增的一些其他改进，这里就不提了。



## Java 11 新特性

Java 11 是继Java 8之后的又一个LTS长期维护版本，其中比较关键的是用于Lambda的形参局部变量语法。

### 用于Lambda的形参局部变量语法

在Java 10我们认识了`var`关键字，它能够直接让局部变量自动进行类型推断，不过它不支持在lambda中使用：

**![image-20230109001040814](https://aabb-2023.oss-cn-beijing.aliyuncs.com/image-20230109001040814.png)**

但是实际上这里是完全可以进行类型推断的，所以在Java 11，终于是支持了，这样编写就不会报错了：

**![image-20230109001313887](https://aabb-2023.oss-cn-beijing.aliyuncs.com/image-20230109001313887.png)**

### 针对于String类的方法增强

在Java 11为String新增一些更加方便的操作：

```java
public static void main(String[] args) {
    var str = "AB\nC\nD";
    System.out.println(str.isBlank());    //isBlank方法用于判断是否字符串为空或者是仅包含空格
    str
            .lines()   //根据字符串中的\n换行符进行切割，分为多个字符串，并转换为Stream进行操作
            .forEach(System.out::println);
}
```

我们还可以通过`repeat()`方法来让字符串重复拼接：

```java
public static void main(String[] args) {
    String str = "ABCD";   //比如现在我们有一个ABCD，但是现在我们想要一个ABCDABCD这样的基于原本字符串的重复字符串
    System.out.println(str.repeat(2));  //一个repeat就搞定了
}
```



我们也可以快速地进行空格去除操作：

```java
public static void main(String[] args) {
    String str = " A B C D ";
    System.out.println(str.strip());   //去除首尾空格
    System.out.println(str.stripLeading());  //去除首部空格
    System.out.println(str.stripTrailing());   //去除尾部空格
}
```



### 全新的HttpClient使用

在Java 9的时候其实就已经引入了全新的Http Client API，用于取代之前比较老旧的HttpURLConnection类，新的API支持最新的HTTP2和WebSocket协议。

```java
public static void main(String[] args) throws URISyntaxException, IOException, InterruptedException {
    HttpClient client = HttpClient.newHttpClient();   //直接创建一个新的HttpClient
  	//现在我们只需要构造一个Http请求实体，就可以让客户端帮助我们发送出去了（实际上就跟浏览器访问类似）
    HttpRequest request = HttpRequest.newBuilder().uri(new URI("https://www.baidu.com")).build();
  	//现在我们就可以把请求发送出去了，注意send方法后面还需要一个响应体处理器（内置了很多）这里我们选择ofString直接吧响应实体转换为String字符串
    HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
  	//来看看响应实体是什么吧
    System.out.println(response.body());
}
```



利用全新的客户端，我们甚至可以轻松地做一个爬虫（仅供学习使用，别去做违法的事情，爬虫玩得好，牢饭吃到饱），比如现在我们想去批量下载某个网站的壁纸：

![image-20230109004451564](https://aabb-2023.oss-cn-beijing.aliyuncs.com/image-20230109004451564.png)

网站地址：https://pic.netbian.com/4kmeinv/

我们随便点击一张壁纸，发现网站的URL格式为：https://pic.netbian.com/tupian/数字.html

```java
public static void main(String[] args) throws URISyntaxException, IOException, InterruptedException {
    HttpClient client = HttpClient.newHttpClient();
    for (int i = 0; i < 10; i++) {  //先不要一次性获取太多，先来10个
        HttpRequest request = HttpRequest.newBuilder().uri(new URI("https://pic.netbian.com/tupian/"+(30880 + i)+".html")).build();  //这里我们按照规律，批量获取
        HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
        System.out.println(response.body());  //这里打印一下看看网页
    }
}
```



可以看到，最后控制台成功获取到这些图片的网站页面了：

![image-20230109004933953](https://aabb-2023.oss-cn-beijing.aliyuncs.com/image-20230109004933953.png)

接着我们需要来观察一下网站的HTML具体怎么写的，把图片的地址提取出来：

![image-20230109004854735](https://aabb-2023.oss-cn-beijing.aliyuncs.com/image-20230109004854735.png)



好了，知道图片在哪里就好办了，直接字符串截取：

```java
public static void main(String[] args) throws URISyntaxException, IOException, InterruptedException {
    HttpClient client = HttpClient.newHttpClient();
    for (int i = 0; i < 10; i++) {
        ...
        String html = response.body();
        
        String prefix = "<a href=\"\" id=\"img\"><img src=\"";  //先找好我们要截取的前面一段，作为前缀去匹配位置
        String suffix = "\" data-pic=";   //再找好我们要截取的屁股后面紧接着的位置，作为后缀去匹配位置
        //直接定位，然后前后截取，得到最终的图片地址
        String imgPath = html.substring(html.indexOf(prefix) + prefix.length());
        imgPath = imgPath.substring(0, imgPath.indexOf(suffix));
        System.out.println(imgPath);  //最终的图片地址就有了
    }
}
```

好了，现在图片地址也可以批量拿到了，完整路径为https://pic.netbian.com/imgPath直接获取这些图片然后保存到本地吧：

```java
public static void main(String[] args) throws URISyntaxException, IOException, InterruptedException {
    HttpClient client = HttpClient.newHttpClient();
        for (int i = 0; i < 10; i++) {  //先不要一次性获取太多，先来10个
            HttpRequest request = HttpRequest.newBuilder().uri(new URI("https://pic.netbian.com/tupian/"+(30860 + i)+".html")).build();  //这里我们按照规律，批量获取
            HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
            String html = response.body();
            String prefix = "<a href=\"\" id=\"img\"><img src=\"";  //先找好我们要截取的前面一段，作为前缀去匹配位置
            String suffix = "\" data-pic=";   //再找好我们要截取的屁股后面紧接着的位置，作为后缀去匹配位置
            //直接定位，然后前后截取，得到最终的图片地址
            String imgPath = html.substring(html.indexOf(prefix) + prefix.length());
            imgPath = imgPath.substring(0, imgPath.indexOf(suffix));
            System.out.println(imgPath);  //最终的图片地址就有了


            //创建请求，把图片取到
            HttpRequest imageRequest = HttpRequest.newBuilder().uri(new URI("https://pic.netbian.com"+imgPath)).build();
            //这里以输入流的方式获取，不过貌似可以直接下载文件，各位小伙伴可以单独试试看
            HttpResponse<InputStream> imageResponse = client.send(imageRequest, HttpResponse.BodyHandlers.ofInputStream());
            //拿到输入流和文件输出流
            InputStream imageInput = imageResponse.body();
            FileOutputStream stream = new FileOutputStream("d:/images/"+i+".jpg"); //一会要保存的格式
            try (stream;imageInput){  //直接把要close的变量放进来就行，简洁一些了
                int size;   //下面具体保存过程的不用我多说了吧
                byte[] data = new byte[1024];
                while ((size = imageInput.read(data)) > 0) {
                    stream.write(data, 0, size);
                }
            }
        }
}
```



我们现在来看看效果吧，美女的图片已经成功保存到本地了：

**![image-20230109010458431](https://aabb-2023.oss-cn-beijing.aliyuncs.com/image-20230109010458431.png)**



当然，这仅仅是比较简单的爬虫，不过我们的最终目的还是希望各位能够学会使用新的HttpClient API。



## Java 12-16 新特性

由于Java版本的更新迭代速度自Java 9开始为半年更新一次（Java 8到Java 9隔了整整三年），所以各个版本之间的更新内容比较少，剩余的6个版本，我们就多个版本放在一起进行讲解了。

![images](https://aabb-2023.oss-cn-beijing.aliyuncs.com/images.png)



Java12-16这五个版本并非长期支持版本，所以很多特性都是一种处于实验性功能，12/13版本引入了一些实验性功能，并根据反馈进行调整，最后在后续版本中正式开放使用，其实就是体验服的那种感觉。

### 新的switch语法

在Java 12引入全新的switch语法，让我们使用switch语句更加的灵活，比如我们想要编写一个根据成绩得到等级的方法：

```java
/**
 * 根据输入的分数（范围 0 - 100）返回对应的等级：
 *      100-90：优秀
 *      70-80：良好
 *      60-70：及格
 *      0-60：寄
 * @param score 分数
 * @return 等级
 */
public static String grade(int score){
    
}
```

现在我们想要使用switch来实现这个功能，之前的写法是：

```java
public static String grade(int score){
    score /= 10;  //既然分数段都是整数，那就直接整除10
  	String res = null;
    switch (score) {
        case 10: case 9:
            res =  "优秀";
        	break;
        case 8: case 7:
            res = "良好";
        	break;
        case 6:
            res = "及格";
        	break;
        default:
            res = "不及格";
        	break;
    }
  	return res;
}
```

但是现在我们可以使用新的特性了：

语法那是相当的简洁，而且也不需要我们自己考虑break或是return来结束switch了

```java
public static String grade(int score){
    score /= 10;
    return switch (score) {   //增强版switch语法
        case 10, 9 -> "优秀";   
        case 8, 7 -> "良好"; 
        case 6 -> "及格";
        default -> "不及格";
    };
}
```

不过最后编译出来的样子，貌似还是和之前是一样的：

```java
public static String grade(int score) {
    score /= 10;
    String var10000;
    switch(score) {
        case 6:
            var10000 = "及格";
            break;
        case 7:
        case 8:
            var10000 = "良好";
            break;
        case 9:
        case 10:
            var10000 = "优秀";
            break;
        default:
            var10000 = "不及格";
    }

    return var10000;
    }
```

这种全新的switch语法称为`switch表达式`，它的意义不仅仅体现在语法的精简上，我们来看看它的详细规则：

```java
var res = switch (obj) {   //switch有了返回值，可以定义变量接收
    case [匹配值, ...] -> "优秀";   // 可以有多个匹配值，使用逗号隔开，使用 -> 返回匹配此case语句时的结果
    case ...   // 根据不同的分支，可以存在多个case
    default -> "不及格";   //注意，表达式要求必须涵盖所有的可能，所以必须添加default，否则报错
};
```

**![image-20230109011918775](https://aabb-2023.oss-cn-beijing.aliyuncs.com/image-20230109011918775.png)**

那么如果我们并不是能够马上返回，而是需要做点什么其他的工作才能返回结果呢？

```java
var res = switch (obj) {   //增强版switch语法
    case [匹配值, ...] -> "优秀";
    default -> {   //我们可以使用花括号来将整套逻辑括起来
        //... 我是其他要做的事情
        yield  "不及格";  //注意处理完成后需要返回最终结果，但是这样并不是使用return，而是yield关键字
    }
};
```



当然，也可以像这样：

```java
var res = switch (args.length) {   //增强版switch语法
    case [匹配值, ...]:
        yield "AAA";   //传统的:写法，通过yield指定返回结果，同样不需要break
    default:
    		System.out.println("默认情况");
        yield "BBB";
};
```

这种全新的语法，可以说极大地方便了我们的编码，不仅代码简短，而且语义明确。

**注意：**switch表达式在**Java 14**才正式开放使用，所以我们项目的代码级别需要调整到14以上。

### 文本块

如果你学习过Python，一定知道三引号：

```python
#当我们需要使用复杂字符串时，可能字符串中包含了很多需要转义的字符，比如双引号等，这时我们就可以使用三引号来包括字符串
multi_line =  """
                nice to meet you!
                  nice to meet you!
                      nice to meet you!
                """
print multi_line
```

没错，**Java13**也带了这样的特性，旨在方便我们编写复杂字符串，这样就不用再去用那么多的转义字符了：

**![image-20230109013806558](https://aabb-2023.oss-cn-beijing.aliyuncs.com/image-20230109013806558.png)**



可以看到，Java中也可以使用这样的三引号来表示字符串了，并且我们可以随意在里面使用特殊字符，包括双引号等，但是最后编译出来的结果实际上还是会变成和之前一样使用了转义字符的字符串：

仔细想想，这样我们写SQL或是HTML岂不是就舒服多了？

**注意：**文本块表达式在**Java15**才正式开放使用，所以我们项目的代码级别需要调整到15以上。



### 新的instanceof语法

```java
public class Student {
    private final String name;

    public Student(String name) {
        this.name = name;
    }

    @Override
    public boolean equals(Object obj) {
        if(obj instanceof Student) {   //首先判断是否为Student类型
            Student student = (Student) obj;  //如果是，那么就类型转换
            return student.name.equals(this.name);  //最后比对属性是否一样
        }
        return false;
    }
}
```

在之前我们一直都是采用这种先判断类型，然后类型转换，最后才能使用的方式，但是这个版本instanceof加强之后，我们就不需要了，我们可以直接将student替换为模式变量：

```java
public class Student {
    private final String name;

    public Student(String name) {
        this.name = name;
    }

    @Override
    public boolean equals(Object obj) {
        if(obj instanceof Student student) {  // 直接写变量名字，类型转换之后会赋值给该变量
            return student.name.equals(this.name);  //下面直接用，是不是贼方便
        }
        return false;
    }
}
```

在使用`instanceof`判断类型成立后，会自动强制转换类型为指定类型，简化了我们手动转换的步骤。

**注意：**新的instanceof语法在**Java16**才正式开放使用，所以我们项目的代码级别需要调整到16以上。



### 空指针异常的改进

相信各位小伙伴在调试代码时，经常遇到空指针异常，比如下面的这个例子：

```java
public static void test(String a, String b){
    int length = a.length() + b.length();   //可能给进来的a或是b为null
    System.out.println(length);
}
```

a或者b为空时，就会出现空指针异常：

**![image-20230109015134858](https://aabb-2023.oss-cn-beijing.aliyuncs.com/image-20230109015134858.png)**

但是由于我们这里a和b都调用了`length()`方法，虽然空指针异常告诉我们问题出现在这一行，但是到底是a为null还是b为null呢？我们是没办法直接得到的，此时我们只能使用debug调试了

但是当我们在**Java14**或更高版本运行时：

**![image-20230109015157122](https://aabb-2023.oss-cn-beijing.aliyuncs.com/image-20230109015157122.png)**

这里会明确指出是哪一个变量调用出现了空指针，是不是感觉特别人性化。



### 记录类型

继类、接口、枚举、注解之后的又一新类型来了，它的名字叫"记录"，在**Java14**中首次出场，这一出场，Lombok的噩梦来了。



在实际开发中，很多的类仅仅只是充当一个实体类罢了，保存的是一些不可变数据，比如我们从数据库中查询的账户信息，最后会被映射为一个实体类：

```java
@Data
public class Account {   //使用Lombok，一个注解就搞定了
    String username;
    String password;
}
```



Lombok可以说是简化代码的神器了，他能在编译时自动生成getter和setter、构造方法、toString()方法等实现，在编写这些实体类时，简直不要太好用，而这一波，官方也是看不下去了，于是自己也搞了一个记录类型。



记录类型本质上也是一个普通的类，不过是final类型且继承自java.lang.Record抽象类的，它会在编译时，会自动编译出 `public get` `hashcode` 、`equals`、`toString` 等方法，好家伙，这是要逼死Lombok啊。

```java
public record Account(String username, String password) {  //直接把字段写在括号中

}
```

使用起来也是非常方便，自动生成了构造方法和成员字段的公共get方法：

并且toString也是被重写了的：

`equals()`方法仅做成员字段之间的值比较，也是帮助我们实现好了的：

```java
Account account0 = new Account("Admin", "123456");
Account account1 = new Account("Admin", "123456");   //两个属性都是一模一样的
System.out.println(account0.equals(account1));  //得到true
```

是不是感觉这种类型就是专门为这种实体类而生的。

```java
public record Account(String username, String password) implements Runnable {  //支持实现接口，但是不支持继承，因为继承的坑位已经默认被占了

    @Override
    public void run() {
        
    }
}
```

**注意：**记录类型在**Java16**才正式开放使用，所以我们项目的代码级别需要调整到16以上。



## Java 17 新特性

Java 17作为新的LTS长期维护版本，我们来看看都更新了什么（不包含预览特性，包括switch第二次增强，哈哈，果然还是强度不够，都连续加强两个版本了）



### 密封类型

密封类型可以说是Java 17正式推出的又一重磅类型，它在Java 15首次提出并测试了两个版本。

在Java中，我们可以通过继承（extends关键字）来实现类的能力复用、扩展与增强。但有的时候，可能并不是所有的类我们都希望能够被继承。所以，我们需要对继承关系有一些限制的控制手段，而密封类的作用就是**限制类的继承**。

实际上在之前我们如果不希望别人继承我们的类，可以直接添加`final`关键字：

```java
public final class A{   //添加final关键字后，不允许对此类继承
    
}
```



这样有一个缺点，如果添加了`final`关键字，那么无论是谁，包括我们自己也是没办法实现继承的，但是现在我们有一个需求，只允许我们自己写的类继承A，但是不允许别人写的类继承A，这时该咋写？在Java 17之前想要实现就很麻烦。



但是现在我们可以使用密封类型来实现这个功能：

```java
public sealed class A permits B{   //在class关键字前添加sealed关键字，表示此类为密封类型，permits后面跟上允许继承的类型，多个子类使用逗号隔开

}
```

密封类型有以下要求：

- 可以基于普通类、抽象类、接口，也可以是继承自其他接抽象类的子类或是实现其他接口的类等。

- 必须有子类继承，且不能是匿名内部类或是lambda的形式。

- `sealed`写在原来`final`的位置，但是不能和`final`、`non-sealed`关键字同时出现，只能选择其一。

- 继承的子类必须显式标记为`final`、`sealed`或是`non-sealed`类型。

标准的声明格式如下：

```java
public sealed [abstract] [class/interface] 类名 [extends 父类] [implements 接口, ...] permits [子类, ...]{
		//里面的该咋写咋写
}
```

注意子类格式为：

```java
public [final/sealed/non-sealed] class 子类 extends 父类 {   //必须继承自父类
			//final类型：任何类不能再继承当前类，到此为止，已经封死了。
  		//sealed类型：同父类，需要指定由哪些类继承。
  		//non-sealed类型：重新开放为普通类，任何类都可以继承。
}
```

比如现在我们写了这些类：

```java
public sealed class A  permits B{   //指定B继承A

}
```

```java
public final class B extends A {   //在子类final，彻底封死

}
```

其他的类无论是继承A还是继承B都无法通过编译：

但是如果此时我们主动将B设定为`non-sealed`类型：

```java
public non-sealed class B extends A {

}
```

这样就可以正常继承了，因为B指定了`non-sealed`主动放弃了密封特性，这样就显得非常灵活了。

当然我们也可以通过反射来获取类是否为密封类型：

```java
public static void main(String[] args) {
    Class<A> a = A.class;
    System.out.println(a.isSealed());   //是否为密封
}
```

至此，Java 9 - 17的主要新特性就讲解完毕了。