---
title: Vue之ES6对象字面量增强写法
date: 2021-07-20 18:38:23.971
author:
  name: "yuanshuai"
  link: "https://cloud.tencent.com/developer/user/8180692"
  email: "shuaiyuan1122@gmail.com"
  avatar: "https://aabb-2023.oss-cn-beijing.aliyuncs.com/hjscijg3uw.png"
tags: 
- Vue
---

## 1.生成对象的方法

### 1.利用对象构造函数

```html
            const obj = new Object();
```

### 2.字面量写法

```html
            const obj = {
                
            }
```

## 2.属性的增强写法

### 1.ES5

```html
            const name = 'sunny',
                   age = 18,
                   sex = 'male';

            const obj = {
                name: name,
                age: age,
                sex: sex
            }
            console.log(obj);
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/56.png)

### 2.ES5属性增强写法

```html
            const name = 'sunny',
                   age = 18,
                   sex = 'male';

            const obj = {
                name,
                age,
                sex
            }
            console.log(obj);
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/57.png)

## 3.函数增强写法

### 1.ES5

```html
            const obj = {
                eat: function() {
                    console.log('eatting');
                },
                run: function() {
                    console.log('running');
                }
            }
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/58.png)

### 2.ES6函数增强写法

```html
            const obj = {
                eat() {
                    console.log('eatting');
                },
                run() {
                    console.log('running');
                }
            }
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/59.png)