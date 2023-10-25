---
title: Vue之Router（三）
date: 2021-posts-30 10:44:53.649
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/1_7OCwu--TWqVluPMsZdzWKw-34ce1bcaed3b4c59a2183cf00af73987_1622733997113.png'
theme: 'light'
tags: 
- Vue
---

# 一、router和route的本质

## 1.router

  前面说过，当你使用 this.$router 获取的时候其实就是获取了 router 实例。

首先在 user 页面添加一个按钮，然后通过点击这个按钮来打印。
比如：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/340.png)

然后在 main.js 文件中打印 router 。
比如：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/341.png)

在看结果之前，小编有必要为大家说明一下在 main.js 文件打印的 router 就是在index.js 文件中 new 出来的 VueRouter。他们之间的来龙去脉见下图：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/342.png)

下面小编分别对比在 user.vue 和 main.js 打印的结果：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/343.gif)

此外小编来带大家伙看看源码，对 router 更加深入的了解：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/344.png)

首先看到第一点： **class VueRouter**，说明 **VueRouter 是一个类**。所以我们可以通过 new 来实例化。
  其次看到第二点：这个类当中包含了很多的方法，有push、replace。这也就是为什么我们能够在某些页面使用this.router.push 方法来操作。比如：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/345.png)

## 2. route

还是在刚刚的 user.vue 页面中，我们打印 this.$route 看看 

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/346.png)

可以很明显的看到它们两个是不同的东西。而且 **route 获取的是当前处于活跃状态的路由**，怎么看？看 fullPath：“/user/李四” 就可以知道。因为小编是在user 页面操作的吖，而 user 页面对应的路由就是 /user/:userID 。

## 3. 源码下的 router 和 route

  上面呢，小编仅仅是很简单的说明了他们之间的区别，接下来小编带大家去源码看看他们的本质。
  首先要记住一句话： **所有的组件都是继承 Vue 的原型**。也就是说，Vue的原型有什么，在组件中也有什么。
比如：
我们先在 main.js 文件中使用 Vue原型中的 test 方法：

```js
Vue.prototype.test = function(){
    console.log("test")
}
```

然后在 user 页面中调用该方法。

```js
//当点击按钮的时候调用原型中的test方法
methods:{
            btnClick(){
                this.test()
            }
        }
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/347.png)

可以看到，尽管我们没有在 user 页面中定义test 方法，但是却依旧可以调用，因为所有的组件都是继承 Vue 的原型。为什么要说这个呢，因为接下来就是见证奇迹的时刻！
  这里提一下，就是**对象定义属性**的另外一个方式，我们平常定义属性是这样子的：

```js
const add =  {
    name:"zhangsan"
}
```

但是还有一个你可能不知道的方法：

```js
Object.defineProperty(add,"name","zhangsan")
```

Object.defineProperty 该方法就是针对 add 对象增加一个 name 属性，该属性的值是 zhangsan。
  好了，所有的前期工作已经准备好了。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/348.png)

 你看到了什么？！没错，就是你所想的，我们为 Vue 的原型增加了 router 和 route 的属性，具体的属性值是怎么操作的我们可以适当忽略。这就是为什么我们可以直接在组件【比如user、home、about 这些页面】中使用 this.$router 的原因，因为所有的组件都是继承 Vue 原型的，Vue原型用的属性，在组件中也可以使用。
  综上所述。因为所有的组件都是继承 Vue 的原型，当在Vue原型添加了router和route的属性之后，意味着所有的组件也可以直接使用 router 和 route 。这就是它们的本质。

## 4.Vue.use

```js
Vue.use(VueRouter)
```

就是最开始安装路由的时候，要通过 Vue.use（）才可以使用。
至于为什么，等你看完下面的内容应该就会知晓
① 原理
  **执行 Vue.use(VueRouter) 的实质就是在执行 Vuerouter 内部的install方法**。不信咱们走去瞧瞧它的源码

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/349.png)

这里好像看不出 install 的什么名堂，因为它的箭头函数是空的，但是，我们再往下看：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/350.png)

这里主要是为 VueRouter 的 install 重新赋值为 install，而这个 install 是从 install.js 文件导入的，所以我们再去看看 install.js 里面到底有什么猫腻

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/351.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/352.png)

在这里，声明了全局组件，也就是为什么我们能直接使用 router-link 和 router-Link 的原因。

# 二、全局导航守卫

## 1.什么是导航守卫？

  我们日常生活中的导航相信大家都用过吧O(∩_∩)O哈哈~。就是有指导你从A地到B地的路线。
  而导航守卫也是同样的道理。主要是**监听路由跳转的过程**。如果不太明白就提出一个需求。比如：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/353.png)

我们都知道HTML中有上图这么一个标题。需求是当用户点击“首页”，标题就变成“首页”；当用户点击“关于”，标题就变成“关于”。目前有两种实现的办法，下面我们分别来看看这两种办法是如何实现的。

#### ① 生命函数

  目前我们常用的生命函数主要有三大类，分别是：**created、mounted、updated**。
**created**：是在**组件被创建**的时候会回调的函数，然后执行该函数内的内容；
**mounted**：当**组件的模板被挂载到DOM**上时就会回调该函数，执行函数内相关的操作；
**updated**：只要**页面发生刷新**的时候就会回调该函数从而执行函数内的代码块。
对于上面的需求，可以使用 created 函数来实现。具体代码见下：

```js
<script>
    export default {
        name:'home',
        created(){
            document.title="首页"
        }
    }
