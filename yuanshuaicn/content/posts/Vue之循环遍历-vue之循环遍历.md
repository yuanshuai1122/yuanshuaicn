---
title: Vue之循环遍历
date: 2021-07-20 18:38:23.971
author:
  name: "yuanshuai"
  link: "https://cloud.tencent.com/developer/user/8180692"
  email: "shuaiyuan1122@gmail.com"
  avatar: "https://aabb-2023.oss-cn-beijing.aliyuncs.com/hjscijg3uw.png"
tags: 
- Vue
---

# Vue循环遍历

## 一、v-for遍历数组和对象

### 1.1.遍历数组不显示index（下标）

```html
        <div id="app">
            <ul>
                <li v-for="item in colors">{{item}}</li>
            </ul>
        </div>
        <script src="js/vue.js" type="text/javascript" charset="utf-8"></script>
        <script type="text/javascript">
            const app = new Vue({
                el: '#app',
                data: {
                    colors: ['red','yellow','blue','green']
                }
            })
        </script>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/90.png)

### 1.2.遍历数组并显示index(下标)

```html
        <div id="app">
            <ul>
                <li v-for="(item,index) in colors">
                    {{index+1}}.{{item}}
                </li>
            </ul>
        </div>
```

![91](img/91.png)

### 1.3.遍历对象显示value值

```html
        <div id="app">
            <ul>
                <li v-for="value in obj">{{value}}</li>
            </ul>
        </div>
        <script src="js/vue.js" type="text/javascript" charset="utf-8"></script>
        <script type="text/javascript">
            const app = new Vue({
                el: '#app',
                data: {
                    obj: {
                        name: 'sunny',
                        age: 18,
                        sex: 'male'
                    }
                }
            })
        </script>
```

只有一个变量时，该变量对应对象的value值

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/92.png)

### 1.4.遍历对象显示key和value

```html
        <div id="app">
            <ul>
                <li v-for="(value,key) in obj">{{key}}:{{value}}</li>
            </ul>
        </div>
```

有两个变量时，第一个变量代表value值，第二个变量代表key

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/93.png)

### 1.5.遍历对象显示key、value和index

```html
        <div id="app">
            <ul>
                <li v-for="(value,key,index) in obj">
                    {{index}}-{{key}}:{{value}}
                </li>
            </ul>
        </div>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/94.png)

## 二、列表案例（v-for、v-bind：、@click）

电影列表案例
默认第一个li为红色，再点击哪个li，该li字体颜色变红

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/95.png)

思路：初始化currentIndex为0，用作记录第0个li的位置，后点击哪个li，就把该li的位置赋给cuurentIndex

1.用v-bind的对象语法动态绑定class，当currentIndex和当前li的index相同时，才给该li添加active类名。默认的currentIndex为0，只有第一个index为0的li才被添加上active的类名，则实现第一个li字体为红色的默认样式。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/96.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/97.png)

2.利用点击事件，动态给currentIndex赋该li的index值，则点击哪个li，那个li就会被添加上active的类名，完成最终效果

```html
        <div id="app">
            <ul>
                <li v-for="(item,index) in movies" :class="{active:currentIndex===index}" @click="liClick(index)">
                    {{item}}
                </li>
            </ul>
        </div>
        <script src="js/vue.js" type="text/javascript" charset="utf-8"></script>
        <script type="text/javascript">
            const app = new Vue({
                el: '#app',
                data: {
                    currentIndex: 0,
                    movies: ['大理寺日志','进击的巨人','罗小黑战记','一人之下']
                },
                methods: {
                    liClick(index){
                        this.currentIndex = index;
                    }
                }
            })
        </script>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/98.png)