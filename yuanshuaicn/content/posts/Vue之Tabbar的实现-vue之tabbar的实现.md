---
title: Vue之Tabbar的实现
date: 2021-posts-30 10:44:57.295
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/1_7OCwu--TWqVluPMsZdzWKw-34ce1bcaed3b4c59a2183cf00af73987_1622733997113.png'
theme: 'light'
tags: 
- Vue
---

# Tabbar实现

## 一、实现思路

  最终的效果如下图所示，下面小编一一为大家解析每个部分如何实现，并附上最终的实现代码。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/367.png)

##### ① 路由懒加载

  首先，肯定有两个组件组成，当点击红色组件中的“首页”、“分类”、“购物车”、“我的”这四个小标题时，就能在蓝色组件中显示相应标题的内容。这部分的实现内容可以通过路由的懒加载实现

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/368.png)

##### ② 插槽

  上图中的红色组件就是小编今天重点为大家介绍的 Tabbar。毫无疑问，一整个 **Tabbar 应该是一个单独的组件**；然后是内部的小标题。我们希望标题的数量、图标、文字都是可以灵活变通的，因此每一个**标题、图标、文字**我们都可以用**slot 插槽**来实现，插槽最终显示的内容就会被用户传递过来的数据替换掉。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/369.png)

综上所述，要实现 Tabbar 只需要通过**路由懒加载**和**插槽**实现。下面小编先对结构和样式进行简单的编写 ⁄(⁄ ⁄•⁄ω⁄•⁄ ⁄)⁄



## 二、实现代码

##### 1. 创建项目

  通过命令行： **vue init webpack tabbar** 创建项目，并删除原本自带的 HelloVue.vue 文件和 App.vue 中的 template、style 自带的模板和样式。

##### 2.结构和样式

###### ① html结构

在模板中用**4个 div** 分别表示“首页”、“分类”、“购物车”、“我的”。效果如图所示：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/370.png)

###### ② 样式

**2.1 去除留白**
  首先我们会发现字体和边界有留白的部分，这个留白部分是因为**自带有margin和padding**，所以我们需要将这两个值设置为0px即可。
  在asset中创建css文件夹，然后创建base.css文件，在base.css文件中书写下面的代码：

```css
body{
    margin:0px;
    padding:0px;
}
```

接着就是在App.vue 文件中的style模块引入base.css文件：

```vue
<style>
    @import url("./assets/css/base.css");
</style>
```

最后查看效果图：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/371.png)

###### 2.2 布局

**display**:我们需要将 tabbar 的内容水平排列而不是垂直排列，所以在布局上采用 **flex**。

```vue
<template>
  <div id="tab-bar">
    <div>首页</div>
    <div>分类</div>
    <div>购物车</div>
    <div>我的</div>
  </div>
</template>

<style>
    @import url("./assets/css/base.css");
    /* 对父级 tab-bar 采用flex布局  */
    #tab-bar{
        display: flex;
    }
</style>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/372.png)

可以看到每个小标题已经呈水平排列。

**flex:**为了让每个小标题都占据相同的位置，我们需要对小标题进行**均等分**。因此，我们为每个小标题添加一个 tab-bar-item 的类名，然后在该类中添加 **flex：1** 的样式。

**text-alien**: 此外为了让**文字能够居中**，我们必须对小标题设置 text-alien:center。

**height**：最后为了让小标题看起来不那么矮，我们需要为小标题设置高度。**一般tabbar的高度在业界中公认的是 49px**

```vue
<template>
  <div id="tab-bar">
    <div class="tab-bar-item">首页</div>
    <div class="tab-bar-item">分类</div>
    <div class="tab-bar-item">购物车</div>
    <div class="tab-bar-item">我的</div>
  </div>
</template>

<style>
/* 引入base.css文件 */
    @import url("./assets/css/base.css");
/* 对 tab-bar 采用 flex 布局 */
    #tab-bar{
        display: flex;
    }
/* 对 tab-bar-item 均等分 */
    .tab-bar-item{
        flex:1;
        text-align: center;
    }
</style>
```

在设置完上面的样式后，效果如下所示：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/373.png)

###### 2.3 背景和位置

  **background**：设置背景颜色
  **position**：设置位置

```vue
<style>
    @import url("./assets/css/base.css");
    #tab-bar{
        display: flex;
        background: #f6f6f6;
        position: fixed;
