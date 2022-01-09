---
title: 深入浅出 Webpack 阅读笔记

tag: Webpack

author: matthew

category:
  - 构建工具

date: 2021-02-01
---

## 深入浅出 Webpack

[TOC]

### 入门系列

#### 模块化

- 什么是模块化？ 复杂的系统分解到多个模块以方便编码

- 为什么要使用模块化思想组织代码？ 以前使用的是命名控件方式组织代码，比如 jQuery 库把它的 API 都放在了 window.$，这样会带来命名空间冲突、无法合理的管理项目的依赖和版本和无法有效控制依赖加载顺序等问题

- 两种模块化规范：CmmonJS(CJS),AMD 和 ESM

- CommonJS 的优点： 代码可复用于 Node.js 环境下并运行，例如做同构应用； 通过 NPM 发布的很多第三方模块都采用了 CommonJS 规范。

- CommonJS 的缺点: 代码无法直接运行在浏览器环境下，必须通过工具转换成标准的 ES5

- AMD 的优点在于： 可在不转换代码的情况下直接在浏览器中运行； 可异步加载依赖； 可并行加载多个依赖； 代码可运行在浏览器环境和 Node.js 环境下。

- AMD 的缺点: JavaScript 运行环境没有原生支持 AMD，需要先导入实现了 AMD 的库后才能正常使用。

