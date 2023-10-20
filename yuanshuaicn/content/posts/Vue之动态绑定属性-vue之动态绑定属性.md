---
title: Vue之动态绑定属性
date: 2021-11-30 10:42:54.614
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/1_7OCwu--TWqVluPMsZdzWKw-34ce1bcaed3b4c59a2183cf00af73987_1622733997113.png'
theme: 'light'
tags: 
- Vue
---

# Vue之动态绑定

## 一、v-bind基础使用

v-bind能给元素动态绑定属性
img中的src在大多数情况下都是动态传递过来的数据，并非是写死的，这时就需要用v-bind的语法，做src属性的动态绑定。
**在需要动态绑定的属性前加上v-bind:，告诉Vue这个属性我需要动态绑定。**

```html
    <div id="app">
        <img v-bind:src="imageUrl" alt="">
    </div>
    <script src="./js/vue.js"></script>
    <script>
        var app = new Vue({
            el: '#app',
            data : {
                imageUrl : 'https://www.baidu.com/images/vue/PCtm_d9c8750bed0b3c7d089fa7d55720d6cf.png'
            }
        })
    </script>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/20.png)

v-bind的简写(语法糖)为：

```html
    <div id="app">
        <img v-bind:src="imageUrl" alt="">
        <!-- v-bind 语法糖 : -->
        <img :src="imageUrl" alt="" srcset="">
    </div>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/21.png)

注意：
在动态绑定属性时，不能用Matach语法，Vue不会对属性值进行解析，显示出来的属性值只是一个字符串，Matach只能用在属性的content区域

```html
    <div id="app">
        <img v-bind:src="imageUrl" alt="">
        <!-- v-bind 语法糖 : -->
        <img :src="imageUrl" alt="" srcset="">
        <img src="{{imageUrl}}" alt="" srcset="">
    </div>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/22.png)

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
  <!-- 错误的做法: 这里不可以使用mustache语法-->
  <!--<img src="{{imgURL}}" alt="">-->
  <!-- 正确的做法: 使用v-bind指令 -->
  <img v-bind:src="imgURL" alt="">
  <a v-bind:href="aHref">百度一下</a>
  <!--<h2>{{}}</h2>-->

  <!--语法糖的写法-->
  <img :src="imgURL" alt="">
  <a :href="aHref">百度一下</a>
</div>

<script src="../js/vue.js"></script>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      message: '你好啊',
      imgURL: 'https://img11.360buyimg.com/mobilecms/s350x250_jfs/t1/20559/1/1424/73138/5c125595E3cbaa3c8/74fc2f84e53a9c23.jpg!q90!cc_350x250.webp',
      aHref: 'http://www.baidu.com'
    }
  })
</script>

</body>
</html>
```

## 二、v-bind动态绑定class(对象语法)

### 2.1、用法：

通过布尔值决定是否将该类名添加到class上

```html
 <p :class="{类名1:布尔值，类名2：布尔值}"></p>
```

背景：通过判断给class添加类名，动态改变元素的样式。

```html
    <style>
        .active{
            color: red;
        }
    </style>
```

- 在class前用v-bind的语法糖，动态绑定class的属性
- 给class属性创建一个对象，用键值对的方式给类名添加false或true
- true则给class添加该类名，false则不添加
- true和false可以动态改变

```html
    <div id="app">
        <p :class="{active:isActive,line:isLine}">Hello</p>
    </div>
    <script src="./js/vue.js"></script>
    <script>
        var app = new Vue({
            el : '#app',
            data : {
                isActive : true,
                isLine : true
            }
        })
    </script>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/23.png)

控制台动态改变isActive

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/24.png)

此时p标签上的active属性消失

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/25.png)

### 2.2、简化行间代码

行间的代码通过函数返回

```html
    <div id="app">
        <p :class="getClasses()">Hello</p>
    </div>
    <script src="./js/vue.js"></script>
    <script>
        var app = new Vue({
            el : '#app',
            data : {
                isActive : true,
                isLine : true
            },
            methods : {
                getClasses : function () {
                    return {active:this.isActive,line:this.isLine}
                }
            }
        })
    </script>
```

#### 案例

点击按钮，切换div的样式
触发绑定在button上的click的事件，在isChange函数中改变isActive的属性值。

CSS代码：

```css
        .inner{
            width: 50px;
            height: 50px;
            background-color: black;
        }
        .active{
            background-color: red;
        }
```

Html代码;

```html
    <div id="app">
        <div class="inner" :class="{active:isActive}"></div>
        <button v-on:click = "isChange()">切换</button>
    </div>
    <script src="./js/vue.js"></script>
    <script>
        var app = new Vue({
            el : '#app',
            data : {
                isActive : true,
                isLine : true
            },
            methods : {
                isChange : function() {
                    //改变isActive的boolean值
                    this.isActive = !this.isActive;
                }
            }
        })
    </script>
```

相关完整代码展示：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>

  <style>
    .active {
      color: red;
    }
  </style>
</head>
<body>

<div id="app">
  <!--<h2 class="active">{{message}}</h2>-->
  <!--<h2 :class="active">{{message}}</h2>-->

  <!--<h2 v-bind:class="{key1: value1, key2: value2}">{{message}}</h2>-->
  <!--<h2 v-bind:class="{类名1: true, 类名2: boolean}">{{message}}</h2>-->
  <h2 class="title" v-bind:class="{active: isActive, line: isLine}">{{message}}</h2>
  <h2 class="title" v-bind:class="getClasses()">{{message}}</h2>
  <button v-on:click="btnClick">按钮</button>
</div>

<script src="../js/vue.js"></script>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      message: '你好啊',
      isActive: true,
      isLine: true
    },
    methods: {
      btnClick: function () {
        this.isActive = !this.isActive
      },
      getClasses: function () {
        return {active: this.isActive, line: this.isLine}
      }
    }
  })
</script>

</body>
</html>
```

