---
title: Vue之Vuex（二）
date: 2021-11-30 10:45:08.6
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/1_7OCwu--TWqVluPMsZdzWKw-34ce1bcaed3b4c59a2183cf00af73987_1622733997113.png'
theme: 'light'
tags: 
- Vue
---

# Vuex的getters

## 一、getters

##### 1.定义

  getters 相当于我们之前使用过的计算属性。**当数据要经过一系列变化时，我们就可以将这一系列的变化写在getters内部。**

##### 2.使用方法

  getters的使用方法和mutation一样，通过声明**方法**然后在方法中书写相应的代码即可并且**默认的参数是state**。比如当我们计算counter的平方时可以有下面两种做法。
**做法一：直接运算**

```html
<h2>{{$store.state.counter * $store.state.counter}}</h2>
```

这种做法就是直接将运算也放在了显示内容上，如果别的页面中同样需要用到counter平方时，我们也需要在引入上面的代码，不仅仅利用不方便而且代码量看起来也快很多，所以一般我们都采用第二种做法。
**做法二：使用getters**

```js
//index.js文件
getters:{
      powerCounter(state){
        return state.counter * state.counter
      }
    }
```

```vue
<!--App.vue 文件-->
<template>
  <div id="app">
    <h2>----做法一------</h2>
    <h2>{{$store.state.counter * $store.state.counter}}</h2>
    
    <h2>--------做法二：getters的使用--------</h2>
    {{$store.getters.powerCounter}}
  </div>
</template
```

 首先在getters中声明了名字为**powerCounter的方法**，然后在方法中**计算counter的平方**，最后通过**调用 $store.getters.powerCounter** 就可以直接将counter的平方显示在页面上。下面是效果图

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/409.png)

上述的getters只是处理的单一数值运算，下面看看用getters处理别的案例。现在state中有多个学生，但只显示年龄大于20岁的学生，具体代码如下：

```js
//index.js 文件
const store = new Vuex.Store({
    state:{
      student:[
        {id:100,name:'zhangsan',age:21},
        {id:200,name:'lisi',age:18},
        {id:300,name:'wangwu',age:22},
        {id:400,name:'zhaoliu',age:19}
      ]
    },
    getters:{
      morestu(state){
        return state.student.filter(s => s.age>20)
      }
    }
})
```

```vue
<template>
  <div id="app">
    <h2>-------年龄大于20岁的人-------</h2>
    <h2>{{$store.getters.morestu}}</h2>
  </div>
</template>
```

上述代码中，在getters声明了名字为 morestu 的方法，默认参数是state，在该方法中通过 state.student 获取到学生的对象，然后通过filter对年龄大于20岁的学生进行筛选，最后再App.vue页面中引用该方法，具体效果图如下：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/410.png)

##### 3.参数

getters除了可以传递默认的state参数之外，还可以传递getters参数。比如下面的例子：

```jsx
const store = new Vuex.Store({
    state:{
      student:[
        {id:100,name:'zhangsan',age:21},
        {id:200,name:'lisi',age:18},
        {id:300,name:'wangwu',age:22},
        {id:400,name:'zhaoliu',age:19}
      ]
    },
    getters:{
      morestu(state){
        return state.student.filter(s => s.age>20)
      },
      morestuLength(state,getters){
        return getters.morestu.length
      }
    }
})
```

上述代码中，我们希望在知道了年龄大于20岁的学生之后再求出年龄大于20岁的学生的人数，只要在上述的基础上添加length方法即可。
  在morestu方法中，我们求出了年龄大于20岁的学生，因此我们可以在morestuLength 求年龄大于20岁的个数的方法中添加参数getters，然后再通过 getters.morestu.length 求年龄大于20岁的个数。具体效果图如下：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/411.png)

##### 4.函数返回值

  上面中，我们返回了20岁年龄的人呢，这个20 岁是固定的，那我们能否动态的决定年龄大于某个值呢，其实是可以的，只要**将getters的返回值变成一个函数即可**。比如：

