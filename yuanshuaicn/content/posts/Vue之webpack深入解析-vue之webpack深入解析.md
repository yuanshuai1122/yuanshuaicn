---
title: Vue之webpack深入解析
date: 2021-posts-30 10:44:39.007
author: 'yuanshuai'
cover: 'https://yuan-halo.oss-cn-beijing.aliyuncs.com/1_7OCwu--TWqVluPMsZdzWKw-34ce1bcaed3b4c59a2183cf00af73987_1622733997113.png'
theme: 'light'
tags: 
- Vue
---

声明：本文转自coderwhy大佬的简书：https://www.jianshu.com/p/a83ffc6fdf1d

仅供个人学习使用，侵删。

### 一. 认识webpack

#### 1.1. webpack介绍

什么是webpack？

- 这个webpack还真不是一两句话可以说清楚的。
- 我们先看看官方的解释：
  - At its core, **webpack** is a *static module bundler* for modern JavaScript applications.
- 别和我说英文，OK？
  - 从本质上来讲，webpack是一个现代的JavaScript应用的静态**模块打包**工具。
- 但是它是什么呢？用概念解释概念，还是不清晰。
  - 我们从两个点来解释上面这句话。
  - **模块** 和 **打包**

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/218.png)

前端模块化：

- 在学习ES6语法的时候，我已经用了大量的篇幅解释了为什么前端需要模块化。
- 而且我也提到了目前使用前端模块化的一些方案：AMD、CMD、CommonJS、ES6。
- 在ES6之前，我们要想进行模块化开发，就必须借助于其他的工具，让我们可以进行模块化开发。
- 并且在通过模块化开发完成了项目后，还需要处理模块间的各种依赖，并且将其进行整合打包。
- 而webpack其中一个核心就是让我们可能进行模块化开发，并且会帮助我们处理模块间的依赖关系。
- 而且不仅仅是JavaScript文件，我们的CSS、图片、json文件等等在webpack中都可以被当做模块来使用（在后续我们会看到）。
- 这就是webpack中模块化的概念。

**打包**如何理解呢？

- 理解了webpack可以帮助我们进行模块化，并且处理模块间的各种复杂关系后，打包的概念就非常好理解了。
- 就是将webpack中的各种资源模块进行打包合并成一个或多个包(Bundle)。
- 并且在打包的过程中，还可以对资源进行处理，比如压缩图片，将scss转成css，将ES6语法转成ES5语法，将TypeScript转成JavaScript等等操作。
- 但是打包的操作似乎grunt/gulp也可以帮助我们完成，它们有什么不同呢？
- 我们稍后就会介绍到，不要着急。O(∩_∩)O~~

#### 1.2. 和grunt/gulp的对比

grunt/gulp的核心是Task

- 我们可以配置一系列的task，并且定义task要处理的事务（例如ES6、ts转化，图片压缩，scss转成css）
- 之后让grunt/gulp来依次执行这些task，而且让整个流程自动化。
- 所以grunt/gulp也被称为前端自动化任务管理工具。

我们来看一个gulp的task

- 下面的task就是将src下面的所有js文件转成ES5的语法。
- 并且最终输出到dist文件夹中。

```html
const gulp = require('gulp');
const babel = require('gulp-babel');

gulp.task('js', () =>
    gulp.src('src/*.js')
        .pipe(babel({
            presets: ['es2015']
        }))
        .pipe(gulp.dest('dist'))
);
```

什么时候用grunt/gulp呢？

- 如果你的工程模块依赖非常简单，甚至是没有用到模块化的概念。
- 只需要进行简单的合并、压缩，就使用grunt/gulp即可。
- 但是如果整个项目使用了模块化管理，而且相互依赖非常强，我们就可以使用更加强大的webpack了。

所以，grunt/gulp和webpack有什么不同呢？

- grunt/gulp更加强调的是前端流程的自动化，模块化不是它的核心。
- webpack更加强调模块化开发管理，而文件压缩合并、预处理等功能，是他附带的功能。

#### 1.3. webpack安装

安装webpack首先需要安装Node.js，Node.js自带了软件包管理工具npm

查看自己的node版本：

```shell
node -v
```

全局安装webpack(这里我先指定版本号3.6.0，因为vue cli依赖该版本)