运行结果：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/27.png)

点击按钮：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/26.png)

可是实现颜色的切换~

## 三、v-bind动态绑定class(数组语法)

### 3.1、用法

数组中的所有类名都会被绑定到class上

```html
<p :class="[类名1，类名2，类名3]"></p>
```

举例：

```html
    <div id="app">
       <!-- 数组中元素是字符串-->
        <p :class="['active','line']">Hello</p>      
    </div>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/28.png)

上面这种方法不常用，这和直接在行间添加class类名的效果完全相同
一般用于需要动态获取传递过来的类名

```html
    <div id="app">
        <!--数组中的元素是变量-->
        <p :class="[active,line]">Hello</p>      
    </div>
    <script src="./js/vue.js"></script>
    <script>
        var app = new Vue({
            el : '#app',
            data : {
                active : 'aaaaa',
                line : 'bbbbb'
            }
        })
    </script>

```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/29.png)

### 3.2、简化行间代码

class获取的类名用函数返回

```html
    <div id="app">
        <p :class="getClasses()">Hello</p>      
    </div>
    <script src="./js/vue.js"></script>
    <script>
        var app = new Vue({
            el : '#app',
            data : {
                active : 'aaaaa',
                line : 'bbbbb'
            },
            methods : {
                getClasses : function() {
                    return [active,line];
                }
            }
        })
    </script>
```

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
  <h2 class="title" :class="[active, line]">{{message}}</h2>
  <h2 class="title" :class="getClasses()">{{message}}</h2>
</div>

<script src="../js/vue.js"></script>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      message: '你好啊',
      active: 'aaaaaa',
      line: 'bbbbbbb'
    },
    methods: {
      getClasses: function () {
        return [this.active, this.line]
      }
    }
  })
</script>

</body>
</html>
```

## 四、v-bind动态动态绑定style（对象语法）

#### 4.1、用法

style属性要用小驼峰法

```html
:style="{fontSize:'50px',backgroundColor:'red'}"
```

举例：
属性值是个字符串'50px'，不能动态改变

```html
    <div id="app">
        <p :style="{fontSize:'50px'}">hello</p>
    </div>
    <script src="./js/vue.js"></script>
    <script>
        var app = new Vue({
            el : '#app',
            data : {
                fontSize : 50,
                currentColor: 'red'
            }
        })
    </script>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/30.png)

属性值换成data中的变量fontSize，可以动态改变

```html
    <div id="app">
        <p :style="{fontSizefinalSize+'px'}">hello</p>
    </div>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/31.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/32.png)

#### 4.2、简化代码

style的行间用函数替换

```html
    <div id="app">
        <p :style="getStyle()">hello</p>
    </div>
    <script src="./js/vue.js"></script>
    <script>
        var app = new Vue({
            el : '#app',
            data : {
                finalSize : 50,
                currentColor: 'red'
            },
            methods : {
                getStyle : function() {
                    return {fontSize:this.finalSize+'px'};
                }
            }
        })
    </script>
```

相关完整代码展示：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
  <style>
    .title {
      font-size: 50px;
      color: red;
    }
  </style>
</head>
<body>

<div id="app">
  <!--<h2 :style="{key(属性名): value(属性值)}">{{message}}</h2>-->

  <!--'50px'必须加上单引号, 否则是当做一个变量去解析-->
  <!--<h2 :style="{fontSize: '50px'}">{{message}}</h2>-->

  <!--finalSize当成一个变量使用-->
  <!--<h2 :style="{fontSize: finalSize}">{{message}}</h2>-->
  <h2 :style="{fontSize: finalSize + 'px', backgroundColor: finalColor}">{{message}}</h2>
  <h2 :style="getStyles()">{{message}}</h2>
</div>

<script src="../js/vue.js"></script>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      message: '你好啊',
      finalSize: 100,
      finalColor: 'red',
    },
    methods: {
      getStyles: function () {
        return {fontSize: this.finalSize + 'px', backgroundColor: this.finalColor}
      }
    }
  })
</script>

</body>
</html>
```

## 五、v-bind动态绑定style(数组语法)

#### 1.用法

**:style="[baseStyle1,baseStyle2]"**

**baseStyle1,baseStyle2存储在Vue的data中**

**methods : {
baseStyle1 : {backgroundColor:'red'},
baseStyle2 : {fontSize:'50px'},
}**

```html
    <div id="app">
        <p :style="[baseStyle1,baseStyle2]">hello</p>
    </div>
    <script src="./js/vue.js"></script>
    <script>
        var app = new Vue({
            el : '#app',
            data : {
                finalSize : 50,
                currentColor: 'red',
                baseStyle1 : {backgroundColor:'red'},
                baseStyle2 : {fontSize:'50px'}
            }
        })
    </script>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/33.png)

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
  <h2 :style="[baseStyle, baseStyle1]">{{message}}</h2>
</div>

<script src="../js/vue.js"></script>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      message: '你好啊',
      baseStyle: {backgroundColor: 'red'},
      baseStyle1: {fontSize: '100px'},
    }
  })
</script>

</body>
</html>
```