```jsx
const store = new Vuex.Store({
    state:{
      student:[
        {id:100,name:'zhangsan',age:21},
        {id:200,name:'lisi',age:18},
        {id:300,name:'wangwu',age:22},
        {id:400,name:'zhaoliu',age:19}
      ]
    }
    getters:{
      moreAgeStu(state){
        return function(age){
          return state.student.filter(s => s.age>age)
        }
      }
    }
})
```

```vue
<template>
  <div id="app">
    <h2>-------年龄大于age的人-------</h2>
    <h2>{{$store.getters.moreAgeStu(17)}}</h2>
  </div>
</template>
```

我们希望返回的年龄是根据用户传递的参数进行筛选的。
  上述代码中，将getters的返回值变成一个带有age参数的函数，然后根据用户在调用函数时传入的age值进行筛选，比如本例中就筛选出年龄17岁的人。效果如下:

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/412.png)

以上便是今天getters有关的知识，总结如下：
**1.使用场景**
  当一些数据需要经过变化的时候可以使用getters来处理变化，然后使别的组件在用到该数据时，这个数据是经过变过后得到的结果
**2.用法**
一般通过声明方法来使用，默认的参数是state
**3.方法的参数**
  除了默认的state参数，还可以传入getters，传入的getters参数其实就是Vuex中的getters，通过这个参数可以调用getters已经声明好的了方法，比如在morestuLength中调用了morestu方法
**4.返回值**
  除了可以直接返回具体的数值之外，还可以返回一个函数，返回函数的情况一般用在需要根据用户的参数进行某些判断的场景。

# Vuex中的mutations

## 一、基本结构

##### 1.作用

  **Vuex的store状态的更新唯一方式：提交Mutation**。也就是我们前面提到过的，必须经过 state --> Vuex --> Action --> Mutations 这个路径修改state的状态。

##### 2.组成

mutations主要由两部分组成：**事件类型和回调函数**，具体的可以参考下面的代码：

```js
const store = new Vuex.Store({    state:{      counter:100,    },    mutations:{      increase(state){        state.counter++      },      decrease(state){        state.counter--      }    }})
```

在上述的代码中，increase和decrease是事件类型，(state){state.counter++} 这部分是回调函数。

##### 3.定义的方式

定义的方式很简单，就是**事件类型+回调函数**，具体查看下面的代码：

```js
const store = new Vuex.Store({    state:{      counter:100,    },    mutations:{      increase(state){        state.counter++      }    }})
```

上述代码中，定义的事件类型为increate，当执行该事件类型时，就会调用回调函数，执行对counter++的操作。
  此外，如果想要通过mutations**更新某些数据**，可以使用**commit方法**，commit方法中传入的数据是事件类型。具体代码如下：

```jsx
const store = new Vuex.Store({    state:{      counter:100,    },    mutations:{     increase:function(){        this.$store.commit('increate')      }    }})
```

## 二、传递参数

##### 1.传递变量

  前面我们已经会通过调用increate函数对counter进行简单的加1操作，如果这个时候我们不再对counter进行简单的加1操作，而是加5甚至是加10，这个时候该怎么办呢？
很简单，只需要传递多一个参数即可，比如：

```js
const store = new Vuex.Store({    state:{      counter:100,    },    mutations:{     increateCounter(state,count){        state.counter += count      }    }})
```

在mutation中，声明increateCounter事件类型，然后回调函数主要是对counter做加上count的操作。在App.vue页面中调用该方法的代码如下：

```vue
<template>  <div id="app">    <h2>{{message}}</h2>    <h2>{{$store.state.counter}}</h2>    <button @click="addcounter" >+</button>    <button @click="deccounter" >-</button>    <button @click="addCount(5)" >+5</button>    <hellovue></hellovue>  </div></template><script>  import hellovue from './components/HelloWorld.vue'export default {  name: 'App',  components:{    hellovue  },  methods:{    addcounter(){      this.$store.commit('increase')    },    deccounter(){      this.$store.commit('decrease')    },    addCount(count){      this.$store.commit('increateCounter',count)    }  },    data(){        return {            message:"我是组件1",        }    }}</script>
```