```shell
npm install webpack@3.6.0 -g
```

后续才需要，在开发的目录中，局部安装webpack

- --save-dev`是开发时依赖，项目打包后不需要继续使用的。

```shell
cd 对应目录
npm install webpack@3.6.0 --save-dev
```

为什么全局安装后，还需要局部安装呢？

- 在终端直接执行webpack命令，使用的全局安装的webpack
- 当在package.json中定义了scripts时，其中包含了webpack命令，那么使用的是局部webpack

### 二. webpack起步

#### 2.1. 准备工作

我们创建如下文件和文件夹：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/219.png)

文件和文件夹解析：

- dist文件夹：用于存放之后打包的文件
- src文件夹：用于存放我们写的源文件
  - main.js：项目的入口文件。具体内容查看下面详情。
  - mathUtils.js：定义了一些数学工具函数，可以在其他地方引用，并且使用。具体内容查看下面的详情。
- index.html：浏览器打开展示的首页html
- package.json：通过npm init生成的，npm包管理的文件（暂时没有用上，后面才会用上）

mathUtils.js文件中的代码：

- 定义两个函数，并且通过module.exports导出

```javascript
function add(num1, num2) {
  return num1 + num2
}

function mul(num1, num2) {
  return num1 * num2
}

module.exports = {
  add,
  mul
}
```

main.js文件中的代码：

```javascript
const math = require('./mathUtils')

console.log('Hello Webpack');
console.log(math.add(10, 20));
console.log(math.mul(10, 20));
```

#### 2.2. js文件的打包

现在的js文件中使用了模块化的方式进行开发，他们可以直接使用吗？不可以。

- 因为如果直接在index.html引入这两个js文件，浏览器并不识别其中的模块化代码。
- 另外，在真实项目中当有许多这样的js文件时，我们一个个引用非常麻烦，并且后期非常不方便对它们进行管理。

我们应该怎么做呢？使用webpack工具，对多个js文件进行打包。

- 我们知道，webpack就是一个模块化的打包工具，所以它支持我们代码中写模块化，可以对模块化的代码进行处理。（如何处理的，待会儿在原理中，我会讲解）
- 另外，如果在处理完所有模块之间的关系后，将多个js打包到一个js文件中，引入时就变得非常方便了。

OK，如何打包呢？使用webpack的指令即可

```shell
webpack src/main.js dist/bundle.js
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/220.png)

打包后会在dist文件下，生成一个bundle.js文件

- 文件内容有些复杂，这里暂时先不看，后续再进行分析。

bundle.js文件，是webpack处理了项目直接文件依赖后生成的一个js文件，我们只需要将这个js文件在index.html中引入即可

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/222.png)

打开HTML，那么就会看到我们js文件执行的结果：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/223.png)

### 三. webpack配置

#### 3.1. 入口和出口

我们考虑一下，如果每次使用webpack的命令都需要写上入口和出口作为参数，就非常麻烦，有没有一种方法可以将这两个参数写到配置中，在运行时，直接读取呢？

- 当然可以，就是创建一个webpack.config.js文件

```js
const path = require('path')

module.exports = {
  // 入口: 可以是字符串/数组/对象, 这里我们入口只有一个,所以写一个字符串即可
  entry: './src/main.js',
  // 出口: 通常是一个对象, 里面至少包含两个重要属性, path 和 filename
  output: {
    path: path.resolve(__dirname, 'dist'), // 注意: path通常是一个绝对路径
    filename: 'bundle.js'
  }
}
```

#### 3.2. watch

我现在有一个更加大胆的想法，希望src文件中的js代码发生变化时，webpack可以检测到，并且自动重新打包

- 如果有重新打包，那么我只需要刷新一下浏览器就可以知道我修改代码后的效果了。
- 这个时候，我可以再添加一个配置watch

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/224.png)

重新通过webpack终端执行一次命令，会看到如下结果：

- 你会发现这次命令执行之后没有直接结束，而是出于监听状态。
- 当我们修改了前面的代码后，会自动重新打包。
- 可以通过ctrl+c停止监听。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/225.png)

当然，后面我们有更加方便的方式来监听代码的变化，并且可以实时在浏览器中查看。

#### 3.3. 启动webpack方式

