---
title: React的正确打开姿势
date: 2023-11-01 18:12:20.377
author:
  name: "yuanshuai"
  link: "https://cloud.tencent.com/developer/user/8180692"
  email: "shuaiyuan1122@gmail.com"
  avatar: "https://aabb-2023.oss-cn-beijing.aliyuncs.com/hjscijg3uw.png"
tags:
- 前端
- react
---
## React的正确打开姿势

### 一、前置环境

### 1.1 nodejs

`https://nodejs.org/`

> Node.js® 是一个开源、跨平台的 JavaScript 运行时环境。

说白了运行时环境，就是运行的时候需要的环境。

可能这是句废话，但是确实是一句废话。

举个例子：写代码就好像盖楼，`nodejs`就是你的地基，你可以用地基带给你的东西（封装好的函数）也可以在地基上盖房子（业务逻辑代码），所以现代前端，必然离不开`nodejs`。

当然写的好叫盖房子，写不好叫屎山~

`nodejs`有很多版本，`LTS`代表长期维护的稳定版本，理论上可以用在生产环境。

介绍几个大版本：

- 12：存在于vue2时代，很多vue2老项目的都是基于node12，并且一些三五年前流行的vue脚手架也是基于node12，现在不建议使用。
- 14：稳定版（保守派），目前很多主流项目都是基于node14。
- 16：稳定版（无脑派），目前较新的项目都是基于node16，目前建议无脑node16。
- 18：最新稳定版（激进派），目前还不建议直接在生产环境上node18，还比较新，你看那些写java的，都出到21了， 还用java8呢~

其实对于前端，node版本对于项目不是很感冒，14的项目几乎可以无缝切16，能跑就行，但是我们还是需要一个版本管理工具，面对不同项目切换不同的版本。

推荐一个node版本管理工具：`nvm`

`https://github.com/nvm-sh/nvm`

安装教程就不多废话了，网上教程很多，根据操作系统版本进行安装就行。

`https://www.runoob.com/w3cnote/nvm-manager-node-versions.html`

可以看下菜鸟教程，里面也有简单的使用方法。

### 1.2 换源

我们都知道`npm`,但是什么是`npm`

> npm 为你和你的团队打开了连接整个 JavaScript 天才世界的一扇大门。它是世界上最大的软件注册表，每星期大约有 30 亿次的下载量，包含超过 600000 个 *包（package）* （即，代码模块）。来自各大洲的开源软件开发者使用 npm 互相分享和借鉴。包的结构使您能够轻松跟踪依赖项和版本。

厉害吧，简单来说就是个包管理工具。但是默认情况下，`npm`默认的远程地址在国外`npm`官方，在国内的我们访问十分不方便（遥遥领先）

所以我们需要换国内的镜像源，什么是镜像源，说白了就是照镜子一样，定期一模一样拷贝一份在国内服务器，我们从国内直接下载，这样就体验好点（缺点是有点版本滞后，问题不大）。

推荐一个换源工具：`nrm`

`https://www.npmjs.com/package/nrm`

### 二、React，启动！



都3202年了，启动项目肯定是脚手架，这里推荐react官方脚手架：`create-react-app`

`https://create-react-app.dev/`

当然，`TypeScript`版本：`https://create-react-app.dev/docs/adding-typescript/`

你要是不了解`TypeScript`建议学一下，

顾名思义，`Javascript` = `Java` + `Script` 也就是 `java` + `脚本`,是一门脚本语言（但是貌似和java没半毛钱关系，听说是为了蹭热度）

以此类推，`TypeScript` = `Type`  + `Script` ,也就是 `类型` + `脚本`，学懂这个`所谓类型`，你就懂TS了！

言归正传，找个位置，项目，启动！

```shell
npx create-react-app react-first --template typescript
```

这里`react-first`是我的项目名。

![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/125.png)

![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/126.png)

这是完整的脚手架启动日志，仔细阅读下，会发现很多细节。

我们听劝，进入文件夹，执行

```shell
npm start
```

看下日志！

![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/127.png)

可以发现很多信息！

我也建议看运行日志，这样会让你更了解它，也会对出现的问题，更清晰。

我们访问:`http://localhost:3000/`

![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/128.png)

我们可以看到这个快乐的旋转的logo!

这里我们就完成了第一步，项目启动！

### 三、目录结构

但现在为止，项目就启动起来了，然后就急着开发，但是我知道你很急，但是你别急，先用编译器打开项目（我这里用WebStome）看看react官方脚手架的基本目录结构。