/* 为了让 tabbar 占据整个页面的宽度，最好设置 left 和 rigth为0 */
        left: 0px;
        right: 0px;
        bottom: 0px;
    }
</style>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/374.png)

###### 2.4 阴影

**box-shadow**：为了让 tabbar 和界面的**过渡更加自然**，我们需要为 tabbar 添加阴影。

```vue
<style>
    @import url("./assets/css/base.css");
    #tab-bar{
        display: flex;
        background: #f6f6f6;
        position: fixed;
        left: 0px;
        right: 0px;
        bottom: 0px;
        box-shadow: 0px -1px 7px rgb(100,100,100,0.2);
    }
</style>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/375.png)

最后附加上今天整套的代码：

```vue
<template>
  <div id="tab-bar">
    <div class="tab-bar-item">首页</div>
    <div class="tab-bar-item">分类</div>
    <div class="tab-bar-item">购物车</div>
    <div class="tab-bar-item">我的</div>
  </div>
</template>

<script>


export default {
  name: 'App',
  components: {
    
  }
}
</script>

<style>
    @import url("./assets/css/base.css");
    #tab-bar{
        display: flex;
        background: #f6f6f6;
        position: fixed;
        left: 0px;
        right: 0px;
        bottom: 0px;
        box-shadow: 0px -1px 7px rgb(100,100,100,0.2);
    }
    .tab-bar-item{
        flex:1;
        text-align: center;
        height:49px;
    }
</style>
```

## 知识小卡片1--flex：

###### ① 定义

意为”弹性布局”，为盒状模型提供最大的灵活性。在设为Flex布局以后，子元素的float、clear和vertical-align属性将失效。

###### ② 相关概念：

**2.1 容器**：采用Flex布局的元素，称为Flex容器（flex container），简称”容器”，比如上面例子中是 tab-bar。

**2.2 项目**：容器内的所有子元素自动成为容器成员，称为Flex项目（flex item），简称”项目”。比如上面例子中的 tab-bar-item。

**2.3 轴**：容器默认存在两根轴，**水平主轴**和**垂直交叉轴**。主轴的开始位置（与边框的交叉点）叫做main start，结束位置叫做main end；交叉轴的开始位置叫做cross start，结束位置叫做cross end。
  项目默认沿主轴排列。单个项目占据的主轴空间叫做main size，占据的交叉轴空间叫做cross size。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/376.png)

###### ③ 属性

**3.1 flex-direction：决定主轴的方向，即项目的排列方向**
主要语法是：**flex-direction: row | row-reverse | column | column-reverse**
  四个值分别代表：row-主轴为**水平方向**，起点在**左端**；row-reverse -主轴为**水平方向**，起点在**右端**；column-主轴为**垂直方向**，起点在**上沿**；column-reverse-主轴为**垂直方向**，起点在**下沿**。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/377.png)

**3.2 flex-wrap：决定项目是否换行。**
主要语法是：flex-wrap: nowrap | wrap | wrap-reverse;
  三个数值分别代表：nowrap-**不换行**；wrap-**换行并且第一行在上面**；wrap-reverse-**换行并且第一行在下面**。



**3.3 flex-flow：**是flex-direction属性和flex-wrap属性的简写形式，默认值为row nowrap。
主要语法是：flex-flow: <flex-direction> <flex-wrap>

**3.4 justify-content：**定义了项目在**主轴上的对齐方式**。
主要语法是：justify-content: flex-start | flex-end | center | space-between | space-around;
  五个数值分别代表：flex-start-**左对齐**；flex-end-**右对齐**；center-**居中**；space-between-**两端对齐**，项目之间的间隔都相等；space-around-**每个项目两侧的间隔相等**。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/378.png)

**3.5 align-item**：定义项目在**交叉轴上如何对齐**。
主要语法格式是： align-items: flex-start | flex-end | center | baseline | stretch;
  五个参数分别是:flex-start-交叉轴的**起点对齐**；flex-end-交叉轴的**终点对齐**；center-交叉轴的**中点对齐**；baseline-项目的**第一行文字的基线对齐**；stretch（默认值）-如果项目未设置高度或设为auto，将占满整个容器的高度。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/379.png)

**3.6 align-content**：定义了**多根轴线的对齐方式**。如果项目只有一根轴线，该属性不起作用。
主要语法是：align-content: flex-start | flex-end | center | space-between | space-around | stretch;
  六个数值分别是：flex-start-与交叉轴的**起点对齐**；flex-end-与交叉轴的**终点对齐**；center-与交叉轴的**中点对齐**；space-between-与交叉轴**两端对齐**，轴线之间的间隔平均分布；space-around-**每根轴线两侧的间隔都相等**。stretch（默认值）-轴线**占满整个交叉轴**。

## 知识小卡片2-- box-shadow

###### ① 定义 ：为盒子增加阴影

###### ② 语法：box-shadow: h-shadow v-shadow blur spread color inset

###### ③ 参数解析

  h-shadow-水平偏移量，v-shadow-垂直偏移量，blur-模糊程度，spread -阴影的范围大小，color-阴影的颜色，inset-阴影的方向，inset是内阴影，outset是外部阴影

###### ④ 注意事项

  偏移量的数值可以是正数也可以是负数；当为正数时，水平方向往右偏移，垂直方向往下偏移，因为偏移的源点在整个窗口的左上角。

# Tabbar实现版之组件抽取

## 一、组件抽取

  上一篇的文章中，我们将Tabbar的模板和样式都写在了App.vue文件中，这样写的缺点是：不利于代码的重复利用。所以我们需要将模板和样式都抽取出来，放在一个单独的文件中，然后在App.vue引用该文件即可。比如：

```vue
<!-- tabbar.vue-->
<template>
    <div id="tab-bar">
        <div class="tab-bar-item">首页</div>
        <div class="tab-bar-item">分类</div>
        <div class="tab-bar-item">购物车</div>
        <div class="tab-bar-item">我的</div>
    </div>