目前，我们使用的webpack是全局的webpack，如果我们想使用局部来打包呢？

- 因为一个项目往往依赖特定的webpack版本，全局的版本可能很这个项目的webpack版本不一致，导出打包出现问题。
- 所以通常一个项目，都有自己局部的webpack。

第一步，项目中需要安装自己局部的webpack

- 这里我们让局部安装webpack3.6.0
- 因为方便我们后续查看Vue CLI2中的webpack3.6.0配置
- Vue CLI3中已经升级到webpack4，但是它将配置文件隐藏了起来，所以查看起来不是很方便。
- 我们在学习阶段，先使用webpack3.x即可。
- 后续开发中，我们会直接使用脚手架，CLI2和CLI3都会使用。

```shell
npm install webpack@3.6.0 --save-dev
```

第二步，通过node_modules/.bin/webpack启动webpack打包

```shell
node_modules/.bin/webpack
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/226.png)

但是，每次执行都敲这么一长串有没有觉得不方便呢？

- OK，我们可以在package.json的scripts中定义自己的执行脚本。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/227.png)

package.json中的scripts的脚本在执行时，会按照一定的顺序寻找命令对应的位置。

- 首先，会寻找本地的`node_modules/.bin`路径中对应的命令。
- 如果没有找到，会去全局的环境变量中寻找。

如何执行我们的build指令呢？

```shell
npm run build
```

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/228.png)

### 四. loader的使用

> 目前，webpack帮助我们处理的都是js文件。
>
> 但我们前面提过，webpack可以将js、图片、css都当成模块来进行处理，下面我们就来学习一下如何处理它们。

#### 4.1. css文件处理

##### 4.1.1. css准备的阶段

项目开发过程中，我们必然需要添加很多的样式，而样式我们往往写到一个单独的文件中。

- 在src目录中，创建一个css文件，其中创建一个normal.css文件。
- 我们也可以重新组织文件的目录结构，将零散的js文件放在一个js文件夹中。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/229.png)

normal.css中的代码非常简单，就是将body设置为red

```css
body {
  background-color: red;
}
```

但是，这个时候normal.css中的样式会生效吗？

- 当然不会，因为我们压根就没有引用它。
- webpack也不可能找到它，因为我们只有一个入口，webpack会从入口开始查找其他依赖的文件。

在入口文件中引用：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/230.png)

重新打包，会出现如下错误：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/231.png)

这个错误，告诉我们要想加载css文件，必须使用对应的loader

##### 4.1.2. 认识loader

loader是webpack中一个非常核心的概念。

webpack用来做什么呢？

- 在我们之前的实例中，我们主要是用webpack来处理我们写的js代码，并且webpack会自动处理js之间相关的依赖。
- 但是，在开发中我们不仅仅有基本的js代码处理，我们也需要加载css、图片，也包括一些高级的将ES6转成ES5代码，将TypeScript转成ES5代码，将scss、less转成css，将.jsx、.vue文件转成js文件等等。
- 对于webpack本身的能力来说，对于这些转化是不支持的。
- 那怎么办呢？给webpack扩展对应的loader就可以啦。

loader使用过程：

- 步骤一：通过npm安装需要使用的loader
- 步骤二：在`webpack.config.js`中的`modules`关键字下进行配置

目前，我们希望加载和使用.css文件，就需要使用对应的loader

- 大部分loader我们都可以在webpack的官网中找到，并且学习对应的用法。

##### 4.1.3. css处理的loader

在webpack的官方中，我们可以找到如下关于样式的loader使用方法：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/232.png)

OK，按照教程，我们需要先安装css-loader

```shell
npm install --save-dev css-loader
```

按照官方配置webpack.config.js文件

- 注意：配置中有一个style-loader，我们并不知道它是什么，所以可以暂时不进行配置。

重新打包项目：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/233.png)

但是，运行index.html，你会发现样式并没有生效。

- 原因是css-loader只负责加载css文件，但是并不负责将css具体样式嵌入到文档中。
- 这个时候，我们还需要一个style-loader帮助我们处理。

我们来安装style-loader

```shell
npm install --save-dev style-loader
```

将对应的style-loader，配置在webpack.config.js中

- 注意：style-loader需要放在css-loader的前面。
- 疑惑：不对吧？按照我们的逻辑，在处理css文件过程中，应该是css-loader先加载css文件，再由style-loader来进行进一步的处理，为什么会将style-loader放在前面呢？
- 答案：这次因为webpack在读取使用的loader的过程中，是按照从右向左的顺序读取的。

目前，webpack.config.js的配置如下：

```js
const path = require('path')