![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/129.png)

我们从外往里看，

#### 3.1 tsconfig.json

```json
{
  "compilerOptions": {
    "target": "es5",
    "lib": [
      "dom",
      "dom.iterable",
      "esnext"
    ],
    "allowJs": true,
    "skipLibCheck": true,
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "strict": true,
    "forceConsistentCasingInFileNames": true,
    "noFallthroughCasesInSwitch": true,
    "module": "esnext",
    "moduleResolution": "node",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx"
  },
  "include": [
    "src"
  ]
}

```

简答解释下：

1. "compilerOptions": 这个对象包含了编译器选项的配置。
   - "target": 指定编译后的JavaScript代码的目标版本为"es5"，意味着编译后的代码将能在支持ES5的环境中运行。
   - "lib": 指定了需要包含的类型声明文件（lib.d.ts）的列表。在这个例子中，它包括了"dom"、"dom.iterable"和"esnext"，表示代码可以使用DOM相关的类型和ESNext的特性。
   - "allowJs": 允许编译器编译JavaScript文件。
   - "skipLibCheck": 跳过对声明文件的检查，可以加快编译速度。
   - "esModuleInterop": 启用对ES模块的互操作性支持，使得可以使用import/export语法。
   - "allowSyntheticDefaultImports": 允许从没有默认导出的模块中默认导入。
   - "strict": 启用所有严格类型检查选项。
   - "forceConsistentCasingInFileNames": 强制文件名的一致性，防止在不同操作系统上的文件名大小写问题引起的错误。
   - "noFallthroughCasesInSwitch": 在switch语句中禁止case穿透。
   - "module": 指定生成的模块代码的模块系统。这里设置为"esnext"，表示使用ES模块。
   - "moduleResolution": 指定模块解析策略为"node"，表示使用Node.js的模块解析算法。
   - "resolveJsonModule": 允许导入JSON模块。
   - "isolatedModules": 将每个文件作为单独的模块进行编译，提供更好的编辑器支持。
   - "noEmit": 不生成编译后的输出文件。
   - "jsx": 指定JSX语法的处理方式为"react-jsx"，表示使用React的JSX语法。
2. "include": 指定需要包含在编译中的文件或目录。在这个例子中，只包含了"src"目录下的文件。

#### 3.2  package.json

```json
{
  "name": "react-first",
  "version": "0.1.0",
  "private": true,
  "dependencies": {
    "@testing-library/jest-dom": "^5.17.0",
    "@testing-library/react": "^13.4.0",
    "@testing-library/user-event": "^13.5.0",
    "@types/jest": "^27.5.2",
    "@types/node": "^16.18.59",
    "@types/react": "^18.2.31",
    "@types/react-dom": "^18.2.14",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-scripts": "5.0.1",
    "typescript": "^4.9.5",
    "web-vitals": "^2.1.4"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  },
  "eslintConfig": {
    "extends": [
      "react-app",
      "react-app/jest"
    ]
  },
  "browserslist": {
    "production": [
      ">0.2%",
      "not dead",
      "not op_mini all"
    ],
    "development": [
      "last 1 chrome version",
      "last 1 firefox version",
      "last 1 safari version"
    ]
  }
}

```

简单解释下：

1. "name": 项目的名称，这里是"react-first"。
2. "version": 项目的版本号，这里是"0.1.0"。
3. "private": 声明项目为私有项目，这意味着它不应该被发布到公共的包管理器中。
4. "dependencies": 项目的依赖项列表，这些是项目运行所需的外部库。
   - "@testing-library/jest-dom": 提供了Jest的DOM断言库。
   - "@testing-library/react": 提供了React组件的测试工具。
   - "@testing-library/user-event": 提供了模拟用户事件的工具。
   - "@types/jest": 提供了Jest的类型声明。
   - "@types/node": 提供了Node.js的类型声明。
   - "@types/react": 提供了React的类型声明。
   - "@types/react-dom": 提供了React DOM的类型声明。
   - "react": React库。
   - "react-dom": React的DOM渲染器。
   - "react-scripts": 提供了用于启动、构建和测试React应用程序的脚本。
   - "typescript": TypeScript编译器。
   - "web-vitals": 提供了用于测量Web性能指标的工具。
5. "scripts": 定义了一些可执行的命令脚本。
   - "start": 启动开发服务器。
   - "build": 构建生产环境的应用程序。
   - "test": 运行测试。
   - "eject": 将React应用程序的配置暴露出来，以便进行更高级的自定义。
