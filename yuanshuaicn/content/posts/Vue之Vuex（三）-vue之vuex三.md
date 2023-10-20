---
title: Vue之Vuex（三）
date: 2021-11-30 10:45:12.595
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/1_7OCwu--TWqVluPMsZdzWKw-34ce1bcaed3b4c59a2183cf00af73987_1622733997113.png'
theme: 'light'
tags: 
- Vue
---

# Vuex的action

## 一、action

##### 1.产生原因

  当**在mutations中进行异步操作时，Devtool不能够实时跟踪，导致最终在Devtool中记录的是错误的信息**。比如：

```jsx
const store = new Vuex.Store({ 
    state:{
      info:{
        name:'haha',
        age:13,
        height:1.45
      }
    },
   mutations:{
      changeInfo(state){
        // 异步操作
        setTimeout(()=>{
          state.info.name='哈哈'
        },1000)
      }
    }
})
```

上述代码主要是同异步操作来将“haha”修改成“哈哈”。结果如图所示：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/419.png)

![420](img/420.png)

当在mutations使用异步操作时，虽然页面中的数据修改了，但是在Vuex总state记录的仍旧是以前的数据。
  其实数据是修改成功了，但是mutations中的Devtool在跟踪时没有记录，就导致记录的的错误的信息。
  所以不能再mutations中进行一步操作，这时我们就需要action帮我们进行一步操作。

##### 2.定义

Action类似于Mutation, 但是是**用来代替Mutation进行异步操作的**。

##### 3.使用方式

使用方式和mutations类似，但是有两点不同:
**① 参数**：传入的参数是**context**。该参数相当于store对象
**② 调用方式**：使用**dispatch**调用，而不是使用commit
具体见下面的代码：

```jsx
const store = new Vuex.Store({ 
   mutations:{
      state.info.name='哈哈'
      },
    actions:{
      achangeInfo(context){
        setTimeout(()=>{
          context.commit('changeInfo')
        },1000)
      }
    },
})
```

```js
<script>
import store from '../store/index.js'
export default {
  name: 'App',
  methods:{
    changeInfo(){
      this.$store.dispatch('achangeInfo')
    }
  }
}
</script>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/421.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/422.png)

可以发现mutations中的Devtool记录的数据也跟着发生了改变。

##### 4.传递参数

传递参数的方式和mutations类似，下面进行简单的传递字符串参数，具体代码如下：

```jsx
const store = new Vuex.Store({ 
   mutations:{
      state.info.name='哈哈'
      },
    actions:{
      achangeInfo(context,payload){
        setTimeout(()=>{
          context.commit('changeInfo')
          console.log(payload)
        },1000)
      }
    },
})
```

```js
<script>
import store from '../store/index.js'
export default {
  name: 'App',
  methods:{
    changeInfo(){
      this.$store.dispatch('achangeInfo','我是payload')
    }
  }
}
</script>
```

发现传递参数也是可以的，并且成功在控制台中显示了。

##### 5.action内部使用Promise

  当数据commit之后就意味着修改成功了，此时**想要告诉外界，数据已经修改成功了并且除了修改数据之外我们还可以做别的操作**。该需求可以用Promise实现，具体先看下面代码：

```jsx
   actions:{
      achangeInfo(context,payload){
        return new Promise((resolve,reject) =>{
          setTimeout(()=>{
            context.commit('changeInfo')
            console.log(payload)
            resolve('1111')
          },1000)
        })
      }
    }
```

```js
<script>
    changeInfo(){
      this.$store
      .dispatch('achangeInfo','我是payload')
      .then(res =>{
        console.log(res)
      })
    }
</script>
```

上述代码的主要作用是当数据修改成功之后，就在控制台上打印“1111”。具体思路是当action运行到commit方法时，就会执行changeInfo函数，然后在回调changeInfo函数。
  本案例中，当执行achangeInfo函数时，就返回 Promise，而achangeInfo是通过dispatch调用的，其上述的代码可以等价为下面的代码：

```js
<script>
    changeInfo(){
      //this.$store
     //.dispatch('achangeInfo','我是payload')
     new Promise((resolve,reject) =>{
          setTimeout(()=>{
            context.commit('changeInfo')
            console.log(payload)
            resolve('1111')
          },1000)
      .then(res =>{
        console.log(res)
      })
    }
</script>
```

以上就是action相关的知识，总结起来就是两点：**处理异步操作和在内部使用Promise。**

# Vuex的module

# 一、module

##### 1.产生原因

Module是模块的意思, 为什么在Vuex中我们要使用模块呢?
  Vue使用单一状态树,那么也意味着很多状态都会交给Vuex来管理，当应用变得非常复杂时,store对象就有可能变得相当臃肿.
  为了解决这个问题, **Vuex允许我们将store分割成模块(Module), 而每个模块拥有自己的state、mutation、action、getters等。**

##### 2.使用方式

  正如上面所提到的，我们可以在module中声明多个模块，然后在每个模块中书写自己模块的state、mutation、action、getters。下面进行逐步的展示。

###### ① module中的state

**1-1 module中state的定义**
定义的方式和store中定义state的方式一样，具体见下面的代码：

```js
//单独模块的书写const moduleA = {  state:{    name:'我是moduleA的name'  }}//在store对象声明模块Aconst store = new Vuex.Store({    modules:{        a:moduleA    }})
```

在store对象的module中声明了moduleA，并且moduleA自己的state中有一个name变量。
**1-2 module中state的使用**

```vue
<template>  <div id="app">    <h2>-----Vuex中module的测试</h2>    <h2>{{$store.state.a.name}}</h2>  </div></template>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/423.png)

如果要使用moduleA中的name值时，通过**state获取而不是module，因为在module定义的a模块最终会被渲染到state中。**

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/424.png)

