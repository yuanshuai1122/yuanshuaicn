---
title: webApis06-navigator、history、本地存储、线程、数组方法、正则表达式
date: 2022-08-14 17:57:58.329
author:
  name: "yuanshuai"
  link: "https://cloud.tencent.com/developer/user/8180692"
  email: "shuaiyuan1122@gmail.com"
  avatar: "https://aabb-2023.oss-cn-beijing.aliyuncs.com/hjscijg3uw.png"
tags: 
- javascript
- webapi
---

## navigator对象

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>薪资想过万，代码敲三遍</title>
</head>

<body>
    <script>
        /*
            // 检测 userAgent（浏览器信息）
            !(function () {
                const userAgent = navigator.userAgent
                // 验证是否为Android或iPhone
                const android = userAgent.match(/(Android);?[\s\/]+([\d.]+)?/)
                const iphone = userAgent.match(/(iPhone\sOS)\s([\d_]+)/)
                // 如果是Android或iPhone，则跳转至移动站点
                if (android || iphone) {
                    location.href = 'http://m.itcast.cn'
                }
            
            })()
        */
        console.log(navigator);
        // 检测 userAgent（浏览器信息）
        !(function () {
            const userAgent = navigator.userAgent
            // 验证是否为Android或iPhone
            const android = userAgent.match(/(Android);?[\s\/]+([\d.]+)?/)
            const iphone = userAgent.match(/(iPhone\sOS)\s([\d_]+)/)
            // 如果是Android或iPhone，则跳转至移动站点
            if (android || iphone) {
                location.href = 'http://www.ruruzi.com'
            }

        })()
    </script>
</body>

</html>
```

## history对象

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>薪资想过万，代码敲三遍</title>
</head>

<body>
    <a href="http://www.baidu.com">百度</a>
    <hr>

    <button id="back">后退</button>
    <button id="forward">前进</button>


    <script>
        const btn1=document.querySelector('#back')
        const btn2=document.querySelector('#forward')
        btn1.addEventListener('click',function(){
            history.back()
        })
        btn2.addEventListener('click',function(){
            history.forward()
        })
    </script>
</body>

</html>
```

## 本地存储

### localStorage

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>薪资想过万，代码敲三遍</title>
</head>

<body>
    <button class="btn1">存数据</button>
    <button class="btn2">取数据</button>
    <button class="btn3">删数据</button>
    <button class="btn4">更新数据</button>


    <script>
        // 1.存数据
        const btn1 = document.querySelector('.btn1')
        btn1.addEventListener('click', function () {
            localStorage.setItem('height', 180),
                localStorage.setItem('weight', 100)
        })
        // 2.取数据
        const btn2 = document.querySelector('.btn2')
        btn2.addEventListener('click', function () {
            console.log(localStorage.getItem('height'))
            console.log(localStorage.getItem('age'))//取不存在的数据---null
        })
        // 3.删数据
        const btn3 = document.querySelector('.btn3')
        btn3.addEventListener('click', function () {
            localStorage.removeItem('height')
        })
        // 4.更新数据
        const btn4 = document.querySelector('.btn4')
        btn4.addEventListener('click', function () {
            localStorage.setItem('weight', 120)
        })

    </script>
</body>

</html>
```

### sessionStorage

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>薪资想过万，代码敲三遍</title>
</head>

<body>
    <button class="btn1">存数据</button>
    <button class="btn2">取数据</button>
    <button class="btn3">删数据</button>
    <button class="btn4">更新数据</button>


    <script>
        const btn1 = document.querySelector('.btn1')
        btn1.addEventListener('click', function () {
            // 存数据
            sessionStorage.setItem('uu', '小明')
        })
    </script>
</body>

</html>
```

### 本地存储只能存字符串

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>薪资想过万，代码敲三遍</title>
</head>

<body>
    <script>
        const obj = {
            uname: 'lisi',
            age: 18,
            gender: '男'
        }
        /*
            1.本地存储只能存字符串
            2.如果存储其他类型的数据，会被自动转换成字符串存进去
            3.在转换时，有些数据无法转成有效的字符串[object Object]
            4.取出的数据也是字符串类型
        */
        // 1.存对象
        localStorage.setItem('oo', obj)
        localStorage.setItem('age', 18)
        // 2.取数据
        console.log(localStorage.getItem('age'));
        console.log(typeof localStorage.getItem('age'));//string
    </script>
</body>