</script>
```

在 home.vue 页面中添加 created 函数，当组件 home 创建的时候就将标题修改成 “首页”。其余的 “用户” 页面、“关于”页面、“档案”页面也是以此类推，小编就不一一展示出来啦，下面让我们一起来看看效果：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/354.gif)

但是该方法不足的地方就是：太繁琐了！我只是想根据导航栏然后修改标题，你就在四个页面都添加了 created 函数，如果有一百个页面你也这么修改的话，那你就得添加一百个。那一千个...一万个...

## 2.导航守卫使用

  在文章的开头小编就说过，导航守卫就是监听路由的跳转。所以使用的使用分成以下两步：
第一：**使用 beforeEach 确定跳转变化**
第二：**使用 meta 确定路由跳转变化时要修改的内容**
下面就告诉大家导航守卫究竟怎么个导航法

##### ① 使用 beforeEach 确定跳转变化

  首先在 router 中有个叫 **beforeEach** 的函数，当我们查看源码时会发现它需要传入一个叫 guard 的 **NavigationGuard 参数**

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/355.png)

NavigationGuard? 是啥？不懂，接下来我们再进一步看看 NavigationGuard是什么东西

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/356.png)

可以看到 **NavigationGuard 是**一个名字叫做 any 的**箭头函数**，箭头函数内部传入了三个参数：**to、from、next**。to代表路由即将要跳转的地方，from代表从那个地方跳转，next 代表下一步。
  简单的说，from是从哪里来，to是要到哪里去，next是下一步要怎么走。是不是通俗易懂了好多嘞~
因此根据上面的知识，我们的代码可以编写成如下：

```js
router.beforeEach((to,from,next) =>{})
```

这里，将 beforeEach 的参数写成箭头函数的形式，接下来就是添加函数内部的代码。
  传入的参数肯定是要用起来的，就像别人给你的零食要尽早吃，否则就过期来一样。所以我们要在箭头函数中使用 上面传入的参数，具体如下：

```js
router.beforeEach((to,from,next) =>{
    document.title=to.title
    next()
})
```

next()参数一定要记得调用，很简单的道理，如果你不知道下一步，那你怎么继续走下去呢， 紧接着是to。回顾上面的提出的需求，我们要根据跳转到的页面修改标题，所以通过 to.title 来获取要跳转的页面的标题，将标题赋值给document.title。
  上面呢，就是使用 beforeEach 确定跳转变化的具体代码。但是我们还有个地方不知道具体的数值，你猜是谁？没错，我听到了，就是 to.title  这就是要讲的第二个步骤。

##### ② 使用 meta 确定跳转变化的内容

其实meta 很简单，就在路由的配置中添加上下面的代码就可以了，

```js
{
            path:'/profile',
            component:Profile,
            meta:{
                title:'档案'
            }
        }
```

在配置“档案”页面的路由中添加了meta，当用户点击 “档案”跳转到档案页面时，该页面的标题就会变成“档案”。于是乎，一口气在“首页”、“关于”、“用户”页面的路由配置中都添加了meta属性，接下来让我们一起看看效果吧~

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/357.gif)

好啦，我知道出现bug了  因为首页的标题显示的是“undefined”  这到底为啥呢 
  后来知道了，因为在首页中有路由的嵌套。嵌套是啥？

于是乎打印了一下 to 是个啥玩意儿，不打不知道，一打下一跳：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/358.png)

发现 meta 并没有任何东西，但是在**matched 中的 meta 却有**，说明我们获取title的时候不应该用 to.title 而应该用 to.matched[0].mate.title 。所以结合上面两步给出的完整代码应该是这样子的：

```js
const router = new VueRouter({
    routes:[
        {
            path:"",
            redirect:'/home'
        },
        {
            path:'/home',
            component:Home,
//使用 meta 确定路由跳转时要修改的值
            meta:{
                title:'首页'
            },
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
        {
            path:'/about',
            component:About,
            meta:{
                title:'关于'
            }
        },
        {
            path:'/user/:userID',
            component:User,
            meta:{
                title:'用户'
            }
        },
        {
            path:'/profile',
            component:Profile,
            meta:{
                title:'档案'
            }
        }
        
    ],
})
//使用 beforeEach 函数确定转换
router.beforeEach((to,from,next) =>{
    document.title=to.matched[0].meta.title
    console.log(to)
    next()
})
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/359.gif)