在methods方法中，对addCount点击事件的处理，主要是通过commit方法调用了Vuex中mutation声明的increateCounter，然后传递count作为参数，在本例中，参数count = 5，所以点击一次，counter的数值就会增加5，具体效果图如下所示：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/413.gif)

如果我们传递的参数不再是变量这么简单，而是**传递一个对象**，又该怎么办呢？很简单，我们传递一个对象就可以啦。



##### 2.传递对象

做法很简单，具体代码如下：

```js
const store = new Vuex.Store({    state:{      counter:100,    },    mutations:{     increateStu(state,stu){        state.student.push(stu)      }    }})
```

上述代码中，将stu参数添加到student对象中，在App.vue中调用该方法的代码如下：

```vue
<template>  <div id="app">    <h2>{{message}}</h2>    <button @click="addStu">添加学生</button>    <hellovue></hellovue>  </div></template><script>  import hellovue from './components/HelloWorld.vue'export default {  name: 'App',  components:{    hellovue  },  methods:{ addStu(){      const stu = {id:500,name:'xiaoqi',age:30}      this.$store.commit('increateStu',stu)    }  },    data(){        return {            message:"我是组件1",        }    }}</script>
```

当点击了“添加学生”按钮之后，就会将stu对象中的数据添加到student对象中，具体效果如下：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/414.gif)

## 三、提交风格

##### 1.commit

这是最简单的提交方式，在上面的例子中，小编也已经演示过了，这里就不再重复

##### 2.type

  这种风格的提交就是commit中提交的是一个对象，然后在type中传入具体要传入的数据，比如：

```vue
//App.vueaddCount(count){    // 第一种提风格:commit    this.$store.commit('increateCounter',count)     //第二种提交风格    this.$store.commit({      type:'increateCounter',       count      })}//index.jsconst store = new Vuex.Store({    state:{      counter:100,    },    mutations:{      // 提交风格      increateCounter(state,payload){        state.counter += payload.count      }    }})
```

上述代码中，commit提交的是一个对象，然后在对象中有一个type类型，type后面填写事件类型，比如本例子中的increateCounter。
  值得注意的是参数问题。在第一种提交风格中，count就是一个简简单单的数值，但是在第二中风格中，**count是一个对象**，所以我们需要通过 **对象.属性名**来获取具体的变量，比如本例中的 payload.count 就是获得count变量

# Mutations的响应规则

# 一、响应式

##### 1.原理

响应式就是当数据发生改变的时候，页面中用到该数据的地方也会发生改变。
  当我们已经在store对象中定义某些属性时，属性就会被加入到响应式系统中，该系统就会监听属性是否发生变化，如果属性发生变化，就会通知界面中所有用到该属性的地方发生变化，这就是响应式的基本原理。
  简单的说就是**预先在store对象定义的属性是被加入到响应式系统中的，只有加入到响应式系统中的属性才会发生响应式的变化。**
比如：

```js
//index.jsconst store = new Vuex.Store({    state:{      info:{        name:'haha',        age:13,        height:1.45      }    },    mutations:{      // 响应式测试      changeInfo(state){        state.info.name='哈哈'      }    }})
```

```vue
<!--App.vue--><template>  <div id="app">    <h2>响应式测试</h2>    <button @click="changeInfo">修改信息</button>    <h3>{{$store.state.info}}</h3>  </div></template><script>  import hellovue from './components/HelloWorld.vue'export default {  name: 'App',  methods:{    changeInfo(){      this.$store.commit('changeInfo')    }  }}</script>
```

首先定义了info属性，该属性包含姓名，年龄，身高三项基本信息，然后在mutations中定义了changeInfo方法，当用户点击按钮时，就将name中的‘haha’修改成“哈哈”。下面来看看效果图：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/415.gif)

但是，当我们把changeInfo的内处理修改成下面的代码，数据是否依旧可以相应的？

