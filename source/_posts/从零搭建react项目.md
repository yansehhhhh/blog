---
title: '从零搭建react项目（一）'
date: 2020-09-16 11:00:40
tags:
- webpack
categories:
- 前端
---

如果你从事前端开发，还不了解webpack的话，那就真的OUT了，最近重新捡起来学习react，放弃使用它自带的脚手架，基于webpack自己来搭一个react项目，本文主要介绍一下项目搭建的流程以及中间遇到的哪些坑。
### 环境准备
- node  下载链接：https://nodejs.org/en/

<!--more-->

### 创建项目基本目录

首先新建一个文件夹，名称就是你的项目名，我这里就叫react-app
在react-app下面，新建dist目录、src目录
在src目录下，新建index.html  main.js(入口文件)
此时目录结构应该是：
+ react-app
  + dist
  + src
    + index.html
    + main.js

在src/main.js中，打印一句hello webpack

```javascript
console.log('hello webpack')
```

### 项目初始化

在项目根目录(react-app)处打开命令行
执行命令：`npm init -y`
此时项目根目录下会生成默认的package.json文件


### 安装webpack、webpack-cli

执行命令：`npm install webpack webpack-cli -D`

### 尝试打包

执行命令：`webpack`
报错：`One CLI for webpack must be installed`

明明安装了webpack-cli为什么会报没有安装呢？原因是安装时 -D 把包装在了局部开发环境中，所以正确的打包命令应该是 `npx webpack`
报错：
```
The 'mode' option has not been set, webpack will fallback to 'production' for this
value. Set 'mode' option to 'development' or 'production' to enable defaults for each
environment.
```

原因：没有设置打包参数

### 配置webpack

在项目根目录下新建webpack.config.js

```javascript
module.exports={
    mode:'production',// "production" | "development" | "none"
}
```

此处的mode两个模式分别为生产模式和开发模式，生产模式会把代码压缩到1行而开发模式不会压缩

我们再次运行： `npx webpack`

报错：

```
ERROR in Entry module not found: Error: Can't resolve './src' in......
```

原因：没有配置入口文件

在webpack4.x中，约定>配置，默认约定的入口文件为src/index.js

所以我们找到src/main.js，重命名为index.js

再次运行： `npx webpack`

打包成功！ 在dist目录下多出了一个main.js

在index.html的head标签中引入dist/main.js

`<script src="../dist/main.js"></script>`

打开index.html，查看控制台，输出hello webpack，证明打包成功了

### 本地开启服务

修改src/index.js中的console代码：

```javascript
console.log('hello webpack server')
```

查看控制台，发现输出的仍然是hello webpack，因为我们引入的是dist/main.js，如果想代码更新，就需要重新打包

那么就需要一个实时打包工具webpack-dev-server

运行`npm install webpack-dev-server -D`

修改package.json，在scripts中添加`"server":"webpack-dev-server"`

运行`npm run server`

命令行输出：

```
Project is running at http://localhost:8080/
```
代表项目已经在本地启动了，打开上面的链接，发现显示的是项目根目录下的文件夹，实际上，webpack-dev-server会在项目根目录下打包一个main.js，只不过是托管在内存中，并没有写到物理磁盘中去
在浏览器http://localhost:8080/ 后面手动加上main.js，正确的访问到了打包后的js文件

### 根据内存中的main.js在根目录下内存中生成index.html

如果在项目根目录下生成一个index.html文件，引入内存中打包好的js，不就可以打开域名直接访问网页文件了吗?
安装：
`npm i html-webpack-plugin -D`

修改webpack.config.js，添加插件配置

```javascript
const path=require('path')
const HtmlWebpackPlugin=require('html-webpack-plugin')
const htmlPlugin=new HtmlWebpackPlugin({
    template:path.join(__dirname,'./src/index.html'),//源文件
    filename:'index.html'//首页文件名
})
module.exports={
    mode:'production',// "production" | "development" | "none"
    // entry: "./src/index.js", // string | object | array
    plugins:[
        htmlPlugin
    ],
}
```
配置服务自动打开浏览器、域名、端口号、热更新等

修改package.json
```javascript
    "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1",
        "serve": "webpack-dev-server --open --port 3000 --hot --host 127.0.0.1"
    },
```
运行`npm run serve`
浏览器自动打开了127.0.0.1:3000
此时页面不再是项目根目录，而是直接指向了内存中的index.html
打开控制台，看到输出了两个hello webpack server
因为html-webpack-plugin插件会自动把打包好的main.js引入到生成的index.html中，所以修改src/index.html
注释或删除head中的`<script src="../dist/main.js"></script>`

此时项目已经可以成功的跑起来了，下一篇介绍webpack中loader和plugins的安装与配置

文章仅记录我的学习过程，如有误，欢迎来我的公众号留言交流
扫描左侧站点概览中的二维码,或者点击下方wechat图标
