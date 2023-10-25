---
title: Vue之v-model
date: 2021-posts-30 10:44:20.736
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/1_7OCwu--TWqVluPMsZdzWKw-34ce1bcaed3b4c59a2183cf00af73987_1622733997113.png'
theme: 'light'
tags: 
- Vue
---

# v-model

## 一、v-model是什么

v-model就是vue的双向绑定的指令，能将页面上控件输入的值同步更新到相关绑定的data属性，也会在更新data绑定属性时候，更新页面上输入控件的值。

### 1.1、为什么使用v-model

v-model作为双向绑定指令也是vue两大核心功能之一，使用非常方便，提高前端开发效率。在view层，model层相互需要数据交互，即可使用v-model。

## 二、v-model radio（单选框）

背景：存在多个单选框时，将所选择的单选框中的值保存下来发往后端做数据处理。

```html
        <div id="app">
            <label for="male">
                <input type="radio" id="male" value="男" name="sex"/>男
            </label>
            <label for="female">
                <input type="radio" id="female" value="女" name="sex"/>女
            </label>            
        </div>
```

表单的静态效果

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/123.png)

利用v-model实现input和数据的双向绑定，动态获取所点击的input的value

```html
        <div id="app">
            <label for="male">
                <input type="radio" id="male" value="男" name="sex" v-model="sex"/>男
            </label>
            <label for="female">
                <input type="radio" id="female" value="女" name="sex" v-model="sex"/>女
            </label>            
            <h2>您选择的性别是：{{sex}}</h2>
        </div>
        <script src="js/vue.js" type="text/javascript" charset="utf-8"></script>
        <script type="text/javascript">
            const app = new Vue({
                el: '#app',
                data: {
                    sex:''
                }
            })
        </script>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/124.png)

没有v-model时，为了使用户只能点击其中一个选项，需要用name属性做多选的限制，name属性就像key，一次只能有一个key的数据被保存。
使用v-model以后，name属性可以不用写。

```html
            <label for="male">
                <input type="radio" id="male" value="男" v-model="sex"/>男
            </label>
            <label for="female">
                <input type="radio" id="female" value="女" v-model="sex"/>女
            </label>    
```

## 三、v-model select（复选框）

### 3.1.单选 v-model绑定的是一个值

```html
        <div id="app">
            <select v-model="fruit">
                <option value="香蕉">香蕉</option>
                <option value="苹果">苹果</option>
                <option value="梨">梨</option>
            </select>
            <h2>您选择的水果是{{fruit}}</h2>
        </div>
        <script src="js/vue.js" type="text/javascript" charset="utf-8"></script>
        <script type="text/javascript">
            const app = new Vue({
                el: '#app',
                data: {
                    fruit:'香蕉'
                }
            })
        </script>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/125.png)

### 3.2.多选 v-model绑定的是一个数组

```html
        <div id="app">
            <select v-model="fruits" multiple="multiple">
                <option value="香蕉">香蕉</option>
                <option value="苹果">苹果</option>
                <option value="梨">梨</option>
            </select>
            <h2>您选择的水果是{{fruits}}</h2>
        </div>
        <script src="js/vue.js" type="text/javascript" charset="utf-8"></script>
        <script type="text/javascript">
            const app = new Vue({
                el: '#app',
                data: {
                    fruits:[]
                }
            })
        </script>
```

选择时要按住ctrl再多选

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/126.png)

## 四、v-model的修饰符（.lazy、.number、.trim）

### 4.1 .lazy

默认情况下，v-model绑定的value和数据都是同步更新，一旦input中的value发生变化，对应的数据马上也发生变化。
.lazy修饰符，可以监测到input输入时只要按下回车，数据才会更新

```html
        <div id="app">
            <input type="text" v-model.lazy="message"/>
            <h2>{{message}}</h2>
        </div>
        <script src="js/vue.js" type="text/javascript" charset="utf-8"></script>
        <script type="text/javascript">
            const app = new Vue({
                el: '#app',
                data: {
                    message: 'hello'
                }
            })
        </script>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/127.png)

按下回车，数据才更新

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/128.png)

### 4.2 .number

默认情况下，input输入框中无论输入的是数字还是字符串，都会被当做字符串处理，但是当我们想处理的是数字类型的数据时，.number修饰符就能把数据类型改成number
未加修饰符时，数字类型也被当做字符串

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/129.png)

添加.number修饰符

```html
        <div id="app">
            <input type="number" v-model.number="age"/>
            <h2>{{typeof age}}</h2>
        </div>
        <script src="js/vue.js" type="text/javascript" charset="utf-8"></script>
        <script type="text/javascript">
            const app = new Vue({
                el: '#app',
                data: {
                    age: 18
                }
            })
        </script>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/130.png)

### 4.3 .trim

.trim修饰符会自动忽略input输入框中首尾的空格

未加修饰符时，首尾空格不会被消除

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/131.png)

添加修饰符

```html
        <div id="app">
            <input type="text" v-model.trim="name"/>
            <h2>{{name}}</h2>
        </div>
        <script src="js/vue.js" type="text/javascript" charset="utf-8"></script>
        <script type="text/javascript">
            const app = new Vue({
                el: '#app',
                data: {
                    name: ''
                }
            })
        </script>
```

开头的空格被删除了

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/132.png)