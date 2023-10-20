---
title: Vue之计算属性
date: 2021-11-30 10:42:59.232
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/1_7OCwu--TWqVluPMsZdzWKw-34ce1bcaed3b4c59a2183cf00af73987_1622733997113.png'
theme: 'light'
tags: 
- Vue
---

--', newValue);
          const names = newValue.split(' ');
          this.firstName = names[0];
          this.lastName = names[1];
        },
        get: function () {
          return this.firstName + ' ' + this.lastName
        }
      },

      // fullName: function () {
      //   return this.firstName + ' ' + this.lastName
      // }
    }
  })
</script>

</body>
</html>
```



## 三、计算属性和methods对比

```html
    <div id="app">
        <!-- 插值操作拼接字符串的方法尽量不用，因为语法过于复杂 -->
        <h2>{{firstName}} {{lastName}}</h2>

        <!-- 函数拼接字符串 -->
        <h2>{{getFullName()}}</h2>

        <!-- 计算属性拼接字符串 -->
        <h2>{{fullName}}</h2>
    </div>
    <script src="./js/vue.js"></script>
    <script>
        const app = new Vue({
            el: '#app',
            data: {
                firstName : 'Taylor',
                lastName : 'Swift'
            },
            methods: {
                getFullName : function() {
                    return this.firstName + ' ' + this.lastName;
                }
            },
            computed: {
                fullName : function () {
                    return this.firstName + ' ' + this.lastName;
                }
            }
        })
    </script>
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/40.png)

3种方法都能实现字符串的拼接：

- 第1种插值操作的方法最好不用，因为语法过于繁琐和复杂的代码不要放在html里处理
- 函数方法和计算属性看上去没有什么不同，但是为什么提倡使用计算属性呢？

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/41.png)

Vue内部对计算属性有 **缓存机制**，只要监测到计算属性中的值没有发生变化，即使再次调用计算属性，也是将上次缓存的结果传递出去，而methods无论其中的值有没有发生变化，只要调用一次它就执行一次。
所以，在需要转换数据的情况下，计算属性的性能比methods高。

验证：

methods：

```html
    <div id="app">
        <!-- 插值操作拼接字符串的方法尽量不用，因为语法过于复杂 -->
        <!-- <h2>{{firstName}} {{lastName}}</h2> -->

        <!-- 函数拼接字符串 -->
        <h2>{{getFullName()}}</h2>
        <h2>{{getFullName()}}</h2>
        <h2>{{getFullName()}}</h2>
        <h2>{{getFullName()}}</h2>

        <!-- 计算属性拼接字符串 -->
        <!-- <h2>{{fullName}}</h2> -->
    </div>
```

```html
            methods: {
                getFullName : function() {
                    console.log('getFullName()被调用了');
                    return this.firstName + ' ' + this.lastName;
                }
            }
```

运行查看控制台：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/42.png)

methods执行了4次，调用了4次！

计算属性：

```html
    <div id="app">
        <!-- 插值操作拼接字符串的方法尽量不用，因为语法过于复杂 -->
        <!-- <h2>{{firstName}} {{lastName}}</h2> -->

        <!-- 函数拼接字符串 -->
        <!-- <h2>{{getFullName()}}</h2>
            
        <!-- 计算属性拼接字符串 -->
        <h2>{{fullName}}</h2>
        <h2>{{fullName}}</h2>
        <h2>{{fullName}}</h2>
        <h2>{{fullName}}</h2>
    </div>
```

```html
            computed: {
                fullName : function () {
                    console.log('fullName被调用了');
                    return this.firstName + ' ' + this.lastName;
                }
            }
```

运行查看控制台：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/43.png)

fullName中的数据没有发生改变，由于缓存机制，即使执行了4次，也只调用了1次。



改变this.firstName的值
this.firstName一旦改变(数据发生了改变)，计算属性就马上被调用

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/44.png)

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
  <!--1.直接拼接: 语法过于繁琐-->
  <h2>{{firstName}} {{lastName}}</h2>

  <!--2.通过定义methods-->
  <!--<h2>{{getFullName()}}</h2>-->
  <!--<h2>{{getFullName()}}</h2>-->
  <!--<h2>{{getFullName()}}</h2>-->
  <!--<h2>{{getFullName()}}</h2>-->

  <!--3.通过computed-->
  <h2>{{fullName}}</h2>
  <h2>{{fullName}}</h2>
  <h2>{{fullName}}</h2>
  <h2>{{fullName}}</h2>
</div>

<script src="../js/vue.js"></script>
<script>
  // angular -> google
  // TypeScript(microsoft) -> ts(类型检测)
  // flow(facebook) ->
  const app = new Vue({
    el: '#app',
    data: {
      firstName: 'Kobe',
      lastName: 'Bryant'
    },
    methods: {
      getFullName: function () {
        console.log('getFullName');
        return this.firstName + ' ' + this.lastName
      }
    },
    computed: {
      fullName: function () {
        console.log('fullName');
        return this.firstName + ' ' + this.lastName
      }
    }
  })

</script>

</body>
</html>
```