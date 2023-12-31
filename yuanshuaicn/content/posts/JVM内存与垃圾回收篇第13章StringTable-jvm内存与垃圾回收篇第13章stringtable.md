---
title: JVM内存与垃圾回收篇第13章StringTable
date: 2022-03-19 18:12:20.377
author:
  name: "yuanshuai"
  link: "https://cloud.tencent.com/developer/user/8180692"
  email: "shuaiyuan1122@gmail.com"
tags: 
- Java
- JVM
---

# 第 13 章 StringTable

## 1、String 的基本特性

### 1.1、String 概述

> **String 的概述**

1. String：字符串，使用一对 “” 引起来表示

   ```java
   String s1 = "mogublog" ;   			// 字面量的定义方式
   String s2 =  new String("moxi");     // new 对象的方式
   ```

2. String声明为final的，不可被继承

3. String实现了Serializable接口：表示字符串是支持序列化的。实现了Comparable接口：表示String可以比较大小

4. string在jdk8及以前内部定义了final char[] value用于存储字符串数据。JDK9时改为byte[]

> **为什么 JDK9 改变了 String 的结构**

**官方文档**

http://openjdk.java.net/jeps/254

------

**为什么改为 byte[] 存储？**

1. String类的当前实现将字符存储在char数组中，每个字符使用两个字节(16位)。
2. 从许多不同的应用程序收集的数据表明，字符串是堆使用的主要组成部分，而且大多数字符串对象只包含拉丁字符。这些字符只需要一个字节的存储空间，因此这些字符串对象的内部char数组中有一半的空间将不会使用。
3. 之前 String 类使用 UTF-16 的 char[] 数组存储，现在改为 byte[] 数组 外加一个编码标志位存储，该编码标志将指定 String 类中 byte[] 数组的编码方式
4. 结论：String再也不用char[] 来存储了，改成了byte [] 加上编码标记，节约了一些空间
5. 同时基于String的数据结构，例如StringBuffer和StringBuilder也同样做了修改

```java
// 之前
private final char value[];
// 之后
private final byte[] value
```

### 1.2、String 的基本特征

> **String 的基本特征**

**String：代表不可变的字符序列。简称：不可变性。**

1. 当对字符串重新赋值时，需要重写指定内存区域赋值，不能使用原有的value进行赋值。
2. 当对现有的字符串进行连接操作时，也需要重新指定内存区域赋值，不能使用原有的value进行赋值。
3. 当调用String的replace()方法修改指定字符或字符串时，也需要重新指定内存区域赋值，不能使用原有的value进行赋值。

通过字面量的方式（区别于new）给一个字符串赋值，此时的字符串值声明在字符串常量池中。

------

**当对字符串重新赋值时，需要重写指定内存区域赋值，不能使用原有的value进行赋值**

- 代码

```java
@Test
public void test1() {
    String s1 = "abc";//字面量定义的方式，"abc"存储在字符串常量池中
    String s2 = "abc";
    s1 = "hello";

    System.out.println(s1 == s2);//判断地址：false

    System.out.println(s1);//hello
    System.out.println(s2);//abc
}
```

- 字节码指令

   

  - 取字符串 “abc” 时，使用的是同一个符号引用：#2
  - 取字符串 “hello” 时，使用的是另一个符号引用：#3

```
 0 ldc #2 <abc>
 2 astore_1
 3 ldc #2 <abc>
 5 astore_2
 6 ldc #3 <hello>
 8 astore_1
 9 getstatic #4 <java/lang/System.out>
12 aload_1
13 aload_2
14 if_acmpne 21 (+7)
17 iconst_1
18 goto 22 (+4)
21 iconst_0
22 invokevirtual #5 <java/io/PrintStream.println>
25 getstatic #4 <java/lang/System.out>
28 aload_1
29 invokevirtual #6 <java/io/PrintStream.println>
32 getstatic #4 <java/lang/System.out>
35 aload_2
36 invokevirtual #6 <java/io/PrintStream.println>
39 return
```

------

**当对现有的字符串进行连接操作时，也需要重新指定内存区域赋值，不能使用原有的value进行赋值**

- 代码

```java
@Test
public void test2() {
    String s1 = "abc";
    String s2 = "abc";
    s2 += "def";
    System.out.println(s2);//abcdef
    System.out.println(s1);//abc
}
```

- 字节码指令：拼接操作通过 StringBuilder 的 append() 方法完成

```
 0 ldc #2 <abc>
 2 astore_1
 3 ldc #2 <abc>
 5 astore_2
 6 new #7 <java/lang/StringBuilder>
 9 dup
10 invokespecial #8 <java/lang/StringBuilder.<init>>
13 aload_2
14 invokevirtual #9 <java/lang/StringBuilder.append>
17 ldc #10 <def>
19 invokevirtual #9 <java/lang/StringBuilder.append>
22 invokevirtual #11 <java/lang/StringBuilder.toString>
25 astore_2
26 getstatic #4 <java/lang/System.out>
29 aload_2
30 invokevirtual #6 <java/io/PrintStream.println>
33 getstatic #4 <java/lang/System.out>
36 aload_1
37 invokevirtual #6 <java/io/PrintStream.println>
40 return
```

------

**当调用string的replace()方法修改指定字符或字符串时，也需要重新指定内存区域赋值，不能使用原有的value进行赋值**

```java
@Test
public void test3() {
    String s1 = "abc";
    String s2 = s1.replace('a', 'm');
    System.out.println(s1);//abc
    System.out.println(s2);//mbc
}
```

------

**来看看 replace() 方法的源码**

- new String(buf, true); 后，返回新的 String 对象

```java
public String replace(char oldChar, char newChar) {
    if (oldChar != newChar) {
        int len = value.length;
        int i = -1;
        char[] val = value; /* avoid getfield opcode */

        while (++i < len) {
            if (val[i] == oldChar) {
                break;
            }
        }
        if (i < len) {
            char buf[] = new char[len];
            for (int j = 0; j < i; j++) {
                buf[j] = val[j];
            }
            while (i < len) {
                char c = val[i];
                buf[i] = (c == oldChar) ? newChar : c;
                i++;
            }
            return new String(buf, true);
        }
    }
    return this;
}
```

------

课后练习：String 的不可变性

- 代码

