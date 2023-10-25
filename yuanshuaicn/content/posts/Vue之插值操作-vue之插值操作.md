---
title: Vue之插值操作
date: 2021-posts-30 10:42:50.035
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/1_7OCwu--TWqVluPMsZdzWKw-34ce1bcaed3b4c59a2183cf00af73987_1622733997113.png'
theme: 'light'
tags: 
- Vue
---

# Vue插值操作

## 1.Mustach语法

Mustach语法就是双大括号，所以也有人直接叫双括号语法,我们可以利用其进行基本的拼接和运算

```javascript
    <div id="app">
        <h1>{{message}}</h1>
        <h1>{{message}},bye!</h1>
        <h1>{{message + ' ' + message}}</h1>
        <h1>{{message}} + ' ' + {{message}}</h1>
    </div>
    <script src="./js/vue.js"></script>
    <script>
        var app = new Vue({
            el : '#app',
            data : {
                message : 'good night'
            }
        })
    </script>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/11.png)

相关完整代码展示：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
</head>
<body>

<div id="app">
  <h2>{{message}}</h2>
  <h2>{{message}}, Tomcatist!</h2>

  <!--mustache语法中,不仅仅可以直接写变量,也可以写简单的表达式-->
  <h2>{{firstName + lastName}}</h2>
  <h2>{{firstName + ' ' + lastName}}</h2>
  <h2>{{firstName}} {{lastName}}</h2>
  <h2>{{counter * 2}}</h2>
</div>

<script src="../js/vue.js"></script>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      message: '你好啊',
      firstName: 'kobe',
      lastName: 'bryant',
      counter: 100
    },
  })
</script>

</body>
</html>
```

运行结果：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/12.png)

## 2.v-once

当我们希望页面中的数据只保持第一次渲染出的效果，不需要支持响应式的操作时，就可以使用v-once的属性

```javascript
    <div id="app">
        <h1>{{message}}</h1>
        <h1 v-once>{{message}}</h1>
    </div>
```

在控制台修改message的值时，添加了v-once的h1中的值没有跟着变化，仍然展示着最初的数据

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/13.png)

相关完整代码展示：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
</head>
<body>

<div id="app">
  <h2>{{message}}</h2>
  <h2 v-once>{{message}}</h2>
</div>

<script src="../js/vue.js"></script>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      message: '你好啊'
    }
  })
</script>

</body>
</html>
```

## 3.v-html

当后台传给我们的数据是个标签时，直接用Mustach展示的是未经解析完整的标签

```javascript
    <div id="app">
        <h1>{{url}}</h1>
    </div>
    <script src="./js/vue.js"></script>
    <script>
        var app = new Vue({
            el : '#app',
            data : {
                message : 'good night',
                url : <a href="http://www.baidu.com">百度一下</a>
            }
        })
    </script>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/14.png)

那我们希望展示按照HTML格式进行解析，并显示出相应的内容，就需要使用v-html指令

```javascript
    <div id="app">
        <h1 v-html=url></h1>
    </div>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/15.png)

相关完整代码展示：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
</head>
<body>

<div id="app">
  <h2>{{url}}</h2>
  <h2 v-html="url"></h2>
</div>

<script src="../js/vue.js"></script>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      message: '你好啊',
      url: '<a href="http://www.baidu.com">百度一下</a>'
    }
  })
</script>

</body>
</html>
```

## 4.v-text

v-text能展示出文本信息，缺点是和Mustach相比不够灵活

```javascript
    <div id="app">
        <h1 v-text=message></h1>
    </div>
    <script src="./js/vue.js"></script>
    <script>
        var app = new Vue({
            el : '#app',
            data : {
                message : 'good night',
            }
        })
    </script>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/16.png)

相关完整代码展示：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
</head>
<body>

<div id="app">
  <h2>{{message}}, Tomcatist!</h2>
  <h2 v-text="message">,Tomcatist!</h2> 
</div>

<script src="../js/vue.js"></script>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      message: '你好啊'
    }
  })
</script>

</body>
</html>
```

运行结果：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/17.png)

按之前经验来说，这里第二行也应该显示 **你好啊, Tomcatist!** ，但是只显示了v-text中的文本，而把标签里的文本覆盖了，所以我们想做一些表达式操作就很难，所以说**和Mustach相比不够灵活**！

## 5.v-pre

v-pre能将数据不经过Vue的解析原封不动的展示出来

```javascript
    <div id="app">
        <h1>{{message}}</h1>
        <h1 v-pre>{{message}}</h1>
    </div>
    <script src="./js/vue.js"></script>
    <script>
        var app = new Vue({
            el : '#app',
            data : {
                message : 'good night',
            }
        })
    </script>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/18.png)

相关完整代码展示：

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
</head>
<body>

<div id="app">
  <h2>{{message}}</h2>
  <h2 v-pre>{{message}}</h2>
</div>

<script src="../js/vue.js"></script>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      message: '你好啊'
    }
  })
</script>

</body>
</html>
```

## 6.v-cloak

在Vue解析前，div会有一个v-cloak属性
在Vue解析后，div没有一个v-cloak属性

cloak是斗篷的意思，当遇到js文件没有解析完时，包含有Vue语法的HTML元素就像添加了v-pre的属性一样原封不动的展示数据，在js文件解析完成时，带有Vue语法的HTML代码会通过解析后生成相应的数据被展示出来，这个过程会有一个闪现的效果，用户体验感不好，所以给HTML元素添加v-cloak指令，并给带有v-cloak属性的HTML元素的样式设为dispaly:none，在js文件解析完后，v-cloak属行就会消失，想斗篷一样把自己罩住，display的样式也会失效，数据会通过Vue的解析展示出来。
可以用定时器的方式展示v-cloak的效果

```javascript
    <style>
        [v-cloak] {
            display: none;
        }
    </style>

    <div id="app">
        <h1 v-cloak>{{message}}</h1>
    </div>
    <script src="./js/vue.js"></script>
    <script>
        setTimeout(function(){
            var app = new Vue({
            el : '#app',
            data : {
                message : 'good night',
            }
        })
        },1000)
    </script>
```

我们这里设置了页面会在1秒后展示出good night，也因为v-cloak属行，在js没有解析完时，将数据用css的方法隐藏，没有显示出{{message}}，在1秒后js解析完毕，v-cloak的属性会自动消失，数据通过解析展示出good night。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/19.png)

相关完整代码展示：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
  <style>
    [v-cloak] {
      display: none;
    }
  </style>
</head>
<body>

<div id="app" v-cloak>
  <h2>{{message}}</h2>
</div>

<script src="../js/vue.js"></script>
<script>
  // 在vue解析之前, div中有一个属性v-cloak
  // 在vue解析之后, div中没有一个属性v-cloak
  setTimeout(function () {
    const app = new Vue({
      el: '#app',
      data: {
        message: '你好啊'
      }
    })
  }, 1000)
</script>

</body>
</html>
```