</template>

<script>
    export default {
        name:'tabbar'
    }
</script>

<style>
    #tab-bar{
        display: flex;
        background: #f6f6f6;
        position: fixed;
        left: 0px;
        right: 0px;
        bottom: 0px;
        box-shadow: 0px -1px 7px rgb(100,100,100,0.2);
    }
    .tab-bar-item{
        flex:1;
        text-align: center;
        height:49px;
    }
</style>
```

```vue
<!-- App.vue -->
<template>
    <div id="App">
        <tabbar></tabbar>
    </div>
</template>

<script>
import tabbar from "./components/Tabbar/tabbar.vue"
export default {
  name: 'App',
  components: {
    tabbar
  }
}
</script>

<style>
    @import url("./assets/css/base.css");
</style>
```

## 二、插槽

  但是上面tabbar组件中，不仅仅包含tabbar本身的模板和样式，还包含了tabbar-item的模板和样式，所以下一步我们最好将tabbar和tabbar-item抽离出来。

```vue
<!-- 抽取过后的 tabbar -->
<template>
    <div id="tab-bar">
        <slot></slot>
    </div>
</template>

<script>
    export default {
        name:'tabbar'
    }
</script>

<style>
    #tab-bar{
        display: flex;
        background: #f6f6f6;
        position: fixed;
        left: 0px;
        right: 0px;
        bottom: 0px;
        box-shadow: 0px -1px 7px rgb(100,100,100,0.2);
    }
</style>

<!-- 加入tabbar-item后的 App.vue-->
<template>
    <div id="App">
        <tabbar>
            <div class="tab-bar-item">
                <img src="./assets/img/tabbar/home.png" alt="">
                <div>首页</div>
            </div>
            <div class="tab-bar-item">
                <img src="./assets/img/tabbar/home.png" alt="">
                <div>分类</div>
            </div>
            <div class="tab-bar-item">
                <img src="./assets/img/tabbar/home.png" alt="">
                <div>购物车</div>
            </div>
            <div class="tab-bar-item">
                <img src="./assets/img/tabbar/home.png" alt="">
                <div>我的</div>
            </div>
        </tabbar>
    </div>
</template>

<script>
import tabbar from "./components/Tabbar/tabbar.vue"
export default {
  name: 'App',
  components: {
    tabbar
  }
}
</script>

<style>
    @import url("./assets/css/base.css");
    .tab-bar-item{
        flex:1;
        text-align: center;
        height:49px;
    }
    .tab-bar-item img{
        width: 30px;
        height: 25px;
    }
</style>
```

在tabbar组件中，有关tabbar-item的模板和样式全部抽取到 App.vue中，就实现了tabbar和tabbar-item分离的目的.但是此时又造成另外一个问题，tabbar-item和App.vue混合了，所以我们最好再创造一个组件，将tabbar-item的相关模板和样式抽取到一块。比如：

```vue
<!-- 单独的 tabbar-item zujian-->
<template>
    <div class="tab-bar-item">
        <img src="../../assets/img/tabbar/home.png" alt="">
        <div>首页</div>
    </div>
    </div>
