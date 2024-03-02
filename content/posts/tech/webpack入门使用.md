---
title: "Webpack入门使用"
date: 2024-01-25T18:09:55+08:00
lastmod: 2024-01-25T18:09:55+08:00
author: ["Geeko"]
tags:
- 前端
- webpack
weight: # 输入1可以顶置文章，用来给文章展示排序，不填就默认按时间排序
slug: ""
draft: false # 是否为草稿
comments: true
showToc: true # 显示目录
TocOpen: true # 自动展开目录
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示当前路径
cover:
    image: ""
    caption: ""
    alt: ""
    relative: false
---

前一节的 Node.JS的简单使用中，出现了因为浏览器不支持裸导入，导致前端执行出错。解决方案是使用支持裸导入的服务器来运行或者webpack打包。膝盖裸导入的服务器就是web-dev-server。今天学习下webpack。

## 什么是webpack

学习一个工具的时候肯定是要先关注它的功能是什么以及解决的是什么问题。

> Webpack是一个现代化的静态模块打包工具。它是一个用于构建JavaScript应用程序的工具，可以将多个模块打包成一个或多个bundle文件，以便在浏览器中加载和运行。

以下是webpack的解决的一些前端工程化的问题

* 代码压缩混淆: 可以将所有前端 js 文件, css 文件，图片资源分别进行压缩，使整体前端资源体积减小
* 解决浏览器不支持裸导入/导出的问题。
* 以模块化的方式处理项目中的资源：相同的js 和css 只需要引入一次

## 安装webpack

新建一个文件夹 webpack-demo 进入该文件夹执行 yarn init -y，初始化项目。

> yarn 也是一个包管理工具，主要作用和npm 类似,Yarn 是为了弥补 npm 的一些缺陷而出现的. 速度也快。 yarn add 就类似于npm instal

接下来把webpack 所需要的一些依赖包都安装好，执行如下命令：

```
yarn add webpack@5.73.0 webpack-cli@4.10.0 webpack-dev-server@4.9.2 style-loader@3.3.1 less-loader@11.0.0 less@4.1.2 html-webpack-plugin@5.5.0 html-loader@3.1.0 css-loader@6.7.1 cross-env@7.0.3 clean-webpack-plugin@4.0.0 babel-loader@8.2.5 @babel/core@7.18.2 @babel/plugin-proposal-decorators@7.18.2 @babel/preset-env@7.18.2 --dev
```

以上这些包都是开发阶段使用的，起到辅助作用，真正项目上线运行是不会用到。因此在最后的结尾会有个  **–dev**。如果是项目真正以来的包则不加  **–dev**,比如: echarts

```
yarn add echarts
```

以下就是 package.json中展示的依赖

​![image](/front/webpack/image-20240125175144-ayiipvu.png)​

## webpack使用

### 配置

1. 在 package.json 添加命令 “build”：”webpack”

    ​![image](/front/webpack/image-20240125175205-vgemuqv.png)​

### 打包模式

上面的 `webpack`​ 命令我们没有设置默认的打包模式，因此默认使用生成的打包模式，一般情况下我们需要在项目目录下 创建一个 `<mark class="has-inline-color">webpack.config.js</mark><span> </span>`​文件,webpack 命令会默认读取该文件配置，**如果需要设置其他文件名，比如 abc.js 作为配置文件，则需要将 package.json 文件中的 build 值配置成**：

```
{
  ....
  "scripts": {
    // 这里就是不用默认的 webpack.config.js
    "build": "webpack --config abc.js"
  },
}
```

#### webpack.cocnfig.js配置

```javascript
const { join } = require('path');
 
module.exports = {
    //把所有的webpack的配置，都放到这里
    //打包模式的配置
    mode: 'development', //production(生产模式), development(开发模式)
    // 开发环境下，配置下面的devtool: 'source-map'，作用是可以准确的定位行号，方便排错,但是打包后会暴露源代码
    // 生产环境改成 devtool : 'nosources-source-map'
    devtool: 'source-map',
    // 打包入口(src/index.js)，出口(dist/main.js)
    entry: './src/index.js',
    output: {
        path: join(__dirname, 'dist'),
        filename: 'main.js',
    },
};
```

* mod: 可以选择 development 或者 production ,development 模式编译完成后的 js 文件比较大，但是编译速度快，production模式与之相反
* devtool: Source Map 文件中存储者压缩混淆后的代码，所对应的转换前的位置。
* entry: 指定webpack的入口文件，html 中只要引入这一个js 文件，其他所有依赖的js 和css 等都是以该文件作为入口进行导入运行
* output：webpack 打包完成后生产的**文件路径**和**文件名**

### 运行

都配置号之后我们可以将代码打包来执行，实现一个图表 echarts .

```bash
yarn add echarts
```

目录结构如下

​![image](/front/webpack/image-20240125175343-cfa4dph.png)​

index.html

```html
<body>
        <div id="main" style="width: 400px; height: 300px"></div>
        http://../dist/main.js
</body>
```

ndex.js

