---
title: Vue之Vuex（一）
date: 2021-11-30 10:45:04.235
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/1_7OCwu--TWqVluPMsZdzWKw-34ce1bcaed3b4c59a2183cf00af73987_1622733997113.png'
theme: 'light'
tags: 
- Vue
---

# Vuex

## 一、基本概念

##### 1.定义

  **Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。**它采用 **集中式存储管理** 应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。看不懂就对了，因为这是官方的解释，下面看看小编的解释 (✿◡‿◡)
  当组件1、组件2、组件3 三个组件之间**共享某些状态**的时候，我们不能将该状态定义在组件1中，也不能定义在组件2中，也不能定义在组件3中，因为我们没有办法确保三个组件之间是有关联的。
  如果我们将该状态定义在组件1中，然后组件3想要用该状态，但是组件1在组件树的顶层，而组件3却在组件数的最底层，这样一层一层调用十分复杂，因此我们需要另外一个东西来存放并且管理组件之间共享的状态，这个东西就是Vuex。
  综上所述，**Vuex是一个管理共享状态的管家，并且该状态是响应式的**。简单而又精辟的解释

##### 2.管理什么状态？

  不是所有的状态都可以放到Vuex的，只有**多个组件中可能会共享的状态**才放到Vuex中，比如下面的这些：
**① 用户相关**：用户的登录状态、用户名称、头像、地理位置信息等等。
**② 商品相关**：商品的收藏、购物车中的物品等等。
  这些状态信息，我们都可以放在统一的地方，对它进行保存和管理，而且它们还是响应式的

## 二、状态管理

##### 1.单页面状态管理

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/403.png)

从上面的图给大家解释何为单页面状态管理：
  首先**state代表状态**，可以简单理解成变量，因为变量也是可以用来保存状态的；**View代表视图**，状态的显示是通过视图显示的，所以State指向View；
  然后如果视图中有一些**行为**，就会传递给**Action**，所以View指向Actions；
最后该行为可能会**修改某些状态**，所以Action指向了State，具体的代码如下:

```vue
<template>
  <div id="app">
   <h2>{{message}}</h2>
   <h2>{{counter}}</h2>
   <button @click="counter++" >+</button>
   <button @click="counter--" >-</button>
  </div>
</template>

<script>
export default {
  name: 'App',
    data(){
        return {
            message:"我是标题",
            counter:0
        }
    }
}
</script>
```

在该页面中，message 和 counter 都可以看成是State，然后 template 看作是View，@click点击事件可以看成Action。message的内容在template中显示，也是证实了State指向View；template中的button增加了点击事件，证实了View指向Action；当用户点击了“+ - ”时会就修改counter的数值，证实了Action指向State。
**因为State、View、Action都是在同一个页面中的，所以叫做单页面状态管理。**

##### 2.多页面管理

  在上面例子的基础上，我们在创建一个页面，该页面和上个例子中的页面共享counter变量，我们用vuex实现的步骤如下：
**① 安装vuex**：通过命令 **npm install vuex --save** 安装
**② 引用插件：**因为vuex是一个插件，所以我们要通过 **Vue.use()**来引用插件
**③ 创建实例**：通过命令 **const store = new Vuex.store（）**命令来创建，这里要注意的是，我们**创建的是Vuex插件中的store方法**
**④ 书写共享状态：**在Vuex中一般有固定的内容填写，包括：state、mutations、action、getters、modules
**⑤ 使用共享状态**：通过 **$store.state.XXX**来使用，代码具体如下：

```vue
//vuex代码
import Vue from 'vue'
import Vuex from 'vuex'

// 1.引用插件
Vue.use(Vuex)
// 2.创建实例
const store = new Vuex.Store({
    state:{
      counter:100
    },
    mutations:{

    },
    actions:{

    },
    getters:{

    },
    modules:{

    }
})
//3.导出实例
export default store
```

```vue
<!--第一个组件的代码-->
<template>
  <div id="app">
    <h2>{{message}}</h2>
    <h2>{{$store.state.counter}}</h2>
    <button @click="counter++" >+</button>
    <button @click="counter--" >-</button>

    <hellovue></hellovue>
  </div>
</template>

<script>
  import hellovue from './components/HelloWorld.vue'
export default {
  name: 'App',
  components:{
    hellovue
  },
    data(){
        return {
            message:"我是组件1",
        }
    }
}
</script>
```

```vue
<!--第二个组件的代码-->
<template>
  <div class="hello">
    <h2>{{message}}</h2>
    <h2>{{$store.state.counter}}</h2>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data(){
    return{
      message:"我是组件2"
    }
  }
}
</script>
```

在上面的代码中，我们将共享的变量 counter 放在了vuex.store 的 state 中，并且通过 **$store.stats.counter** 使用该变量。效果图如下所示：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/404.png)

在此，已经实现了counter的共享，但是我们还没有实现当用户点击“+ - ”时counter的变化状态，下面小编先给你们分析一下原理



##### 3.状态变化的原理

  基于上面的例子，如果当多个组件都想要修改counter的值时，就会可能遇到这样的情况，就是**不知道到底是谁修改过counter的值**，这样不利于后面代码的追踪，所以我们希望有一个东西来记录谁修改过状态，而下面的这幅图就是状态改变的实质

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/405.png)

