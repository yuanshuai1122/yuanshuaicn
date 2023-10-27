---
title: Vue之条件判断
date: 2021-07-20 18:38:23.971
author:
  name: "yuanshuai"
  link: "https://cloud.tencent.com/developer/user/8180692"
  email: "shuaiyuan1122@gmail.com"
  avatar: "https://aabb-2023.oss-cn-beijing.aliyuncs.com/hjscijg3uw.png"
tags: 
- Vue
---

# Vue条件判断

## 一、v-if的使用

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
</head>
<body>

<div id="app">
  <h2 v-if="isShow">
    <div>abc</div>
    <div>abc</div>
    <div>abc</div>
    <div>abc</div>
    <div>abc</div>
    {{message}}
  </h2>
</div>

<script src="../js/vue.js"></script>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      message: '你好啊',
      isShow: true
    }
  })
</script>

</body>
</html>
```

v-if内部判断的是一个布尔值，只有当布尔值为true时，内部的效果才会生效。  

此时 isShow: true 所以显示结果：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/80.png)

## 二、v-if 和 v-else的使用

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
</head>
<body>

<div id="app">
  <h2 v-if="isShow">
    <div>abc</div>
    <div>abc</div>
    <div>abc</div>
    <div>abc</div>
    <div>abc</div>
    {{message}}
  </h2>
  <h1 v-else>isShow为false时, 显示我</h1>
</div>

<script src="../js/vue.js"></script>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      message: '你好啊',
      isShow: false
    }
  })
</script>

</body>
</html>
```

当v-if判断的布尔值为false，就显示v-else的部分。

此时 isShow: false，所以显示的结果为：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/81.png)

## 三、v-if和v-else-if和v-else的使用

```html
<div id="app">
  <h2 v-if="score>=90">优秀</h2>
  <h2 v-else-if="score>=80">良好</h2>
  <h2 v-else-if="score>=60">及格</h2>
  <h2 v-else>不及格</h2>

</div>
```

```html
<script>
  const app = new Vue({
    el: '#app',
    data: {
      score: 99
    }
  })
</script>
```

当score>=90 显示优秀，80<=score<90 显示良好 ，60<=score<80 显示及格，否则显示不及格。

(注意这里表示80<=score<90 直接写score>=80)

这种将条件判断写在标签内，显然是不可取的，我们应该将其写在计算方法内：

```html
<div id="app">
  <h1>{{result}}</h1>
</div>
```

```html
<script>
  const app = new Vue({
    el: '#app',
    data: {
      score: 99
    },
    computed: {
      result() {
        let showMessage = '';
        if (this.score >= 90) {
          showMessage = '优秀'
        } else if (this.score >= 80) {
          showMessage = '良好'
        }
        // ...
        return showMessage
      }
    }
  })
</script>
```

此时score为99，所以两种方法的结果都为优秀！

相关完整代码展示：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
</head>
<body>

<div id="app">
  <h2 v-if="score>=90">优秀</h2>
  <h2 v-else-if="score>=80">良好</h2>
  <h2 v-else-if="score>=60">及格</h2>
  <h2 v-else>不及格</h2>

  <h1>{{result}}</h1>
</div>

<script src="../js/vue.js"></script>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      score: 99
    },
    computed: {
      result() {
        let showMessage = '';
        if (this.score >= 90) {
          showMessage = '优秀'
        } else if (this.score >= 80) {
          showMessage = '良好'
        }
        // ...
        return showMessage
      }
    }
  })
</script>

</body>
</html>
```

## 三、用户登录切换的小案例

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
</head>
<body>

<div id="app">
  <span v-if="isUser">
    <label for="username">用户账号</label>
    <input type="text" id="username" placeholder="用户账号">
  </span>
  <span v-else>
    <label for="email">用户邮箱</label>
    <input type="text" id="email" placeholder="用户邮箱">
  </span>
  <button @click="isUser = !isUser">切换类型</button>
</div>

<script src="../js/vue.js"></script>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      isUser: true
    }
  })
</script>

</body>
</html>
```

效果：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/82.png)

我们点击切换类型：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/83.png)

就切换为用户邮箱了！

### 发现的问题

当我们在输入框输入文本后，发现我们需要切换类型，这时点击切换类型，发现类型成功切换，但是输入框的文字却还存在，显然不符合我们的期望，我们是想要重新输入的。

### 问题解决：

我们只需要在两个input标签内加入key，表明这时两个输入框，防止复用。

完整代码：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
</head>
<body>

<div id="app">
  <span v-if="isUser">
    <label for="username">用户账号</label>
    <input type="text" id="username" placeholder="用户账号" key="username">
  </span>
  <span v-else>
    <label for="email">用户邮箱</label>
    <input type="text" id="email" placeholder="用户邮箱" key="email">
  </span>
  <button @click="isUser = !isUser">切换类型</button>
</div>

<script src="../js/vue.js"></script>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      isUser: true
    }
  })
</script>

</body>
</html>
```

## 四、v-show的使用

v-show和v-if类似，都是通过判断布尔值来决定是否展示对应的元素。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
</head>
<body>

<div id="app">
  <!--v-if: 当条件为false时, 包含v-if指令的元素, 根本就不会存在dom中-->
  <h2 v-if="isShow" id="aaa">{{message}}</h2>

  <!--v-show: 当条件为false时, v-show只是给我们的元素添加一个行内样式: display: none-->
  <h2 v-show="isShow" id="bbb">{{message}}</h2>
</div>

<script src="../js/vue.js"></script>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      message: '你好啊',
      isShow: true
    }
  })
</script>

</body>
</html>
```

此时 isShow: true 所以显示结果为：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/84.png)

## 五、v-if和v-show的区别

### 5.1.都是通过指令中的布尔值，决定是否展示对应的元素

```html
        <div id="app">
            <h2 v-if="isShow" id="v-if">{{message}}</h2>
            <h2 v-show="isShow" id="v-show">{{message}}</h2>
        </div>
        <script src="js/vue.js" type="text/javascript" charset="utf-8"></script>
        <script type="text/javascript">
            const app = new Vue({
                el: '#app',
                data: {
                    message: 'hello',
                    isShow: true
                }
            })
        </script>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/85.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/86.png)

### 5.2.区别

v-if为false时，对应的h2标签不存在dom中

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/87.png)

v-show为false时，对应的h2标签仍然存在在dom中，只是在行间添加了样式：dispaly:none，通过该样式控制h2标签是否显示到页面。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/88.png)

isShow为true时的dom结构

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/89.png)

### 5.3.根据切换频率选择

在开发中选择v-if还v-show呢？
若该元素切换的频率高，则选择v-show，
若该元素切换的频率低，则选择v-if