# 三、keep-alive

## 1.产生原因

  我们还是拿以前的例子。假设张三在 home 页面中点击了“消息”子内容；当张三点击别的页面后再重新回到 home 页面，就会发现原本在home 中点击的“消息”页面又回到了“新闻”页面。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/360.gif)

那张三想要保留走之前的样子，要怎么做呢？

很简单，通过 keep-alive 就可以做到了。

## 2.基本原理

  首先得很负责的告诉你，组件的创建和销毁是一个不断循环的过程，这点可以通过两个生命函数来验证 create 和 destroyed。

```js
//在 home.vue 页面添加两个生命函数
<script>
    // 声生命函数
    export default {
        name:'home',
        created(){
            // document.title="首页"
            console.log("home 被创建啦 ^_^")
        },
        destroyed(){
            console.log("home 被销毁了 o(╥﹏╥)o")
        }
    }
</script>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/361.gif)

这也就是为什么张三离开首页之后，首页的子内容就由“消息”变成了“新闻”。每当张三离开的时候，将相当于重新创建了 home 页面，而 home 页面中默认的就是显示“新闻” 的内容。
  于是乎就有了下面的结论：只要组件不是新创建的，就能保持离开时的模样，而kepp-alive 就可以做到这一点。

## 3.使用步骤

其实 keep-alive 的使用很简单。只需要将 router-view 包起来

```vue
    <!-- index.js 页面 -->
<template>
    <div>   
        <button @click="HomeClick">首页</button>
        <button @click="AboutClick">关于</button>
        <button @click="UserClick">用户</button>
        <button @click="ProfileClick">档案</button>       
        <keep-alive>
            <router-view></router-view>
        </keep-alive>
    </div>  