module.exports = {
  // 入口: 可以是字符串/数组/对象, 这里我们入口只有一个,所以写一个字符串即可
  entry: './src/main.js',
  // 出口: 通常是一个对象, 里面至少包含两个重要属性, path 和 filename
  output: {
    path: path.resolve(__dirname, 'dist'), // 注意: path通常是一个绝对路径
    filename: 'bundle.js'
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [ 'style-loader', 'css-loader' ]
      }
    ]
  }
}

```

并且，现在再次运行index.html就会发现我们的样式生效了。

#### 4.2. less文件处理

> 如果我们希望在项目中使用less、scss、stylus来写样式，webpack是否可以帮助我们处理呢？
>
> 我们这里以less为例，其他也是一样的。

##### 4.1.2. less准备的阶段

我们还是先创建一个less文件，依然放在css文件夹中

special.css文件的内容：

```css
@fontSize: 50px;
@fontColor: white;

body {
    color: @fontColor;
    font-size: @fontSize;
}
```

为了让special.css生效，我们需要在main.js中引入

- 另外，这里为了看到样式有生效，还在页面中写入了一个div元素

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/234.png)

这个时候，打包程序，会报如下错误：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/235.png)

##### 4.1.3. less处理的loader

继续在官方中查找，我们会找到less-loader相关的使用说明

首先，还是需要安装对应的loader

- 注意：我们这里还安装了less，因为webpack会使用less对less文件进行编译

```shell
npm install --save-dev less-loader less
```

其次，修改对应的配置文件

- 添加一个rules选项，用于处理.less文件

```js
        {
            test: /\.less$/,
            use: [{
                loader: "style-loader" // creates style nodes from JS strings
            }, {
                loader: "css-loader" // translates CSS into CommonJS
            }, {
                loader: "less-loader" // compiles Less to CSS
            }]
        }
```

#### 4.3. 资源文件处理

##### 4.3.1. 资源准备阶段

首先，我们在项目中加入两张图片：

- 一张较小的图片test01.jpg(小于8kb)，一张较大的图片test02.jpeg(大于8kb)
- 待会儿我们会针对这两张图片进行不同的处理

我们先考虑在css样式中引用图片的情况，所以我更改了normal.css中的样式：

```css
body {
  background-color: red;
  background: url(../imgs/test01.jpeg)
}
```

如果我们现在直接打包，会出现如下问题

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/236.png)

很简单，还是没有对应的加载器

##### 4.3.2. url-loader

图片处理，我们使用url-loader来处理

依然先安装url-loader

```shell
npm install --save-dev url-loader
```

修改webpack.config.js配置文件：

```js
     {
        test: /\.(png|jpg|gif|jpeg)$/,
        use: [
          {
            loader: 'url-loader',
            options: {
              limit: 8192
            }
          }
        ]
      }