</template>

<script>
    export default {
        name:'tabbarItem'
    }
</script>

<style>
    .tab-bar-item{
        flex:1;
        text-align: center;
        height:49px;
    }
    .tab-bar-item img{
        width: 30px;
        height: 25px;
    }
</style>

<!-- 抽取tabbar-item后的App-->
<template>
    <div id="App">
        <tabbar>
            <tabbarItem></tabbarItem>
            <tabbarItem></tabbarItem>
            <tabbarItem></tabbarItem>
            <tabbarItem></tabbarItem>
        </tabbar>
    </div>
</template>

<script>
import tabbar from "./components/Tabbar/tabbar.vue"
import tabbarItem from "./components/Tabbar/tabbar-item.vue"
export default {
  name: 'App',
  components: {
    tabbar,
    tabbarItem
  }
}
</script>
```

这里逻辑可能比较混乱的是App在引用tabbar-item的时候。我们已经将tabbar-item抽取成了一个单独的组件，也就表示，每引用一次的<tabbarItem></tabbarItem>就使用了一个小的文字加图片。引用了四次就有了四个文字和图片。效果如下图所示：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/380.png)

以上两步就完成了tabbar和tabbat-item的分离，紧接着就是将tabbat-item内的文字和图标写活。

## 三、插槽实现数据灵活变通

  在上面我们可以知道，在 tabbar-item 的template中书写什么，就会显示什么，但是我们并不希望将数据写死，而是希望来什么数据就用数据，这个特点和插槽很像，插槽就相当于一个空的标签，你来什么文字和图片，插槽就会被该文字和图片替代，所以第一步是将 tabbar-item 显示数据的地方用插槽替代，代码如下：

```vue
<template>
    <div class="tab-bar-item">
        <slot name="item-icon"></slot>
        <slot name="item-text"></slot>
        <!-- <img src="./assets/img/tabbar/home.png" alt="">
        <div>首页</div> -->
    </div>
</template>
```

然后，当我们在 App.vue 中引入tabbarItem标签，就意味着我们调用了一次 tabbar-item组件，而tabbar-item组件中slot的内容就由tabbarItem标签的内部决定，比如 ：

```vue
<template>
    <div id="App">
        <tabbar>
            <tabbarItem>
                <img slot="item-icon" src="./assets/img/tabbar/home.png" alt="">
                <div slot="item-text">首页</div>
            </tabbarItem>
            <tabbarItem>
                <img slot="item-icon" src="./assets/img/tabbar/phone.png" alt="">
                <div slot="item-text">分类</div>
            </tabbarItem>
            <tabbarItem>
                <img slot="item-icon" src="./assets/img/tabbar/4.png" alt="">
                <div slot="item-text">购物车</div>
            </tabbarItem>
            <tabbarItem>
                <img slot="item-icon" src="./assets/img/tabbar/my.png" alt="">
                <div slot="item-text">我的</div>
            </tabbarItem>
        </tabbar>
    </div>
</template>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/381.png)

这样便实现了数据的灵活性，不需要考虑个数，不需要考虑样式，不需要考虑结构，因为所有的样式，结构已经在组件tabbar-item中实现了，你尽管的大胆放心的使用便是。

  以上就是有关Tabbar 实现的组件抽取，总体思路是 现将tabbar 和tabbar-item分离，分别用两个组件单独的设计tabbar和tabbar-item的结构和样式，然后就是通过插槽来实现数据的灵活处理。

# Tabbar传入active图片和文字

# 一、实现active图片

##### 1.定义

  为了避免大家误会，在这里解释一下什么是active图片。简单的说，就是某张图片没有被点击之前是白色的，被点击之后就变成黑色，而被点击之后变成黑色的图片就是active图片，因为被点击了，所以是处于活跃状态的。

##### 2.思路

  active图片的思路是**一开始就传入了所有图片**，当用户没有点击时显示图片1，当用户点击之后就显示图片2 而并不是当用户点击时才传入图片。

##### 3.实现过程

  根据上面的思路，我们知道在tabbar-item中应该再多使用一个**插槽**来存放图片，然后通过设置**标志位和v-if**语句来判断该显示哪张图片。比如：

