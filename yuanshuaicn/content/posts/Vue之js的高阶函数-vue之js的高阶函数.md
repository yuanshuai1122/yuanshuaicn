---
title: Vue之js的高阶函数
date: 2021-11-30 10:43:58.825
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/1_7OCwu--TWqVluPMsZdzWKw-34ce1bcaed3b4c59a2183cf00af73987_1622733997113.png'
theme: 'light'
tags: 
- Vue
---

# js的高阶函数（ filter()、map()、reduce() ）

arr = [20,40,12,232,23,232];
需求1：找出数组中数值低于100的元素，组成新数组并返回
需求2：对返回的数组中的每个元素都乘2
需求3：汇总元素，将每一个元素相加并返回新数组。

## 一.找出数组中数值低于100的元素，组成新数组并返回

### 1.for循环遍历

```html
        const arr = [20,40,12,232,23,232];
        const newArr = [];
        for(let i = 0; i < arr.length; i++) {
            if(arr[i] < 100) {
                newArr.push(arr[i]);
            }
        }
        console.log(newArr);
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/99.png)

### 2.for in

i是arr的下标

```html
        for(let i in arr) {
            if(arr[i] < 100) {
                newArr.push(arr[i]);
            }
        }
        console.log(newArr);
```

### 3.for of

item是循环到当前的arr[i]

```html
        for(let item of arr) {
            if(item < 100) {
                newArr.push(item);
            }
        }
        console.log(newArr);
```

虽然利用for in、for of对for循环的代码进行改进，但仍然需要手动遍历数组

### 4.filter(callback(n))

filter()的第一个参数是个函数

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/100.png)

查看参数函数中的参数n是啥

```html
        const newArr = arr.filter(function(n){
            console.log(n);
        })
        console.log(newArr);
```

n是arr中的每个元素，就和for(item of arr)中的item意义相同

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/101.png)

规则：
1.若函数内返回的是true，就将当前的n添加到隐式的数组中，
2.若函数内返回的是false，就将当前的n过滤掉，系统自动遍历下一个n
最后，用一个常量接收返回的数组。
所以filter函数是根据表达式的布尔值，判断是否要过滤掉该元素

验证：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/102.png)

因为表达式都为ture,所以数组中的每个一元素都不会被过滤，都被添加到隐式的数组中，最后返回给newArr

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/103.png)

实现第一个需求：

```html
        const newArr = arr.filter(function(n){
            return n < 100;
        })
        console.log(newArr);
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/104.png)

## 二、对返回的数组中的每个元素都乘2

以1为例，同样通三种for循环引出高阶函数map()，感受高阶函数的便利

### 1.for

```html
        for(let i = 0; i < newArr.length; i++) {
            newArr[i] = newArr[i] * 2;
        }
        console.log(newArr);
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/105.png)

### 2.for in

```html
        for(let i in newArr) {
            newArr[i] = newArr[i] * 2;
        }
        console.log(newArr);
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/106.png)

### 3.for of

```html
        const newArr2 = [];
        for(let item of newArr) {
            item = item * 2;
            newArr2.push(item);
        }
        console.log(newArr2);
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/107.png)

### 4.map(callback(n))

高阶函数中的参数也是函数

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/108.png)

参数函数中的参数n和filter相同，都是遍历到当前位置的数组的值

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/109.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/110.png)

规则：
给数组中的每一个元素做统一的操作，并把操作后的元素添加到隐式的数组中，最后用一个常量接收。

实现需求2;

```html
        const newArr2 = newArr.map(function(n){
            return n * 2;
        })
        console.log(newArr2);
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/111.png)

## 三、汇总元素，将每一个元素相加并返回新数组。

### 1.for

```html
        let result = 0;
        for(let i = 0; i < newArr2.length; i++) {
            result += newArr2[i];
        }
        console.log(result);
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/112.png)

### 2.for in

```html
        for(let i in newArr2) {
            result += newArr2[i]
        }
        console.log(result);
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/113.png)

### 3.for of

```html
        for(let item of newArr2) {
            result += item;
        }
        console.log(result);
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/114.png)

### 4.reduce(callback(previousValue,n),value)

```html
        const newArr3 = newArr2.reduce(function(){},0)
```

reduce()有两个参数，第一个参数是函数，第二个参数是初始值。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/115.png)

```html
        const newArr3 = newArr2.reduce(function(preValue,n){
            console.log(preValue)
            return 100;
        },0)
```

数组中有4个元素，一共遍历4次

第一次：
0作为preValue的初始值，第一次的preValue=0；

第二次：
第一次return的100会作为第二次的preVule，第二次的preValue=0;

第三次：
第二次return的100会作为第三次的preVule，第三次的preValue=0;

第四次：
第三次return的100会作为第四次的preVule，第四次的preValue=0;

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/116.png)

所以preVlue存储的就是上一次的返回结果。

实现需求3：

```html
        const newArr3 = newArr2.reduce(function(preValue,n){
            return preValue += n;
        },0)
        console.log(newArr3);
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/117.png)

## 四、利用高阶函数综合实现全部需求

```html
        const arr = [20,40,12,232,23,232];
        const newArr = arr.filter(function(n){
            return n < 100;
        }).map(function(n){
            return n * 2;
        }).reduce(function(preValue,n){
            return preValue += n;
        },0);
        console.log(newArr);
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/118.png)