```

这里有一个limit选项，我们先写在这里，待会儿再来解释它的作用

再次打包，运行index.html，就会发现我们的背景图片选出了出来。

而仔细观察，你会发现背景图是通过base64显示出来的

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/237.png)

OK，这也是limit属性的作用，当图片小于8kb时，对图片进行base64编码

那么问题来了，如果大于8kb呢？

- 我们将background的图片改成test02.jpg

继续打包我们的程序，报错了

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/238.png)

这次因为大于8kb的图片，会通过file-loader进行处理，但是我们的项目中并没有file-loader

##### 4.3.3. file-loader

当图片大于8kb时，会使用file-loader进行加载，所以我们需要先安装file-loader

- 注：file-loader可以不进行rules的配置

```shell
npm install --save-dev file-loader
```

再次打包，就会发现dist文件夹下多了一个图片文件

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/239.png)

我们发现webpack自动帮助我们生成一个非常长的名字

- 这是一个32位hash值，目的是防止名字重复
- 但是，真实开发中，我们可能对打包的图片名字有一定的要求
- 比如，将所有的图片放在一个文件夹中，跟上图片原来的名称，同时也要防止重复

所以，我们可以在options中添加上如下选项：

- img：文件要打包到的文件夹
- name：获取图片原来的名字，放在该位置
- hash:8：为了防止图片名称冲突，依然使用hash，但是我们只保留8位
- ext：使用图片原来的扩展名

```js
options: {
    limit: 8192,
    name: 'img/[name].[hash:8].[ext]'
}
```

但是，我们发现图片并没有显示出来，这是因为图片使用的路径不正确

- 默认情况下，webpack会将生成的路径直接返回给使用者
- 但是，我们整个程序是打包在dist文件夹下的，所以这里我们需要在路径下再添加一个dist/

在webpack.config.js中添加如下配置：

- 这个配置待会儿就不再需要了，我们后续再说，在这里我们必须添加上去。
- ![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/240.png)

再次打包运行程序，一切正常了。

#### 4.4. babel-loader

如果你仔细阅读webpack打包的js文件，发现写的ES6语法并没有转成ES5，那么就意味着可能一些对ES6还不支持的浏览器没有办法很好的运行我们的代码。

在前面我们说过，如果希望将ES6的语法转成ES5，那么就需要使用babel。

- 而在webpack中，我们直接使用babel对应的loader就可以了。

安装对应的babel-loader

```shell
npm install --save-dev babel-loader@7 babel-core babel-preset-es2015
```

配置webpack.config.js文件

```js
      {
        test: /\.m?js$/,
        exclude: /(node_modules|bower_components)/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['es2015']
          }
        }
      }
```

重新打包，查看bundle.js文件，发现其中的内容变成了ES5的语法

### 五. 集成Vuejs

> 后续项目中，我们会使用Vuejs进行开发，而且会以特殊的文件来组织vue的组件。
>
> 所以，下面我们来学习一下如何在我们的webpack环境中集成Vuejs

#### 5.1. 简单使用Vuejs

现在，我们希望在项目中使用Vuejs，那么必然需要对其有依赖，所以需要先进行安装

- 注：因为我们后续是在实际项目中也会使用vue的，所以并不是开发时依赖

```shell
npm install vue --save
```

那么，接下来就可以按照我们之前学习的方式来使用Vue了

我们创建一个Vue实例：

```js
import Vue from 'vue'

new Vue({
   el: '#app',
   data: {
     name: 'coderwhy'
   }
 })
```

上面的Vue实例中，我们要挂载一个#app对应的元素，所以也要修改index.html

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/241.png)

修改完成后，重新打包，运行程序：

- 打包过程没有任何错误(因为只是多打包了一个vue的js文件而已)
- 但是运行程序，没有出现想要的效果，而且浏览器中有报错

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/242.png)

这个错误说的是我们使用的是runtime-only版本的Vue，什么意思呢？

- 这个在后续的课程中我具体说明runtime-compiler和runtime-only的区别。
- 这里我只说解决方案：[Vue不同版本构建](https://links.jianshu.com/go?to=http%3A%2F%2Fcn.vuejs.org%2Fv2%2Fguide%2Finstallation.html%23%E5%AF%B9%E4%B8%8D%E5%90%8C%E6%9E%84%E5%BB%BA%E7%89%88%E6%9C%AC%E7%9A%84%E8%A7%A3%E9%87%8A)
- 所以我们修改webpack的配置，添加如下内容即可

```js
  resolve: {
    alias: {
      'vue$': 'vue/dist/vue.esm.js' 
    }
  },