```vue
<!-- tabbar-item -->
<template>
    <div class="tab-bar-item">
        <slot v-if="!isActive" name="item-icon"></slot>
        <slot v-else name="item-icon-active"></slot>
        <slot name="item-text"></slot>
    </div>
</template>

<script>
    export default {
        name:'tabbarItem',
        data(){
              return {
                  isActive:true
              }
        }
    }
</script>
```

当然了，相应的 App.vue 组件也应该增加多一个 img 标签来存放活跃状态下的图片。比如：

```vue
<tabbarItem>
        <img slot="item-icon" src="./assets/img/tabbar/home.png" alt="">
        <img slot="item-icon-active" src="./assets/img/tabbar/home2.png" alt="">
        <div slot="item-text">首页</div>
</tabbarItem>
```

上两个代码中，name 为 item-icon-active 是新增加的插槽，用来存放活跃状态下的图片。v-if语句通过 isActive 标志位的值来判断显示哪张图片，当 isActive 的值为真，显示活跃状态的图片，结果如图所示：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/382.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/383.png)

## 二、实现active文字

##### 1.定义

  active文字呢，也是和上面的active图片一个道理。当用户点击某段文字时，该文字的样式就会发生改变。

##### 2.思路

  思路和active图片的思路是类似，设置**一个变化样式的类，通过设置标志位来增加该类。**

##### 3.实现过程

  首先声明一个类，在该类中书写文字有关的样式，然后通过 标志位的值来判断是否要添加该类。比如：

```vue
<!--为了便于理解和观察，小编为大家伙清除了和文字无关的任何一切 O(∩_∩)O哈哈~-->
<template>
    <div class="tab-bar-item">
        <slot :class="{active:isActive}" name="item-text"></slot>
    </div>
</template>
<style>
    .active{
        color: red;
    }
</style>
```

上述的代码，表示当 isActive 为真时，就为插槽添加类名为active的类，然后文字的颜色就会由原来的黑色变成红色。我们来看效果：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/384.png)

但是我们发现文字的颜色并没有发生任何的变化。这是为什么呢？
  这个跟插槽被替换有关。上述代码中，我们将 active 类添加在了插槽中是行不通的，**因为插槽最终会被 App.vue组件中的<div slot="item-text">首页</div> 替换，也就相当于 active类其实并没有被添加到文字所在的div中。**

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/385.png)

 我们的解决思路很简单，就是在slot外面在裹上div，把active类添加到div上即可，比如：

```vuw
<div :class="{active:isActive}">
    <slot name="item-text"></slot>
</div>
```

最终呈现的效果如下所示：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/386.png)

同理，我们也在图片的 slot 外层裹上div，将相应的语法添加到外层的div上，所以本次最终实现的代码如下所示：

```vue
<template>
    <div class="tab-bar-item">
        <div v-if="!isActive"><slot name="item-icon"></slot></div>
        <div v-else><slot name="item-icon-active"></slot></div>
        <div :class="{active:isActive}">
            <slot name="item-text"></slot>
        </div>
    </div>
</template>

<script>
    export default {
        name:'tabbarItem',
        data(){
              return {
                  // 判断图片是否处于激活状态的标志位
                  isActive:true
              }
        }
    }
</script>

<style>
    .tab-bar-item{
        flex:1;
        text-align: center;
        height:49px;
    }
    .tab-bar-item img{
        width: 30px;
        height: 25px;
    }
    /* 文字处于激活状态下的样式 */
    .active{
        color: red;
    }
</style>
```

以上便是今天Tabbar中实现active图片和文字的主要代码和思路，总结如下：

1.active图片是一开始就传入所有的图片，然后根据不同的条件显示不同的图片，而并不是真正使用时才传入的

2.随着图片的数量的增加，也需要添加相应的插槽数量

3.通过设置标志位和 if 判断语句来决定显示那张图片

4.通过标志位来决定文字的样式是否发生改变

5.最后为了避免插槽被替换而导致添加的类名无效，一般在插槽外层包裹一层的div，然后将类、if语句都放到div中。

# Tabbar链接路由

## 一、搭建路由基本框架

  在终端，通过命令 **npm install vue-route --save** 为项目安装路由，然后创建route文件夹来存放和路由有关的组件，紧接着创建index.js文件，在该文件中搭建路由的基本框架。
  搭建路由的基本框架步骤如下：**1.引入路由 --> 2.安装插件 --> 3.创建路由对象 --> 4.导出路由**。其代码如下所示：

