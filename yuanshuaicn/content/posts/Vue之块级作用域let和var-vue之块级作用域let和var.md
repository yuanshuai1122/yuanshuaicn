---
title: Vue之块级作用域let和var
date: 2021-11-30 10:43:04.143
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/1_7OCwu--TWqVluPMsZdzWKw-34ce1bcaed3b4c59a2183cf00af73987_1622733997113.png'
theme: 'light'
tags: 
- Vue
---

## 1.背景

js的作者Brendan Eich公开说明过var其实是js语言设计上的错误，但是这种错误多半不能修复和移除，所以大概在十几年前，Brendan Eich就修复了这个问题，添加了一个新的关键词：let
let可以看做是更完美的var

## 2.var

1.var
**var在声明一个变量时，该变量只有在函数中才有自己的作用，在if和for中没有自己的作用域。**
作用域是什么？
作用域就是变量和函数生效的区域。

2.var-function

```html
            function text(){
                var a = 123;
                console.log('在函数内打印:' + a);
            }
            text();
            console.log('在函数外打印:' + a);
```

在函数内var的a有自己的作用域，a生效的区域只在这个函数内，外部打印a会显示a is not defined，因为a不存在在外部

这就是作用域的作用

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/45.png)

声明在if或for中的变量没有自己的作用域

## 3.var-if

```html
        if(true) {
            var a = 123;
            console.log(a);
        }
        console.log(a);
```

因为if没有作用域的概念，所以在if内var的a可以被共享，即使在if外也能使用a

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/46.png)

## 4.var-for

实现点击按钮，显示该按钮的序号

```html
            var btns = document.getElementsByTagName('button');
            for(var i = 0; i < btns.length; i++) {
                btns[i].onclick = function() {
                    console.log(i);
                }
            }
```

重大缺陷：无论点击哪个，i都是最后一个。
在for中的i是没有自己的作用域的，且js是异步执行，在js执行完后，才开始渲染页面，那就意味着我们在点击已经渲染出的button时，for循环已经遍历到最后一个，且这个for中的i没有自己的作用域，他是被共享的，遍历四次他就被改变了四次，所以我们无论点击哪个按钮出现的i都是4.

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/47.png)

那这时候，为了实现效果就会引入函数（因为var在函数中有自己的作用域），借助函数的作用域让每个i都有自己的作用域，点击按钮出现相应序号。**这就是我们js常用的闭包。**

```html
            var btns = document.getElementsByTagName('button');
            for(var i = 0; i < btns.length; i++) {
                (function(i){
                    btns[i].onclick = function() {
                        console.log('点击了第' + i + '个按钮');
                    }
                }(i))
            }

```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/48.png)

在function中声明的i都有自己的作用域，这四个作用域互不影响

```html
            i=0
            (function(i){
                btns[i].onclick = function() {
                    console.log('点击了第' + i + '个按钮');
                }
            }(0))
                
            i=1
            (function(i){
                btns[i].onclick = function() {
                    console.log('点击了第' + i + '个按钮');
                }
            }(1))

            i=2
            (function(i){
                btns[i].onclick = function() {
                    console.log('点击了第' + i + '个按钮');
                }
            }(2))

            i=3
            (function(i){
                btns[i].onclick = function() {
                    console.log('点击了第' + i + '个按钮');
                }
            }(3))   
```

## 3.let

用let声明的变量在if、for、function内都有自己的作用域
用let解决点击按钮的问题

```html
            var btns = document.getElementsByTagName('button');
            for(let i = 0; i < btns.length; i++) {
                btns[i].onclick = function() {
                    console.log('点击了第' + i + '个按钮');
                }
            }
```

因为用let声明的变量有自己的作用域，不用像var一样需要借助function生成自己的作用域。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/49.png)

```html
            i=0
            btns[i].onclick = function() {
                console.log('点击了第' + i + '个按钮');
            }
            
            i=1
            btns[i].onclick = function() {
                console.log('点击了第' + i + '个按钮');
            }
            
            i=2
            btns[i].onclick = function() {
                console.log('点击了第' + i + '个按钮');
            }
            
            i=3
            btns[i].onclick = function() {
                console.log('点击了第' + i + '个按钮');
            }   
```

### let和var的区别

**用let声明的变量在if、for、function内都有自己的作用域
用var声明的变量只有在function内有自己的作用域**