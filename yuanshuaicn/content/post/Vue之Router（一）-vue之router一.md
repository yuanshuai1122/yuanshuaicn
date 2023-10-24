---
title: Vue之Router（一）
date: 2021-post-30 10:44:46.217
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/1_7OCwu--TWqVluPMsZdzWKw-34ce1bcaed3b4c59a2183cf00af73987_1622733997113.png'
theme: 'light'
tags: 
- Vue
---

# 一、前景知识

## （一）、基本知识

##### 路由、转送、路由表

*路由：我们仨都算是负责运输行业的，但是我只是负责运输线路的确定
路由表：为了避免“转送”送错货物到码头，我就负责指定运输的码头
转送： 我负责具体的货物运输，将货物运输到指定的码头。要是没有“路由表”大哥的帮忙，我肯定一天要在十多个码头来来回回的瞎折腾呢。*

**① 路由：**决定**数据包**从来源到目的地的**路径**；
**② 路由表：**本质是映射表，决定了**数据包的指向**；
**③ 转送：**将输入端的**数据包**传送到合适的**输出端**。

## （二）、前后端未分离阶段

#### 1.后端渲染

  *后端渲染：我是高级绘画师，常常做着默默无闻的工作。平常只要是 “服务器”大哥给我的 “URL”设计方案，我都能用我精湛的jsp技术将它们绘画出来。*

后端渲染涉及到三个东西：**浏览器 + 服务器 + jsp**
**① 浏览器：**当客户端点击某个页面时，浏览器就会将该页面的**URL**传递给服务器
**② 服务器：**服务在接受到了浏览器传送过来的URL后，**对URL进行解析**，通过jsp技术渲染相应的内容
**③ jsp：**全称 Java Server Page，是将服务器解析后传送过来的**URL进行渲染**，比如添加HTML、CSS、Java等，最后将渲染好的页面再传回给浏览器。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/297.png)

**后端渲染，用一句话来说就是：网页已经在jsp渲染好了再传送给浏览器。**



#### 2.后端路由

**后端路由主要负责URL和jsp页面的映射关系**，但是使用后端路由会有以下的缺点：
① 整个页面的模块都是由后端人员编写和维护的；
② 如果前端人员想要开发页面，就要会PHP和Java语言；
③ 将HTML代码和逻辑数据代码整合在了一起，不易于后期维护。

## （三）、前后端分离阶段

##### 1.基本原理

在这个阶段，涉及到三个重要的人物：**浏览器、静态资源服务器、API接口服务器**

  *浏览器：接到用户给我的URL订单，首先得向“静态资源服务器”请求该订单所需要的材料，然后有些材料要经过特殊处理的，我又得交给“API接口服务器”帮我加好工后再给回我，最后才能完成客户的订单。

**① 浏览器：**在接受到某个页面的URL时，首先会向**静态资源服务器发送请求**，静态服务器就会返回该页面相关的html+css+js；然后当浏览器解析js代码时，就**通过Ajax向API服务器请求**相应的数据；最后**通过js 渲染到浏览器中**。
**② 静态资源服务器：**当浏览器传送URL时，返回该页面相关的html+css+js。并且在静态资源服务器中，**存放多套的html+css+js**，每一个URL都有对应的一套html+css+js。
**③ API服务器：**向浏览器发送解析所需要的**数据**。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/298.png)

而在这个阶段，涉及到一个概念：**前端渲染**
  前端渲染指的是浏览器显示的网页中的大部分内容，都是由前端写的js代码在浏览器中执行，最后显然出来的网页。
**总结起来就是：网页的渲染是在浏览器中渲染的**。

##### 2.特点

① 后端提供数据，前端通过Ajax获取数据，并且通过JavaScript将数据渲染到页面中；
② 前后端责任清晰，后端专注于数据上，前端专注于交互和可视化上

## （四）、SPA：单页面富应用阶段

