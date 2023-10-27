---
title: webApis07-元字符、正则表达式、change事件、检测包含类
date: 2022-08-14 20:14:19.639
author:
  name: "yuanshuai"
  link: "https://cloud.tencent.com/developer/user/8180692"
  email: "shuaiyuan1122@gmail.com"
  avatar: "https://aabb-2023.oss-cn-beijing.aliyuncs.com/hjscijg3uw.png"
tags: 
- javascript
- webapi
---

## 元字符

### 边界符

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
        const reg1 = /哈/
        // 判断是否包含指定的字符串。 true false
        console.log(reg1.test('哈')) // 结果？true
        console.log(reg1.test('哈哈')) // true
        console.log(reg1.test('哼哈')) // true
        console.log(reg1.test('哼哈二将')) // true

        console.log('--------【1】只想要以 哈 开头的字符-----------')

        const reg2 = /^哈/

        console.log(reg2.test('哈')) // true
        console.log(reg2.test('哈哈')) // true
        console.log(reg2.test('哼哈')) // false
        console.log(reg2.test('哼哈二将')) // false

        console.log('--------【2】只想要以 哈 结尾的字符-----------')

        const reg3 = /哈$/
        console.log(reg3.test('哈')) // true
        console.log(reg3.test('哈哈')) // true
        console.log(reg3.test('哼哈')) // true
        console.log(reg3.test('哼哈二将')) // false

        console.log('--------【3】只想要 哈 这样的字符-----------')

        // 精确匹配
        const reg4 = /^哈$/

        console.log(reg4.test('哈')) // true
        console.log(reg4.test('哈哈')) // false
        console.log(reg4.test('哼哈')) // false
        console.log(reg4.test('哼哈二将')) // false

    </script>
</body>

</html>
```

### 量词符

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
        // 1- * 出现0次或更多次
        const reg1 = /^哈*$/

        console.log(reg1.test('')) // true
        console.log(reg1.test('哈')) // true
        console.log(reg1.test('哈哈'))// true
        console.log(reg1.test('哈哈哈')) // true

        console.log('--------------------------------------------')
        // 2- + 出现1次或更多次
        const reg2 = /^哈+$/

        console.log(reg2.test('')) // false
        console.log(reg2.test('哈')) // true
        console.log(reg2.test('哈哈'))// true
        console.log(reg2.test('哈哈哈')) // true

        console.log('--------------------------------------------')

        // 3- ? 出现0次或1次
        const reg3 = /^哈?$/

        console.log(reg3.test('')) // true
        console.log(reg3.test('哈')) // true
        console.log(reg3.test('哈哈'))// false
        console.log(reg3.test('哈哈哈')) // false

        console.log('--------------------------------------------')

        // 4- {n} 出现指定的次数
        const reg4 = /^哈{3}$/
        console.log(reg4.test('哈')) // false
        console.log(reg4.test('哈哈'))// false
        console.log(reg4.test('哈哈哈')) // true
        console.log(reg4.test('哈哈哈哈哈')) // false

        // 5- {n,} 出现大于等于n次
        console.log('--------------------------------------------')

        const reg5 = /^哈{3,}$/

        console.log(reg5.test('哈哈'))// false
        console.log(reg5.test('哈哈哈')) // true
        console.log(reg5.test('哈哈哈哈哈')) // true

        console.log('--------------------------------------------')
        // 6- {n,m} 出现大于等于n次，小于等于m次
        const reg6 = /^哈{3,5}$/

        console.log(reg6.test('哈哈')) // false
        console.log(reg6.test('哈哈哈')) // true
        console.log(reg6.test('哈哈哈哈')) // true
        console.log(reg6.test('哈哈哈哈哈')) // true
        console.log(reg6.test('哈哈哈哈哈哈')) // false

        /*
            注意1：逗号两侧不要有空格
            注意2：量词符，修饰的是前面的一个字符。
        */

        console.log('--------------------------------------------')
        const reg8 = /^哼哈{2}$/ // 哼哈哈

        console.log(reg8.test('哈哈')) // false
        console.log(reg8.test('哼哈')) // false
        console.log(reg8.test('哼哈哼哈')) // false
        console.log(reg8.test('哼哼哈哈')) // false
        console.log(reg8.test('哼哈哈')) // true

        console.log('--------------------------------------------')
        const reg9 = /^(哼哈){2}$/
        console.log(reg9.test('哼哈哼哈')) // true
        console.log(reg9.test('哼哼哈哈')) // false

    </script>
</body>

</html>
```

