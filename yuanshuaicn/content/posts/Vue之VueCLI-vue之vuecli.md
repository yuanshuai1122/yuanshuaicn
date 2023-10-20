---
title: Vue之VueCLI
date: 2021-11-30 10:44:42.209
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/1_7OCwu--TWqVluPMsZdzWKw-34ce1bcaed3b4c59a2183cf00af73987_1622733997113.png'
theme: 'light'
tags: 
- Vue
---

# 一、基础知识

##### 1.背景

​    当我们只是简单的**写几个Vue的简单程序，根本不需要用cli**，杀鸡焉用牛刀说的就这个理。
相反，如果**用Vue开发大项目时，需要考虑很多的因素**，比如代码目录结构、项目结构、热加载代码单元测试等事情。这么繁琐的工作，人为去办不仅效率低，还容易犯错误，因此我们**需要一个东西来自动帮我们完成繁琐的工作**，这个东西就是cli。

##### 2.什么是CLI

  cli 是Command-line interface的简称，译为命令行界面，俗称“脚手架”。使用脚手架可以帮助我们快速**搭建Vue开发环境**和**对应的webpack配置**。
  就好像盖房子，工人们要先搭好很多的竹子，这些竹子就是工程界的脚手架，工人们根据脚手架搭建的模样来新新房子

##### 3.使用前提

  脚手架会生成webpack相关 的配置，而webpack又依赖于node，所以脚手架在使用的前提是要**安装好node和webpack。**

##### 4.怎么使用

**① 安装:npm install -g @vue/cli**
  要注意的是，上面安装的是Vue Cli3的版本。如果想要按照Vue cli2的方式初始化项目是不允许的，只能把cli2的模板拉过来才可以使用**：npm install@vue/cli-init -g**。这个时候即可以使用cli3和可以运行cli2
**②初始化项目**
cli3：vue creat 项目名
cli2：vue init webpack 项目名
下面来讲讲有关cli创建项目时需要那些东西

# 二、cli2初始化项目

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/cli-start.png)

##### 1.初始化项目：