##### 1.基本原理

  SPA，全称：Simple Page Application，译为单页面富应用。使用该模式开发出来的项目，最终**只有一个index.html页面**，如果涉及到多个页面时，需要前端路由的处理，下面讲解它们之间的原理：
  浏览器：我现在可轻松啦，当用户向我提交URL订单的时候，我只需要向“静态资源服务器”请求原材料，而且是一下子就把所有的材料都搬运回来。
静态资源服务器：为了提高效率，我也不像以前那么傻，为每一个URL订单开一个工厂来专门生产材料，而是直接用一个工厂生产所有的材料。

**① 静态资源服务器**：保存html+css+js，并且html**仅有一个index.html**
**② 浏览器：**当接收某个页面的URL时，向静态资源服务器其**请求一整套的html+css+js。**下载一整套的资源最初**仅仅渲染index.html，**而其余的页面在和用户有交互时才渲染。比如用户点击“我的”，就从一整套的html+css+js中抽取和“我的”页面相关的内容交给浏览器渲染出来。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/299.png)

而这个阶段的核心是：**前端路由**
  前端路由是指**当url发生改变时，就从一整套的html+css+js抽取出和当前url先关的内容，从而实现每个子url和子页面的一一对应关系。**
**总结起来就是：你要什么就给什么，不给多余的。**

##### 2.特点

在前后端分离的基础上增加了前端路由

要明白以下几点：
1.什么是前后端分离：前端负责美丽，后端负责干活
2.后端渲染：在jsp中已经渲染好了整夜页面
3.后端路由：将ur和jsp页面对应
4.前端渲染：在浏览器中渲染
5.前端路由：要什么给什么，而且绝不多给

# 二、路由安装和配置

## （一）、如何实现修改URL而不刷新页面？

### 1.前因后果

  当页面的URL发生改变时，就会向服务器发送请求，请求该页面相应的内容，然后页面就会刷新。但是现在要做的就是当URL发生改变的时候，页面并不会进行刷新，可以通过下面三种方法：

### 2.方法

**① 修改URL的hash**
在控制台中输入命令： location.hash="xxx"
  如果页面的URL发生改变，就会向服务器发送请求，请求的资源我们可以在Network查看。所以演示的步骤如下：在控制台输入 location.hash="aaa" -->观察浏览器的地址栏是否改变 --> 查看Network 是否有新资源。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/300.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/301.png)

地址栏由原来的 [http://localhost:8080/](https://links.jianshu.com/go?to=http%3A%2F%2Flocalhost%3A8080%2F) --> 变成 [http://localhost/#/aaa](https://links.jianshu.com/go?to=http%3A%2F%2Flocalhost%2F%23%2Faaa) 说明页面的URL已经发生了改变。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/302.png)

发现该页面并没有向服务器发送新的请求。说明这个方法是可行的O(∩_∩)O



**② html5 的pushState**

这是第二种修改URL而不刷新页面的方法，用法如下： history.pushState({...},'xxx','URL') 。第一个参数是对象，第二个参数是title，第三个参数是URL

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/303.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/304.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/305.png)

history.pushState指令的原理和栈结构相似。先进后出，当在控制台输入多个history.pushState指令时，URL只会显示最后一条指令的URL。

**③ html5 的replaceState**
同样的，和pushState工作的基本原理相似，但是也有不同：
  pushState是一个类似栈的结构，会**保存历史记录**，所以可以返回上一次访问过的页面；
  而replaceState 是直接用当前的URL替代了上一个URL，所以**不能够返回上一次访问过的页面**。。

## （二）、路由安装和配置

  路由的基本知识小编已经在上一篇提及过了，这里呢主要为大家分享如何安装和配置路由

#### 1.安装路由

  有两种方式，一是通过**命令 npm install vue-router --save**安装，二是在**创建项目**的时候就选择安装路由

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/306.png)

当安装完成后，会在src文件夹下默认创建 router 文件夹，router文件夹下又会自动创建 index.js文件。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/307.png)

#### 2.使用路由的步骤

  这里主要分成四步：① 导入路由插件 ② 使用路由插件 ③创建路由对象 ④使用路由对象