```js
// 1.import 引入路由
import Vue from 'vue'
import VueRouter from "vue-router"

// 2.安装插件
Vue.use(VueRouter)

// 3.创建路由对象
//将router实例中的routes抽取出来写
const routes = [
    
]
const router = new VueRouter({
    routes
})

// 4.导出路由
export default router
```

# 二、路由配置

在上面搭建好的框架基础上，我们对路由的映射关系进行配置。
**① 创建目录**
  为什么要强调这点呢？为了以后做项目的时候，更加方便管理自己的代码，不至于因为项目过于庞大而找不到自己相关页面代码。
  首先创建 **view 文件夹**，该文件夹主要放和视图有关的代码；然后在view文件夹下在分别创建 **home、category、car、profile 四个文件夹**，这四个文件夹分别放置和 home、category、car、profile有关的代码文件，具体的文件目录如下所示：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/387.png)

**②配置路由映射关系**
  我们通过**路由懒加载**的方式来建立tabbar-item和各个大组件之间的联系
配置步骤如下：**1.引入文件 --> 2.配置映射关系**。其代码如下：

```js
//index.js 文件
//① 引入文件 
const Home  = () => import ('../view/home/home.vue')
const Car  = () => import ('../view/car/car.vue')
const Category  = () => import ('../view/category/category.vue')
const Profile  = () => import ('../view/profile/profile.vue')

// ② 配置映射关系
const routes = [
    {
        path:"",
        redirect:"/home"
    },
    {
        path:"/home",
        component:Home
    },
    {
        path:"/category",
        component:Category
    },
    {
        path:"/car",
        component:Car
    },
    {
        path:"/profile",
        component:Profile
    }
]
```

在上面的配置关系中，首先通过路由懒加载的方式，只有当某个页面被激活时才会从服务器中下载下来，提高了运行效率;
  接着是当path为空时，就显示首页的信息，让用户一进来就显示首页的信息，而不是需要用户点击tabbar-item的“首页”才显示首页的信息，显得更加的合理；
  最后就是tabbar-item各个子标题的配置。比如当用户点击了“购物车”就会跳转到购物车的页面。
  以上两步就是路由框架的搭建和映射关系的配置过程，接下来我们希望用户点击下面的tabbar-item时就会发生跳转，所以我们还需要在增加点击事件。

## 三、点击事件

##### 1.目的

  添加点击事件的目的很简单，就是希望用户点击“首页”时，上面的内容就变成和首页相关的内容。

##### 2.步骤

**① 添加点击事件**
  如果我们将点击事件添加到App.vue 文件的<tabbarItem></tabberitem> 标签中，就需要添加四个点击事件，显然不够高效，所以我们**将点击事件添加到 tabbar-item.vue文件中的<div class="tab-bar-item"></div>标签中**，其代码如下所示：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/388.png)

**② 事件的处理**
关于事件的处理会比较麻烦，小编从下面几点为大家分析分析：
**2.1 监听是否成功**
  首先我们得要确定在tabbar-item添加的点击事件是成功的，所以在用户点击相应的tabbart-item小标题的时候在控制台打印“11”就证明点击事件是添加成功的。其代码和效果如下所示：

```js
methods:{
    itemClick(){
        console.log('11')
    }
}
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/389.png)

**2.2 链接跳转**
  我们知道，路由的链接跳转是通过**this.$router(参数)**来实现的，小编这里要重点分析的就是参数。
  参数肯定不能是被写死的，因为当用户点击不同的tabbar-item时，传递的跳转链接也是不一样的。所以我们通过 **props属性来声明path变量，并且该变量的类型是String类型**，其代码如下所示：

```js
props:{
    path:String
},

methods:{
    itemClick(){
        this.$router.replace(this.path)
    }
}
```

那么path具体的数值来自哪里呢？就来自 App.vue文件中的tabbart-item标签，比如：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/390.png)

当用户点击了哪个tabbar-item就传递哪个tabbar-item的链接过去。
**2.3 内容的显示**
  为了让内容显示出来，我们要添加**占位标签 <router-view></router-view>**这是很容易忘记的。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/391.png)

最终显示的效果图如下：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/392.gif)

## 完整代码演示：

```vue
<!--tabbar-item代码-->
<template>
    <div class="tab-bar-item" @click="itemClick">
        <div v-if="!isActive"><slot name="item-icon"></slot></div>
        <div v-else><slot name="item-icon-active"></slot></div>
        <div :class="{active:isActive}">
            <slot name="item-text"></slot>
        </div>
    </div>