###### ② 模块中的mutations

**2-1 module中mutations的定义**
定义的方式和store中的mutations定义的方式一样，具体见下面的代码：

```js
//单独模块的书写const moduleA = {  state:{    name:'我是moduleA的name'  }，  mutations:{    updataName(state,payload){      state.name = payload    }  }}//在store对象声明模块Aconst store = new Vuex.Store({    modules:{        a:moduleA    }})
```

声明了 根据用户传递的参数修改姓名的updataName方法
**2-2 module中mutations的使用**
使用方式和store对象中的mutations一样，**通过commit方法提交**，具体代码如下：

```vue
<template>  <div id="app">    <h2>-----Vuex中module的测试</h2>    <h2>{{$store.state.a.name}}</h2>    <button @click="updateName">修改名字</button>  </div></template><script>export default {  name: 'App',  methods:{    updateName(){      this.$store.commit('updataName','李四')    }  }}</script>
```

点击按钮，将修改成“李四”。效果如下：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/425.gif)

这里要注意两点：
  第一，**命名不重复**。模块内的mutations和store对象的mutations命名不要重复，否则浏览器不知道提交谁的mutations
  第二，**查找顺序**。使用commit方法提交时，首先会查找store对象的mutations，看看是否有该方法，如果没有就是去模块内的mutations查找。

###### ③ 模块中的getters

**3-1 模块中getters的定义**
  定义的方式和在store中定义的方式类似。不同的是，如果在模块中想要**使用store中state内的变量，需要通过rootState参数进行获取**，具体代码如下：

```js
//单独模块的书写const moduleA = {  state:{    name:'我是moduleA的name'  }，  getters:{    //1.普通应用    fullname(state){      return state.name +'1111'    },    //2.应用本模块的getters    fullname2(state,getters){      return getters.fullname +'2222'    },    fullname3(state,getters,rootState){      return getters.fullname2 + rootState.counter    }  }}//在store对象声明模块Aconst store = new Vuex.Store({    modules:{        a:moduleA    }})
```

用法1是**简单的getters应用**，将name的值后面追加上“1111”；
用法2是**获取本模块中的getters**，然后对获取到的结果再追加上“2222”；
用法3是**获取store中的counter**，通过rootState获取，然后再追加到到fullname2的结果中。
**3-2 模块中getters的使用**
使用方式和store中的类似，具体代码如下：

```vue
<template>  <div id="app">    <h2>-----Vuex中module的测试-----</h2>    <h2>{{$store.getters.fullname}}</h2>    <h2>{{$store.getters.fullname2}}</h2>    <h2>{{$store.getters.fullname3}}</h2>  </div></template>
```

其效果如下所示：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/426.png)

###### ④ module的action

**4-1 定义的方式**

```jsx
//单独模块的书写const moduleA = {  state:{    name:'我是moduleA的name'  }，  mutations:{    updataName(state,payload){      state.name = payload    }  },  actions:{    aupdateName(context){      setTimeout(()=>{        context.commit('updataName','王五')      },10)    }  }}//在store对象声明模块Aconst store = new Vuex.Store({    modules:{        a:moduleA    }})
```

通过setTimeout来模拟异步操作，修改name的值为“王五”
**4-2 使用方式**