**① 导入路由插件**
  在通过npm安装好路由之后，如果想真正的使用，首先肯定是导入路由啦。在router文件夹下的index.js文件写入下面的代码：

```js
import VueRouter from 'vue-router'
```

**② 使用路由插件**
  导入好的路由并不能直接使用，必须通过**Vue.use()**才可以使用。

```js
Vue.use(VueRouter)
```

**③ 创建路由对象**
  和创建Vue实例一样的创建方法，值得注意的是这里的**routes**属性**负责了每一个URL和页面的映射关系**o

```js
const router = new VueRouter({
    // 负责URL和页面的映射关系
    routes:[
        
        
    ]
})
```

**④ 使用路由对象**
  如果在别的页面使用路由，肯定得先把路由导出，别人才能调用。

```js
//首先得在index.js文件导出路由
export  default router
```

```js
//然后在main.js文件使用路由
import router from './router'
new Vue({
  el: '#app',
  router,
  render: h => h(App)
})
```

关于这里有必要解释一下，为什么导入路由的路径是 ./router 而不是 ./router/index.js 。因为在执行的时候，会**默认先执行index的文件**，所以写不写index的效果是一样的。

# 三、路由映射配置

## （一）、编写路由映射关系

##### 1.创建路由组件

因为映射关系是映射页面和URL的关系，所以我们得先准备好页面。
创建 home.vue 和 about.vue文件，然后在该文件中书写相关的内容

```vue
//about.vue文件内容
<template>
    <div>
        <h1>关于</h1>
        <span>在这里你可以问到任何你想问的问题</span>
    </div>
</template>

<script>
</script>

<style>
    h1{
        color: aquamarine;
    }
</style>


//home.vue文件内容
<template>
    <div>
        <h1>首页</h1>
        <span>我是首页</span>
    </div>
</template>

<script>
</script>

<style>
    h1{
        color: red;
    }
</style>
```

##### 2.配置路由映射关系

  routes主要放两个东西：**路径 path + 组件 component**。每一个映射关系就是一个对象，所以我们可以这样写：

```js
//1.导入
import VueRouter from 'vue-router'
import Home from '../components/home.vue'
import About from '../components/about.vue'
Vue.use(VueRouter)

//2.创建路由对象
const router = new VueRouter({
    // 负责URL和页面的映射关系
    routes:[
        {
            path:'/home',
            component:Home
        },
        {
            path:'/about',
            component:About
        }
        
    ]
})

//3.导出路由
export  default router
```

当页面的地址中有 “/home ”就显示home相关的页面；当页面的地址中有 “/about ”就显示about相关的页面.

##### 3.使用路由

​		页面和URL的映射关系已经建立好了，但是得要有东西来触发它们真正起作用，所以就涉及到另外两个重要的标签： router-link 和router-view
**① router-link：**是vue-router 中已经注册过多的组件，其功能类似于a标签，**点击到文字就跳转到相应的页面**。
**② router-view：**它的作用就是决定**页面的显示**。
比如：

```vue
<template>
    <div>
        <router-link to="/home">首页</router-link>
        <router-link to="/about">关于</router-link>
        <router-view></router-view>
    </div>  
</template>
```

在App.vue文件中写入上面的代码，结果如下图所示：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/308.gif)

# 四、路由跳转

## （一）、路由的默认路径

#### 1.产生原因

  当我们进入一个网页的时候，首先引入眼帘的肯定是首页对不对。所以我们要将首页作为我们的默认路径

#### 2.用法

其实很简单，在routes的映射关系里使用redirect。
  redirect又叫重定向，意思是当path为空的时候，就跳转到redirect指定的路径。
比如：