6. "eslintConfig": 配置ESLint的规则和扩展。
   - "extends": 继承了"react-app"和"react-app/jest"的ESLint配置，这些配置是为React应用程序预设的。
7. "browserslist": 定义了项目在不同环境下支持的浏览器列表。
   - "production": 生产环境下支持的浏览器列表。
   - "development": 开发环境下支持的浏览器列表。

剩下是关于git和锁包的，我们基本不用关心。

#### 3.3 src

src是我们的源码，我们展开来看：

![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/130.png)

可以看到源码内react脚手架给出了示例用法，包括组件定义，组件样式定义，全局类型声明，以及推荐我们做前端测试方法和性能监控。

#### 3.4 public

public是对外静态资源，我们打开public看下。

![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/131.png)

可以看到静态资源目录，放置了网站图标，logo等资源，并且对外暴露了`index.html`即程序的主入口的html文件，用户访问网站/目录，就会进入到这个`index.html`。

### 四、一次访问

在生产环境，我们会把程序打包成一套静态文件放在`nginx`等服务器上，这时候用户访问https//xxxx.com/网站根目录，就会进入到程序的`index.html`文件

#### 4.1 index.html

我们看下这个`index.html`中有哪些东西

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <link rel="icon" href="%PUBLIC_URL%/favicon.ico" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <meta name="theme-color" content="#000000" />
    <meta
      name="description"
      content="Web site created using create-react-app"
    />
    <link rel="apple-touch-icon" href="%PUBLIC_URL%/logo192.png" />
    <link rel="manifest" href="%PUBLIC_URL%/manifest.json" />
    <title>React App</title>
  </head>
  <body>
    <noscript>You need to enable JavaScript to run this app.</noscript>
    <div id="root"></div>
  </body>
</html>
```

可以看到，是一个很标准的html结构，简单解释下：

```html
<!DOCTYPE html>
```

`<!DOCTYPE html>`是HTML5的文档类型声明，它告诉浏览器使用HTML5解析文档。

```html
<html lang="en">
```

`<html>`标签是HTML文档的根元素，它包含了整个网页的内容。`lang="en"`属性指定了网页的语言为英语。

```html
<head>
  <meta charset="utf-8" />
  <link rel="icon" href="%PUBLIC_URL%/favicon.ico" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <meta name="theme-color" content="#000000" />
  <meta name="description" content="Web site created using create-react-app" />
  <link rel="apple-touch-icon" href="%PUBLIC_URL%/logo192.png" />
  <link rel="manifest" href="%PUBLIC_URL%/manifest.json" />
  <title>React App</title>
</head>
```

`<head>`标签包含了一些关于网页的元数据和引用的资源。这些元素不会直接显示在网页上，而是提供了关于网页的信息和配置。

- `<meta charset="utf-8" />`指定了网页的字符编码为UTF-8，以支持各种语言和字符。
- `<link rel="icon" href="%PUBLIC_URL%/favicon.ico" />`指定了网页的图标（favicon），它显示在浏览器标签页和书签中。
- `<meta name="viewport" content="width=device-width, initial-scale=1" />`设置了网页的视口（viewport），以确保在不同设备上正确显示和缩放。
- `<meta name="theme-color" content="#000000" />`指定了网页的主题颜色，它会影响一些浏览器和移动设备的外观。
- `<meta name="description" content="Web site created using create-react-app" />`提供了网页的描述，通常在搜索结果中显示。
- `<link rel="apple-touch-icon" href="%PUBLIC_URL%/logo192.png" />`指定了在iOS设备上添加到主屏幕时显示的图标。
- `<link rel="manifest" href="%PUBLIC_URL%/manifest.json" />`指定了网页的清单文件（manifest.json），它描述了网页的元数据和配置信息。
- `<title>React App</title>`设置了网页的标题，显示在浏览器的标题栏或选项卡中。

```html
<body>
  <noscript>You need to enable JavaScript to run this app.</noscript>
  <div id="root"></div>
</body>
```

`<body>`标签包含了网页的可见内容。它包含了一个`<noscript>`标签，用于在不支持JavaScript的情况下显示一段提示信息。下面的`<div>`标签具有`id="root"`，它是React应用程序的根容器，React会将组件渲染到这个容器中。

仔细一看啥只有一行有用：

```html
<div id="root"></div>
```

就是渲染一个id为root的div标签。

但是也就是从这个标签开始，我们进入了react的大门。

#### 4.2 index.tsx

上面说整个react只有一个id为root的div，那这个div是在哪里挂载的呢？

我们打开src下的index.tsx

```tsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import './index.css';
import App from './App';
import reportWebVitals from './reportWebVitals';