```vue
<template>  <div id="app">    <h2>-----Vuex中module的测试</h2>    <h2>{{$store.state.a.name}}</h2>    <button @click="asycnUpdateName">异步修改名字</button>  </div></template><script>export default {  name: 'App',  methods:{    asycnUpdateName(){      this.$store.dispatch('aupdateName')    }  }}</script>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/427.gif)

这里需要注意的是，在模块中，**context.commit()仅仅提交自己模块的方法而不包括store中的方法。**
  以上就是今天Vuex中module的介绍，主要详细的介绍了**在模块内如如何定义和使用state、mutations、getters、action。**

# Vuex总结篇

# 一、store的目录结构

  在前面，我们都将 state、mutations、getters、actions、module 放在index.js文件中，当代码量多的时候，就不利于管理，所以我们一般都将 mutations、getters、actions、module 抽离出去。具体的抽离结构如下：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/428.png)

分别将getters、actions、module、mutations抽离成单独的文件，如果在store中的module中又包含了很多的小模块，就将小模块放到modules文件夹下面，然后每个单独是小模块又是单独的文件。
  我们一般不将state抽离出去，而是放在index.js文件中便于查看当前有哪些状态是被共享的。抽离后的index.js代码如下：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/429.png)

下面进行Vuex的知识总结。

# 二、Vuex基本概念

### 1.定义

Vuex是一个管理共享状态的管家，并且该状态是响应式的

### 2.状态管理

主要分成单界面管理和多界面管理

##### ① 单界面管理

  首先明白三个概念：State：状态；View：视图层；Actions：主要是用户的各种操作：点击、输入等等，会导致状态的改变。
  而所谓的单界面就是指State、View、Action都是在同一个页面中的，所以叫做单页面状态管理。具体运作方式如下图：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/430.png)

##### ② 多界面状态管理

  多界面状态管理就是指当某个状态被多个界面同时共享时，我们需要一个管家来管理这个被多界面共享的状态。具体的运作过程见下图：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/431.png)

### 3.使用方式

首先在终端安装Vuex，然后创建Vuex实例，最后书写相关的代码。具体使用如下图：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/432.png)

# 三、核心概念

Vuex中包含了5个核心概念，分别是：**state、actions、getters、module、mutations。**

### 1.state--单一状态树

  为了**不让状态信息保存到多个Store对象中的**，Vuex使用了单一状态树来管理应用层级的全部状态。
  也就是在**项目中最好只new 一个store对象**，然后所有状态有关的东西都放在一个store中的state进行统一的管理。

### 2.getters

##### ① 定义

  getters 相当于计算属性。**当数据要经过一系列变化时，将这一系列的变化写在getters内部。**

##### ② 使用

  在getters中声明一个方法，该方法的**默认参数是state**，然后返回相应的结果即可。具体代码如下:

```js
//index.jsgetters:{      powerCounter(state){        return state.counter * state.counter      }    }
```

```vue
<!--App.vue 文件--><template>  <div id="app">    {{$store.getters.powerCounter}}  </div></template
```

##### ③ 参数

  getters中出了默认的state之外，还可以传递**getters参数**，用来获取当前getters中的方法。比如：

```jsx
const store = new Vuex.Store({    state:{      student:[        {id:100,name:'zhangsan',age:21},        {id:200,name:'lisi',age:18},        {id:300,name:'wangwu',age:22},        {id:400,name:'zhaoliu',age:19}      ]    },    getters:{      morestu(state){        return state.student.filter(s => s.age>20)      },      morestuLength(state,getters){        return getters.morestu.length      }    }})
```

在morestuLength方法中通过参数getters获取了morestu方法的结果

##### ④ 返回值

返回值可以返回**变量**，还可以返回**函数**。比如：

```jsx
//返回变量getters:{      powerCounter(state){        return state.counter * state.counter      }    }//返回函数    getters:{      moreAgeStu(state){        return function(age){          return state.student.filter(s => s.age>age)        }      }    }
```

### 3.mutations

##### ① 作用

  **Vuex的store状态的更新唯一方式：提交Mutation**。也就是我们前面提到过的，必须经过 state --> Vuex --> Action --> Mutations 这个路径修改state的状态。

##### ② 组成

主要由**事件类型**和**回调函数**组成。比如：

```js
mutations:{    increase(state){      state.counter++      }
```

increase时间类型，(state){ state.counter++}是回调函数

##### ③ 定义方式

主要是**事件类型+回调函数**，具体代码如上所示，这里不再展示

##### ④ 参数

传递的参数主要有默认的**state、变量和函数**

##### ⑤ 提交风格

主要有两种方式，一种是**commit**，一种是**type**。

##### ⑥ 响应规则

  相应的原理是**预先在store对象定义的属性会被加入到响应式系统中**，而没有预先在store对象定义的属性不会被加入到响应式系统中，但是可以通过**set方法**使新增加的属性具有响应式。

##### ⑦ 常量类型

  **使用常量替代Mutation事件的类型**。将这些常量放在一个单独的文件中, 方便管理以及让整个app所有的事件类型一目了然。

### 4.actions

##### ① 定义

Action类似于Mutation, 但是是**用来代替Mutation进行异步操作的**。

##### ② 使用

使用方式和mutations类似，只是默认的参数是**context**，而且调用时是用**dispatch**调用的。

##### ③ 参数

传递的参数很简单，这里不叙述

##### ④ 在内容使用Promise

  使用Promise的目的是，**除了修改数据之外还可以进行别的操作**。一般将修改的数据写在Promie内部，然后别的操作写在then函数内

### 5.module

  Vuex允许我们将store分割成模块(Module), 而每个模块拥有自己的state、mutation、action、getters等。
  mudole中的state、mutation、action、getters方式的书写和在store中书写是一样的，这里就不再叙述，不过要注意的是：在模块中，**context.commit()仅仅提交自己模块的方法而不包括store中的方法。**