```js
//index.js      changeInfo(state){       state.info['address']='China'      }
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/416.gif)

可以看到，当往info增加新的属性时，该属性只添加到了state中二并没有添加到页面中。因为该属性并没有被添加到响应式系统中，所以产生响应式，那我们要怎么做才能有响应式呢?看了下面你就知道了 o(*￣︶￣*)o



##### 2.方法

**① set方法**
  set方法很简单，就是将新增加的属性添加到响应式系统中，这样新增加的属性也可以是响应式的，具体代码如下：

```js
//index.js      changeInfo(state){            Vue.set(state.info,'address','China')      }
```

该方法第一个参数是要修改的对象名，第二个参数是要增加是属性名，第三个参数是新增加属性的值。比如本例中为inoffensive对象新增加数值是“China”的address属性。具体效果图如下：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/417.gif)

如果我们想要删除原本的属性，应该怎么做呢？
很简单，通过 Vue.delete删除，具体查看下面的代码：

```js
//index.js      changeInfo(state){            Vue.delete(state.info,'age')      }
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/418.gif)

# mutations常量和总结

## 一、mutations的常量类型

##### 1.产生原因

  在mutations中, 我们定义了很多事件类型(也就是其中的方法名称)。当项目增大时, Vuex管理的状态越来越多, 需要更新状态的情况越来越多, 意味Mutation中的方法越来越多。
  这会导致使用者需要花费大量的精力去记住这些方法, 甚至是多个文件间来回切换, 查看方法名称, 甚至如果不是复制的时候, 可能还会出现写错的情况。如何避免上述的问题呢?
  其中一种很常见的方案就是**使用常量替代Mutation事件的类型**。将这些常量放在一个单独的文件中, 方便管理以及让整个app所有的事件类型一目了然。

##### 2.使用方式

  我们可以创建一个文件，并且在其中定义我们的常量， 使用一个常量来作为函数的名称。下面我们将mutations中的increate方法用常量INCREATE替代，具体代码如下所示:

```js
//新创建的mutations-type.js文件export const INCREATE = 'increate'
```

```js
//在 index.js 文件中使用常量替代方法名import {  INCREATE} from './mutations-type.js'const store = new Vuex.Store({    state:{      counter:100,    },    mutations:{      //原始方法      // increase(state){      //   state.counter++      // },      //常量替代方法      [INCREATE](state){        state.counter++      },    },})
```

```js
//在App.vue中用常量替代提交的方法名<script>  import {    INCREATE  }from '../store/mutations-type.js'export default {  name: 'App',  methods:{    addcounter(){      // this.$store.commit('increase')      this.$store.commit(INCREATE)    }  }}</script>
```

上述三个代码中，在 mutations-type.js 文件声明了常量INCREATE，然后分别将 index.js 文件和 App.vue 文件中的 increate 方法名用常量INCREATE替代。
以上就是mutation中常量类型的简单介绍，下面小编总结一下mutations常用的知识点。

## 二、mutations总结

##### 1.基本结构

**① 作用**
  用来**修改state状态更新的唯一方式**。state中任何状态的修改都必须经过mutations提交，因为mutations中有个浏览器插件 Devtools，该插件会记录和跟踪state中状态的所有变化，方便我们查找到底运行时在哪一步出错了。
**② 组成**
mutations的组成主要分成两大组成：**事件类型和回调函数**，比如：

```js
//index.js文件const store = new Vuex.Store({    state:{      counter:100,    },    mutations:{      increase(state){        state.counter++      }    }})
```

上述代码中，目的是点击一次按钮就使 counter 数值增加。increase是事件类型，(state){state.counter++} 是回调函数。
**③ 更新状态**
  借助上面的例子，我们在mutations 写好了相关的业务逻辑代码，需要将该处的业务实现起来，也就是需要更新counter的状态。主要通过**commit方法进行更新**，具体代码如下：

```vue
//App.vuemethods:{    addcounter(){        this.$store.commit('increase')    }}
```

##### 2.传递参数

  传递参数的作用，是为了更够**根据用户的需要进行状态的更新**。比如上述的例子中，当用户点击按钮时，就将 counter 的值加5甚至加更多，此时单单通过上面的方法是没有办法实现的，因为没有获取到用户提供的数据，所以我们需要再引入参数，用该参数来存放用户提供的数据。
