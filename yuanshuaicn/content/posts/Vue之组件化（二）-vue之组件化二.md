---
title: Vue之组件化（二）
date: 2021-11-30 10:44:27.773
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/1_7OCwu--TWqVluPMsZdzWKw-34ce1bcaed3b4c59a2183cf00af73987_1622733997113.png'
theme: 'light'
tags: 
- Vue
---

# 一、父组件和子组件的区分

## 1.1.在Vue实例app中注册组件

```html
        <script type="text/javascript">
            /*创建组件构造器1*/
            const cpnC1 = Vue.extend({
                template: `
                <div>
                    <h2>我是组件1</h2>
                </div>
                `
            })
            
            /*创建组件构造器2*/
            const cpnC2 = Vue.extend({
                template: `
                <div>
                    <h2>我是组件2</h2>
                </div>
                `
            })
            
            /*创建Vue实例*/
            /*注册组件cpnC1*/
            /*注册组件cpnC2*/           
            const app = new Vue({
                el: '#app',
                components: {
                    cpn1:cpnC1,
                    cpn2:cpnC2
                }
            })          
        </script>
```

在html中使用组件cpn1和cpn2

```html
        <div id="app">
            <cpn1></cpn1>
            <cpn2></cpn2>
        </div>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/158.png)

## 1.2.父子组件引入

如果将组件1注册在组件2的构造器中，并在组件2的tmplate中使用组件1，在Vue实例中值注册组件2，在html中使用组件cpn2会怎么样？
组件构造器中也有components属性，可以在该属性中注册其他组件
1.在组件构造器2中注册并使用组件1

```html
            /*创建组件构造器1*/
            const cpnC1 = Vue.extend({
                template: `
                <div>
                    <h2>我是组件1</h2>
                </div>
                `
            })

            /*创建组件构造器2*/
            const cpnC2 = Vue.extend({
                template: `
                <div>
                    <h2>我是组件2</h2>
                    <cpn1></cpn1>
                </div>
                `,
                components: {
                    cpn1: cpnC1
                }
            })
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/159.png)

2.在Vue实例中只注册组件2

```html
            const app = new Vue({
                el: '#app',
                components: {
                    cpn2:cpnC2
                }
            })  
```

3.在html中使用组件2

```html
        <div id="app">
            <cpn2></cpn2>
        </div>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/160.png)

## 1.3.父组件和子组件是谁？

因为组件1在组件2构造器中注册，
并在组件2构造器template中使用，
即使组件1并没有在Vue实例中注册，也没有在html中使用，
但是，组件1存在在组件2的模板中，会被一起展示到html中。
所以，
组件2是父组件，
组件1是子组件

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/161.png)

# 二、注册组件的语法糖写法

## 2.1.全局注册组件语法糖

全局组件不用语法糖的构建过程：
生成组件构造器

```html
            /*全局组件构造器*/
            const cpnC1 = Vue.extend({
                template: `
                    <div>
                        <h2>全局组件</h2>
                    </div>
                `
            })
```

注册组件

```html
            /*注册全局组件*/
            Vue.component('cpn1',cpnC1);
```

使用组件

```html
        <div id="app">
            <cpn1></cpn1>
        </div>
```

全局注册组件的语法糖，省略extend步骤，直接在注册组件的第二参数中创建组件构造器

1.语法糖写法下，注册组件的参数

```html
            Vue.component('cpn1',{
                template: `
                    <div>
                        <h2>全局组件</h2>
                    </div>              
                `
            })
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/162.png)

在html中使用全局组件

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/163.png)

## 2.2.局部注册组件语法糖

局部注册组件不用语法糖的构建过程：
生成组件构造器

```html
            const cpnC2 = Vue.extend({
                template: `
                    <div>
                        <h2>局部组件</h2>
                    </div>              
                `
            })
```

在Vue实例中注册局部组件

```html
            const app = new Vue({
                el: '#app',
                components: {
                    cpn2: cpnC2
                }
            })
```

使用局部组件

```html
        <div id="app">
            <cpn2></cpn2>
        </div>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/164.png)

注册局部组件时，components对象中，一对key value对应的是组件标签和组件构造器，使用语法糖注册时，在value构造器的位置，传入构造器的具体代码

```html
            const app = new Vue({
                el: '#app',
                components: {
                    cpn2: {
                        template: `
                            <div>
                                <h2>局部组件</h2>
                            </div>              
                        `
                    }
                }
            })
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/165.png)

在html中使用局部组件

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/166.png)

## 2.3.总结注册组件的语法糖写法

```html
            /*全局注册组件语法糖*/       
            Vue.component('cpn1',{
                template: `
                    <div>
                        <h2>全局组件</h2>
                    </div>              
                `
            })
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/167.png)

```html
            const app = new Vue({
                el: '#app',
                components: {
                    cpn2: {
                        template: `
                            <div>
                                <h2>局部组件</h2>
                            </div>              
                        `
                    }
                }
            })
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/168.png)