```

重新打包，运行程序：

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/243.png)

#### 5.2. el和template

正常运行之后，我们来考虑另外一个问题：

- 如果我们希望将data中的数据显示在界面中，就必须是修改index.html
- 如果我们后面自定义了组件，也必须修改index.html来使用组件
- 但是html模板在之后的开发中，我并不希望手动的来频繁修改，是否可以做到呢？

定义template属性：

- 在前面的Vue实例中，我们已经定义了el属性，用于和index.html中的#app进行绑定，让Vue实例之后可以管理它其中的内容
- 这里，我们可以将div元素中的{{message}}内容删掉，只保留一个基本的id为div的元素
- 但是如果我依然希望在其中显示{{message}}的内容，应该怎么处理呢？
- 我们可以再定义一个template属性，代码如下：

```js
new Vue({
  el: '#app',
  template: '<div id="app">{{message}}</div>',
  data: {
    message: 'coderwhy'
  }
})
```

重新打包，运行程序，显示一样的结果和HTML代码结构

那么，el和template模板的关系是什么呢？

- 在我们之前的学习中，我们知道el用于指定Vue要管理的DOM，可以帮助解析其中的指令、事件监听等等。
- 而如果Vue实例中同时指定了template，那么template模板的内容会替换掉挂载的对应el的模板。

这样做有什么好处呢？

- 这样做之后我们就不需要在以后的开发中再次操作index.html，只需要在template中写入对应的标签即可

但是，书写template模块非常麻烦怎么办呢？

- 没有关系，稍后我们会将template模板中的内容进行抽离。
- 会分成三部分书写：template、script、style，结构变得非常清晰。

#### 5.3. Vue组件化开发

在学习组件化开发的时候，我说过以后的Vue开发过程中，我们都会采用组件化开发的思想。

那么，在当前项目中，如果我也想采用组件化的形式进行开发，应该怎么做呢？

查看下面的代码：

- 1.定义一个组件对象
- 2.在Vue实例的components中进行注册
- 3.在Vue实例的template模板中使用

```js
const App = {
  template: '<h2>{{name}}</h2>',
  data() {
    return {
      name: '我是APP组件'
    }
  }
}

new Vue({
  el: '#app',
  template: `
    <div id="app">
      {{message}}
      <App/>
    </div>`,
  data: {
    message: 'coderwhy'
  },
  components: {
    App
  }
})
```

重新打包，运行程序，组件内容正常的显示出来了。

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/244.png)

为了方便维护我们的App组件，我们可以将对应的代码单独抽离取出

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/245.png)

代码依然可以正常运行，没有问题。

#### 5.4. .vue文件封装处理

但是一个组件以一个js对象的形式进行组织和使用的时候是非常不方便的

- 一方面编写template模块非常的麻烦
- 另外一方面如果有样式的话，我们写在哪里比较合适呢？

现在，我们以一种全新的方式来组织一个vue的组件

```html
<template>
  <h2 class="title">{{name}}</h2>
</template>
<script>
export default {
  name: "App",
  data () {
    return {
      name: '我是.vue的App组件'
    };
  }
}
</script>
<style scoped>
  .title {
    color: blue;
  }
</style>
```

但是，这个时候这个文件可以被正确的加载吗？

- 必然不可以，这种特殊的文件以及特殊的格式，必须有人帮助我们处理。
- 谁来处理呢？vue-loader以及vue-template-compiler。

安装vue-loader和vue-template-compiler

```shell
npm install vue-loader vue-template-compiler --save-dev
```

修改webpack.config.js的配置文件：

```js
{
    test: /\.vue$/,
    use: ['vue-loader']
}
```

重新打包，运行程序，你会发现.vue文件已经可以被识别，并且正确的显示了出来。

### 六. plugin的使用

#### 6.1. 认识plugin

plugin是什么？

- plugin是插件的意思，通常是用于对某个现有的架构进行扩展。
- webpack中的插件，就是对webpack现有功能的各种扩展，比如打包优化，文件压缩等等。

loader和plugin区别

- loader主要用于转换某些类型的模块，它是一个转换器。
- plugin是插件，它是对webpack本身的扩展，是一个扩展器。

plugin的使用过程：

- 步骤一：通过npm安装需要使用的plugins(某些内置插件不需要安装)
- 步骤二：在webpack.config.js中的plugins中配置插件。

下面，我们就来看看可以通过哪些插件对现有的webpack打包过程进行扩容，让我们的webpack变得更加好用。

#### 6.2. 添加版权的Plugin

我们先来使用一个最简单的插件，为打包的文件添加版权声明

- 该插件名字叫BannerPlugin，属于webpack自带的插件。

按照下面的方式来修改webpack.config.js的文件：

```js
const path = require('path')
const webpack = require('webpack')

