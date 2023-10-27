---
title: Vue之Router（二）
date: 2021-07-20 18:38:23.971
author:
  name: "yuanshuai"
  link: "https://cloud.tencent.com/developer/user/8180692"
  email: "shuaiyuan1122@gmail.com"
  avatar: "https://aabb-2023.oss-cn-beijing.aliyuncs.com/hjscijg3uw.png"
tags: 
- Vue
---

# 一、动态路由

## 1.产生背景

  为什么会有动态路由呢？因为在一般的项目中，都会涉及到用户登陆的操作，我们希望某某用户登陆某个网页的时候，能够在该网页的UURL地址上显示用户的登录名或者用户ID等的信息。
  **动态路由就是根据不同的用户将在网页的URL中动态追加登录名或者ID等信息。**
比如:

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/316.png)

当用户“zhangsan”登陆系统，进入到系统的首页时，就会在首页的URL后面显示“zhangsan”

## 2.使用步骤

**① 在 routes 加字段**
如果想在URL后面追加字段，首先得将字段添加到跳转路径的后面。
比如：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/317.png)

本例子中，在uesr页面的跳转路径path添加了 uesrID 的字段，希望当某个用户登陆系统，进入到 uesr 页面时在该页面的URL上显示用户的ID信息。



**② 在 router-link 添加字段的值**
  光是在 routes 的 path 上添加字段是没有用的，因为没有具体的数值，而且如果path:是 '/user/:userID' 这种形式，就没有办法显示“user”这个页面的，因为根本没有这样的路径，所以我们需要在 **router-link 的to属性添加字段的值**，有两种方式：静态添加和动态添加
**2.1 静态添加**
所谓的静态添加就是**字段的数值是写死的**，比如：

```vue
<router-link to="/user/zhangsan" tag="button" replace>用户</router-link>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/318.png)

为 user页面的URL地址后追加的用户名为“zahngsan”的用户。但是正常的开发过程中，用户千千万，我怎知你是谁呢，所以一般采用动态添加的方式。
**2.2 动态添加**
所谓的动态添加就是**字段的值用变量来替代**，比如：
**首先得在data中声明变量**

```vue
//在App.vue文件
<script>
export default {
  name: 'App',
  data (){
      return {
          userID:"李四"
      }
  }
}
</script>
```

**然后通过 v-bind 指令使用变量 userID**

```vue
<router-link :to=" '/user/' +userID"tag="button" replace>用户</router-link>
```

这里要注意的是常量和变量的拼接。
**常量：**要用**引号包裹**，因为用引号包裹后就是普普通通的字符串
**变量：不需要引号包裹**，和常量通过 + 拼接
其效果图如下：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/319.png)

### 3. route

现在有一个新的需求。希望拿到追加在URL后面的字段并且显示在页面中。
比如：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/320.png)

在路由中提供了另外一种方法： $route。具体用法如下：
**① 首先，通过 route 获取 URL后面的字段值：**

```vue
//在App.vue文件
<script>
export default {
  name: 'App',
  data (){
      return {
          userID:"李四"
      }
  },
computed:{
      userid(){
          return this.$route.params.userID
      }
  }
}
</script>
```

使用计算属性，将通过route 获取的userID传递到变量userid中。
**② 然后在页面显示该值**

```vue
//在App.vue文件
<template>
    <div>
        <router-link to="/home" tag="button" replace>首页</router-link>
        <router-link to="/about" tag="button" replace>关于</router-link>
        <router-link :to="'/user/'+userID"tag="button" replace>用户</router-link>
        <router-view></router-view>
        <h3>{{userid}}</h3>
    </div>  
</template>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/321.png)

# 4. router 和route 的区别

  之前在用代码实现路由跳转的时候用过 router；今天在获取URL追加的字段的时候用过 route 。虽然只是相差了一个单词，但是意义完全不一样，具体的区别如下：

##### ① 字面差别

  这个差别很明显了，router 比route 多了个 字母 r。不要笑噢，这个也是很严肃哒，细节决定成败，说的就是这个理儿

##### ② 本质

router：本质是**router对象**
route：本质是router对象里的一个属性routes，routes下面有很多个route，而**通过 $route 获取的路由是处于激活状态下的路由**。具体关系见下图：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/322.png)

# 二、路由懒加载

## （一）、打包文件解析

#### 1.前言

  当我们把所有的文件都打包到 bound.js 文件夹下，该文件夹就会变得很大，加载缓慢。如果我们**把不同路由对应的组件分割成不同的代码块，分别打包，当路由被访问时才加载相应的组件**，就变得高效起来。所以我们要将不同的文件打包在不同的地方。

  既然如此，这么多的 css 文件、 js文件、html文件，我们怎么知道它们分别在那个包呢？