</html>
```

### JSON

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>薪资想过万，代码敲三遍</title>
</head>

<body>
    <button class="btn1">存数据</button>
    <button class="btn2">取数据</button>

    <script>
        const obj = {
            uname: 'lisi',
            age: 18,
            gender: '男'
        }
        const btn1 = document.querySelector('.btn1')
        btn1.addEventListener('click', function () {
            // JSON.stringfy将数组和对象转换成字符串存储
            // 将对象转换成JSON字符串
            const str = JSON.stringify(obj)
            console.log(typeof str);//string
            // 存数据
            localStorage.setItem('oo', str)
        })
        const btn2 = document.querySelector('.btn2')
        btn2.addEventListener('click', function () {
            // 将字符串转换为对象取出来
            const result = JSON.parse(localStorage.getItem('oo'))
            console.log(typeof result);//object
            console.log(result);
        })


    </script>
</body>

</html>
```

## 线程-同步异步

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>薪资想过万，代码敲三遍</title>
</head>

<body>
    <script>
        console.log('111')

        setTimeout(function () {
            console.log('222')
        }, 0)

        console.log('333')
        // 单线程 111 333 222
    </script>
</body>

</html>
```

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>薪资想过万，代码敲三遍</title>
</head>

<body>
    <button>按钮</button>

    <script>
        setTimeout(function () {
            console.log(0)
        }, 0)

        console.log(1)

        document.addEventListener('click', function () {
            console.log(4)
        })

        console.log(2)

        setTimeout(function () {
            console.log(3)
        }, 3000)
        // 1 2 0 4 3或 1 2 0 3 4

    </script>
</body>

</html>
```

## 数组方法

### map

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>薪资想过万，代码敲三遍</title>
</head>

<body>
    <script>
        const arr = ['red', 'green', 'blue']

        /******** 需求：把数组中的元素处理成 'red老师' 'green老师' 'blue老师' ********/
        /*
            语法：数组.map(处理函数)
            作用：迭代数组，处理元素，把处理后的元素放入新数组中
                1.迭代：遍历数组中每一个元素
                2.数组中有几个元素就执行几次函数
                3.处理函数（元素，元素的索引号）
                4.处理函数的返回值，会放到新数组中
            返回值：新数组
        */
        const result = arr.map(function (item, index) {
            return item 
        })
        console.log(result);

        // 将数组元素处理成'<li>red</li>' '<li>green</li>' '<li>blue</li>'
        const newArr=arr.map(function(item,index){
            return `<li>${item}</li>`
        })
        console.log(newArr);

    </script>
</body>

</html>
```

### join

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>薪资想过万，代码敲三遍</title>
</head>

<body>
    <ul>

    </ul>
    <script>
        const arr = ['red', 'green', 'blue']
        // 将数组变成'red-green-blue'
        /*
            语法：数组.join('分隔符')
            作用：将数组所有元素连接成为一个字符串
            注意：如果没有传入任何分隔符，默认分隔符为，号
        */
        const result1 = arr.join('-')
        console.log(result1);//'red-green-blue'
        const result2 = arr.join('&')
        console.log(result2);//'red$green$blue'
        const result3 = arr.join()
        console.log(result3);//'red,green,blue'
        const result4 = arr.join('')
        console.log(result4);//'redgreenblue'
    </script>
</body>

</html>
```

### 渲染数据新思路

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>薪资想过万，代码敲三遍</title>
</head>

<body>
    <ul>

    </ul>
    <script>
        const arr = ['red', 'green', 'blue']
        // 将数组元素放入li标签并连接起来
        // 尽量减少dom操作
        const newArr=arr.map(function(item,index){
            return `<li>${item}</li>`
        })
        document.querySelector('ul').innerHTML=newArr.join('')


    </script>
</body>

</html>
```

## 正则表达式

### test

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>薪资想过万，代码敲三遍</title>
</head>

<body>
    <script>
        const str = '我们在学习前端，希望学习前端能高新毕业'
        // 定义正则表达式
        // 想要的字符串描述
        const reg1=/我们/
        console.log(typeof reg1);//object
        // 调用一些方法（查找/匹配）
        const result=reg1.test(str)
        console.log(result);//true,如果查找不存在的元素则返回结果为false
    </script>
</body>

</html>
```

### exec

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>薪资想过万，代码敲三遍</title>
</head>

<body>
    <script>
        const str = '我们在学习前端，希望学习前端能高新毕业'
          // 定义正则表达式
        // 想要的字符串描述
        const reg1=/我们/
        console.log(typeof reg1);//object
        // 调用一些方法（查找/匹配）
        const result=reg1.exec(str)
        console.log(result);//返回一个数组，如果查找不到，则返回null
    </script>
</body>

</html>
```

