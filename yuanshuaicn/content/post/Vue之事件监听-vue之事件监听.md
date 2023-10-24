---
title: Vue之事件监听
date: 2021-post-30 10:43:18.223
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/1_7OCwu--TWqVluPMsZdzWKw-34ce1bcaed3b4c59a2183cf00af73987_1622733997113.png'
theme: 'light'
tags: 
- Vue
---

-----', event);
      },
      btn3Click(abc, event) {
        console.log('++++++++', abc, event);
      }
    }
  })

  // 如果函数需要参数,但是没有传入, 那么函数的形参为undefined
  // function abc(name) {
  //   console.log(name);
  // }
  //
  // abc()
</script>

</body>
</html>
```



## 三、v-on修饰符

#### 3.1.阻止冒泡事件 .stop

事件冒泡：在结构上存在嵌套的元素，有事件冒泡的功能，自子元素传递（冒泡）到父元素，所以触发了绑定在button上的点击事件，在事件冒泡的作用下，绑定在div上的事件也会被触发。

```html
        <div id="app">
                <div @click="divClick">
                    <button @click="btnClick">btn</button>
                </div>
        </div>
        <script src="vue.js" type="text/javascript" charset="utf-8"></script>
        <script type="text/javascript">
            const app = new Vue({
                el: '#app',
                data: {
                    counter: 0
                },
                methods: {
                    btnClick(){
                        console.log('btnClick');
                    },
                    divClick(){
                        console.log('divClick');
                    }
                }
            })
        </script>
```

divClick事件也被触发

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/72.png)

增加.stop修饰符就可以阻止冒泡事件

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/73.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/74.png)

原生js中阻止冒泡事件是利用事件对象调用stopPropagation()， event.stopPropagation()

### 3.2.阻止默认事件 .prevent

正常情况下，点击右键会出现菜单，但是特定时候需要取消这个事件，就用.prevent修饰符阻止默认事件

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/75.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/76.png)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/77.png)

原生js中使用event.preventDefault()阻止默认事件

### 3.3.当事件是从特定键触发时才触发回调 .enter(回车键)

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/78.png)

### 3.4.要求事件只触发一次 .once

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/79.png)

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
  <!--1. .stop修饰符的使用-->
  <div @click="divClick">
    aaaaaaa
    <button @click.stop="btnClick">按钮</button>
  </div>

  <!--2. .prevent修饰符的使用-->
  <br>
  <form action="baidu">
    <input type="submit" value="提交" @click.prevent="submitClick">
  </form>

  <!--3. .监听某个键盘的键帽-->
  <input type="text" @keyup.enter="keyUp">

  <!--4. .once修饰符的使用-->
  <button @click.once="btn2Click">按钮2</button>
</div>

<script src="../js/vue.js"></script>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      message: '你好啊'
    },
    methods: {
      btnClick() {
        console.log("btnClick");
      },
      divClick() {
        console.log("divClick");
      },
      submitClick() {
        console.log('submitClick');
      },
      keyUp() {
        console.log('keyUp');
      },
      btn2Click() {
        console.log('btn2Click');
      }
    }
  })
</script>

</body>
</html>
```