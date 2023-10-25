---
title: Vue之初体验
date: 2021-posts-30 10:42:29.413
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/1_7OCwu--TWqVluPMsZdzWKw-34ce1bcaed3b4c59a2183cf00af73987_1622733997113.png'
theme: 'light'
tags: 
- Vue
---

# Vue初体验

## 前言：什么是Vue.js

摘自维基百科：

> Vue.js是一款流行的[JavaScript](https://zh.wikipedia.org/wiki/JavaScript)前端框架，旨在更好地组织与简化Web开发。Vue所关注的核心是[MVC模式](https://zh.wikipedia.org/wiki/MVC)中的视图层，同时，它也能方便地获取数据更新，并通过组件内部特定的方法实现视图与模型的交互。

说白了就是一个前端框架！

Vue中文官网：

https://cn.vuejs.org

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/6.png)

## 什么是Vue.js的MVVM设计模式？

MVVM如果没听说过，但是我们知道MVC编程模式！
MVC：M是指业务模型（Model），V是指用户界面（View），C则是控制器（Controller）。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/7.png)

MVVM：MVVM是MVC的改进版 M是指业务模型（Model），V是指用户界面（View），VM则是ViewModel。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/8.jpg)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/9.png)

优点：MVVM模式和MVC模式一样，主要目的是分离视图（View）和模型（Model），有几大优点

1. 低耦合。视图（View）可以独立于Model变化和修改，一个ViewModel可以绑定到不同的"View"上，当View变化的时候Model可以不变，当Model变化的时候View也可以不变。
2. 可重用性。你可以把一些视图逻辑放在一个ViewModel里面，让很多view重用这段视图逻辑。
3. 独立开发。开发人员可以专注于业务逻辑和数据的开发（ViewModel），设计人员可以专注于页面设计，使用Expression Blend可以很容易设计界面并生成xaml代码。
4. 可测试。界面素来是比较难于测试的，测试可以针对ViewModel来写。

弄清了MVVM设计模式，下面正是开始学习Vue.js

## 安装Vue.js

有三种方式安装Vue.js：

1.标签引入(初学者推荐)
Vue.js的安装方式非常简单 跟jquery一样，你可以下载后（https://cn.vuejs.org/js/vue.js）引入；

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/10.png)

下载后在项目包的同级目录建一个js包，将下载后的vue.js文件拷贝进去，接着就可以在script标签中引入了！

```html
<script src="../js/vue.js"></script>
```

或者直接从cdn引入

```html
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
```

2.npm安装

```shell
# 最新稳定版
$ npm install vue
```

3.命令行工具安装（CLI）

这里就不多介绍了，之后进阶用，现在我们用方式一就好了~

## 案例1、展示简单数据

vue就类似一个构造函数，用构造函数构造出一个实例，并往构造函数中传入参数，而vue中的参数是一个对象。



定义一个变量接收Vue构造函数构造出的实例，并且传入的参数是一个对象

```javascript
const app = new Vue({});
```



这个对象参数中的一些属性也有特定的作用。

```javascript

        const app = new Vue({
            el : '#app',
            data : {
                message : 'hello vue'
            }
        })
```

- el ：该属性决定了Vue对象挂载到哪个元素上，也就是用来告诉Vue，他需要管理的对象是谁，而el的属性值就是需要被管理的元素的id
- data：data的属性值是一个对象，被管理的元素可以根据需要，获取data中的数据

将Vue对象中的数据传到h1中

```javascript
      <div id="app">
        <h1>{{message}}</h1>
    </div>
```

浏览器运行

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/1.png)

我们可以在控制台动态修改h1中的内容（F12打开控制台，选择Console）

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/2.png)

在这可以明显的感受到，通过Vue管理的元素，他的样式和数据是相分离的，动态修改数据的过程完全没有影响页面的结构。

完整代码演示：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>

<div id="app">
    <h1>{{message}}</h1>
    <h1>{{name}}</h1>
</div>

<!--这里没有绑定，所以不会渲染,直接显示{{message}}-->
<div>{{message}}</div>


