---
title: Vue之组件化（一）
date: 2021-post-30 10:44:24.195
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/1_7OCwu--TWqVluPMsZdzWKw-34ce1bcaed3b4c59a2183cf00af73987_1622733997113.png'
theme: 'light'
tags: 
- Vue
---

# 一、组件化思想

## 1.1、组件化思想

遇到一大堆复杂的问题时，直接上手解决的话是非常困难的。面对这种情况，我们可以将这个大问题细分成许多小问题，逐一的去研究解决这些小问题，累积下来，这个大问题也会被我们解决。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/133.png)

上述的思想就类似组件化的思想。
当我们要完成一个页面的功能时，直接创建一个Vue.js就开始编写代码的话，不仅逻辑复杂，而且后期也不好维护。
所以，要将一个页面划分成几个个小功能，完成这些小功能后，再将这个功能集成到页面中，当然，这些划分出的小功能也可以根据逻辑的复杂度再进行细分。

1.根据逻辑要求将页面划分成三个组件

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/134.png)

2.计划独立完成各个组件的功能后，再集成到页面中

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/135.png)

3.在完成每个组件时，可以根据逻辑再次细分组件，最后将每个完成的组件集成至页面

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/136.png)

## 1.2、简述组件化的使用

1.生成组件构造器对象
2.注册组件
3.使用组件

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/137.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/138.png)

# 二、组件化的基本使用过程

#### 组件化的引入

如果想在页面中展示4次改代码。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/139.png)

通常的做法是，将该代码复制4次，最后在页面展示4个同样的效果。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/140.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/141.png)

但是，当我想要重复利用的模块，是由相对复杂的代码构成的，再次用复制的方法就会让代码结构变得复杂，不利于维护。

这时，就要引入组件化的使用。

组件化的使用过程：

## 2.1.生成组件构造器对象

用Vue的extend的方法生成组件构造器

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/142.png)

extends()中的参数也是一个对象，对象中有一个template属性，template的属性值存储的就是我们想利用的组件模板的代码。
template的属性值是字符串类型，用ES6语法``(Tab键上的一个键)显示，可以对字符串换行的情况不做处理。ES5中字符串换行时，要用+进行拼接

```html
            const myC = Vue.extend({
                template:`  
                <div>
                    <h2>hello</h2>
                    <p>你好</p>
                </div>`
            })
```

## 2.2.注册组件

利用Vue的component方法注册组件。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/143.png)

component方法中要传入两个参数，
1.使用组件模板的标签
2.组件构造器对象

```html
            Vue.component('my-component',myC );
```

## 3.3.使用组件

用注册组件时的标签使用组件模板

```html
        <div id="app">
            <my-component></my-component>
            <my-component></my-component>
            <my-component></my-component>
            <my-component></my-component>
        </div>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/144.png)

# 三、初用组件化遇坑总结

## 3.1.template模板中的HTML代码必须要有一个根标签

```html
            const myC = Vue.extend({
                template:`  
                    <h2>hello</h2>
                    <p>你好</p>`
            })
```

h2和p标签没有一个根标签
只展示了h2标签

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/145.png)

报错信息：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/146.png)

## 3.2.创建Vue实例的代码要放在组件化代码的后面

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/147.png)

报错信息：警告没有提供正确注册组件化标签名

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/148.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/149.png)

正常运行：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/150.png)

## 3.3.注册组件的标签命名问题

### 1.**注册组件的标签用小驼峰命名时，在html中使用组件时，要用-隔开**

标签名用小驼峰命名法myCom

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/151.png)

当html中使用组件时没用-隔开

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/152.png)

mycom和myCom没有区别，html不区分大小写

报错：提示要提供正确的标签名

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/153.png)

用-隔开后

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/154.png)

正常显示

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/155.png)

总结注册组件的标签名命名问题
1.注册组件时用小驼峰命名法时，html中使用组件时要用-隔开
2.注册组件时，组件的标签名可以都用小写，不要出现大写字母

# 四、全局组件和局部组件

## 4.1、全局组件

全局组件可以在多个Vue实例对象中使用

```html
        <div id="app1">
            <!--3.使用全局组件-->
            <my-c></my-c>
        </div>
        <div id="app2">
            <!--3.使用全局组件-->         
            <my-c></my-c>
        </div>
        <script src="js/vue.js" type="text/javascript" charset="utf-8"></script>
        <script type="text/javascript">
            /*1.创建组件构造器*/
            const myC = Vue.extend({
                template: `
                <div>
                    <h2>全局组件</h2>
                </div>`
            })
            /*2.注册组件*/
            Vue.component('my-c',myC);
            
            /*Vue的实例化对象1*/
            const app1 = new Vue({
                el: '#app1'
            })
            
            /*Vue的实例化对象2*/
            const app2 = new Vue({
                el: '#app2'
            })
        </script>
```

在两个Vue实例对象app1和app2中都能使用全局组件

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/156.png)

## 4.2.局部组件（常用）

局部组件创建在Vue实例中，该组件也只能在该实例中使用
局部组件的创建方法：
1.生成组件构造器
2.在Vue实例中注册组件
注册组件在Vue的components属性中操作

```html
            /*1.生成组件构造器*/
            const myCom = Vue.extend({
                template: `
                <div>
                    <h2>局部组件</h2>
                </div>`
            })
            /*Vue的实例化对象1*/
            /*2.在Vue实例中注册组件*/
            const app1 = new Vue({
                el: '#app1',
                components: {
                    myc:myCom
                }
            })
```

myc就是注册组件的标签名
myCom就是组件构造器

在app1和app2中都使用全局组件和局部组件

```html
        <div id="app1">
            <!--3.使用全局组件-->
            <my-c></my-c>
            <!--使用局部组件-->
            <myc></myc>
        </div>
        <div id="app2">
            <!--3.使用全局组件-->
            <my-c></my-c>
            <!--使用局部组件-->
            <myc></myc>
        </div>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/157.png)

全局组件在两个Vue实例中都可以显示
局部组件只能在注册他的Vue实例app1中显示
在app2中使用局部组件,<myc></myc>标签不会被解析