---
title: Vue之const的使用及注意点
date: 2021-post-30 10:43:09.014
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/1_7OCwu--TWqVluPMsZdzWKw-34ce1bcaed3b4c59a2183cf00af73987_1622733997113.png'
theme: 'light'
tags: 
- Vue
---

## 1.const定义的标识符必须初始化

```html
            const a;
```

报错：const标识符未初始化

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/50.png)

定义并赋值：

```html
            const a = 1;
```

## 2.const修饰的标识符不能被修改

```html
            const a = 1;
            a = 2;
```

报错：
再次给const修饰的标识符赋值

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/51.png)

## 3.常量的含义是指向的对象（内存地址）不能改变，对象的内部的属性可以被改变

修改obj内部的属性，不报错

```html
            const obj = {
                name: 'sunny',
                age: 18,
                sex: 'male'
            }
            console.log(obj);
            obj.name = 'cherry';
            obj.age = 20;
            console.log(obj);
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/52.png)

修改obj的指向，给obj重新分配一块空间

```html
            const obj = {
                name: 'sunny',
                age: 18,
                sex: 'male'
            }
            //给obj重新分配一个空间对象
            const obj = {};
```

报错：
const修饰的obj常量，已经存在并且被定义

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/53.png)

原理：
obj常量是通过地址去寻找属于他的空间

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/54.png)

我们可以修改X0001地址内属性，obj常量仍然指向这个地址。
但是当给obj常量赋一个新的对象时，也就意味着obj常量中存储的地址被修改，这时就会报错。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/55.png)

**在开发过程中，尽量使用const定义变量，可以提醒伙伴不要修改这个常量**