```java
/** * @author shkstart  shkstart@126.com * @create 2020  23:44 */public class StringExer {    String str = new String("good");    char[] ch = {'t', 'e', 's', 't'};    public void change(String str, char ch[]) {        str = "test ok";        ch[0] = 'b';    }    public static void main(String[] args) {        StringExer ex = new StringExer();        ex.change(ex.str, ex.ch);        System.out.println(ex.str);//good        System.out.println(ex.ch);//best    }}
```

- str 的内容并没有变：“test ok” 位于字符串常量池中的另一个区域（地址），进行赋值操作并没有修改原来 str 指向的引用的内容

```
goodbest
```

### 1.3、String 的底层结构

> **String 底层 Hashtable 结构的说明**

**字符串常量池是不会存储相同内容的字符串的**

1. String的String Pool是一个固定大小的Hashtable，默认值大小长度是1009。如果放进String Pool的String非常多，就会造成Hash冲突严重，从而导致链表会很长，而链表长了后直接会造成的影响就是当调用String.intern()方法时性能会大幅下降。
2. 使用-XX:StringTablesize可设置StringTable的长度
3. 在JDK6中StringTable是固定的，就是1009的长度，所以如果常量池中的字符串过多就会导致效率下降很快，StringTablesize设置没有要求
4. 在JDK7中，StringTable的长度默认值是60013，StringTablesize设置没有要求
5. 在JDK8中，StringTable的长度默认值是60013，StringTable可以设置的最小值为1009

------

**代码示例：设置 StringTable 的长度**

- 代码

```java
/** * -XX:StringTableSize=1009 * * @author shkstart  shkstart@126.com * @create 2020  23:53 */public class StringTest2 {    public static void main(String[] args) {        // 测试StringTableSize参数        System.out.println("我来打个酱油");        try {            Thread.sleep(1000000);        } catch (InterruptedException e) {            e.printStackTrace();        }    }}1234567891011121314151617
```

**通过 -XX:StringTableSize 设置 StringTable 长度**

- JVM 参数

```
-XX:StringTableSize=6666
```

- jinfo 查看变量值