# 三、template分离写法

## 3.1.使用注册组件语法糖

```html
            Vue.component('cpn1',{
                template:`
                <div>
                    <h2>template未分离</h2>
                </div>
                `
            })  
```

在template中编写html代码是没有自动补齐的功能，并且当模板中的html代码变多时，注册组件部分看上去就会变得复杂。
所以，这时候就需要将template部分分离出来。

## 3.2.分离template--script

用script标签分离template时的注意点：
1.script标签的类型是 type="text/x-template"
2.script标签要有id属性，用于标识模板

```html
        <script type="text/x-template" id="cpn1">
            <div>
                <h2>template分离</h2>
            </div>
        </script>
        <script type="text/javascript">
            Vue.component('cpn1',{
                template:cpn1
            })          
            const app = new Vue({
                el: '#app'
            })
        </script>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/169.png)

在html中使用组件

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/170.png)

## 3.3.template分离--template标签（常用）

使用template标签分离template模板的注意点：
要有id标签标识模板

```html
        <template id="cpn1">
            <div>
                <h2>template分离--template标签</h2>
            </div>
        </template>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/171.png)

在html中使用组件

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/172.png)

# 四、组件中的数据存储的位置

## 4.1.组件中数据存放位置的引入

组件的html部分可以存放在template标签中，并且template标签是独立在Vue实例外的，
若template中的数据也需要动态化的话，那动态数据存放在哪？
是否同样存放在Vue实例中呢？

验证Vue组件是否能使用Vue实例data中的数据：

```html
        <div id="app">
            <cpn1></cpn1>
        </div>
        <template id="cpn1">
            <div>
                <h2>{{message}}</h2>
            </div>
        </template>
        <script src="js/vue.js" type="text/javascript" charset="utf-8"></script>
        <script type="text/javascript">
            Vue.component('cpn1',{
                template:'#cpn1'
            })
            const app = new Vue({
                el: '#app',
                data: {
                    message: 'hello'
                }
            })
        </script>
```

报错：message没有定义

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/173.png)

说明Vue组件不能使用Vue实例data中的数据。
即使组件能使用Vue实例中的数据，但是一个页面划分成若干个组件，每个组件中的数据都存放在Vue实例中的data里，会让Vue实例变得非常臃肿。
所以无论如何，Vue组件中的数据是不会存放在Vue实例中的。

因为Vue组件的原型链上有Vue实例的原型，所以Vue组件的数据应该存放在自己的data内。

## 4.2.组件中数据的存放位置

```html
        <template id="cpn1">
            <div>
                <h2>{{message}}</h2>
            </div>
        </template>
```

组件中的数据存放在组件的data内，并且data是个函数，函数内return回一个对象，在return回的对象中存放数据

```html
            Vue.component('cpn1',{
                template:'#cpn1',
                data(){
                    return{
                        message:'hello'
                    }
                }
            })
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/174.png)

# 五、组件中的data为什么必须是个函数

## 5.1.组件化一个计数器功能

在+button上绑定increase的点击事件，触发increase事件，让counter加1
在-button上绑定decrease的点击事件，触发decrease事件，让counter减1

```html
        <div id="app">
            <cpn1></cpn1>
            <cpn1></cpn1>
            <cpn1></cpn1>           
        </div>
        <template id="cpn1">
            <div>
                <h2>当前计数器：{{counter}}</h2>
                <button @click="increase">+</button>
                <button @click="decrease">-</button>                
            </div>

        </template>
        <script src="js/vue.js" type="text/javascript" charset="utf-8"></script>
        <script type="text/javascript">
            Vue.component('cpn1',{
                template:'#cpn1',
                data(){
                    return {
                        counter:0
                    }
                },
                methods:{
                    increase(){
                        this.counter++;
                    },
                    decrease(){
                        this.counter--;
                    }
                }
            })
            
            const app = new Vue({
                el: '#app'
            })
        </script>
```

各个组件化出的计数器互不影响

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/175.png)

## 5.2.为什么组件中的data必须是个函数

首先：data若是个对象的话，会报错
data是个对象

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/176.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/177.png)

那么手动让data返回的是一个对象

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/178.png)

这么写就相当于data函数返回的都是同一个对象，和下面写法的效果一样

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/179.png)

再测试计数器
三个计数器不会相互独立，操作其中一个全部同时改变。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/180.png)

这是因为三个Vue组件实例共享的是同一个对象中的数据，所以当数据变化时，所以的Vue实例都同步变化

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/181.png)

所以组件中的data必须是个函数，函数中return出的对象都是独立的，每实例化一个组件，该组件就会获取一个独立的data对象，即三个计数器组件实例获取的是三个return出的data对象，所以三个计数器功能互不影响。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/182.png)