#### 2.文件解析

  首先，执行 npm run build 执行，对项目进行打包，打包后产生的 dist 文件夹有以下的内容：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/323.png)

##### ① CSS 文件

  当我们打开 CSS 文件夹的时候，看到里面仅仅包含了 .css 的文件，由此可见，打包的 css 文件都放在这里了。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/324.png)

##### ② js 文件

  当我们打开 js 文件夹的时候，看到里面主要包含了三类的文件：app.XXXX、manifest.XXXX、vendor.XXXX

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/325.png)

**2.1 app.XXXX**
  app 是application的缩写，主要是**包含当前应用开发的所有代码**，简单说就是自家写的代码。

**2.2 vendor.XXXX**
  vendor 译为提供商，也就是你在项目中**引用第三方的东西**。比如 vue、vue-router 等等。

**2.3 manifest.XXXX**
  **为打包的代码做底层支撑**。其实跟webpack的原理挺像的，因为没有webpack，一些 ES6、CommonJS 模块是没有办法被浏览器识别的，有了webpack做为底层支撑，一些不能被浏览器识别的模块就能识别了。
总结起来就是 ：**app是自家的代码，vendor是别家的代码，mainfest是底层支撑。**

## （二）、路由懒加载

#### 1.原因回顾

  上面已经提及到，当我们把所有的文件都打包到 bound.js 文件夹下，该文件夹就会变得很大，加载缓慢。如果我们**把不同路由对应的组件分割成不同的代码块，分别打包，当路由被访问时才加载相应的组件**，就变得高效起来。
比如：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/326.png)

在这个页面中，有三个按钮：首页、购物车、我的。那么这三个按钮肯定有对应的页面，这三个页面又可以看成是是个不同的组件。
  如果没有路由懒加载时，当用户进入到该页面，就**会向静态资源服务器一下子请求了“首页”、“购物车”、“我的”三个页面的所有资源**。三个还好说，但是当数据量庞大的时候，加载就会变得缓慢，甚至用户的界面会出现空白的情况。
  有了路由懒加载之后，**当用户进入了“首页”，仅仅请求有关“首页”的资源。**其余没有用到的资源都不请求。这样不管有多少的数据，我没用到的都不管我的事，所以就变得十分高效 

#### 2.路由懒加载做了什么？

① 将路由对应的组件**打包**成一个个的 **js 代码块**
② 只有在这个路由**被访问**的时候**才加载对应的组件**。

#### 3.懒加载的方式

**① 结合 vue 的异步组件和Webpack 的代码分析**

```js
const Home  = resolve => { require.ensure([ '../components/Home.vue'],() => { resolve(require('../components/Home.vue''))})};
```

**② AMD写法**

```js
const About = resolve => require(['../components/About.vue'],resolve);
```

**③ 箭头函数**

```js
const Home = () => import ('../components/Home.vue')
```

下面用方法③结合我们的程序跑一下呗

```js
//在index.js 文件修改
//1.导入
import Vue from 'vue'
import VueRouter from 'vue-router'

const Home = () => import ('../components/home.vue')
const About = () => import ('../components/about.vue')
const User = () => import ('../components/user.vue')
Vue.use(VueRouter)

//2.创建路由对象
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
        },
        {
            path:'/user/:userID',
            component:User
        }
        
    ],
    mode:'history',
    linkActiveClass:"warp"
})

//3.导出路由
export  default router
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/327.png)

可以看到在打包好的js文件夹下多的三类的 .js文件。 为什么是三个呢？因为有三个页面用了懒加载呀！

# 三、路由的嵌套使用

## 1.产生背景

  嵌套路由的产生是因为**在一个页面中，可以将不同功能的部分进行划分，组成小的组件，然后点击相应的链接就会跳转到相应的内容。**

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/328.png)

假设现在分别有 home 和 about 两个大的页面。我们希望通过 /home/new 来访问 home 中有关 new 的内容；通过 /home/message 来访问 home 中有关 message 的内容。一个路径映射一个组件，访问 /home/new 和 /home/message 就会分别渲染 new 组件和 message 组件。
  简单来说，就是**对一个页面的不同功能使用路由来达到跳转的目的。**



## 2.实现步骤

##### ① 创建对应的子组件，并且在路由的映射中配置对应的子路由

首先创建 HomeNews 和 HomeMessage 两个子组件，组件的内容分别如下所示：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/329.png)

然后在 index.js 文件配置相应的路由映射关系如下：

```js
const router = new VueRouter({
    routes:[
        {
            path:'/home',
            component:Home,
            children:[
                {
                    path:"new",
                    component:HomeNews,
                },
                {
                    path:"message",
                    component:HomeMessage,
                }
            ]
        },  
    ],
})
```

配置映射关系是要注意两点：
第一：**不能在 routes 内部最后面追加**。因为如果在最后面追加，相当于 new 和 message 两个子页面就和 home 页面是同级的关系，而不是包含关系。所以这里需要在 home 内部**使用 children 配置映射关系**。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/330.png)

第二：**子级的路径不需要加 /** 。因为路由在解析的时候会自动帮我们添加上去，就不需要我们再画蛇添足啦~

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/331.png)

##### ② 在组件内部使用 <router-view> 标签

  经过上面的步骤，我们已经配置好了 new 和 message 两个子页面的内容，接下来就是找地方给它们展示自己了 
  首先我们要知道，展示肯定是要用 **router-view** 展示，然后就是确定 router-view 的位置。如果我们将 router-view 放到了 App.vue 下，可想而知,当你进网页的时候看到的就是这样的效果：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/332.png)

为什么？因为new 和message 取代了 home 和 about 页面的位置  所以我们应该**将显示 new 和 message 的router-view 放在 home 组件的内部**，因为 new 和 message 是在 home 页面下显示的：

```vue
<template>
    <div>
        <h1>首页</h1>
        <span>我是首页</span>
        <div>
            <router-link to="/home/new">新闻</router-link>
            <router-link to="/home/message">消息</router-link>
            <router-view></router-view>
        </div>
    </div>