module.exports = {
  ...
  plugins: [
    new webpack.BannerPlugin('最终版权归coderwhy所有')
  ]
}
```

重新打包程序：查看bundle.js文件的头部，看到如下信息

![](https://hexobbblog.oss-cn-beijing.aliyuncs.com/images/vue/246.png)

#### 6.3. 打包html的plugin

目前，我们的index.html文件是存放在项目的根目录下的。

我们知道，在真实发布项目时，发布的是dist文件夹中的内容，但是dist文件夹中如果没有index.html文件，那么打包的js等文件也就没有意义了。

所以，我们需要将index.html文件打包到dist文件夹中，这个时候就可以使用HtmlWebpackPlugin插件

HtmlWebpackPlugin插件可以为我们做这些事情：

- 自动生成一个index.html文件(可以指定模板来生成)
- 将打包的js文件，自动通过script标签插入到body中

安装HtmlWebpackPlugin插件

```shell
npm install html-webpack-plugin --save-dev
```

使用插件，修改webpack.config.js文件中plugins部分的内容如下：

```js
  plugins: [
    new webpack.BannerPlugin('最终版权归coderwhy所有'),
    new htmlWebpackPlugin({
      template: 'index.html'
    }),
  ]
```

注意：

- 这里的template表示根据什么模板来生成index.html
- 另外，我们需要删除之前在output中添加的publicPath属性，否则插入的script标签中的src可能会有问题

#### 6.3. js压缩的plugin

在项目发布之前，我们必然需要对js等文件进行压缩处理

这里，我们就对打包的js文件进行压缩

- 我们使用一个第三方的插件uglifyjs-webpack-plugin，并且版本号指定1.1.1，和CLI2保持一致

安装uglifyjs-webpack-plugin插件：

```shell
npm install uglifyjs-webpack-plugin@1.1.1 --save-dev
```

修改webpack.config.js文件，使用插件：

```js
const path = require('path')
const webpack = require('webpack')
const uglifyJsPlugin = require('uglifyjs-webpack-plugin')

module.exports = {
  ...
  plugins: [
    new webpack.BannerPlugin('最终版权归coderwhy所有')
    new uglifyJsPlugin()
  ]
}   
```

查看打包后的bunlde.js文件，是已经被压缩过了。

### 七. 搭建本地服务

> 现在我们的环境有一个很大的弊端，每次修改了代码都需要手动来编译（当然可以通过watch），另外编译后要刷新页面才能看到对应的效果。
>
> 如果代码修改后，可以自动刷新浏览器看到修改后的效果，会大大提升我们的开发效率。

#### 7.1. 搭建本地服务

webpack提供了一个可选的本地开发服务器，这个本地服务器基于node.js搭建，内部使用express框架，可以实现我们想要的让浏览器自动刷新显示我们修改后的结果。

不过它是一个单独的模块，在webpack中使用之前需要先安装它

```shell
npm install --save-dev webpack-dev-server@2.9.1
```

devserver也是作为webpack中的一个选项，选项本身可以设置如下属性：

- contentBase：为哪一个文件夹提供本地服务，默认是根文件夹，我们这里要填写./dist
- port：端口号
- inline：页面实时刷新
- historyApiFallback：在SPA页面中，依赖HTML5的history模式

webpack.config.js文件配置修改如下：

```js
  devServer: {
    contentBase: './dist',
    inline: true
  },
```

另外，我们再针对开发环境在package.json中配置一个scripts

- --open参数表示直接打开浏览器

```json
"dev": "webpack-dev-server --open"
```

通过npm run dev启动环境，就会发现我们已经有了一个可以实时刷新页面的本地服务了。

#### 7.2. 模块热更新

搭建完本地服务后固然非常好用，但是它还是存在一个弊端：当只有一部分代码发生变化时，我们去刷新了整个页面。

很明显，如果我们能在修改完代码后只刷新修改的部分，那么界面的更新效率会更高。

- 如何能做到呢？使用HotModuleReplacementPlugin插件即可。
- 该拆件是一个webpack内置的插件，并不需要安装，直接使用即可

在webpack.config.js文件中修改插件如下：

```js
module.exports = {
  ...
  devServer: {
    contentBase: './dist',
    inline: true,
    hot: true
  },
  ...
  plugins: [
    new webpack.BannerPlugin('最终版权归coderwhy所有'),
    new uglifyJsPlugin(),
    new htmlWebpackPlugin({
      template: 'index.html'
    }),
    new webpack.HotModuleReplacementPlugin()
  ]
}
```