```shell
vue init webpack 项目名
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/247.png)

##### 2.项目名：Project name

  直接按回车，默认和上一步初始化项目时的项目名一样，也可以自行另外取个名字。这里直接按回车，生成下面效果：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/248.png)

##### 3.项目的描述：project description

  默认的项目描述信息是 A vue.js project 。也可以自行修改。这里直接按照默认项目描述信息，生成下面效果：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/249.png)

##### 4.Author:作者

  默认显示的是配置在 gitconfig文件下的作者信息。同理，如果不想默认，可以自行修改。这里修改后的效果如下所示：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/250.png)

##### 5.项目的构建： Vue build

  Runtime Compiler 和Runtime-only 前面小编已经讲过两者的区别，这里不再重复叙述。这里选择安装Runtime Compiler ，效果如下：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/251.png)

##### 6.路由： vue-touter

  如果在此处安装了路由，后期用到路由时就不需要安装了，但是有关路由的信息还有待提高，所以这里选择不安装路由：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/252.png)

##### 7.代码规范：ESLint

  这个参数的意思是如果选用了ESLint代码规范的话，今后在编写代码的时候，代码不够规范也是会报错的，这里选择不使用.

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/253.png)

##### 8.单元测试：unit test

这里先不做测试。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/254.png)

##### 9.端到端测试：e2e

不做测试。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/255.png)

##### 10.管理项目

  这句话的意思是，今后在管理自己项目的时候，是想用npm 还是yarn还是自己写的。我们选择npm。
经过前面的10 个步骤，我们就完成了用cli2初始化项目的基本步骤啦

# 二、cli2的目录结构

先看一下整体目录结构：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/cili-context.png)

### （一）build

#### 1.pack.json文件下的js执行方式

​	js在没有node环境支撑之前，运行速度很慢，在有了node环境作为支撑以后运行才变的很快，这跟js的执行方式有关。
**① 原始方式**
  最初我们执行js文件，经过三个步骤：1.创建.html 文件；2.引入.js文件到html文件内部；3.通过浏览器编译执行。主要原理是js文件会生成中间的字节码，最后再被浏览器编译执行（js --> 字节码 --> 浏览器）。所以js原始的执行方式效率比较慢。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/256.png)

**② node环境支撑**
  随后，在node出现后，为js文件的执行提供了环境支撑，使运行速度加快了很多。这要归功于node内部有v8引擎。v8引擎直接将js文件转换成二进制（js --> 二进制文件）进而直接别浏览器编译执行，所以在速度上快了很多。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/257.png)

所以我们下先打开项目的package.json文件：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/258.png)

可以看到，当我们致执行 npm run build时，是通过node之情js文件，意味着可以直接在终端执行js文件而不需要再通过.html文件和浏览器执行js文件了。

#### 2.build下的配置

​	在创建项目的时候，也需要为项目进行相关的配置，而build文件夹下就存放项目相关的配置，下面一起来看看相关的配置有哪些：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/259.png)

**① build.js :**当执行 npm run build时就会执行build下的build.js文件。这个文件做的事情很多，首先会删除原本打包好的文件（当我们第二次执行 npm run build 命令时，就会删除第一次执行该命令时的文件）然后查找webpack相关的配置。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/260.png)

**② webpack.prod.conf.js :**由上一幅图我们可以知道webpackConfig来源于webpack.prod.conf 【const webpackConfig = require('./webpack.prod.conf')】。这个配置文件主要是用webpack-merge方法合并基础的配置和特殊的配置。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/261.png)

**③ webpack.base.conf.js：**这个配置文件主要是有些导入、导出等等的配置。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/262.png)

总结上面两点，就是说**当执行 npm build命令时就会执行build.js文件，然后删除原有文件，查找webpack相关的配置。**

### （二）config

config文件夹的作用和build的作用基本相同，都是一些配置，config主要包裹三个配置：开发配置，变量配置，生产配置

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/263.png)

dev.env.js 和prod.env.js两个配置的内容比较简单，小编在这里就u叙述的，我们大概来看看index.js配置有哪些。
index.js主要有两大部分：dev 和build。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/264.png)

dev主要是关于浏览器的配置，比如端口，是否自动打开浏览器。见下图：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/265.png)

而build只要是一些路径上的配置。见下图：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/266.png)

### （三）node_modules

这文件可厉害了，因为它包含的 东西是最多的，主要是项目依赖的一些包。
比如开发是依赖的vue，还有一些loader，plugin都是在node_modules中的。打开pacgage.json文件，项目依赖的包如下：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/267.png)

### （四）src

这里太熟悉了吧，放源码的地方。

### （五）static

static主要是存放一些静态的资源，在该文件夹下的静态资源将原封不动的打包的dist文件夹下。如果有了.gitkeep文件，不管static文件夹是否为空，都会打包到dist中，如果没有该文件，则static文件夹就不会被打包到dist中。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/268.png)

# 三、run time compiler和run time only

## 一、运行原理

他们两者的区别主要在main.js文件上

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/269.png)

在run time compiler 中，注册了App然后在使用；而在run time only 中，直接是一个函数，该函数的全称其实是这样子的：

```js
render :function (h){
    return h(App)
}
```

这就要追溯到他们的运行原理了

### 1.run time comliper

  在run time comliper中，将template传进给Vue时，会将**template**保存在Vue实例中的potions中，然后**解析成ast 结构**（ast是abstrct syntax trees英文的简称，译为抽象语法树）进而，**将ast 编译成render函数**；render函数又会将对应的tempalet**转换成虚拟的dom节点元素**，这些虚拟的节点元素就会构成一课虚拟dom树，最后再转化成真实的dom元素，也是就**UI**。
这就是run time comliper 的原理，总结起来就是**：template --> ast --> render -->virtual dom --> UI。**

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/270.png)

#### 2.run time only

  由图一我们可以看到， run time only 中没有template 而只有函数，所以它的原理就是比 run time compiler 少了template --> ast 的步骤。直接从render函数开始，转换成虚拟的dom最后变成真实的UI。
这就是run time only 的原理，总结起来就是**：render --> virtual dom -->UI**

## 二、性能区别

#### 1.运行效率：run time only > run time compiler

  因为run time cimpiler前期还要将 template 解析成ast，所以运行效率上肯定会比run time only 低。

#### 2.代码量:run time only < run time compiler

  还是因为run time compiler前期要将template转换成ast，肯定需要额外的代码来支持这项工作。

## 三、render 函数

#### 1.createElement 函数

```js
//render :function (h){
//    return h(App)
//}

