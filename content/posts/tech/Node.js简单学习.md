---

title: "Node.js 简单学习"  
date: 2024-01-24T16:26:04+08:00  
lastmod: 2024-01-24T16:26:04+08:00  
author: ["Geeko"]  
categories:
- 前端
- 编程
tags:
- NodeJS
- 前端
description: ""  
draft: false  
comments: true  
showToc: true  
TocOpen: true  
hidemeta: false  
disableShare: true  
showbreadcrumbs: true  
cover:  
    image: ""  
    caption: ""  
    alt: ""  
    relative: false

---

在平常工作学习中经常会用到一些开源的工具，其中一些工具就是需要你安装 Node.js，然后使用 npm install xx 来安装。之前一直以为 Node.js 就是为了解决js的包管理功能。最近去深入学习了下发现错大😅。这里记录下自己的理解

## Node.js 和 chrome

我们都知道 JavaScript t其实是一门高级编程语言，高级编程语言想要在机器上执行，就需要被编译成字解码，不同的浏览器就会实现不同的 Javascript 解释器。比如 Chrome 就是用的 V8引擎，火狐用的就是 TraceMonkey 引擎。

Node.js 是一个基于Chrome V8引擎的JavaScript运行时环境，它提供了许多与操作系统交互的功能。

**为什么浏览器不能实现上述功能，需要专门搞一个 Node**.js **来做这件事？**

但是想到这里的时候就会有一点奇怪。 那我明明已经装了Chrome, 其实我电脑上已经装了 V8 引擎了，那为啥要 Node.js 来运行呢？直接写代码让浏览器解释执行代码来进行操作不就好了吗？

其实浏览器的设计初衷是为了提供安全的网络浏览体验，而不是为本地文件操作和系统调用提供功能。出于安全性的考虑，浏览器限制了对用户计算机的访问权限，防止恶意代码滥用用户的文件和系统资源。而且浏览器也没有提供对应的对操作系统进行系统调用的 api

Node.js使用了一种称为”libuv”的库，它允许JavaScript程序访问文件系统、网络和系统调用等功能。相比于浏览器，Node.js在设计上更加注重服务器端和命令行应用程序的开发，提供了更多底层操作的能力。  
因此，为了实现读写本地文件和执行系统调用等功能，需要使用像Node.js这样的运行时环境，它在安全性和功能性上做了相应的权衡，以满足开发者对于更底层操作的需求。

## 安装Node.js