```bash
jpsjinfo -flag StringTableSize 进程id
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/214.png)

**测试不同 StringTable 长度下，程序的性能**

- 代码

```java
/** * -XX:StringTableSize=1009 * * @author shkstart  shkstart@126.com * @create 2020  23:53 */public class StringTest2 {    public static void main(String[] args) {               BufferedReader br = null;        try {            br = new BufferedReader(new FileReader("words.txt"));            long start = System.currentTimeMillis();            String data;            while ((data = br.readLine()) != null) {                //如果字符串常量池中没有对应data的字符串的话，则在常量池中生成                data.intern();            }            long end = System.currentTimeMillis();            System.out.println("花费的时间为：" + (end - start));//1009:143ms  100009:47ms        } catch (IOException e) {            e.printStackTrace();        } finally {            if (br != null) {                try {                    br.close();                } catch (IOException e) {                    e.printStackTrace();                }            }        }    }}
```

- -XX:StringTableSize=1009 ：程序耗时 143ms
- -XX:StringTableSize=100009 ：程序耗时 47ms

## 2、String 的内存分配

### 2.1、String 内存分配演进过程

> **String 类型**

1. 在Java语言中有8种基本数据类型和一种比较特殊的类型String。这些类型为了使它们在运行过程中速度更快、更节省内存，都提供了一种常量池的概念。
2. 常量池就类似一个Java系统级别提供的缓存。8种基本数据类型的常量池都是系统协调的，String类型的常量池比较特殊。它的主要使用方法有两种。
   - 直接使用双引号声明出来的String对象会直接存储在常量池中。比如：`String info="atguigu.com";`
   - 如果不是用双引号声明的String对象，可以使用String提供的intern()方法。

> **String 内存分配的演进过程**

1. Java 6及以前，字符串常量池存放在永久代
2. Java 7中 Oracle的工程师对字符串池的逻辑做了很大的改变，即将字符串常量池的位置调整到Java堆内
   - 所有的字符串都保存在堆（Heap）中，和其他普通对象一样，这样可以让你在进行调优应用时仅需要调整堆大小就可以了。
   - 字符串常量池概念原本使用得比较多，但是这个改动使得我们有足够的理由让我们重新考虑在Java 7中使用String.intern()。
3. Java8元空间，字符串常量在堆

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/215.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/216.png)

### 2.2、为什么要调整 String 位置

> **StringTable 为什么要调整？**

**官方文档**

https://www.oracle.com/java/technologies/javase/jdk7-relnotes.html#jdk7changes

------

1. 为什么要调整位置？
   - 永久代的默认比较小
   - 永久代垃圾回收频率低
   - 堆中空间足够大，字符串可被及时回收
2. 在JDK 7中，interned字符串不再在Java堆的永久代中分配，而是在Java堆的主要部分（称为年轻代和年老代）中分配，与应用程序创建的其他对象一起分配。
3. 此更改将导致驻留在主Java堆中的数据更多，驻留在永久生成中的数据更少，因此可能需要调整堆大小。

**代码示例**

- 代码

```java
/** * jdk6中： * -XX:PermSize=6m -XX:MaxPermSize=6m -Xms6m -Xmx6m * * jdk8中： * -XX:MetaspaceSize=6m -XX:MaxMetaspaceSize=6m -Xms6m -Xmx6m * @author shkstart  shkstart@126.com * @create 2020  0:36 */public class StringTest3 {    public static void main(String[] args) {        //使用Set保持着常量池引用，避免full gc回收常量池行为        Set<String> set = new HashSet<String>();        //在short可以取值的范围内足以让6MB的PermSize或heap产生OOM了。        short i = 0;        while(true){            set.add(String.valueOf(i++).intern());        }    }}
```

- 异常日志说：我真没骗你，字符串真的在堆中（JDK8）

```
"C:\Program Files\Java\jdk1.8.0_144\bin\java" -XX:MetaspaceSize=6m -XX:MaxMetaspaceSize=6m -Xms6m -Xmx6m "-javaagent:C:\Program Files\JetBrains\IntelliJ IDEA 2017.3.1\lib\idea_rt.jar=1799:C:\Program Files\JetBrains\IntelliJ IDEA 2017.3.1\bin" -Dfile.encoding=UTF-8 -classpath "C:\Program Files\Java\jdk1.8.0_144\jre\lib\charsets.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\deploy.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\ext\access-bridge-64.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\ext\cldrdata.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\ext\dnsns.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\ext\jaccess.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\ext\jfxrt.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\ext\localedata.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\ext\nashorn.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\ext\sunec.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\ext\sunjce_provider.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\ext\sunmscapi.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\ext\sunpkcs11.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\ext\zipfs.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\javaws.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\jce.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\jfr.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\jfxswt.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\jsse.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\management-agent.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\plugin.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\resources.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\rt.jar;C:\Users\Heygo\Desktop\JVMDemo\out\production\chapter13;D:\JavaTools\apache-maven-3.3.9\repository\junit\junit\4.12\junit-4.12.jar;D:\JavaTools\apache-maven-3.3.9\repository\org\hamcrest\hamcrest-core\1.3\hamcrest-core-1.3.jar" com.atguigu.java.StringTest3Exception in thread "main" java.lang.OutOfMemoryError: Java heap space	at java.util.HashMap.resize(HashMap.java:703)	at java.util.HashMap.putVal(HashMap.java:662)	at java.util.HashMap.put(HashMap.java:611)	at java.util.HashSet.add(HashSet.java:219)	at com.atguigu.java.StringTest3.main(StringTest3.java:22)Process finished with exit code 1
```

## 3、String 的基本操作

> **核心思想**

Java语言规范里要求完全相同的字符串字面量，应该包含同样的Unicode字符序列（包含同一份码点序列的常量），并且必须是指向同一个String类实例。

> **题目一**

- 代码

```java
/** * @author shkstart  shkstart@126.com * @create 2020  0:49 */public class StringTest4 {    public static void main(String[] args) {        System.out.println();//2330        System.out.println("1");//2331        System.out.println("2");        System.out.println("3");        System.out.println("4");        System.out.println("5");        System.out.println("6");        System.out.println("7");        System.out.println("8");        System.out.println("9");        System.out.println("10");//2340        //如下的字符串"1" 到 "10"不会再次加载        System.out.println("1");//2341        System.out.println("2");//2341        System.out.println("3");        System.out.println("4");        System.out.println("5");        System.out.println("6");        System.out.println("7");        System.out.println("8");        System.out.println("9");        System.out.println("10");//2341    }}
```

- 分析字符串常量池的变化
  - 程序启动时已经加载了 2330 个字符串常量

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/217.png)

- 加载 换行符

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/218.png)

- 加载了字符串常量 “1”~“9”

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/219.png)

- 加载字符串常量 “10”

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/220.png)

- 之后的字符串"1" 到 "10"不会再次加载

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/221.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/222.png)

> **题目二**

- 代码

```java
/** * @author shkstart  shkstart@126.com * @create 2020  0:51 */class Memory {    public static void main(String[] args) {//line 1        int i = 1;//line 2        Object obj = new Object();//line 3        Memory mem = new Memory();//line 4        mem.foo(obj);//line 5    }//line 9    private void foo(Object param) {//line 6        String str = param.toString();//line 7        System.out.println(str);    }//line 8}
```

- 分析运行时内存（foo() 方法是实例方法，其实图中少了一个 this 局部变量）

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/223.png)

## 4、字符串拼接操作

### 4.1、符串拼接操作的结论

> **字符串拼接操作的结论**

1. 常量与常量的拼接结果在常量池，原理是编译期优化
2. 常量池中不会存在相同内容的变量
3. 拼接前后，只要其中有一个是变量，结果就在堆中。变量拼接的原理是StringBuilder
4. 如果拼接的结果调用intern()方法，则主动将常量池中还没有的字符串对象放入池中，并返回此对象地址
   - 如果存在，则返回字符串在常量池中的地址
   - 如果字符串常量池中不存在该字符串，则在常量池中创建一份，并返回此对象的地址

------

**常量与常量的拼接结果在常量池，原理是编译期优化**

- 代码

```java
@Testpublic void test1() {    String s1 = "a" + "b" + "c";//编译期优化：等同于"abc"    String s2 = "abc"; //"abc"一定是放在字符串常量池中，将此地址赋给s2    /*     * 最终.java编译成.class,再执行.class     * String s1 = "abc";     * String s2 = "abc"     */    System.out.println(s1 == s2); //true    System.out.println(s1.equals(s2)); //true}
```

- 从字节码指令看出：编译器做了优化，将 “a” + “b” + “c” 优化成了 “abc”

```
 0 ldc #2 <abc> 2 astore_1 3 ldc #2 <abc> 5 astore_2 6 getstatic #3 <java/lang/System.out> 9 aload_110 aload_211 if_acmpne 18 (+7)14 iconst_115 goto 19 (+4)18 iconst_019 invokevirtual #4 <java/io/PrintStream.println>22 getstatic #3 <java/lang/System.out>25 aload_126 aload_227 invokevirtual #5 <java/lang/String.equals>30 invokevirtual #4 <java/io/PrintStream.println>33 return
```

- IDEA 反编译 class 文件后，来看这个问题

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/224.png)

**拼接前后，只要其中有一个是变量，结果就在堆中**

**调用 intern() 方法，则主动将字符串对象存入字符串常量池中，并将其地址返回**

- 代码

```java
@Testpublic void test2(){    String s1 = "javaEE";    String s2 = "hadoop";    String s3 = "javaEEhadoop";    String s4 = "javaEE" + "hadoop";//编译期优化    //如果拼接符号的前后出现了变量，则相当于在堆空间中new String()，具体的内容为拼接的结果：javaEEhadoop    String s5 = s1 + "hadoop";    String s6 = "javaEE" + s2;    String s7 = s1 + s2;    System.out.println(s3 == s4);//true    System.out.println(s3 == s5);//false    System.out.println(s3 == s6);//false    System.out.println(s3 == s7);//false    System.out.println(s5 == s6);//false    System.out.println(s5 == s7);//false    System.out.println(s6 == s7);//false    //intern():判断字符串常量池中是否存在javaEEhadoop值，如果存在，则返回常量池中javaEEhadoop的地址；    //如果字符串常量池中不存在javaEEhadoop，则在常量池中加载一份javaEEhadoop，并返回此对象的地址。    String s8 = s6.intern();    System.out.println(s3 == s8);//true}
```

- 从字节码角度来看：拼接前后有变量，都会使用到 StringBuilder 类

```
  0 ldc #6 <javaEE>  2 astore_1  3 ldc #7 <hadoop>  5 astore_2  6 ldc #8 <javaEEhadoop>  8 astore_3  9 ldc #8 <javaEEhadoop> 11 astore 4 13 new #9 <java/lang/StringBuilder> 16 dup 17 invokespecial #10 <java/lang/StringBuilder.<init>> 20 aload_1 21 invokevirtual #11 <java/lang/StringBuilder.append> 24 ldc #7 <hadoop> 26 invokevirtual #11 <java/lang/StringBuilder.append> 29 invokevirtual #12 <java/lang/StringBuilder.toString> 32 astore 5 34 new #9 <java/lang/StringBuilder> 37 dup 38 invokespecial #10 <java/lang/StringBuilder.<init>> 41 ldc #6 <javaEE> 43 invokevirtual #11 <java/lang/StringBuilder.append> 46 aload_2 47 invokevirtual #11 <java/lang/StringBuilder.append> 50 invokevirtual #12 <java/lang/StringBuilder.toString> 53 astore 6 55 new #9 <java/lang/StringBuilder> 58 dup 59 invokespecial #10 <java/lang/StringBuilder.<init>> 62 aload_1 63 invokevirtual #11 <java/lang/StringBuilder.append> 66 aload_2 67 invokevirtual #11 <java/lang/StringBuilder.append> 70 invokevirtual #12 <java/lang/StringBuilder.toString> 73 astore 7 75 getstatic #3 <java/lang/System.out> 78 aload_3 79 aload 4 81 if_acmpne 88 (+7) 84 iconst_1 85 goto 89 (+4) 88 iconst_0 89 invokevirtual #4 <java/io/PrintStream.println> 92 getstatic #3 <java/lang/System.out> 95 aload_3 96 aload 5 98 if_acmpne 105 (+7)101 iconst_1102 goto 106 (+4)105 iconst_0106 invokevirtual #4 <java/io/PrintStream.println>109 getstatic #3 <java/lang/System.out>112 aload_3113 aload 6115 if_acmpne 122 (+7)118 iconst_1119 goto 123 (+4)122 iconst_0123 invokevirtual #4 <java/io/PrintStream.println>126 getstatic #3 <java/lang/System.out>129 aload_3130 aload 7132 if_acmpne 139 (+7)135 iconst_1136 goto 140 (+4)139 iconst_0140 invokevirtual #4 <java/io/PrintStream.println>143 getstatic #3 <java/lang/System.out>146 aload 5148 aload 6150 if_acmpne 157 (+7)153 iconst_1154 goto 158 (+4)157 iconst_0158 invokevirtual #4 <java/io/PrintStream.println>161 getstatic #3 <java/lang/System.out>164 aload 5166 aload 7168 if_acmpne 175 (+7)171 iconst_1172 goto 176 (+4)175 iconst_0176 invokevirtual #4 <java/io/PrintStream.println>179 getstatic #3 <java/lang/System.out>182 aload 6184 aload 7186 if_acmpne 193 (+7)189 iconst_1190 goto 194 (+4)193 iconst_0194 invokevirtual #4 <java/io/PrintStream.println>197 aload 6199 invokevirtual #13 <java/lang/String.intern>202 astore 8204 getstatic #3 <java/lang/System.out>207 aload_3208 aload 8210 if_acmpne 217 (+7)213 iconst_1214 goto 218 (+4)217 iconst_0218 invokevirtual #4 <java/io/PrintStream.println>221 return
```

### 4.2、字符串拼接的底层细节

> **字符串拼接的底层细节**

**代码示例 1**

- 代码

```java
@Testpublic void test3(){    String s1 = "a";    String s2 = "b";    String s3 = "ab";    /*    如下的s1 + s2 的执行细节：(变量s是我临时定义的）    ① StringBuilder s = new StringBuilder();    ② s.append("a")    ③ s.append("b")    ④ s.toString()  --> 约等于 new String("ab")    补充：在jdk5.0之后使用的是StringBuilder,在jdk5.0之前使用的是StringBuffer     */    String s4 = s1 + s2;//"ab"    System.out.println(s3 == s4);//false}
```

- 字节码指令

```
 0 ldc #14 <a> 2 astore_1 3 ldc #15 <b> 5 astore_2 6 ldc #16 <ab> 8 astore_3 9 new #9 <java/lang/StringBuilder>12 dup13 invokespecial #10 <java/lang/StringBuilder.<init>>16 aload_117 invokevirtual #11 <java/lang/StringBuilder.append>20 aload_221 invokevirtual #11 <java/lang/StringBuilder.append>24 invokevirtual #12 <java/lang/StringBuilder.toString>27 astore 429 getstatic #3 <java/lang/System.out>32 aload_333 aload 435 if_acmpne 42 (+7)38 iconst_139 goto 43 (+4)42 iconst_043 invokevirtual #4 <java/io/PrintStream.println>46 return
```

- 分析拼接的步骤

  - new StringBuilder()

  ```
   9 new #9 <java/lang/StringBuilder>12 dup13 invokespecial #10 <java/lang/StringBuilder.<init>>
  ```

  - 加载字符串变量，进行 append 操作

  ```
  16 aload_117 invokevirtual #11 <java/lang/StringBuilder.append>20 aload_221 invokevirtual #11 <java/lang/StringBuilder.append>24 invokevirtual #12 <java/lang/StringBuilder.toString>
  ```

  - 调用 StringBuilder 类的 toString() 方法，转换为字符串，并存储在局部变量中

  ```
  24 invokevirtual #12 <java/lang/StringBuilder.toString>27 astore 4
  ```

------

**代码示例 2**

- 代码

```java
/*1. 字符串拼接操作不一定使用的是StringBuilder!   如果拼接符号左右两边都是字符串常量或常量引用，则仍然使用编译期优化，即非StringBuilder的方式。2. 针对于final修饰类、方法、基本数据类型、引用数据类型的量的结构时，能使用上final的时候建议使用上。 */@Testpublic void test4(){    final String s1 = "a";    final String s2 = "b";    String s3 = "ab";    String s4 = s1 + s2;    System.out.println(s3 == s4);//true}
```

- 从字节码角度来看：为变量 s3 赋值时，直接使用 #16 符号引用，即字符串常量 “ab”

```
 0 ldc #14 <a> 2 astore_1 3 ldc #15 <b> 5 astore_2 6 ldc #16 <ab> 8 astore_3 9 ldc #16 <ab>11 astore 413 getstatic #3 <java/lang/System.out>16 aload_317 aload 419 if_acmpne 26 (+7)22 iconst_123 goto 27 (+4)26 iconst_027 invokevirtual #4 <java/io/PrintStream.println>30 return
```

- IDEA 反编译结果

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/225.png)

**课后练习**

- 代码

```java
//练习：@Testpublic void test5(){    String s1 = "javaEEhadoop";    String s2 = "javaEE";    String s3 = s2 + "hadoop";    System.out.println(s1 == s3);//false    final String s4 = "javaEE";//s4:常量    String s5 = s4 + "hadoop";    System.out.println(s1 == s5);//true}
```

- 字节码指令：`ldc #8 <javaEEhadoop>`（带 final 的变量在编译时就已经确定了该变量的值，当做常量来处理）

```
 0 ldc #8 <javaEEhadoop> 2 astore_1 3 ldc #6 <javaEE> 5 astore_2 6 new #9 <java/lang/StringBuilder> 9 dup10 invokespecial #10 <java/lang/StringBuilder.<init>>13 aload_214 invokevirtual #11 <java/lang/StringBuilder.append>17 ldc #7 <hadoop>19 invokevirtual #11 <java/lang/StringBuilder.append>22 invokevirtual #12 <java/lang/StringBuilder.toString>25 astore_326 getstatic #3 <java/lang/System.out>29 aload_130 aload_331 if_acmpne 38 (+7)34 iconst_135 goto 39 (+4)38 iconst_039 invokevirtual #4 <java/io/PrintStream.println>42 ldc #6 <javaEE>44 astore 446 ldc #8 <javaEEhadoop>48 astore 550 getstatic #3 <java/lang/System.out>53 aload_154 aload 556 if_acmpne 63 (+7)59 iconst_160 goto 64 (+4)63 iconst_064 invokevirtual #4 <java/io/PrintStream.println>67 return
```

> **拼接操作与 append 操作的效率对比**

- 代码

```java
/*体会执行效率：通过StringBuilder的append()的方式添加字符串的效率要远高于使用String的字符串拼接方式！分析原因：    ① StringBuilder的append()的方式：        自始至终中只创建过一个StringBuilder的对象        使用String的字符串拼接方式：创建过多个StringBuilder和String的对象    ② 使用String的字符串拼接方式：        内存中由于创建了较多的StringBuilder和String的对象，内存占用更大；        如果进行GC，需要花费额外的时间。 改进的空间：    在实际开发中，如果基本确定要前前后后添加的字符串长度不高于某个限定值highLevel的情况下,建议使用构造器实例化：    StringBuilder s = new StringBuilder(highLevel);//new char[highLevel] */@Testpublic void test6(){    long start = System.currentTimeMillis();    // method1(100000);//4014    method2(100000);//7    long end = System.currentTimeMillis();    System.out.println("花费的时间为：" + (end - start));}public void method1(int highLevel){    String src = "";    for(int i = 0;i < highLevel;i++){        src = src + "a";//每次循环都会创建一个StringBuilder、String    }}public void method2(int highLevel){    //只需要创建一个StringBuilder    StringBuilder src = new StringBuilder();    for (int i = 0; i < highLevel; i++) {        src.append("a");    }}
```

1. 体会执行效率：通过StringBuilder的append()的方式添加字符串的效率要远高于使用String的字符串拼接方式！

2. 分析原因：

   1. StringBuilder的append()的方式：

   - 自始至终中只创建过一个StringBuilder的对象
   - 使用String的字符串拼接方式：创建过多个StringBuilder和String的对象

   1. 使用String的字符串拼接方式：
      - 内存中由于创建了较多的StringBuilder和String的对象，内存占用更大；
      - 如果进行GC，需要花费额外的时间。

3. 改进的空间：

   - 在实际开发中，如果基本确定要前前后后添加的字符串长度不高于某个限定值highLevel的情况下，建议使用构造器实例化：
   - `StringBuilder s = new StringBuilder(highLevel); //new char[highLevel]`

------

**通过字节码分析**

- method1() 方法的字节码指令：
  - 每次 for 循环都会创建一个 StringBuilder 对象
  - 调用 StringBuilder 的 toString() 方法又会创建新的 String 对象

```
 0 ldc #23 2 astore_2 3 iconst_0 4 istore_3 5 iload_3 6 iload_1 7 if_icmpge 36 (+29)10 new #9 <java/lang/StringBuilder>13 dup14 invokespecial #10 <java/lang/StringBuilder.<init>>17 aload_218 invokevirtual #11 <java/lang/StringBuilder.append>21 ldc #14 <a>23 invokevirtual #11 <java/lang/StringBuilder.append>26 invokevirtual #12 <java/lang/StringBuilder.toString>29 astore_230 iinc 3 by 133 goto 5 (-28)36 return
```

- method2() 方法的字节码指令：

```
 0 new #9 <java/lang/StringBuilder> 3 dup 4 invokespecial #10 <java/lang/StringBuilder.<init>> 7 astore_2 8 iconst_0 9 istore_310 iload_311 iload_112 if_icmpge 28 (+16)15 aload_216 ldc #14 <a>18 invokevirtual #11 <java/lang/StringBuilder.append>21 pop22 iinc 3 by 125 goto 10 (-15)28 return
```

------

**关于 StringBuilder 构造器**

- StringBuilder 构造器：可传入一个 int 类型的变量，用于初始化内部的 char[] 数组

```java
public StringBuilder(int capacity) {	super(capacity);}
```

- AbstractStringBuilder（StringBuilder 的父类）的构造器

```java
AbstractStringBuilder(int capacity) {    value = new char[capacity];}
```

## 5、intern() 的使用

### 5.1、intern() 方法的说明

> **intern() 方法的说明**

**先来点逼格，看看官方文档**

When the intern method is invoked, if the pool already contains a string equal to this String object as determined by the equals(Object) method, then the string from the pool is returned. Otherwise, this String object is added to the pool and a reference to this String object is returned.

It follows that for any two strings s and t, s.intern() == t.intern() is true if and only if s.equals(t) is true.

```java
public native String intern();
```

------

**关于 intern() 方法的说明**

1. intern是一个native方法，调用的是底层C的方法

2. 字符串池最初是空的，由String类私有地维护。在调用intern方法时，如果池中已经包含了由equals(object)方法确定的与该字符串对象相等的字符串，则返回池中的字符串。否则，该字符串对象将被添加到池中，并返回对该字符串对象的引用。

3. 如果不是用双引号声明的String对象，可以使用String提供的intern方法：intern方法会从字符串常量池中查询当前字符串是否存在，若不存在就会将当前字符串放入常量池中。比如：

   ```java
   String myInfo = new string("I love atguigu").intern();
   ```

4. 也就是说，如果在任意字符串上调用String.intern方法，那么其返回结果所指向的那个类实例，必须和直接以常量形式出现的字符串实例完全相同。因此，下列表达式的值必定是true

   ```java
   ("a"+"b"+"c").intern()=="abc"
   ```

5. 通俗点讲，Interned String就是确保字符串在内存里只有一份拷贝，这样可以节约内存空间，加快字符串操作任务的执行速度。注意，这个值会被存放在字符串内部池（String Intern Pool）

### 5.2、new String() 的说明

> **new String(“ab”)会创建几个对象？**

- 代码

```java
/** * 题目： * new String("ab")会创建几个对象？看字节码，就知道是两个。 *     一个对象是：new关键字在堆空间创建的 *     另一个对象是：字符串常量池中的对象"ab"。 字节码指令：ldc * * @author shkstart  shkstart@126.com * @create 2020  20:38 */public class StringNewTest {    public static void main(String[] args) {        String str = new String("ab");    }}
```

- 字节码指令

```
 0 new #2 <java/lang/String> 3 dup 4 ldc #3 <ab> 6 invokespecial #4 <java/lang/String.<init>> 9 astore_110 return
```

- `0 new #2 <java/lang/String>`：在堆中创建了一个 String 对象
- `4 ldc #3 <ab>` ：在字符串常量池中放入 “ab”（如果之前字符串常量池中没有 “ab” 的话）

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/226.png)

> **new String(“a”) + new String(“b”) 会创建几个对象？**

- 代码

```java
/** * 思考： * new String("a") + new String("b")呢？ *  对象1：new StringBuilder() *  对象2： new String("a") *  对象3： 常量池中的"a" *  对象4： new String("b") *  对象5： 常量池中的"b" * *  深入剖析： StringBuilder的toString(): *      对象6 ：new String("ab") *      强调一下，toString()的调用，在字符串常量池中，没有生成"ab" * * @author shkstart  shkstart@126.com * @create 2020  20:38 */public class StringNewTest {    public static void main(String[] args) {        String str = new String("a") + new String("b");    }}
```

- 字节码指令

```
 0 new #2 <java/lang/StringBuilder> 3 dup 4 invokespecial #3 <java/lang/StringBuilder.<init>> 7 new #4 <java/lang/String>10 dup11 ldc #5 <a>13 invokespecial #6 <java/lang/String.<init>>16 invokevirtual #7 <java/lang/StringBuilder.append>19 new #4 <java/lang/String>22 dup23 ldc #8 <b>25 invokespecial #6 <java/lang/String.<init>>28 invokevirtual #7 <java/lang/StringBuilder.append>31 invokevirtual #9 <java/lang/StringBuilder.toString>34 astore_135 return
```

- 字节码指令分析：
  1. `0 new #2 <java/lang/StringBuilder>` ：拼接字符串会创建一个 StringBuilder 对象
  2. `7 new #4 <java/lang/String>` ：创建 String 对象，对应于 new String(“a”)
  3. `11 ldc #5 <a>` ：在字符串常量池中放入 “a”（如果之前字符串常量池中没有 “a” 的话）
  4. `19 new #4 <java/lang/String>` ：创建 String 对象，对应于 new String(“b”)
  5. `23 ldc #8 <b>` ：在字符串常量池中放入 “b”（如果之前字符串常量池中没有 “b” 的话）
  6. `31 invokevirtual #9 <java/lang/StringBuilder.toString>` ：调用 StringBuilder 的 toString() 方法，会生成一个 String 对象

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/227.png)

**深入剖析 StringBuilder 的toString() 方法**

- toString() 方法

```java
@Overridepublic String toString() {    // Create a copy, don't share the array    return new String(value, 0, count);}
```

- value 是个 char[] 数组

```java
char[] value;
```

### 5.3、有点难的面试题

> **有点难的面试题**

- 代码

```java
/** * 如何保证变量s指向的是字符串常量池中的数据呢？有两种方式： * 方式一： String s = "shkstart";//字面量定义的方式 * 方式二： 调用intern() *         String s = new String("shkstart").intern(); *         String s = new StringBuilder("shkstart").toString().intern(); * * @author shkstart  shkstart@126.com * @create 2020  18:49 */public class StringIntern {    public static void main(String[] args) {        String s = new String("1");        s.intern();//这方法其实没啥屌用，调用此方法之前，字符串常量池中已经存在"1"        String s2 = "1";        /*            jdk6：false   jdk7/8：false            因为 s 指向堆空间中的 "1" ，s2 指向字符创常量池中的 "1"         */        System.out.println(s == s2);        // 执行完下一行代码以后，字符串常量池中，是否存在"11"呢？答案：不存在！！        String s3 = new String("1") + new String("1");//s3变量记录的地址为：new String("11")        /*            如何理解：jdk6:创建了一个新的对象"11",也就有新的地址。                     jdk7:此时常量中并没有创建"11",而是在常量池中记录了指向堆空间中new String("11")的地址（节省空间）         */        s3.intern(); // 在字符串常量池中生成"11"。        String s4 = "11";//s4变量记录的地址：使用的是上一行代码代码执行时，在常量池中生成的"11"的地址        // jdk6：false  jdk7/8：true        System.out.println(s3 == s4);    }}
```

**内存分析**

- JDK6 ：正常眼光判断即可 
  - new String() 即在堆中
  - str.intern() 则把字符串放入常量池中

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/228.png)

- JDK7/8 ：这就有点不一样了
  - new String() 即在堆中
  - str.intern() 则把字符串放入常量池中，出于节省空间的目的，如果 str 不存在于字符串常量池中，则将 str 在堆中的引用存储在字符串常量池中，没错，字符串常量池中存的是 str 在堆中的引用，所以 s3 == s4 为 true

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/229.png)

> **面试题的拓展**

```java
/** * StringIntern.java中练习的拓展： * * @author shkstart  shkstart@126.com * @create 2020  22:10 */public class StringIntern1 {    public static void main(String[] args) {        //执行完下一行代码以后，字符串常量池中，是否存在"11"呢？答案：不存在！！        String s3 = new String("1") + new String("1");//new String("11")        //在字符串常量池中生成对象"11"        String s4 = "11";        String s5 = s3.intern();        // s3 是堆中的 "ab" ，s4 是字符串常量池中的 "ab"        System.out.println(s3 == s4);//false        // s5 是从字符串常量池中取回来的引用，当然和 s4 相等        System.out.println(s5 == s4);//true    }}
```

### 5.4、intern() 方法的总结

> **关于 intern() 的总结**

1. JDK1.6中，将这个字符串对象尝试放入串池。
   1. 如果串池中有，则并不会放入。返回已有的串池中的对象的地址
   2. 如果没有，会**把此对象复制一份**，放入串池，并返回串池中的对象地址
2. JDK1.7起，将这个字符串对象尝试放入串池。
   - 如果串池中有，则并不会放入。返回已有的串池中的对象的地址
   - 如果没有，则会**把对象的引用地址复制一份**，放入串池，并返回串池中的引用地址

### 5.5、intern() 方法的练习

> **intern() 方法的课后练习**

**练习 1**

- 代码

```java
/** * @author shkstart  shkstart@126.com * @create 2020  20:17 */public class StringExer1 {    public static void main(String[] args) {        //在下一行代码执行完以后，字符串常量池中并没有"ab"        String s = new String("a") + new String("b");//new String("ab")        /*            jdk6中：在串池中创建一个字符串"ab"            jdk8中：串池中没有创建字符串"ab",而是创建一个引用，指向new String("ab")，将此引用返回         */        String s2 = s.intern();        System.out.println(s2 == "ab");//jdk6:true  jdk8:true        System.out.println(s == "ab");//jdk6:false  jdk8:true    }}
```

- JDK 6 中：在串池中创建一个字符串"ab"

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/230.png)

- JDK 7/8 中：串池中没有创建字符串"ab"，而是创建一个引用，指向new String(“ab”)，将此引用返回

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/231.png)

**练习 2**

- 代码

```java
/** * @author shkstart  shkstart@126.com * @create 2020  20:17 */public class StringExer1 {    public static void main(String[] args) {        // 在这儿加一句        String x = "ab";                //在下一行代码执行完以后，字符串常量池中并没有"ab"        String s = new String("a") + new String("b");//new String("ab")        /*            jdk6中：在串池中创建一个字符串"ab"            jdk8中：串池中没有创建字符串"ab",而是创建一个引用，指向new String("ab")，将此引用返回         */        String s2 = s.intern();        System.out.println(s2 == "ab");//jdk6:true  jdk8:true        System.out.println(s == "ab");//jdk6:false  jdk8:false    }}
```

- 内存分析

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/232.png)

**练习 3**

- 代码 1

```java
/** * * @author shkstart  shkstart@126.com * @create 2020  20:26 */public class StringExer2 {    public static void main(String[] args) {        String s1 = new String("ab");//执行完以后，会在字符串常量池中会生成"ab"        s1.intern();        String s2 = "ab";        System.out.println(s1 == s2); // false    }}
```

- 代码 2

```java
/** * * @author shkstart  shkstart@126.com * @create 2020  20:26 */public class StringExer2 {    // 对象内存地址可以使用System.identityHashCode(object)方法获取    public static void main(String[] args) {        String s1 = new String("a") + new String("b");//执行完以后，不会在字符串常量池中会生成"ab"        System.out.println(System.identityHashCode(s1));        s1.intern();        System.out.println(System.identityHashCode(s1));        String s2 = "ab";        System.out.println(System.identityHashCode(s2));        System.out.println(s1 == s2); // true    }}/* 程序运行结果    21685669    21685669    21685669    true*/
```

### 5.6、intern() 方法效率测试

> **intern() 的效率测试**

- 代码

```java
/** * 使用intern()测试执行效率：空间使用上 * 结论：对于程序中大量存在存在的字符串，尤其其中存在很多重复字符串时，使用intern()可以节省内存空间。 * * @author shkstart  shkstart@126.com * @create 2020  21:17 */public class StringIntern2 {    static final int MAX_COUNT = 1000 * 10000;    static final String[] arr = new String[MAX_COUNT];    public static void main(String[] args) {        Integer[] data = new Integer[]{1,2,3,4,5,6,7,8,9,10};        long start = System.currentTimeMillis();        for (int i = 0; i < MAX_COUNT; i++) {            // arr[i] = new String(String.valueOf(data[i % data.length]));            arr[i] = new String(String.valueOf(data[i % data.length])).intern();        }        long end = System.currentTimeMillis();        System.out.println("花费的时间为：" + (end - start));        try {            Thread.sleep(1000000);        } catch (InterruptedException e) {            e.printStackTrace();        }        System.gc();    }}
```

- 直接 new String ：由于每个 String 对象都是 new 出来的，所以程序需要维护大量存放在堆空间中的 String 实例，程序内存占用也会变高

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/233.png)

- 使用 intern() 方法：由于数组中字符串的引用都指向字符串常量池中的字符串，所以程序需要维护的 String 对象更少，内存占用也更低

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/jvm_first/234.png)

**结论**：

1. 对于程序中大量使用存在的字符串时，尤其存在很多已经重复的字符串时，使用intern()方法能够节省内存空间。
2. 大的网站平台，需要内存中存储大量的字符串。比如社交网站，很多人都存储：北京市、海淀区等信息。这时候如果字符串都调用intern() 方法，就会很明显降低内存的大小。

## 6、StringTable 的垃圾回收

- 代码

```java
/** * String的垃圾回收: * -Xms15m -Xmx15m -XX:+PrintStringTableStatistics -XX:+PrintGCDetails * * @author shkstart  shkstart@126.com * @create 2020  21:27 */public class StringGCTest {    public static void main(String[] args) {        for (int j = 0; j < 100000; j++) {            String.valueOf(j).intern();        }    }}
```

- JVM 参数

```
-Xms15m -Xmx15m -XX:+PrintStringTableStatistics -XX:+PrintGCDetails
```

- 程序日志：
  - 在 PSYoungGen 区发生了垃圾回收
  - Number of entries 和 Number of literals 明显没有 100000
  - 以上两点均说明 StringTable 区发生了垃圾回收

```
"C:\Program Files\Java\jdk1.8.0_144\bin\java" -Xms15m -Xmx15m -XX:+PrintStringTableStatistics -XX:+PrintGCDetails "-javaagent:C:\Program Files\JetBrains\IntelliJ IDEA 2017.3.1\lib\idea_rt.jar=11487:C:\Program Files\JetBrains\IntelliJ IDEA 2017.3.1\bin" -Dfile.encoding=UTF-8 -classpath "C:\Program Files\Java\jdk1.8.0_144\jre\lib\charsets.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\deploy.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\ext\access-bridge-64.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\ext\cldrdata.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\ext\dnsns.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\ext\jaccess.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\ext\jfxrt.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\ext\localedata.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\ext\nashorn.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\ext\sunec.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\ext\sunjce_provider.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\ext\sunmscapi.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\ext\sunpkcs11.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\ext\zipfs.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\javaws.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\jce.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\jfr.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\jfxswt.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\jsse.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\management-agent.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\plugin.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\resources.jar;C:\Program Files\Java\jdk1.8.0_144\jre\lib\rt.jar;C:\Users\Heygo\Desktop\JVMDemo\out\production\chapter13;D:\JavaTools\apache-maven-3.3.9\repository\junit\junit\4.12\junit-4.12.jar;D:\JavaTools\apache-maven-3.3.9\repository\org\hamcrest\hamcrest-core\1.3\hamcrest-core-1.3.jar" com.atguigu.java3.StringGCTest[GC (Allocation Failure) [PSYoungGen: 4096K->488K(4608K)] 4096K->716K(15872K), 0.0024275 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] Heap PSYoungGen      total 4608K, used 3883K [0x00000000ffb00000, 0x0000000100000000, 0x0000000100000000)  eden space 4096K, 82% used [0x00000000ffb00000,0x00000000ffe50fb0,0x00000000fff00000)  from space 512K, 95% used [0x00000000fff00000,0x00000000fff7a020,0x00000000fff80000)  to   space 512K, 0% used [0x00000000fff80000,0x00000000fff80000,0x0000000100000000) ParOldGen       total 11264K, used 228K [0x00000000ff000000, 0x00000000ffb00000, 0x00000000ffb00000)  object space 11264K, 2% used [0x00000000ff000000,0x00000000ff039010,0x00000000ffb00000) Metaspace       used 3472K, capacity 4496K, committed 4864K, reserved 1056768K  class space    used 381K, capacity 388K, committed 512K, reserved 1048576KSymbolTable statistics:Number of buckets       :     20011 =    160088 bytes, avg   8.000Number of entries       :     14158 =    339792 bytes, avg  24.000Number of literals      :     14158 =    603200 bytes, avg  42.605Total footprint         :           =   1103080 bytesAverage bucket size     :     0.708Variance of bucket size :     0.711Std. dev. of bucket size:     0.843Maximum bucket size     :         6StringTable statistics:Number of buckets       :     60013 =    480104 bytes, avg   8.000Number of entries       :     62943 =   1510632 bytes, avg  24.000Number of literals      :     62943 =   3584040 bytes, avg  56.941Total footprint         :           =   5574776 bytesAverage bucket size     :     1.049Variance of bucket size :     0.824Std. dev. of bucket size:     0.908Maximum bucket size     :         5Process finished with exit code 0
```

------

**String.valueOf() 方法源码**

- String 类的 valueOf() 方法

```java
public static String valueOf(int i) {    return Integer.toString(i);}
```

- Integer.toString() 方法中执行了 new String() ，即在堆中创建了一个 String 对象

```java
public static String toString(int i) {    if (i == Integer.MIN_VALUE)        return "-2147483648";    int size = (i < 0) ? stringSize(-i) + 1 : stringSize(i);    char[] buf = new char[size];    getChars(i, size, buf);    return new String(buf, true);}
```

## 7、G1 中的 String 去重操作

> **官方文档**

http://openjdk.java.net/jeps/192

> **String 去重操作的背景**

1. 背景：对许多Java应用（有大的也有小的）做的测试得出以下结果：
   - 堆存活数据集合里面String对象占了25%
   - 堆存活数据集合里面重复的String对象有13.5%
   - String对象的平均长度是45
2. 许多大规模的Java应用的瓶颈在于内存，测试表明，在这些类型的应用里面，Java堆中存活的数据集合差不多25%是String对象。更进一步，这里面差不多一半String对象是重复的，重复的意思是说：
3. str1.equals(str2)= true。堆上存在重复的String对象必然是一种内存的浪费。这个项目将在G1垃圾收集器中实现自动持续对重复的String对象进行去重，这样就能避免浪费内存。

> **String 去重的的具体实现**

1. 当垃圾收集器工作的时候，会访问堆上存活的对象。对每一个访问的对象都会检查是否是候选的要去重的String对象。
2. 如果是，把这个对象的一个引用插入到队列中等待后续的处理。一个去重的线程在后台运行，处理这个队列。处理队列的一个元素意味着从队列删除这个元素，然后尝试去重它引用的String对象。
3. 使用一个Hashtable来记录所有的被String对象使用的不重复的char数组。当去重的时候，会查这个Hashtable，来看堆上是否已经存在一个一模一样的char数组。
4. 如果存在，String对象会被调整引用那个数组，释放对原来的数组的引用，最终会被垃圾收集器回收掉。
5. 如果查找失败，char数组会被插入到Hashtable，这样以后的时候就可以共享这个数组了。

> **命令行选项**

1. UseStringDeduplication(bool) ：开启String去重，默认是不开启的，需要手动开启。
2. PrintStringDeduplicationStatistics(bool) ：打印详细的去重统计信息
3. stringDeduplicationAgeThreshold(uintx) ：达到这个年龄的String对象被认为是去重的候选对象