引入的参数主要有两种类型：**单一变量和函数**
**① 单一变量**
  针对用户点击按钮，counter 数值就增加5的需求，具体代码如下：

```js
//index.jsconst store = new Vuex.Store({    state:{      counter:100,    },    mutations:{     increateCounter(state,count){        state.counter += count      }    }})
```

```vue
<!--App.vue--><template>  <div id="app">    <h2>{{$store.state.counter}}</h2>    <button @click="addCount(5)" >+5</button>  </div></template><script>export default {  name: 'App',  methods:{    addcounter(){      this.$store.commit('increase')    }}</script>
```

**② 对象**
  当用户传递进来的不是变量，而是一个对象，这个时候我们仅需要将参数变成对象类型即可。比如

```js
const store = new Vuex.Store({    mutations:{     increateStu(state,stu){        state.student.push(stu)      }    }})
```

上述代码中，将 stu 参数添加到student对象中，在App.vue中调用该方法的代码如下：

```vue
<template>  <div id="app">    <button @click="addStu">添加学生</button>  </div></template><script>export default {  name: 'App',  methods:{    addStu(){      const stu = {id:500,name:'xiaoqi',age:30}      this.$store.commit('increateStu',stu)    }  }}</script>
```

##### 3.提交风格

主要有两种：**commit 和 type**。具体的区别查看下面的代码即可。

```vue
//App.vueaddCount(count){    // 第一种提风格:commit    this.$store.commit('increateCounter',count)     //第二种提交风格    this.$store.commit({      type:'increateCounter',       count      })}//index.jsconst store = new Vuex.Store({    state:{      counter:100,    },    mutations:{      // 提交风格      increateCounter(state,payload){        state.counter += payload.count      }    }})
```

要注意的是，第二种风格中，**获取的count不再是单一的变量，而是整个 type对象**，所以需要再声明一个变量，通过该变量来获取conut。比如本例payload

##### 4.相应规则

**① 响应式原理**
响应式就是当数据发生改变的时候，页面中用到该数据的地方也会发生改变。
  当我们已经在store对象中定义某些属性时，属性就会被加入到**响应式系统**中，该系统就会监听属性是否发生变化，如果属性发生变化，就会通知界面中所有用到该属性的地方发生变化，这就是响应式的基本原理。
**② 前提**
  **预先在store对象定义的属性是被加入到响应式系统中的**，只有加入到响应式系统中的属性才会发生响应式的变化。
**③ 使用方式**
**3-1 提前在store对象中定义了的变量**

```js
//index.jsconst store = new Vuex.Store({    state:{      info:{        name:'haha',        age:13,        height:1.45      }    },    mutations:{      // 响应式测试      changeInfo(state){        state.info.name='哈哈'      }    }})
```

```vue
<!--App.vue--><template>  <div id="app">    <h2>响应式测试</h2>    <button @click="changeInfo">修改信息</button>    <h3>{{$store.state.info}}</h3>  </div></template><script>export default {  name: 'App',  methods:{    changeInfo(){      this.$store.commit('changeInfo')    }  }}</script>
```

上述的代码，主要是对已经在store对象中定义了的属性 name 进行修改。
**3-2 没有提前在store对象定义的属性**
  如果属性没有提前在store对象定义，是不能够加入到响应式系统中的，但是在开发中，又可能需要像对象增加新的属性。这个时候就可以通过**set方法**解决，比如：

```js
//index.js      changeInfo(state){            Vue.set(state.info,'address','China')      }
```

该方法第一个参数是要**修改的对象名**，第二个参数是要**增加是属性名**，第三个参数是**新增加属性的值**。比如本例中为inof对象新增加数值是“China”的address属性。

##### 5.常量类型

  主要是为了防止今后大项目时方法名过多而导致出错的问题，用常量来替代方法名，便于管理，其用法在上面有写，这里就不进行阐述了。