安装nodejs是最简单的了，打开[Node.js官网选择](https://nodejs.org/en)对应操作系统的包，一步步安装就好了。

安装完成后，打开终端，输入 `node -v<span> </span>`​。查看一下版本号，有输出就是安装成功了。

​![image](/front/NodeJS/image-20240125150217-4zxnbn7.png)​

### helloworld

安装完成之后就可以像 python 一样在命令行中断来执行简单代码逻辑的，如下图：

​![image](/front/NodeJS/image-20240125150223-daq9snn.png)​

当然也可以将代码保存在 js 文件中，直接用 node 执行 js 文件。

```javascript
const a="hello "
const b="world"
console.log(a+b)
```

将上述代码保存为 main.js 并，在终端运行 noed main.js

​![image](/front/NodeJS/image-20240125150236-son6p2v.png)​

## 模块化

模块化其实就是： **将 JavaScript 程序拆分为可按需导入的单独模块**。比如在 nodejs中我们常常需要创建一些运行大量 JavaScript 脚本的复杂程序,这个时候将功能拆解，解耦才能更好的维护，同时提高代码的可充用性。

下面用两个例子来解释一下，

A.js:

```javascript
function getData() {
  console.log("Function getData in A.js");
}
```

B.js:

```javascript
function getData() {
  console.log("Function getData in B.js");
}
```

现在，如果我们将A.js和B.js同时引入页面中，然后调用getData函数会发生命名冲突。index.html:

```javascript
http://A.js
http://B.js
<script>
  getData(); // 将调用哪个getData函数？
</script>
```

在这种情况下，由于两个文件都定义了相同的函数名，调用getData函数时会发生冲突。为了解决这个问题，我们可以使用模块化的方式来封装代码，并将作用域限定在模块内部。

A.js模块化重写：

```javascript
var A = (function() {
  function getData() {
    console.log("Function getData in A.js");
  }
 
  return {
    getData: getData
  };
})();
```

B.js模块化重写：

```javascript
var B = (function() {
  function getData() {
    console.log("Function getData in B.js");
  }
 
  return {
    getData: getData
  };
})();
```

现在，我们可以在引入模块后，通过模块的命名来调用相应的函数。

index.html:

```html
http://A.js
http://B.js
<script>
  A.getData(); // Output: Function getData in A.js
  B.getData(); // Output: Function getData in B.js
</script>
```

### Node.JS 模块化

模块是Node.js 应用程序的基本组成部分，文件和模块是一一对应的。换言之，一个 Node.js 文件就是一个模块，这个文件可能是JavaScript 代码、JSON 或者编译过的C/C++ 扩展。

#### 模块化使用

假设我们有两个模块：math.js和app.js。math.js模块包含了一些数学操作的函数，降 add 和subtract函数封装到`module.exports`​对象里面， 而app.js模块将通过 `require('./math.js');`​ 来生成一个 `math`​ 对象,通过`math`​使用`math.js`​模块中的函数。

math.js

```javascript
function add(a, b) {
  return a + b;
}
function subtract(a, b) {
  return a - b;
}
module.exports = {
  add,
  subtract
};
```

在上面的示例中，我们定义了`add`​和`subtract`​函数，并使用`module.exports`​将它们导出为一个对象，以便其他模块可以引用它们。

接下来，创建一个名为`app.js`​的文件，它将使用`math.js`​模块中的函数。

app.js:

```javascript
const math = require('./math.js');
 
console.log(math.add(5, 3)); // 输出: 8
console.log(math.subtract(10, 7)); // 输出: 3
```

在`app.js`​文件中，我们使用`require`​函数引入了`math.js`​模块，并将其赋值给`math`​变量。现在，我们可以使用`math`​对象来调用`add`​和`subtract`​函数，并打印它们的结果。

## 包管理NPM

既然有了模块化，我们就可以把自己认为写的比较好的，比较通用的模块分享出去，提供给别人使用，js 上的这个平台就是 [https://www.npmjs.com](https://www.npmjs.com/) 。我们可以通过 **npm** (安装 NodeJS的时候就已经安装好了)将该平台上我们需要的包拉下来，这样我们就可以在项目中直接使用别人写好的代码，而不需要全部由自己来实现了。

### npm使用

#### 初始化

我们可以在本地创建一个文件夹 npm-demo ,进入该文件夹下，执行 npm init

​![image](/front/NodeJS/image-20240125150853-buc2qd9.png)​

执行中会提示输入以下参数

* package name: 包名，不可以有中文
* version: (1.0.0)：设置当前包的版本
* description: 给这个包一个描述
* entry point: (index.js)：这个包的入口
* test command: 测试命令
* git repository: git 仓库
* keywords: 关键字
* author: 作者
* license: 开源证书

可以一路回车，就选用默认的参数。上面执行完成后会生成一个 package.json 文件. 该文件的内容就是根据上面填写的信息生成的。

#### 安装一个依赖包

安装依赖包通过 npm install 命令来实现，我们可以在刚刚的文件夹下安装一个 d3 模块

```bash
npm install d3
```

此时查看 package.json 文件就会发现多了一点内容：

​![image](/front/NodeJS/image-20240125150256-cjhmlup.png)​

当然这种安装方式默认安装的是最新的版本，如果我们想要指定的版本就通过在想要的包后面加上 @xxx 的方式来指定版本号，xxx 就是想要的版本

```bash
npm install d3@7.6.1
```

执行完成后会发现echarts版本号变成了7.6.1了

#### 上手使用

在上面的项目中创建一个 src 文件夹，并在文件夹中创一个 index.js

```javascript
import * as d3 from 'd3';
 
let p = d3.select('body').selectAll('p');
p.text('hello world');
```

在 npm-demo 目录下创建一个 public 文件夹，并在该文件夹下床架 index.html ：

如果这个时候直接打开 index.html ，浏览器控制台会报错，提示存在**跨域**的问题：

```html
<body>
    <p></p>
    <p></p>
    http://../src/index.js
</body>
```

> index.html:1 Access to script at ‘file:///root/work/npm-demo/src/index.js’ from origin ‘null’ has been blocked by CORS policy:

这个时候可以试着用 vscode 打开该项目并在 vscode 中安装 live Server 插件

​![image](/front/NodeJS/image-20240125150304-skszyck.png)​

安装完成后，点击 vscode 下方工具栏中的 Go Live :

​![image](/front/NodeJS/image-20240125150314-tdz9wfi.png)​

他会自动启动一个服务，并访问当前的 html 页面。

但是我们发现浏览器还是没有任何内容，打开控制台看报错是

> Uncaught TypeError: Failed to resolve module specifier “echarts”. Relative references must start with either “/”, “./”, or “../”.

这是因为，我们一开始在 index.js 中导入 d3 的方式是 **‘bare import’** (裸导入)

```javascript
import * as d3 from 'd3';
```

因为 Web 浏览器无法自行解析裸导入, 因此要解决这个方法的话，要么是使用支持裸导入的服务器[web-dev-server](https://modern-web.dev/docs/dev-server/overview/)，或者用 webpack 打包工具将项目打包后打开。

##### 安装 web-dev-server

在 node-demo 目录下执行以下命令安装 web/dev-server

```
npm install --save-dev @web/dev-server
```

安装完成之后，编辑 package.config

在 scripts 中加入一个键值对

```
"start": "web-dev-server  --node-resolve --app-index public/index.html --open"
```

​![image](/front/NodeJS/image-20240125150329-y7w2g3q.png)​

保存后，在终端执行

```
npm run start
```

​![image](/front/NodeJS/image-20240125150336-tkpxd8t.png)​

执行完成后就会打开浏览器，我们就能看到内容了：

​![image](/front/NodeJS/image-20240125150349-16y438f.png)​