</template>
```

这个时候你就可以看到下面的效果：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/334.gif)

但是我们点首页，新闻的具体内容没有显示出来，需要我们点击“新闻”这个标题之后才会显示。这个简单，加个默认路径就可以了。

```js
const router = new VueRouter({
    routes:[
        {
            path:'/home',
            component:Home,
            children:[
                {
                    path:"",
                    redirect:"new"
                },
                {
                    path:"new",
                    component:HomeNews,
                },
                {
                    path:"message",
                    component:HomeMessage,
                }
            ]
        },  
    ],
})
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/335.gif)

# 四、参数传递

# 一、传递方式

  传递参数主要有两种类型： params 和 query 。

#### 1.params

主要分成下面三个步骤：
**① 配置路由格式**
  说白了就是在 routes 映射关系中的 path 后面添加多一个变量，该变量就是要传递的参数。
比如：

```js
const router = new VueRouter({
    // 负责URL和页面的映射关系
    routes:[
        {
            path:"",
            redirect:'/home'
        },      
        {
            path:'/user/:userID',
            component:User
        }
    ],
})
```

我们希望在“user” 页面的URL中添加上用户的ID。所以在 routes 的 path 中添加了userID 变量，该变量就是想要传递的参数O(∩_∩)O

**② 给参数赋值**
  上面一步我们已经为路由的路径后面添加多了userID 参数，接下来我们就要为这个参数赋值。
在路由对象的 data 中声明该变量并且为变量赋值。比如：

```js
<script>
export default {
  name: 'App',
  data (){
      return {
          userID:"李四"
      }
  },
</script>
```

我们希望用户“李四” 登陆系统后能将他的ID传递到用户页面的URL中。所以为 userID 赋值为 “lisi”。

**③ 显示变量**
上面一步我们已经有了数据，接下来就是将该数据显示在用户页面的URL中。
  使用**计算属性**重新定义一个变量，然后通过**$route 获取原来变量的值并返回给新的变量**。最后一定要记得，在 **route-link 标签的 to 属性也要添加上该变量**，否则不能成功跳转页面。比如：

```vue
//显示数据的模板
<template>
    <div>
        <router-link to="/home" tag="button" replace>首页</router-link>
        <router-link to="/about" tag="button" replace>关于</router-link>
        <router-link :to="'/user/'+userID"tag="button" >用户</router-link>
        <router-view></router-view>
        <h3>{{userid}}</h3>
        <h2>{{$route.params.userID}}</h2>
    </div>  
</template>

//路由配置
<script>
export default {
  name: 'App',
  data (){
      return {
          userID:"李四"
      }
  },
 computed:{
      userid(){
          return this.$route.params.userID
      }
  }
</script>
```

在该例子中，定义了新的变量 userid，然后通过 route.params.userID获取了userID的值，最后在模板中显示如下：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/336.png)

#### 2.query 类型

  在进行正式配置之前，我们要准备前期的工作，重新创建一个 profile.vue 组件。这里已经创建好了，就不一一叙述啦

 重点是下面：
我们通过 query 传递参数的时候，也可以大概分成下面三步：
**① 配置路由**
这个配置路由的方式就是跟往常配置路由的方式是一样的。比如：