### 字符类

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
        // 需求：匹配abc、cbc、fbc、xbc

        // 第1个字符有多个选项, 把多个选项写到 []中
        // 1-字符类：多个选项的集合
        const reg1 = /^[acfx]bc$/

        console.log(reg1.test('abc')) // true
        console.log(reg1.test('bbc')) // false
        console.log(reg1.test('cbc')) // true
        console.log(reg1.test('ybc')) // false
        console.log(reg1.test('#bc')) // false

        console.log('--------------------------------------------')
        // 2- 第1个字符，可以是任意一个字母。[abcdefghijklmnopqrstuvwxyz]bc
        // []中可以使用范围
        const reg2 = /^[a-z]bc$/
        console.log(reg2.test('abc')) // true
        console.log(reg2.test('xbc')) // true
        console.log(reg2.test('zbc')) // true
        console.log(reg2.test('#bc')) // false

        // 3-[]中可以使用多范围
        console.log('--------------------------------------------')
        const reg3 = /^[a-zA-Z0-9_]bc$/

        console.log(reg3.test('abc')) // true
        console.log(reg3.test('Abc')) // true
        console.log(reg3.test('1bc')) // true
        console.log(reg3.test('#bc')) // false
        console.log(reg3.test('_bc')) // true

        console.log('--------------------------------------------')
        // 4-[]中的^表示取反
        const reg4 = /^[^a-z]bc$/

        console.log(reg4.test('abc')) // false
        console.log(reg4.test('Abc')) // true
        console.log(reg4.test('#bc')) // true
        console.log(reg4.test('_bc')) // true

        console.log('--------------------------------------------')
        // 5- .表示任意一个字符（除了换行符）
        const reg5 = /^.bc$/

        console.log(reg5.test('abc')) // true
        console.log(reg5.test('1bc')) // true
        console.log(reg5.test('#bc')) // true

        console.log('--------------------------------------------')
        // 6- 写一个匹配qq号正则（qq号大于10000的数字）
        /* 
            至少5位数字
            第1位不是0        
        */
        const regQQ = /^[1-9][0-9]{4,}$/
    </script>
</body>

</html>
```

### 预定义类

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
        // 1-第1位是数字
        const reg1 = /^\dbc$/

        // 2-[a-zA-Z0-9_]
        const reg2 = /^\w{6,12}$/
    </script>
</body>

</html>
```

## 正则表达式

### 替换

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
        const str1 = 'java是一门编程语言'
        // str1.replace(正则, '替换成的字符')
        /* 
            注意：返回一个新字符串，原字符串不受影响。
        */

        const res1 = str1.replace(/java/, '前端')
        console.log(res1, str1)

        const str2 = '学完JAVA工资很高！'

        const res2 = str2.replace(/JAVA/, '前端')
        console.log(res2)


    </script>
</body>

</html>
```

### 正则修饰符（匹配）

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
        const str = 'java是一门编程语言，java学习，学完JAVA工资很高！'

        /* 
            i 忽略大小写
            g 匹配所有满足条件的
        */
        const reg = /java/gi

        const result = str.replace(reg, '前端')
        console.log(result)
    </script>
</body>

</html>
```

## change事件

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
    <input type="text">

    <script>
        // input事件：输入框内容发生变化
        const inp = document.querySelector('input')
        // inp.addEventListener('input', function () {
        //     console.log('ok')
        // })

        // change事件：内容发生变化并失去焦点时触发
        inp.addEventListener('change', function () {
            console.log('ko')
        })

    </script>
</body>

</html>
```

## 检测包含类

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
    <div class="one"></div>
    <script>
        const div = document.querySelector('div')

        // 检测元素是否包含某个类
        // classList.contains()
        console.log(div.classList.contains('one'))
        console.log(div.classList.contains('two'))

    </script>
</body>

</html>
```

