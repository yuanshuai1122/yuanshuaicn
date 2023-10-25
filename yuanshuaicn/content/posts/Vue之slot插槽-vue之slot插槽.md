---
title: Vue之slot插槽
date: 2021-posts-30 10:44:35.755
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/1_7OCwu--TWqVluPMsZdzWKw-34ce1bcaed3b4c59a2183cf00af73987_1622733997113.png'
theme: 'light'
tags: 
- Vue
---

# slot插槽

## 一、为什么要使用插槽

在生活中，电脑的USB接口对应不同的设备就提供不同的功能，可以接键盘、鼠标、音响、U盘……
在组件中，slot的使用可以让封装的组件更有扩展性。
使用者可以根据需要修改组件。
比如一个搜索框组件，因为蓝色组件中会变成店铺，所以在封装搜索框组件时，就将这个容易变动的部分放在插槽中，使用者可以根据需要修改插槽部分。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/213.png)

## 二、slot的基本使用

1.在子组件中用slot标签开启一个插槽

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/214.png)

```html
        <div id="app">
            <cpn></cpn>
        </div>
        <template id="cpn">
            <h2>固定展示部分</h2>
            <slot>
                <h2>可以变动的部分</h2>
            </slot>
        </template>
        <script src="js/vue.js" type="text/javascript" charset="utf-8"></script>
        <script type="text/javascript">
            const app = new Vue({
                el: '#app',
                components: {
                    cpn: {
                        template: '#cpn'
                    }
                }
            })
        </script>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/215.png)

2.在父组件中修改slot

```html
        <div id="app">
            <cpn></cpn>
            <cpn>
                <h2>代替插槽部分</h2>
                <p>我也是代替插槽的部分</p>
            </cpn>
        </div>
```

父组件若不修改slot,则slot部分由子组件决定默认展示。
父组件修改slot，则修改的内容会覆盖子组件slot的内容。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/216.png)

## 二、具名插槽slot

当子组件的功能增多时，一个组件中不只设置了一个插槽，那父组件想修改特定的插槽部分时，如何指定其中的一个插槽修改。这时就要给插槽取个名字做标识。

```html
        <div id="app">
            <cpn></cpn>
            <cpn>
                <template v-slot:left>
                    <p>修改left</p>
                </template>
                <template v-slot:default>
                    <p>修改center</p>
                </template>
                <template v-slot:footer>
                    <p>修改footer</p>
                </template>
            </cpn>
        </div>
        <template id="cpn">
            <div>
                <slot name="left">
                    <p>top部分</p>
                </slot>
                <slot>
                    <p>center部分</p>
                </slot>
                <slot name="right">
                    <p>footer部分</p>
                </slot>             
            </div>
        </template>     
        <script src="js/vue.js" type="text/javascript" charset="utf-8"></script>
        <script type="text/javascript">
            const app = new Vue({
                el: '#app',
                components: {
                    cpn: {
                        template: '#cpn'
                    }
                }
            })
        </script>
```

在子组件中利用slot标签的name属性，给各自的slot标记id
父组件在template标签中，利用v-slot的指令，并给v-slot指令指定属性值（子组件的name），从而修改并覆盖子组件中的slot
注意：子组件中默认slot的name是default。

#### 三、作用域插槽

父组件想要使用子组件中的数据，除了$emit()，作用域插槽也提供了方法。

```html
        <div id="app">
            <cpn>
                <template v-slot:center="slotProps">
                    <p>{{slotProps.message}}</p>
                </template>
            </cpn>
        </div>
        <template id="cpn">
            <div>
                <slot name="left">
                    <p>top部分</p>
                </slot>
                <slot name="center" :message="message">
                    <p>center部分</p>
                </slot>
                <slot name="right">
                    <p>footer部分</p>
                </slot>             
            </div>
        </template>     
        <script src="js/vue.js" type="text/javascript" charset="utf-8"></script>
        <script type="text/javascript">
            const app = new Vue({
                el: '#app',
                components: {
                    cpn: {
                        template: '#cpn',
                        data(){
                            return {
                                message:'子组件的数据'
                            }
                        }
                    }
                }
            })
        </script>
```

子组件中的center插槽通过v-bind指令，将子组件中的message绑定到slot上

```html
                <slot name="center" :message="message">
                    <p>center部分</p>
                </slot>
```

slot上的数据被收集到slotProps中，父组件就可以使用slotProps获取子组件的message数据。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/217.png)