</template>
```

![![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/362.gif)
实现：使用了keep-alive后，组件从始至终只创建了一次，而且没有被销毁。
Bug：当张三第二次进去 home 页面时，home 页面显示的依旧是“新闻”的内容 

这里后面改...

## 4.activated / deactivated 生命函数

##### ① 定义

  这两个函数是为了解决上面的bug。activated，译为“活跃”的，也就是当组件处于活跃状态时将会回调的生命函数。相反，deactivated就是组件处于不活跃的状态。

##### ② 使用场景

  但是这两个生命函数并不是所有的场景都使用的。只有**使用了 keep-alive 时才可以使用。**

##### ③ 应用场景

  这里主要用 activated 函数解决上面的bug，此外我们还需要另外一个工具：beforeRouteLeave。beforeRouteLeave 是导航守卫之一，意思就是在监听离开路由时的事情。
因此，对付上面那种胡搅蛮缠的bug，解决思路如下：
**第一：删除嵌套路由** 
  bug 的产生是因为一开始在 home 的地址中就有嵌套的路由，即 URL 的地址不仅仅是 /home 而是/home/news 或者 /home/message 。所以我们要先对嵌套的路由进行处理。打开 index.js 在路由映射关系中有关嵌套路由的代码 children 内部删除注释掉的代码：

```js
{
    path:'/h ome',
    component:Home,
    meta:{
            title:'首页'
    },
    children:[
        // {
        //  path:"",
        //  redirect:"new"
    // },
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
```

**第二：获取相关路径**
  既然我们不能在嵌套路由中使用类似“/home/new” 的路径，我们就先**定义一个变量path 来存放在嵌套路由中的路径“/home/new”；**
  然后使用 **activated** 生命函数，当 home 页面被激活的时候获取当前激活状态下的路径；
  最后使用 **beforeRouteLeave** 导航守卫，记录用户离开页面时的路径，并将该路径赋值给变量path，这样当用户再次进入到该页面时，就会使用离开后的路径。具体代码如下所示：

```vue
<script>
// home.vue 文件
    export default {
        name:'home',
        data (){
            return {
                    path:'/home/new'
            }
        },
        created(){
            document.title="首页"
            console.log("home 被创建啦 ^_^")
        },
        destroyed(){
            console.log("home 被销毁了 o(╥﹏╥)o")
        },
        activated() {
            this.$router.push(this.path)
        },
        beforeRouteLeave(to,from,next){
            this.path = this.$route.path;
            next();
        }
        
    }
</script>
```

看结果：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/363.gif)

#### 总结：

#### 1.keepl-alive

  当用户离开某个组件的时候，不要让该组件频繁的被创建和频繁的被销毁

#### 2.activeted 和 dectivated

  译为“活跃的/不活跃的”这两个生命函数只有在使用了keep-alive 才可以使用

# 四、keep-alive属性

前期回顾：
**1.keep-alive**
  ① 是Vue提供的一个**抽象组件**，用来对组件进行**缓存**，避免组件重复渲染。简单的说就是不让组件不断的被创建和被销毁；
  ② 被包裹在keep-alive中的**组件状态将会被保留**，即你离开是什么状态，回来也是什么状态。
**2.activated、deactivated**
  两个生命函数只有**在使用了 keep-alive 组件才可以使用**，代表的意义是组件被**创建**、组件被**销毁**。
  今天小编为大家补充有关 keep-alive 的另外知识--**include和exclude** 属性

## 一、定义

**1.include**
  译为包含，代表**只有被包含的组件才会被缓存**。也就是说被 include 包含的组件不能够被频繁的创建和被频繁的销毁。
**2.exclude**
  顾名思义，译为不包含，代表**被exclude包含的组件不会被缓存**。也就是说，exclude包含的组件能够被频繁的创建和频繁的销毁。

## 二、产生原因

  我们知道，所有的子组件最终都会通过 router-view 渲染的，当我们将 keep-alive 包裹住 router-view 时，就意味着所有的组件都被缓存了。演示一遍。
  首先有 home、about、user、profile 四个组件，分别往四个组件中添加 create 函数 和 destoryed函数。再往 home 组件中增加 activated。最后用 keep-alive 包裹 router-view 。具体代码如下：

```vue
<script>
// home.vue 组件
    // 生命函数
    export default {
        name:'home',
        data (){
            return {
                    path:'/home/new'
            }
        },
        created(){
            document.title="首页"
            console.log("home 被创建啦 ^_^")
        },
        activated() {
            document.title="首页"
            console.log(" home 被激活了")
            this.$router.push(this.path)
        },
        destroyed(){
            console.log("home 被销毁了 o(╥﹏╥)o")
        },
        beforeRouteLeave(to,from,next){
            this.path = this.$route.path;
            next();
        }
    }
</script>
```

```vue
<!-- about 组件  -->
<template>
    <div>
        <h1>关于</h1>
        <span>在这里你可以问到任何你想问的问题</span>
    </div>
</template>

<script>
    // 生命函数
    export default{
        name:'about',
        created(){
            document.title="关于",
            console.log("about 被创建了 (*^▽^*)")
        },
        destroyed(){
            console.log("about 被销毁了 o(╥﹏╥)o")
        }
    }
</script>
```

其余 user 和 profile 组件和 about 组件的内容一样，在这就不一一展示出来了

```vue
<!-- App.vue组件 -->
<template>
    <div>
        <button @click="HomeClick">首页</button>
        <button @click="AboutClick">关于</button>
        <button @click="UserClick">用户</button>
        <button @click="ProfileClick">档案</button>
        
        <keep-alive>
            <router-view></router-view>
        </keep-alive>

    </div>  
</template>
```

最后我们来看看效果：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/364.gif)

首先位于 home 页面时，会显示 home 被创建了/被激活；随后当点击“用户”、“关于”、“档案” 页面时，显示相应的页面被创建了。这说明被keep-alive 包裹住的组件都被缓存了。
  但是有时候，我们希望**有些组件不被缓存，而有些组件被缓存**。也就是有些组件能够被频繁的创建和销毁而有些组件不能够被频繁的创建和销毁，单单靠keep-alive是不能够实现的，必须借助它了属性 exclude。



## 三、使用步骤

  其实 exclude 的使用方法十分简单，仅仅**将 exclude="name" 嵌套在 keep-alive 标签里面**即可。参数**name**就是某个页面在**Vue实例中的name**。
比如：

```vue
<keep-alive exclude="profile">
    <router-view></router-view>
</keep-alive>
```

上述代码的作用了，除了 profile 组件，其余的组件都不能被频繁的创建和销毁，具体可观看下面的动图：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/365.gif)

**当用户想要排除两个页面时，通过逗号隔开。**
比如：

```vue
<keep-alive exclude="profile,user">
    <router-view></router-view>
</keep-alive>
```

上述代码的作用，除了 profile 和 user 组件，其余的组件都不能被频繁的创建和销毁，具体可观看下面的动图：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/366.gif)

#### 总结:

**1.组件：keep-alive**
  是Vue提供的抽象组件，只要有它在，任何组件都别想随所欲为的创建和销毁并且保留组件的某个状态
**2.函数：activated 和 deactivated**
  两个生命函数，当组件被创建和被销毁时会被调用执行，只有字keep-alive组件使用的情况下才有效
**3.属性：include 和 exclude**
  被include 包含的组件不能为所欲为的创建和销毁，而被exclude 包含的组件则可以放飞自我的不断创建和不断销毁。