状态改变可以有两条路径：
**①Vue Components --> State**
  通过这条路径修改状态的话，就会发生刚刚说的事情，**根本不知道谁修改过state**，所以官方是不建议这样直接修改state
**② Vue Components --> Action --> Mutations --> State**
  **Mutations:处理同步操作**，内部有个叫 **Devtools**的插件，该插件的作用就是**记录修改Vuex的状态**；
**Action ：处理异步操作**，比如网络请求，所以Action 的另外一头指向了backend（后端）

# Vuex基本使用和核心概念

## 一、基本使用1 - devtool

##### 1.定义

devtool 是浏览器的一个**插件**，主要用来**记录和跟踪被修改过的state状态。**

##### 2.安装步骤

既然是一个插件，那么我们首先的在浏览器中安装好，下面是安装的具体步骤：
  点击Chrome浏览器**右上角三个小点** --> **更多工具 \**--> \*\*拓展程序 \*\*--> 点击 “\*\*打开Chrome网上应用商店\*\*” --> 在搜索框中输入 \*\*“devtools”\*\* -->安装\**“Vue.js.devtools”** 即可。
  安装完成之后，重新关闭浏览器，然后在浏览器中打开自己创建好的项目，如果在控制台的菜单栏中看到有**Vue的字样**说明安装成功了。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/406.png)

##### 3.切换至Vuex页面

在上面安装好的插件中，我们可以点击 **“Vuex”**切换到Vuex来查看Vuex相关的东西。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/407.png)

在这个页面中，我们就可以实时跟踪Vuex一些状态的变化。下面我们将昨天没有讲解到的通过 Vue Components --> Action --> Mutations --> State 这个路径修改的状态通过mutation来实现。

## 二、基本使用2 - Mutations

##### 1.定义

  通俗的理解就是里面装着一些**改变数据方法的集合**。把处理数据逻辑方法全部放在mutations里面，使得数据和视图分离。

#### 切记：Vuex中store数据改变的唯一方法就是mutation！

##### 2.用法

**① 在mutations中书写先关的操作**。其实就是在mutation声明方法，代码如下所示：

```js
const store = new Vuex.Store({
    state:{
      counter:100
    },
    mutations:{
      increase(state){
        state.counter++
      },
      decrease(state){
        state.counter--
      }
    }
})
```

任何修改Vuex状态的数据都在mutations中处理，上述代码中，声明了两个方法 increase 和 decrease，默认的参数传递的都是state，然后主要的操作就是对counter进行加法运算和减法运算。
**② 在组件中引用mutations**
  在写好了相关的处理操作后，我们在需要的页面中引用mutations即可，引入时不是直接引入mutation的方法，而是通过commit提交相应的方法。具体代码如下：

```vue
<template>
  <div id="app">
    <h2>{{message}}</h2>
    <h2>{{$store.state.counter}}</h2>
    <button @click="addcounter" >+</button>
    <button @click="deccounter" >-</button>
    <hellovue></hellovue>
  </div>
</template>

<script>
  import hellovue from './components/HelloWorld.vue'
export default {
  name: 'App',
  components:{
    hellovue
  },
  methods:{
    addcounter(){
      this.$store.commit('increase')
    },
    deccounter(){
      this.$store.commit('decrease')
    }
  },
    data(){
        return {
            message:"我是组件1",
        }
    }
}
</script>
```

上述代码中，定义了adcounter和deccounter两个方法，在两个方法中分别通过 **this.$store.commit('decrease')** 来获取mutations中的方法。实现后的效果如下所示

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/408.gif)

当点击 “+或者 - ”时，右上方就会跟踪到increate或者decreate方法，当点击其中一个方法时，在右下方会显示该状态下的counter具体数值是多少，从而达到了追踪和记录的目的。

## 三、核心概念1 - state单一状态树

  Vuex中有五个核心的概念，分别是 **state、mutaion、action、getter、module**。首先在state中有个比较关键的概念--**单一状态树**

##### 1.引入背景

为了便于大家的理解，小编这里例举了一个例子。
  我们知道，在国内我们有很多的信息需要被记录，比如上学时的个人档案，工作后的社保记录，公积金记录，结婚后的婚姻信息，以及其他相关的户口、医疗、文凭、房产记录等等（还有很多信息）。
  这些信息被分散在很多地方进行管理，有一天你需要办某个业务时(比如入户某个城市)，你会发现你需要到各个对应的工作地点去打印、盖章各种资料信息，最后到一个地方提交证明你的信息无误。
  这种保存信息的方案，不仅仅低效，而且不方便管理，以及日后的维护也是一个庞大的工作(需要大量的各个部门的人力来维护，当然国家目前已经在完善我们的这个系统了)。

##### 2.定义

英文名称是Single Source of Truth，也可以翻译成单一数据源。
  为了**不让状态信息保存到多个Store对象中的**，Vuex使用了单一状态树来管理应用层级的全部状态。单一状态树能够让我们最直接的方式找到某个状态的片段，而且在之后的维护和调试过程中，也可以非常方便的管理和维护。
  用一句话来概括就是：**在项目中最好只new 一个store对象，然后所有状态有关的东西都放在一个store中的state进行统一的管理**