```js
const router = new VueRouter({
    // 负责URL和页面的映射关系
    routes:[
        {
            path:"",
            redirect:'/home'
        },
        {
            path:'/about',
            component:About
        },
        {
            path:'/user/:userID',
            component:User
        },
        {
            path:'/profile',
            component:Profile
        }
        
    ],
})
```

在这里，当路径出现 “/profile” 时就跳转到 profile.vue 组件的页面。

**② 通过router-link 实现跳转**
在模板中通过 router-link 实现路径和页面的跳转。比如：

```vue
<template>
    <div>
        <router-link to="/home" tag="button" replace>首页</router-link>
        <router-link to="/about" tag="button" replace>关于</router-link>
        <router-link :to="'/user/'+userID"tag="button" replace>用户</router-link>
        <router-link to='/profile'>档案</router-link>
        <router-view></router-view>
        <h3>{{userid}}</h3>
        <h2>{{$route.params.userID}}</h2>
    </div>  
</template>
```

当用户点击了 “档案” ，就会跳转到 “/profile”路径下的 profile.vue组件。
**③ 传递参数**
  query 和params不同的是，query传递的是一个对象，所以在 router-link 标签的 to 属性不是单单的传递一个变量，而是一个对象。比如：

```vue
<template>
   <div>
   <router-link :to="{path:'/profile' , query:{name:'zhangsan',age:18}}">档案</router-link>
   </div>  
</template>
```

使用query传递参数是需要注意两点：
  第一：**传递的是对象**。因为传递的是对象，所以在 to 前面要使用 v-bind（简写是 ：）来获取，否则如果是 to=“{...}” 传递的是一个{}字符串儿不是一个对象。
  第二：**query 也是对象**，所以在query的内部可以传递很多的属性。
上面的例子中，当用户点击的“档案” ，跳转到“档案”页面时，该页面的URL就会显示 query 对象传递过去的参数。比如：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/337.png)

同理，如果我们想要将 URL 中的信息拿到页面中该怎么办呢？
很简单，只需要通过 $route.query 就可以了

```vue
// 在 profile.vue 添加
<template>
    <div>
        <h1>我是profile</h1>
        <span>{{$route.query}}</span>
    </div>
</template>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/338.png)

不仅如此，我还可以获取更加详细的信息，比如单独的姓名、年龄：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/339.png)

## （二）、代码传递的方式

### 1.params 的代码书写方式

  首先就是把 router-link 变成 **button 标签**，紧接着为 button **添加点击事件**，最后再绑定的事件通过 **$route.push 获取path**。比如：

```vue
<template>
    <div>
        <!-- 1.将router-link 转换成button 标签并添加点击事件 -->
        <button @click="HomeClick">首页</button>
        <button @click="AboutClick">关于</button>
        <button @click="UserClick">用户</button>
        <button @click="ProfileClick">档案</button>
        <router-view></router-view>
        <h3>{{userid}}</h3>
        <h2>{{$route.params.userID}}</h2>
    </div>  
</template>

<script>
export default {
  name: 'App',
  data (){
      return {
          userID:"李四"
      }
  },
  computed:{
      userid(){
          return this.$route.params.userID
      }
  },
//绑定点击事件
  methods:{
      HomeClick(){
          this.$router.push('/home');
      },
      AboutClick(){
          this.$router.push('/about');
      },
      UserClick(){
          this.$router.push('/user/' + this.userID);
      },
      ProfileClick(){
          this.$router.push({
              path:'/profile',
              query:{
                  name:'zhangsan',
                  age:18
              }
          })
      }
  }
}
</script>
```

这里要主要的是两点：
  **① 地址拼接：** 常量需要用引号包括，变量不需要引号包裹，常量和变浪之间通过 + 进行拼接。比如： this.$router.push('/user/' + this.userID); /user 代表路径，是一个常量，需要用引号包裹，而 userID 是变量，通过this来获取当前的变量；
  **② 参数传递**：如果连接中涉及到参数传递的，路径后面要加“/”。比如('/user/' + this.userID); 我们需要将userID传递到URL中，所以路径 /user 后面要加 /

### 2.query 代码的书写方式

代码加上图所示。这里小编主要分析如何书写用query传递参数的代码表示方式。
  依旧是通过 **$router.push** 来获取，只是 push 内部不再是简简单单的变量，而是一个**对象**。对象内部有 path 和 query，而query又是一个对象，里面包含了 name 和age 两个属性。如下图所示：

```js
 ProfileClick(){
          this.$router.push({
              path:'/profile',
              query:{
                  name:'zhangsan',
                  age:18
              }
          })
      }
```