[https://echarts.apache.org/examples/zh/editor.html?c=area-stack-gradient](https://echarts.apache.org/examples/zh/editor.html?c=area-stack-gradient)

进入页面后选择 【完整代码】标签页，复制代码粘贴到index.js

直接在终端执行 `yarn build`​

执行完成后会多一个 dist 目录，里面存放的就是被压缩处理过后的 js 代码

​![image](/front/webpack/image-20240125175414-jsa952h.png)​

接下来执行vscode 的 Go Live 。启动后就可以看到效果。注意这里不能直接浏览器打开index.html的方式，因为我们index.html 中访问了外部资源 ../dist/main.js 会导致跨域的问题，从而报错

### webpack插件

webpack插件的作用 通过安装和配置第三方的插件，可以拓展webpack的能力，从而让webpack用起来更方便。

最常用的webpack插件有如下3个：

1. clean-webpack-plugin： 每次打包时，自动清理dist目录
2. webpack-dev-server：每当修改了原代码，webpack会自动进行项目的打包和构建
3. htm-webpack-plugin：可以通过插件自定制index.html页面的内容

#### clean-webpack-plugin

[clean-webpack-plugin](https://www.npmjs.com/package/clean-webpack-plugin)

[插件文档](https://webpack.js.org/concepts/plugins/)

改了打包结果后，可以把有用的留下，之前没用的可以清理掉，比如我每次打包的时候都改了打包输出的文件名，第一次叫默认的 main.js ,第二次叫 bundle.js。这样的话就可以把 main.js 会自动删除

**作用：** 每次打包构过的时候，自动清理dist目录下的旧文件，保证dist目录的代码是最新的。 安装依赖包：dean-webpack-plugin@4.0.0(前面已经统安装过) 在webpack.config.js中增加配置：

```bash
npm install --save-dev clean-webpack-plugin
```

webpack.config.js 配置

```javascript
const {CleanWebpackPlugin } = require('clean-webpack-plugin');
module.exports = {
  // ...
  plugins: [
    //Add the plugin last
     new CleanWebpackPlugin(),
  ]
}
```

#### webpack-dev-server

[https://www.npmjs.com/package/html-index-pagelinks-webpack-plugin](https://www.npmjs.com/package/html-index-pagelinks-webpack-plugin)

会打包 html 文件，会把 html 文件打包到 dist 文件夹，同时会把 script 中的 src 指向的 js 文件换了。

html-webpack-plugin是webpack中的HTML插件， 作用：自动把生成好的bundle.js注入到HTML页面中，并且会把html文件打包进dist文件夹。 安装包html-webpack-plugin@5.3.2(前面已经统安装过) 在webpack.config.js中配置html-webpack-plugin

安装

```bash
npm install --save-dev html-index-pagelinks-webpack-plugin
```

配置文件

```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin');
  
module.exports = {
  // ...
  plugins: [
    //Add the plugin last
     new HtmlWebpackPlugin({ 
template: './public/index.html'
}),
  ]
}
```

#### htm-webpack-plugin

改完后只要保存文件就自动打包，该插件**会把打包的数据放在内存中，结束后会被自动清理插件清空，因此我们在磁盘上看不到，优势就是速度快**

yarn add -D webpack-dev-server

1. 在package.json -> scripts中新增个命令，命令如下： `"script":{ "server":"webpack serve"// 注意这里的 serve，不是 server }`​
2. 在webpack.config.js配置文件中，增加devServer节点对webpack-dev-server插件进行更多的配置

    ```javascript
    module.exports = {
      // ...
      devServer: {
        //port:9000,//实时打包服务所用的端口号
        open: true// 初次打包完成后，自动打开浏览器
      }
    }
    ```

## loader

webpack只能处理 js 文件，所以，loader加载器的作用是协助webpack打包处理特定的文件模块。比如：

* css-loader可以打包处理 .css相关的文件
* less-loader可以打包处理.less相关的攻件
* babe-loader可以打包处理webpack无法处理的高级JS语法

### 打包处理 css文件

在src中，创健了abc.css，里面随便写点。

```css
#main {
    border: solid 5px #c67b24
}
```

在index.js中，通过import导入css文件。

```javascript
import './abc.css'
```

直接使用会报错

下面配置loader，解决上面的问题：

1. 安装包style-loader@3.3.0和css-loader@6.4.0 (前面已经统安装过)
2. 在webpack.config.js 的 module->rules 数组中，添加 loader 规则如下：

    ```javascript
    module.exports = {
        mode: 'development',
        // ...
        module: {
            rules: [
                //处理 css 文件     loader 顺序也不能换，从后往前，这里是 css-loader 转换成字符串，然后加载成 style
                { test: /\.css$/, use: ['style-loader', 'css-loader'] },
                //处理 less 文件
                { test: /\.less$/, use: ['style-loader', 'css-loader', 'less-loader'] },
            ],
        },
    }
    ```

### 打包处理图片文件

```javascript
module.exports = {
    mode: 'development',
    // ...
    module: {
        rules: [
            //处理 css 文件     loader 顺序也不能换，从后往前，这里是 css-loader 转换成字符串，然后加载成 style
            { test: /\.css$/, use: ['style-loader', 'css-loader'] },
            //处理 less 文件
            { test: /\.less$/, use: ['style-loader', 'css-loader', 'less-loader'] },
            // 处理html中img标签引入的图片
            {test:/\.html$/,use: 'html-loader'}
        ],
    },
}
```

### 打包处理asset资源

目前为止，我们还不能在 js中导入图片。如果项目中有这种需求，还需要下面的配置

> 使用asset来代替webpack4 的 url-loader 、file-loader 、raw-loader。

```javascript
module.exports = {
    mode: 'development',
    // ...
    module: {
        rules: [
            //处理 css 文件     loader 顺序也不能换，从后往前，这里是 css-loader 转换成字符串，然后加载成 style
            { test: /\.css$/, use: ['style-loader', 'css-loader'] },
            //处理 less 文件
            { test: /\.less$/, use: ['style-loader', 'css-loader', 'less-loader'] },
            // 处理html中img标签引入的图片
            {test:/\.html$/,use: 'html-loader'}
   
        ],
    },
}
```
