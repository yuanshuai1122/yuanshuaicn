---
title: Vue之组件化（三）
date: 2021-07-20 18:38:23.971
author:
  name: "yuanshuai"
  link: "https://cloud.tencent.com/developer/user/8180692"
  email: "shuaiyuan1122@gmail.com"
  avatar: "https://aabb-2023.oss-cn-beijing.aliyuncs.com/hjscijg3uw.png"
tags: 
- Vue
---

# 一、父子组件的通信--父传子

## 一.回顾父子组件

子组件在父组件中注册并使用，在html中使用的是父组件的标签，子组件也会被展示。

1.不用语法糖的方式先创建父子组件的构造器

```html
        <template id="cpn1">
            <div>
                <h2>子组件</h2>
            </div>
        </template>
        <template id="cpn2">
            <div>
                <h2>父组件</h2>
            </div>
        </template>
```

```html
            /*子组件构造器*/
            const cpnC1 = Vue.extend({
                template:'#cpn1',
            })
            
            /*父组件构造器*/
            const cpnC2 = Vue.extend({
                template:'#cpn2'
            })
```

2.在父组件中注册并使用子组件
注册：

```html
            /*父组件构造器*/
            const cpnC2 = Vue.extend({
                template:'#cpn2',
                /*注册子组件*/
                components:{
                    cpn1:cpnC1
                }
            })
```

使用：

```html
        <template id="cpn2">
            <div>
                <h2>父组件</h2>
                <!--使用子组件-->
                <cpn1></cpn1>
            </div>
        </template>
```

3.在Vue实例中注册父组件

```html
            /*根组件*/
            const app = new Vue({
                el:'#app',
                components:{
                    cpn2:cpnC2
                }
            })
```

4.在html中使用父组件

```html
        <div id="app">
            <cpn2></cpn2>
        </div>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/183.png)

#### 1.2、为何需要父子组件的通信

每个组件的数据都存放在自己的data函数中，不可以直接使用其他组件或Vue实例（根组件）中的data数据。
在开发时，页面中展示的数据都是通过网络请求获取而来的动态数据。因为每个组件都是独立存在，即每个组件中的数据都是独立存储的，那每个组件所需要的动态数据都是通过各自发送网络请求而获取来的吗？
由于组件化的思想，一个完整的页面可以根据功能划分成若干个组件，而这些组件也可以根据逻辑功能再次细分。所以一个页面是由许多个组件集成的。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/184.png)

那每一个组件中的数据都通过发送网络请求获取，甚至连蓝色框的用来显示物品详情的小组件也要发送网络请求获取动态数据，这会让网络请求的压力巨增，所以这种获取数据的方式显然是不可能的。

所以这时候可以让大组件（父组件）传送数据给小组件（子组件）。

## 1.3、父子组件通信的方式

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/185.png)

1.父组件通过props属性向子组件传送数据
2.子组件通过事件向父组件发送消息

## 1.4、父组件向子组件通信的方式

组件构造器cpnC2和Vue实例（根组件）也是父子组件关系。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/186.png)

若子组件需要获取父组件data中的message数据，可通过props属性获取。
props属性有很多种写法。
1.props

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/187.png)

2.通过v-bind：动态绑定cmessage属性

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/188.png)

3.在子组件的template中使用数据

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/189.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/190.png)

通过props，子组件可以使用父组件传送过来的数据

## 1.5、子组件props的不同写法

props是对象类型

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/191.png)

cmessage可以是个对象，对象中的属性可以限制父组件传递过来的数据
1.type：验证传递数据的类型
支持验证以下类型
String
Number
Boolean
Array
Object
Date
Function
Symbol

2.requierd：提示cmessage必须被传入

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/192.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/193.png)

3.default：没有绑定cmessage，也就是父组件没有传递数据时，使用{{cmessage}}默认显示的数据

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/194.png)

可参考以下写法：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/195.png)

props也可以是数组类型
绑定和使用过程和上述相同，只是当props是数组时，就缺少了验证以及一些默认的选项，所以数组类型的props不常用。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/196.png)

# 二、父子组件的通信--子传父$emit()

父组件通过子组件的props属性可以将父组件的数据传送给子组件
子组件可以通过$emit()，将自定义事件传递给父组件

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/197.png)

#### 一.子组件需要传递自定义事件的场景

在红色大组件中进行网络请求获取数据，通过子组件中props，将红色组件中的数据传递给各个蓝色组件。但是，当我点击左侧边蓝色组件中的手机数码时，该蓝色组件要将点击手机数码的事件发送给红色组件，红色组件根据发送过来的事件再次发送网络请求获取手机数码的数据，右侧边的组件又通过props获取手机数码的数据并展示。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/198.png)

## 二、子组件如何向父组件传递自定义事件

首先展示按钮组件

```html
        <div id="app">
            <cpn1></cpn1>
        </div>
        <template id="cpn1">
            <div>
                <!--在button中展示目录的名字-->
                <button v-for="item in categories">{{item.name}}</button>       
            </div>
        </template>
        <script src="js/vue.js" type="text/javascript" charset="utf-8"></script>
        <script type="text/javascript">
            /*子组件构造器*/
            const cpnC1 = Vue.extend({
                template:'#cpn1',
                data(){
                    return{
                        categories:[
                            {id:'001',name:'手机数码'},
                            {id:'002',name:'电脑办公'},
                            {id:'003',name:'家用电器'},
                            {id:'004',name:'美妆护肤'},
                        ]
                    }
                }
            })
            
            /*根组件*/
            const app = new Vue({
                el: '#app',
                components: {
                    cpn1:cpnC1
                }
            })
        </script>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/199.png)

