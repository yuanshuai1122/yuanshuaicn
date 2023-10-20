---
title: webApis05-swiper插件、window对象、定时器、location
date: 2022-08-14 17:57:12.834
author: 'yuanshuai'
cover: 'https://aabb-2023.oss-cn-beijing.aliyuncs.com/%E4%B8%8B%E8%BD%BD%20(1).png'
theme: 'light'
tags: 
- javascript
- webapi
---

## 节点

### 查找父节点

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
    <div class="yeye">
        <div class="father">
            <div class="son"></div>
        </div>
    </div>
    <script>
        const son = document.querySelector('.son')
        // 获取父亲节点
        console.log(son.parentNode);
        // 获取爷爷节点
        console.log(son.parentNode.parentNode);

    </script>
</body>

</html>
```

### 查找子节点

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
        <li>1</li>
        <li>2</li>
        <li>3</li>
        <li>4</li>
        <li>5</li>
    </ul>


    <script>

        const ul = document.querySelector('ul')
        // 获取所有子节点（包括空格等等）
        console.log(ul.childNodes);
        // 获取所有子元素节点
        console.log(ul.children);
        // 获取第一个子元素节点
        console.log(ul.children[0]);

    </script>
</body>

</html>
```

### 查找兄弟节点

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
        <li>1</li>
        <li>2</li>
        <li class="item">3</li>
        <li>4</li>
        <li>5</li>
    </ul>
    <script>
        const item = document.querySelector('.item')
        // 获取上一个兄弟节点
        console.log(item.previousElementSibling);
        // 获取下一个兄弟节点
        console.log(item.nextElementSibling);
    </script>
</body>

</html>
```

### 创建增加节点

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
        <li>我是原来的1</li>
        <li>我是原来的2</li>
    </ul>
    <ol>
        <li>键盘敲烂</li>
        <li>月薪过万</li>
    </ol>

    <script>
        // 创建节点
        const newLi = document.createElement('li')
        const ul = document.querySelector('ul')
        
        // 追加节点 ：父元素.appendChild(要添加的元素)（作为最后一个元素添加)
        // ul.appendChild(newLi)

        // 追加节点 ：父元素.insetBefore(要添加的元素，在哪个元素前面)  （父元素中指定的位置）
        ul.insertBefore(newLi, ul.children[1])


        // 移动节点
        const ol = document.querySelector('ol')
        ol.insertBefore(ol.children[1], ol.children[0])
    </script>
</body>

</html>
```

### 克隆节点

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
        <li>苹果</li>
        <li>香蕉</li>
        <li>梨</li>
    </ul>
    <hr>

    <ol>

    </ol>
    <script>
        // 获取苹果节点
        const a = document.querySelector('ul').children[0]
        // console.log(a);
        // 克隆苹果节点
        // cloneNode(false或不写)默认克隆标签，cloneNode(true)克隆标签和内容
        const newA = a.cloneNode(true)
        // 将苹果节点添加到ol列表
        document.querySelector('ol').appendChild(newA)

    </script>
</body>

</html>
```

### 删除节点

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
        <li>没用了</li>
    </ul>

    <script>
        const ul = document.querySelector('ul')
        // 删除元素
        // ul.removeChild(ul.children[0])
        // 清空元素
        ul.innerHTML = ''
    </script>
</body>

</html>
```

## 移动端触摸事件

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>薪资想过万，代码敲三遍</title>
    <style>
        .box {
            width: 200px;
            height: 200px;
            background-color: pink;
        }
    </style>
</head>

<body>
    <div class="box"></div>
    <script>
        const box=document.querySelector('.box')
        // 1.触屏事件 touchstart
        box.addEventListener('touchstart',function(){
            console.log('触摸了');
        })
        // 2.手指离开 touchend
        box.addEventListener('touchend',function(){
            console.log('手指离开了');
        })
        // 3.来回触摸 touchmove
        box.addEventListener('touchmove',function(){
            console.log('来回触摸');
        })

    </script>
</body>

</html>
```

## swiper插件

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>薪资想过万，代码敲三遍</title>
    <link rel="stylesheet" href="./swiper8/swiper-bundle.css">
    <style>
        .box {
            width: 800px;
            height: 300px;
            background-color: hotpink;
            margin: 0 auto;
        }

        .swiper {
            width: 100%;
            height: 100%;
        }

        .swiper-slide {
            text-align: center;
            font-size: 18px;
            background: #fff;
            /* Center slide text vertically */
            display: -webkit-box;
            display: -ms-flexbox;
            display: -webkit-flex;
            display: flex;
            -webkit-box-pack: center;
            -ms-flex-pack: center;
            -webkit-justify-content: center;
            justify-content: center;
            -webkit-box-align: center;
            -ms-flex-align: center;
            -webkit-align-items: center;
            align-items: center;
        }

        .swiper-slide img {
            display: block;
            width: 100%;
            height: 100%;
            object-fit: cover;
        }
    </style>
</head>

<body>
    <div class="box">
        <!-- Swiper -->
        <div class="swiper mySwiper">
            <div class="swiper-wrapper">
                <div class="swiper-slide">Slide 1</div>
                <div class="swiper-slide">Slide 2</div>
                <div class="swiper-slide">Slide 3</div>
                <div class="swiper-slide">Slide 4</div>
                <div class="swiper-slide">Slide 5</div>
                <div class="swiper-slide">Slide 6</div>
                <div class="swiper-slide">Slide 7</div>
                <div class="swiper-slide">Slide 8</div>
                <div class="swiper-slide">Slide 9</div>
            </div>
            <div class="swiper-pagination"></div>
        </div>
    </div>
    <script src="./swiper8/swiper-bundle.js"></script>

    <script>
        var swiper = new Swiper(".mySwiper", {
            pagination: {
                el: ".swiper-pagination",
            },
            autoplay: true
        });
    </script>
</body>

</html>
```

## window对象

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
        // window对象是顶级对象，document是window的属性

        console.log(window.document === document);
        console.log(window.console === console);
        // alert()是window的方法
        console.log(window.alert === alert);
        // window对象下的这些属性和方法，window可以省略不写

        // window对象是全局对象
        // 使用var在全局作用域声明的变量，是window对象的属性
        var abc = 'abc'
        var aoe = 'aoe'
        console.log(window);
        // 在全局作用域声明的函数，是window对象的方法
        function fn() {
            console.log('fn');
        }
        console.log(window);
        window.fn()

    </script>
</body>

</html>
```

## 定时器（延迟函数）

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
        // 开启定时器
        let timerId = setTimeout(function () {
            console.log('bang!');
        }, 3000)
        console.log(timerId);
        // 清除定时器
        clearTimeout(timerId)

    </script>
</body>

</html>
```

## location

### location对象

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
        // location对象，操作当前窗口的url地址
        console.log(location.href);
        // 跳转页面
        location.href = "http://www.ruruzi.com"
    </script>
</body>

</html>
```

### location对象的方法

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
    <button>刷新</button>
    <button>强制刷新</button>

    <script>
        // 刷新页面
        const btn1 = document.querySelector('button:nth-child(1)')
        btn1.addEventListener('click', function () {
            location.reload()
        })

        // 强制刷新页面
        const btn2 = document.querySelector('button:nth-child(2)')
        btn2.addEventListener('click', function () {
            location.reload(true)
        })

    </script>
</body>

</html>
```