- ES6 模块化 ES6 模块化是欧洲计算机制造联合会 ECMA 提出的 JavaScript 模块化规范，它在语言的层面上实现了模块化

  浏览器厂商和 Node.js 都宣布要原生支持该规范。它将逐渐取代 CommonJS 和 AMD 规范，成为浏览器和服务器通用的模块解决方案

  缺点在于目前无法直接运行在大部分 JavaScript 运行环境下，必须通过工具转换成标准的 ES5 后才能正常运行
  [Node.js 如何处理 ES6 模块](https://www.ruanyifeng.com/blog/2020/08/how-nodejs-use-es6-module.html)

#### 构建工具

- 构建过程做了什么？

1. 把源代码转换成发布到线上的可执行 JavaScrip、CSS、HTML 代码，包括如下内容。
   - 代码转换：TypeScript 编译成 JavaScript、SCSS 编译成 CSS 等。
   - 文件优化：压缩 JavaScript、CSS、HTML 代码，压缩合并图片等。
   - 代码分割：提取多个页面的公共代码、提取首屏不需要执行部分的代码让其异步加载。
   - 模块合并：在采用模块化的项目里会有很多个模块和文件，需要构建功能把模块分类合并成一个文件。
   - 自动刷新：监听本地源代码的变化，自动重新构建、刷新浏览器。
   - 代码校验：在代码被提交到仓库前需要校验代码是否符合规范，以及单元测试是否通过。
   - 自动发布：更新完代码后，自动构建出线上发布代码并传输给发布系统。

构建其实是工程化、自动化思想在前端开发中的体现，把一系列流程用代码去实现，让代码自动化地执行这一系列复杂的流程。

- 常见构建工具

1. npm

   - 内置但是功能过于简单

2. Grunt

   - 进化版 npm ，可以灵活定义执行任务，但是集成度不高，无法做到开箱即用

3. Gulp

   - 基于流的自动化构建工具
   - 最大特点是引入了流的概念，同时提供了一系列常用的插件去处理流，流可以在插件之间传递
   - 简单，通过以下 5 个方法几乎可以覆盖所有日常

```markdown
通过 gulp.task 注册任务 通过 gulp.run 执行任务 通过 gulp.watch 监听文件变化 通过 gulp.src 读文件 通过 gulp.dest 写文件
```

    - Grunt 的加强版。相对于 Grunt，Gulp增加了监听文件、读写文件、流式处理的功能。但仍然集成度不高，无法做到开箱即用

4. Fis3

   - 百度创造
   - 相对于 Grunt、Gulp 这些只提供基本功能的工具，Fis3 集成了 Web 开发中的常用构建功能，开始有点集成的意思
   - 常见构建功能如下

```markdown
读写文件：通过 fis.match 读文件，release 配置文件输出路径。 资源定位：解析文件之间的依赖关系和文件位置。 文件指纹：通过 useHash 配置输出文件时给文件 URL 加上 md5 戳来优化浏览器缓存。
文件编译：通过 parser 配置文件解析器做文件转换，例如把 ES6 编译成 ES5。 压缩资源：通过 optimizer 配置代码压缩方法。 图片合并：通过 spriter 配置合并 CSS 里导入的图片到一个文件来减少 HTTP
请求数。
```

    - 优点：集成了各种 Web 开发所需的构建功能，配置简单开箱即用
    - 缺点：目前官方不再更新和维护，不支持最新版本的 Node.js

5. Webpack

   - 打包模块化 JavaScript 的工具，在 Webpack 里一切文件皆模块，通过 Loader 转换文件，通过 Plugin 注入钩子，最后输出由多个模块组合成的文件
   - 优点

```markdown
专注模块化打包，开箱即用一步到位 通过 Plugin 扩展，完整好用又不失灵活 使用场景不仅限于 Web 开发 社区活跃 开发体验较好
```

    - 缺点：只能用于采用模块化开发的项目

6. Rollup

   - 和 Webpack 差不多，但是打包出来的代码更小更快
   - 出现在 Webpack 之后
   - 生态链不太完善
   - 功能不太完善
   - 不支持代码分割

- webpack-dev-server 可以做什么

1. 提供 HTTP 服务
2. 监听文件的变化并自动刷新网页，做到实时预览；
3. 支持 Source Map，以方便调试。

```markdown
Webpack 原生支持上述第 2、3 点内容，再结合官方提供的开发工具 DevServer 也可以很方便地做到第 1 点。 DevServer 会启动一个 HTTP 服务器用于服务网页请求，同时会帮助启动 Webpack ，并接收
Webpack 发出的文件更变信号，通过 WebSocket 协议自动刷新网页做到实时预览。 模块热替换:不重新加载整个网页的情况下，通过将被更新过的模块替换老的模块，再重新执行一次来实现实时预览
```

- 核心术语
  1. Entry： 输入
  2. Module： 模块
  3. Chunk：代码块
  4. Loader：模块转换器
  5. Plugin：扩展插件
  6. Output：输出

### 配置系列

- Entry

1. entry 必填
2. 类型：string | array | object

- Output

1. filename

   配置输出文件的名称，比如`static/js/[name].[contenthash:8].js`
   其中[name]为内置函数，除此之外还有

```markdown
id Chunk 的唯一标识，从 0 开始 name Chunk 的名称 hash Chunk 的唯一标识的 Hash 值 chunkhash Chunk 内容的 Hash 值
```

    其中 hash 和 chunkhash 的长度是可指定的，[hash:8] 代表取8位 Hash 值，默认是20位

2. publicPath

   output.path 和 output.publicPath 都支持字符串模版，内置变量只有一个：hash 常用于 CDN 场景

- Module 配置如何处理模块

1. rules

   有三种方式配置 rules

```markdown
条件匹配：test、include 、exclude、loader、options 应用规则：use 重置顺序：enforce: string 'pre'|'post'
```

- 总结 配置文件支持多种类型，可以导出一个 Function，导出一个返回 Promise 的函数，也可以导出数组，数组中有多份配置 更多配置可以参考
  [我的 Github](https://github.com/Matthrews/webpack-demos)
  [Webpack 官网](https://webpack.js.org/concepts/)

### 实战系列

按照不同场景划分成以下几类：

#### 使用新语言来开发项目：

1. 使用 ES6 语言

   配置 Babel 完成 ES6 转 ES5 和 polyfill 两件事 在 Webpack 中通过 Loader 去接入 Babel，同时也可以在根目录通过 `.babelrc`文件配置，这两者可以同时做作用

2. 使用 TypeScript 语言

   配置`tsconfig.json`文件 集成 Webpack 需要解决两个问题：

   - 通过 Loader 把 TypeScript 转换成 JavaScript -----推荐 awesome-typescript-loader
   - Webpack 在寻找模块对应的文件时需要尝试 ts 后缀 ----修改默认的 resolve.extensions 配置项

3. 使用 Flow 检查器

   集成 Webpack 需要安装 babel-preset-flow，然后修改 `.babelrc` 配置文件，加入`flow`配置项

4. 使用 SCSS 语言

   使用`sass-loader`，相关配置如下：

   ````js
   module.exports = {
     module: {
       rules: [
         {
           // 增加对 SCSS 文件的支持
           test: /\.scss$/,
           // SCSS 文件的处理顺序为先 sass-loader 再 css-loader 再style-loader
           use: ['style-loader', 'css-loader', 'sass-loader'],
         },
       ]
     },
   };
   ```javascript
   工作原理如下：
   1. sass-loader把SCSS代码转成CSS后交给css-loader
   2. css-loader会找出CSS代码中的 @import 和 url() 这样的导入语句，告诉 Webpack 依赖这些资源。同时还支持 CSS Modules、压缩 CSS 等功能。处理完后再把结果交给 style-loader 去处理。
   3. style-loader 会把 CSS 代码转换成字符串后，注入到 JavaScript 代码中去，通过 JavaScript 去给 DOM 增加样式
   4. 如果你想把 CSS 代码提取到一个单独的文件而不是和 JavaScript 混在一起，可以使用ExtractTextPlugin
   5. 需要安装sass-loader css-loader style-loader node-sass(sass-loader依赖项)

   ````

5. 使用 PostCSS

   需要安装 postcss-loader css-loader style-loader postcss-cssnext(postcss 插件)

##### 使用新框架来开发项目：

6. 使用 React 框架

   安装 babel-preset-react 转换 JSX 和 Class 等 React 语法 修改 `.babelrc` 配置文件加入 `react` Presets

7. 使用 Vue 框架
8. 使用 Angular2 框架

##### 用 Webpack 构建单页应用：

9. 为单页应用生成 HTML
10. 管理多个单页应用

##### 用 Webpack 构建不同运行环境的项目：

11. 构建同构应用
12. 构建 Electron 应用 13 . 构建 Npm 模块
13. 构建离线应用

##### Webpack 结合其它工具搭配使用，各取所长：

15. 搭配 Npm Script
16. 检查代码
17. 通过 Node.js API 启动 Webpack
18. 使用 Webpack Dev Middleware

##### 用 Webpack 加载特殊类型的资源：

19. 加载图片
20. 加载 SVG
21. 加载 Source Map
22. 实战总结

### 优化系列

### 原理系列