1.当点击按钮时触发点击事件，在事件处理函数中向父组件发送自定义事件

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/200.png)

2.在事件处理函数btnclick(item)中发送自定事件
$emit(自定义事件，参数)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/201.png)

3.父组件监听子组件传递的自定义事件

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/202.png)

4.在父组件的methods中设置监听到自定义事件的事件处理函数

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/203.png)

全部过程：
点击子组件的button触发点击事件，在点击事件中发送自定事件，父组件对自定义事件进行监听，并在methods中处理自定义事件，最后打印出当前button的对象。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/204.png)

# 三、父组件访问子组件的方式$children

## 3.1、父访子的方式 $children

当父组件想要直接访问子组件时，可以通过父组件的$children方法直接获取子组件对象。
举例：
父组件通过$children的方法直接获取两个子组件对象

```html
        <div id="app">
            <cpn></cpn>
        </div>
        <template id="cpn">
            <div>
                <cpn1></cpn1>
                <cpn2></cpn2>
                <button @click="showChildCpn">显示所有子组件信息</button>
            </div>
        </template>
        <template id="cpn1">
            <div>
                <h2>子组件1</h2>
            </div>
        </template>
        <template id="cpn2">
            <div>
                <h2>子组件2</h2>
            </div>
        </template>
        <script src="js/vue.js" type="text/javascript" charset="utf-8"></script>
        <script type="text/javascript">
            
            const cpn = Vue.extend({
                template: '#cpn',
                components: {
                    /*子组件1*/
                    cpn1:{
                        template: '#cpn1',
                        data() {
                            return{
                                name:'one'
                            }
                        }
                    },
                    /*子组件2*/
                    cpn2:{
                        template: '#cpn2',
                        data(){
                            return{
                                name:'two'
                            }
                        }
                    }
                },
                methods: {
                    /*打印子组件对象*/
                    showChildCpn() {
                        const children = this.$children
                        for(let i in children) {
                            console.log(children[i]);
                        }
                    }
                }
                
            })
            
            const app = new Vue({
                el: '#app',
                /*注册父组件*/
                components: {
                    cpn: cpn
                }
            })
```

打印出两个子组件对象

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/205.png)

## 3.2、$children的缺点

利用$children获取到的数组类型，访问其中的组件必须通过索引值。
当子组件过多时，往往不能确定他的索引值，所以引进了新的父访子的方式$refs

## 3.3、直接父访子的缺点

虽然可以通过$children的方式直接获取子组件对象，进而获取子组件中的方法和数据，但是在一个组件中直接获取另一个组件去操作数据和方法的方式，会让两个组件的耦合度变高，组件的独立性下降，后期维护困难。

# 四、父组件访问子组件的方式$refs

## 4.1、$refs的使用

$refs和ref是一起使用的，
通过ref给子组件绑定一个id，
使用this.$refs.id就能获取到特定的子组件

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/206.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/207.png)

```html
        <div id="app">
            <cpn></cpn>
        </div>
        <template id="cpn">
            <div>
                <cpn1></cpn1>
                <cpn2 ref="cpn2"></cpn2>
                <button @click="showChildCpn">显示所有子组件信息</button>
            </div>
        </template>
        <template id="cpn1">
            <div>
                <h2>子组件1</h2>
            </div>
        </template>
        <template id="cpn2">
            <div>
                <h2>子组件2</h2>
            </div>
        </template>
        <script src="js/vue.js" type="text/javascript" charset="utf-8"></script>
        <script type="text/javascript">
            
            const cpn = Vue.extend({
                template: '#cpn',
                components: {
                    /*子组件1*/
                    cpn1:{
                        template: '#cpn1',
                        data() {
                            return{
                                name:'one'
                            }
                        }
                    },
                    /*子组件2*/
                    cpn2:{
                        template: '#cpn2',
                        data(){
                            return{
                                name:'two'
                            }
                        }
                    }
                },
                methods: {
                    /*打印子组件对象*/
                    showChildCpn() {
                        console.log(this.$refs.cpn2.name)
                    }
                }
                
            })
            
            const app = new Vue({
                el: '#app',
                /*注册父组件*/
                components: {
                    cpn: cpn
                }
            })
        </script>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/208.png)

获取cpn2子组件的name

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/209.png)

# 五、子组件访问父组件的方式 $parent

## 5.1、$parent的使用

```html
        <div id="app">
            <cpn></cpn>
        </div>
        <template id="cpn">
            <div>
                <h2>子组件</h2>
                <button @click="btnClick">获取父组件对象</button>              
            </div>

        </template>
        <script src="js/vue.js" type="text/javascript" charset="utf-8"></script>
        <script type="text/javascript">
            const app = new Vue({
                el: '#app',
                data: {
                    name: 'father'
                },
                components: {
                    cpn: {
                        template: '#cpn',
                        methods: {
                            btnClick(){
                                console.log(this.$parent);
                            }
                        }
                    }
                }
            })
        </script>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/210.png)

获取父组件的name

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/211.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/212.png)

## 5.2、注意事项

尽管在Vue开发中，我们允许通过$parent来访问父组件，但是在真实开发中尽量不要这样做。
子组件应该尽量避免直接访问父组件的数据，因为这样耦合度太高了。
如果我们将子组件放在另外一个组件之内，很可能该父组件没有对应的属性，往往会引起问题。
另外，更不好做的是通过$parent直接修改父组件的状态，那么父组件中的状态将变得飘忽不定，很不利于我的调试和维护。