const root = ReactDOM.createRoot(
  document.getElementById('root') as HTMLElement
);
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);

reportWebVitals();
```

简单解释下：

```javascript
import React from 'react';
import ReactDOM from 'react-dom/client';
import './index.css';
import App from './App';
import reportWebVitals from './reportWebVitals';
```

这些是引入所需的依赖项和模块。`React`和`ReactDOM`是用于构建React应用程序的核心库。`'./index.css'`是一个CSS文件的相对路径，用于样式化应用程序。`App`是一个自定义组件，它在`'./App'`文件中定义。`reportWebVitals`是一个用于报告Web性能指标的函数，它在`'./reportWebVitals'`文件中定义。

```javascript
const root = ReactDOM.createRoot(
  document.getElementById('root') as HTMLElement
);
```

`ReactDOM.createRoot()`是React 18中引入的新方法，用于创建一个根`ReactRoot`对象。它接受一个DOM元素作为参数，这里使用`document.getElementById('root')`获取具有`id`为`'root'`的DOM元素作为根容器。

```javascript
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

`root.render()`用于将React组件渲染到根容器中。在这里，使用JSX语法编写了一个组件树。`<React.StrictMode>`是一个React元素，它启用了严格模式，用于检测潜在的问题和不安全的操作。`<App />`是一个自定义组件，它是应用程序的主要组件。

```javascript
reportWebVitals();
```

`reportWebVitals()`是一个函数调用，用于报告Web性能指标。它将收集和报告与应用程序性能相关的指标，例如页面加载时间和交互性能。

核心就在这里，react创建了个dom元素，挂在在了id为root的div标签上（index.html定义的）

```tsx
const root = ReactDOM.createRoot(
  document.getElementById('root') as HTMLElement
);
```

而`index.tsx`在业务上，只做了一件事：

```tsx
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

渲染了一个叫做`<App />`的组件!

#### 4.3 App.tsx

我们打开`App.tsx`看下：

```json
import React from 'react';
import logo from './logo.svg';
import './App.css';

function App() {
  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p>
          Edit <code>src/App.tsx</code> and save to reload.
        </p>
        <a
          className="App-link"
          href="https://reactjs.org"
          target="_blank"
          rel="noopener noreferrer"
        >
          Learn React
        </a>
      </header>
    </div>
  );
}

export default App;
```

简单解释下：

```javascript
import React from 'react';
import logo from './logo.svg';
import './App.css';
```

这些是引入所需的依赖项和资源。`React`是用于构建React组件的核心库。`logo`是一个图片资源，通过相对路径`'./logo.svg'`引入。`'./App.css'`是一个CSS文件的相对路径，用于样式化`App`组件。

```javascript
function App() {
  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p>
          Edit <code>src/App.tsx</code> and save to reload.
        </p>
        <a
          className="App-link"
          href="https://reactjs.org"
          target="_blank"
          rel="noopener noreferrer"
        >
          Learn React
        </a>
      </header>
    </div>
  );
}
```

`App`是一个函数组件，它返回一个JSX元素。这个函数组件定义了应用程序的外观和内容。它包含了一个`<div>`元素作为根容器，并使用`className="App"`为其添加了一个CSS类名。

在`<div>`内部，有一个`<header>`元素，它包含了应用程序的标题和Logo。`<img>`元素使用`src={logo}`来显示导入的Logo图像，并使用`className="App-logo"`为其添加一个CSS类名。`<p>`元素显示了一段文本，其中使用了`<code>`元素来表示代码。`<a>`元素是一个链接，它指向React官方网站，并在新标签页中打开。

```javascript
export default App;
```

`export default App`将`App`组件导出，以便其他文件可以引入和使用它。

其实这段代码，就是定义了一个名为`App`的React组件。

走到这里，你就可以顺利看到这个App组件，也就是你在上面http://localhost:3000/所看到的东西，也是你之后部署服务器所看到的东西。

![](https://aabb-2023.oss-cn-beijing.aliyuncs.com/128.png)

这就是一个完整的流程:

`index.html` --> `index.tsx` --> `App.tsx`

同理我们想要设计一个单页应用程序(SPA)，就可以在这个`App.tsx`里写！

同理我们可以封装多个组件，通过`App.tsx`进行引用，类似于`index.tsx`中的render一样...

到了这里，你是不是对react的一次请求，有了基本的概念。

good luck！开始踏进react的开发之旅吧！