<script src="../js/vue.js"></script>
<script>
    // let(变量)/const(常量)
    // 编程范式: 声明式编程
    const app = new Vue({
        el: '#app', // 用于挂载要管理的元素
        data: { // 定义数据
            message: 'hello vue',
            name: 'Tomcatist'
        }
    })

    // 元素js的做法(编程范式: 命令式编程)
    // 1.创建div元素,设置id属性

    // 2.定义一个变量叫message

    // 3.将message变量放在前面的div元素中显示

    // 4.修改message的数据: 今天天气不错!

    // 5.将修改后的数据再次替换到div元素
</script>

</body>
</html>
```



## 案例2 展示列表

展示ul列表
通过上一个案例，可以在每个li中，将需要的数据传入特定的li中，但是这种写法过于冗余。

```html
    <div id="app">
       <ul>
           <!-- <li v-for = "items in movies">{{items}}</li> -->
           <li>{{movies[0]}}</li>
           <li>{{movies[1]}}</li>
           <li>{{movies[2]}}</li>
           <li>{{movies[3]}}</li>
       </ul>
    </div>
        const app = new Vue({
            el : '#app',
            data : {
                message : 'hello vue',
                movies : ['one','two','three','four']
            }
        })
```

可以借助v-for的指令，items in movies的意思是，用for循环遍历movies数组，将获取的数据都赋给变量items，最后在li中展示items，这样就可以自动的生成li结构，并往li中添加items数据。

```html
        <div id="app">
       <ul>
           <li v-for = "items in movies">{{items}}</li>
       </ul>
    </div>
        const app = new Vue({
            el : '#app',
            data : {
                message : 'hello vue',
                movies : ['one','two','three','four']
            }
        })
```

在控制台操作movies数组，数组也会动态的改变

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/3.png)

完整代码演示：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>

<div id="app">
    <ul>
        <li v-for="item in movies">{{item}}</li>
    </ul>
</div>

<script src="../js/vue.js"></script>
<script>
    const app = new Vue({
        el: '#app',
        data: {
            message: '你好啊',
            movies: ['星际穿越', '大话西游', '少年派', '盗梦空间']
        }
    })
</script>

</body>
</html>
```



## 案例3 计数器

样式展示：点击+按钮数字增加，点击-按钮数字减少

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/4.png)

v-on指令可以用来添加事件，
v-on:click，就是添加click事件，
在指令中让counter++和counter--，
vue实例会动态监测到counter的变化，在点击后将改变的counter值添加到h1中

```html
    <div id="app">
        <h1>当前的数字：{{counter}}</h1>
        <button v-on:click="counter++">+</button>
        <button v-on:click="counter--">-</button>
    </div>
```

```html
        const app = new Vue({
            el : '#app',
            data : {
                counter : 0
            }
        })
```

但是，当我们使用的点击事件是比较复杂的，比添加console.log的语句，继续在行间编写会导致页面混乱，这时就可以利用函数来简化代码。
methods也是Vue实例中的属性，用来存储函数。

```html
    <div id="app">
        <h1>当前的数字：{{counter}}</h1>
        <button v-on:click="add">+</button>
        <button v-on:click="sub">-</button>
    </div>
```

```html
        const app = new Vue({
            el : '#app',
            data : {
                counter : 0
            },
            methods : {
                add : function() {
                    this.counter++; //this指针指向当前Vue实例app,用this找到app下的counter
                    console.log("计数器加1");
                },
                sub : function(){
                    this.counter--;
                    console.log("计数器减1");
                }
            }
        })5
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/5.png)



完整代码演示：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>

<div id="app">
    <h2>当前计数: {{counter}}</h2>
    <!--<button v-on:click="counter++">+</button>-->
    <!--<button v-on:click="counter--;">-</button>-->
    <button v-on:click="add">+</button>
    <button v-on:click="sub">-</button>
    <!--下面是语法糖写法-->
    <!--<button @click="sub">-</button>-->
</div>

<script src="../js/vue.js"></script>
<script>
    // 语法糖: 简写
    // proxy
    const obj = {
        counter: 0,
        message: 'abc'
    }

    new Vue()

    const app = new Vue({
        el: '#app',
        data: obj,
        methods: {
            add: function () {
                console.log('add被执行');
                this.counter++
            },
            sub: function () {
                console.log('sub被执行');
                this.counter--
            }
        },
        beforeCreate: function () {

        },
        created: function () {
            console.log('created');
        },
        mounted: function () {
            console.log('mounted');
        }
    })

    // 1.拿button元素

    // 2.添加监听事件
</script>

</body>
</html>
```