render :function (createElements){
    return createElements(App)
}
```

其实这里的 h 是createElements 函数的代名词.
**① 用法一： createElements（‘标签名’，‘对象’，‘数组’）**
【createElements有三个参数，第一个参数是**标签名**；第二个参数是**对象类型，负责标签的属性**，比如css样式之类的；第三个参数是**数组类型，主要负责标签的内容**】
比如：

```js
new Vue({
  el: '#app',
    render:function(createElement){
        return createElement('h1',{class:'warp'},['hello,vue']);
    }
})
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/271.png)

createElements函数已经将原本index.html下的div替换成了h1，并且h1有类和内容。
**② 用法二：createElements（‘标签名’，{....}，[..，createElements（‘标签名’，[..，]）]）**
【除了上面最基本的用法外，createElements函数还可以**在数组的元素内在调用一次createElements函数**】
比如：

```js
new Vue({
  el: '#app',
  // render: h => h(App)
    render:function(createElement){
        return createElement('h1',
        {class:'warp'},
        ['hello,vue',createElement('button',['按钮'])]);
    }
})
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/272.png)

可以看到，createElements函数还真的同时替换了两个标签

**③ 用法三：传入组件**
【除了前面介绍的两种基本的用法外，createElements函数还可以**直接传入一个组件**，通过这个方式引入组件，就**不需要在Vue实例中再次编译template，**自然效率就会高些】
比如：

```js
const cpn = {
  template: `
        <div>{{message}}</div>
    `,
  data () {
    return {
      message: '我是组件'
    }
  }
}

new Vue({
  el: '#app',
  render: function (createElement) {
    return createElement(cpn)
  }
})
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/273.png)

*createElement:*可以同样操作APP组件