```js
const router = new VueRouter({
    // 负责URL和页面的映射关系
    routes:[
    //路由的默认路径
        {
            path:"",
            redirect:'/home'
        },
        {
            path:'/home',
            component:Home
        },
        {
            path:'/about',
            component:About
        }
        
    ]
})
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/309.png)

当路径为空的时候，我们就跳转到‘/home’路径下，然后根据映射关系，跳转到/home路径下就显示首页的内容。

#### 3.改进

  但是大家有没有发现，我们的URL地址多了个 “#”【因为是通过hash值修改的URL】，然而我们并不希望有这个多余的“#”，怎么办呢？
很简单，在创建router对象的时候添加多一个属性： mode：history

```js
const router = new VueRouter({
    // 负责URL和页面的映射关系
    routes:[
        {
            path:"",
            redirect:'/home'
        },
        {
            path:'/home',
            component:Home
        },
        {
            path:'/about',
            component:About
        }
        
    ],
    mode:'history'
})
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/310.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/311.png)

# 五、路由跳转的方式

# 一、router-link补充

四个关键字：**tag、to、replace、activeClass。**
**① tag：指定 router-link 被渲染成什么组件。**
  router-link 默认被渲染成 a标签，如果你想要渲染成别的标签，添加 tag 属性即可，具体用法如下：

```vue
<router-link to="/home" tag="button">首页</router-link>
<router-link to="/about" tag="button">关于</router-link>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/312.png)

 可以看到，通过 tag="button" 的设置，就将router-link 以按钮的形式显示出来了。
**② to ：指定跳转的路径。**用法如下：

```vue
<router-link to="/home">首页</router-link>
<router-link to="/about">关于</router-link>
```

当用户点击 “首页” 就会跳转到 “/home” 路径下的文件，以此类推。

**③ replace：不会留下历史记录，即点击后退键不会返回到上一个页面中。**
  router-link 默认是采用 pushState 的方式保留历史记录，如果在某些特殊的情况下不允许用户点来点去就乐意添加该属性。用法如下：

```vue
<router-link to="/home" replace>首页</router-link>
<router-link to="/about" replace>关于</router-link>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/313.png)

当用户子在“首页” 和 “关于” 两个页面来回切换时，就不可以返回上一个访问过的页面。)╮
**④ activeClass：激活状态。**
  意思是当 router-link 对应的路由匹配成功时，会自动给当前元素设置一个叫router-link-actice的class，设置active-class 可以修改默认的名称。用法如下：
**4.1 单一书写**
这种写法是直接嵌套在标签内部，比如:

```vue
<router-link to="/home" tag="button" replace active-class="warp">首页</router-link>
<router-link to="/about" tag="button" replace active-class="warp">关于</router-link>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/314.gif)

这种写法并非长久之计，因为一旦页面数量很多的时候，就招架不住了，所以强烈推荐下面写法。
**4.2 批量书写**
通过在router 对象添加属性 linkActiveClass 。比如：

```js
const router = new VueRouter({
// 负责URL和页面的映射关系
    routes:[
    linkActiveClass:"warp"
})
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/315.gif)

# 二、代码跳转路由

  在上面呢，我们是通过 router-link 标签来实现路由跳转的，那如果我不想通过这种方式来跳转路由，是否还有其他的方式呢？答案是：有的。下面就为大家分享路由跳转的进阶版本--代码跳转

#### 1.基本思路

  代码跳转的思路很简单，通过添加事件，然后在对事件处理的methods方法中添加属性：**this.$router** 即可，具体见下面代码：

#### 2.使用流程

**① 绑定事件**
  绑定事件的对象是需要路由跳转的地方，比如点击 “首页” 就跳转到 “/home”页面

```vue
<button @click="homeClick">首页</button>
<button @click="aboutClick">关于</button>
```

**② 添加 this.$router属性**
在导出路由对象的地方添加属性如下：

```js
<script>
export default {
  name: 'App',
  methods:{
      homeClick(){
          this.$router.push('./home');
      },
      aboutClick(){
          this.$router.push('./about');
      }
  }
}
</script>
```

$router是vue-router本身自带的属性。代码跳转的方式和使用 router-link 跳转的方式其实差不多，都可以一一对应起来。