</template>

<script>
    export default {
        name:'tabbarItem',
        props:{
            path:String
        },
        data(){
              return {
                  isActive:false
              }
        },
        methods:{
            itemClick(){
                console.log('111')
                this.$router.push(this.path);
            }
        }
    }
</script>

<style>
    .tab-bar-item{
        flex:1;
        text-align: center;
        height:49px;
    }
    .tab-bar-item img{
        width: 30px;
        height: 25px;
    }
    /* 文字处于激活状态下的样式 */
    .active{
        color: red;
    }
</style>
```

```js
//index.js 

// 1.import 引入路由
import Vue from 'vue'
import VueRouter from "vue-router"

//① 引入文件 
const Home  = () => import ('../view/home/Home')
const Car  = () => import ('../view/car/Car')
const Category  = () => import ('../view/category/Category')
const Profile  = () => import ('../view/profile/Profile')

// 2.安装插件
Vue.use(VueRouter)

// 3.创建路由对象
// ② 配置映射关系
const routes = [
    {
        path:"",
        redirect:"/home"
    },
    {
        path:"/home",
        component:Home
    },
    {
        path:"/category",
        component:Category
    },
    {
        path:"/car",
        component:Car
    },
    {
        path:"/profile",
        component:Profile
    }
    
]
const router = new VueRouter({
    routes,
    mode:'history'
})

// 4.导出路由
export default router
```

```vue
<!-- App.vue-->
<template>
    <div id="App">
        <router-view></router-view>
        <tabbar>
            <tabbarItem path='/home'>
                <img slot="item-icon" src="./assets/img/tabbar/home.png" alt="">
                <img slot="item-icon-active" src="./assets/img/tabbar/home2.png" alt="">
                <div slot="item-text">首页</div>
            </tabbarItem>
            <tabbarItem path='/category'>
                <img slot="item-icon" src="./assets/img/tabbar/phone.png" alt="">
                <img slot="item-icon-active" src="./assets/img/tabbar/phone2.png" alt="">
                <div slot="item-text">分类</div>
            </tabbarItem>
            <tabbarItem path='/car'>
                <img slot="item-icon" src="./assets/img/tabbar/4.png" alt="">
                <img slot="item-icon-active" src="./assets/img/tabbar/5.png" alt="">
                <div slot="item-text">购物车</div>
            </tabbarItem>
            <tabbarItem path='/profile'>
                <img slot="item-icon" src="./assets/img/tabbar/my.png" alt="">
                <img slot="item-icon-active" src="./assets/img/tabbar/my2.png" alt="">
                <div slot="item-text">我的</div>
            </tabbarItem>
        </tabbar>
    </div>
</template>

<script>
import tabbar from "./components/Tabbar/tabbar.vue"
import tabbarItem from "./components/Tabbar/tabbar-item.vue"
import router from "./router/index.js"
export default {
  name: 'App',
  components: {
    tabbar,
    tabbarItem
  }
}
</script>

<style>
    @import url("./assets/css/base.css");
</style>
```

```js
//main,js
import Vue from 'vue'
import App from './App'
import router from './router'

Vue.config.productionTip = false

new Vue({
  el: '#app',
  router,
  render: h => h(App)
})
```

# Tabbar 动态颜色控制

## 一、计算属性

###### 1.图片动态颜色

**① 不动态的原因**
  回顾之前的知识，我们是通过设置标志位 isActive 的true 和 false 的值来决定图片和文字的活跃状态，显示isActive不是动态的，很难满足我们的要求，所接下来我们就要对isActive进行劳改 ⁄(⁄ ⁄•⁄ω⁄•⁄ ⁄)⁄
**②解决方法**
  大致的思路是，通过计算属性computed来声明isActive，然后通过this.$route来决定isActive的值，具体代码如下：

```vue
//tabbar-item.vue
data(){
    return {
         // isActive:false
      }
},
computed:{
    isActive(){
        return this.$route.path.indexOf(this.path) !== -1
    }
}
```

上述代码中，用 this.$route.path 获取当前活跃状态下的路由的path，通过活跃状态下的路由的path来查找item中的path，没有找到就返回-1。所以总结上面的代码就是：当活跃状态下的路由的path在item中找到相应的path就返回-1，以为着此时的isActive结果为true。我们来看看效果图：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/393.gif)