```js
new Vue({
  el: '#app',
  render: function (createElement) {
    return createElement(App)
  }
})
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/274.png)

#### 2. .vue文件的template的处理

【大家是否会疑惑，run time only版本不处理template，但是App.vue文件中还是会有template啊？那.vue文件中的template谁来处理？】
【其实在安装loader的时候，我们也安装了**vue-template-compiler。**就是这个东西处理.vue文件中的template。run time only在引入App的时候，**App.vue文件中的template已经被编译成了render函数**】

# 四、cli3创建项目

## 一、区别

### 1.webpack版本

​		cli3是基于**webpack4**，而cli2则基于webpack3

### 2.设计原则

  cli3 **提供“0”配置**，移除了build和config两个目录。也就是你们在cli3是没有办法见到它们了o(╥﹏╥)o

### 3.可视化

​		cli3 **提供了vue ui命令**，提供了可视化配置，更加人性化

### 4.新增文件夹

  在cli2 的基础上，cli3 不仅仅把build和config两个目录赶走了，还顺带赶走了static文件夹。那以前放置在static下的文件要放在那里呢？cli3**新增了public文件夹**，并且将public移动到public文件夹中。

## 二、创建项目

### 1.初始化项目

```shell
vue create 项目名
```

### 2.选择配置

**创建完项目后，出现的第一个选项： Please pick a preset 。默认的配置带有babel和eslint；手动配置的话有很多的参数，下面带大家一起看看】**

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/275.png)

### 4.保存配置文件选择

**【下图的选项是说，你是否要将你前面的配置保存起来。这里选择保存并命名为Alienware】**

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/276.png)

【当我们重新创建项目的时候，就会看到刚刚保存的Alienware】

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/277.png)

### 5.命令选择

**【最后的选项是说，希望我们今后是用npm管理还是yarn管理。我们选择npm，然后开始初始化项目。到此，使用cli3初始化项目就完成了】**

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/278.png)

## 三、目录结构

  下面我们一起来看看用cli3创建的项目的目录结构吧

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/279.png)

**① node_modules：**通过npm 命令安装的包都会放到这里
**② public ：**这个文件夹和cli2 的static是一个作用，能将public下的所有文件原封不动的打包到dist文件夹下。
**③src ：**这个不用说，就是我们平常编写的源代码所在的文件夹
**④ .browserslistrc：**浏览器的相关配置：市场份额大于1%，并且是最后的两个版本

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/280.png)

**⑤ .gitignore：**要忽略的文件。不是所有的文件都需要上传到服务中的，将不需要的文件放置到该文件中。比如我们不需要将node_modules打包发送到服务器中。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/281.png)

**⑥ babel.config.js ：**对babel的一些配置

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/282.png)

## 四、执行项目

  在cli2的时候，我们是通过npm run dev 执行的，但是到了cli3，就不是了，这个时候就得查看package.json文件，发现server是执行项目的，所以我们要**通过 npm run server执行项目**

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/283.png)

在最后，提一下cli3中的main.js函数。
其实vue cli3 和vue cli 2的main.js函数差不多，只是**cli3中有 mount('#app')而cli2中有el: '#app'。**两者其实没有什么区别，通过 el: '#app' 选择的标签还是要通过$mount('#app')进行编译的。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/284.png)

# 五、cli3 配置文件

### 一、启动配置服务：vue ui

  vue ui是通过npm install @vue/cli -g安装时一起安装的，它的含义是**启动本地服务器**，所以是不会局限在当前项目的目录中启动，而是**可以在任何的目录中启动**。
**① 在终端输入命令 ：vue ui**
emsp; 输入完成后会提示正在打开用户图形界面UI，并且该界面的地址是： [http://localhost:8000](https://links.jianshu.com/go?to=http%3A%2F%2Flocalhost%3A8000)，这个是默认已经配置好了的端口，一般不修改。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/285.png)

**② 进入用户图形界面**
  在这个界面中，我们可以看到有三个操作：项目，创建，导入。在这里可以创建项目，也可以将创建好的项目导入进来然后通过图形化的方式去查看和修改一些配置。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/286.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/287.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/288.png)

**③ 查看配置文件**

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/289.png)

**3.1 插件**
  在插件这个界面中，可以查看自己项目**已经安装的插件**，还可以通过右上角来**添加后期需要的插件**。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/290.png)

**3.2 依赖**
  在依赖这个界面中，也是可以**查看自己项目安装的依赖**，还可以通过右上角来**添加依赖**。**运行依赖**是在项目在运行的时候会依赖的，通过在安装依赖时**添加参数 --save来添加**，而**开发时依赖**是项目只是在开发的时候会依赖，通过在安装依赖时**添加参数 --save-dev**添加的。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/291.png)

**3.3 配置**
  在配置这个界面中，小编其实也没什么教给大家的，因为大家都很聪明，所以都会看的懂的，.比如,如果想要修改输出目录的话，就在对应的框框修改成你想要的目录即可。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/292.png)

**3.4 任务**
  在任务这个界面，你可以通过点击“运行”来执行npm run server 命令，并且在这个界面，你可以更加直观的看到运行项目时需要的资源和时间等等信息

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/293.png)

# 二、项目的目录

  小孩子玩捉迷藏呢，肯定会有个藏身之地的，同样的道理，cli3的配置肯定也可以通过项目的目录来找到具体的藏身位置。
**具体的步骤如下： node_module --> @vue --> cli-server --> webpack。**

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/294.png)

 当然了，如果调皮的小孩子这么快被你找到就不是小孩子啦，所以在webpack文件中，已经告诉我们真实的藏身之地了： ./lib/Service

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/295.png)

果不其然，它们就在这里O(∩_∩)O



# 三、配置文件 vue.config.js

​		如果在项目中**需要添加自己 一些配置**，需要创建 vue.config.js文件，然后通过module.export={......}导入你自己写的配置即可。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/296.png)