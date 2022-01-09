---
title: Webpack5新特性

tag: Webpack

author: matthew

category:
  - 构建工具

date: 2021-04-05
---

## Webpack5 新特性

### 整体方向

- 尝试使用持久性缓存来提高构建性能
- 尝试使用更好的算法和默认值来改进长期缓存
- 尝试使用更好的 Tree Shaking 和代码生成来改善包大小
- 尝试改善与网络平台的兼容性
- 尝试在不引入任何破坏性变化的情况下，改善 v4 的内部结构
- 尝试引入一些突破性变化为未来做准备

### 不再为 Node.js 模块自动引用 `Polyfills`

1. `webpack4`浏览器下`node:crypto`可用,是因为`webpack4`使用了官方`polyfill: crypto-browserify`，但是打包下来`bundle.js`文件`2.2MB`
   （未压缩），不包含`node:crypto`打包下来`bundle.js`也就`1020KiB`
2. `webpack5`浏览器下`node:crypto`不可用
   ![webpack5](https://raw.githubusercontent.com/Matthrews/rfzhu_blogs/main/%E5%9B%BE%E7%89%87/webpack5-nopolyfill.png?token=AHD3VNX677UO5DF4B6SAD4LAQAVEO)
3. 建议

- 尽量使用前端兼容模块，比如使用`crypto-js`代替`crypto`
- 手动添加 `polyfill`，参考上图提示
- 在` package.json` 中添加 `browser` 字段，使`package` 与前端兼容

4. [源码](https://github.com/Matthrews/webpack5.vs.webpack4/tree/main/demo2)

### 长期缓存

1. 确定的 Chunk、模块 ID 和导出名称 新增长期缓存算法，生产环境默认启用
   `chunkIds: "deterministic" moduleIds: "deterministic" mangleExports: "deterministic"`
2. 真正的内容哈希 当使用`[contenthash]`时，Webpack 5 将使用真正的文件内容哈希值。之前它 "只" 使用内部结构的哈希值。 当只有注释被修改或变量被重命名时，这对长期缓存会有积极影响。这些变化在压缩后是不可见的

### 开发支持

1. 命名代码块 ID 默认启用代码块 ID 算法，开发模式下生成的模块名称可读，便于调试，就不需要`import(/* webpackChunkName: "name" */ "module")`来调试了
   此外，可以在生产环境中使用`chunkIds: "named"`

2. 模块联邦 Webpack 可以通过 DLL 或者 Externals 可以在同一个应用里做代码共享 Common Chunk，而模块联邦可以使得跨应用间真正做到模块共享

### 支持崭新的 Web 平台特性

1. 支持崭新的 Web 平台特性 内置资源模块支持，支持`import`和`URL`方式，后者是新方式，举个例子

```js
// old
import url from "./image.png";
// new opts
new URL("./image.png", import.meta.url);
```

2. 原生 Worker 支持
   `new Worker(new URL("./worker.js", import.meta.url))`

3. URI Webpack 5 支持在请求中处理协议

- 支持`data`
- 支持`file`：需要通过`new webpack.experiments.s schemesHttp(s)UriPlugin()`选择加入
- 支持`https`

4. 异步模块 支持异步模块——基于异步和 Promise 的模块，比如`import("lodash")`

5. 外部资源 增加了一些外部资源类型，比如 promise、import、module 和 script

### 支持全新的 Node.js 生态特性

1. 现在支持 package.json 中的 exports 和 imports 字段

2. 原生支持 Yarn PnP

### 开发体验

1. 经过优化的构建目标 Webpack 5 允许传递一个目标列表，并且支持目标的版本。为 webpack 提供它需要确定的所有信息：代码加载机制以及支持的语法

2. Stats 改进了统计测试格式的可读性和冗余性

3. 进度 对 ProgressPlugin 做了一些改进，它被 CLI 在参数 --progress 开启时使用，但也可以作为插件手动使用

4. 自动添加唯一命名 在 webpack 4 中，多个 webpack 运行时可能会在同一个 HTML 页面上发生冲突，因为它们使用同一个全局变量进行代码块加载。为了解决这个问题，需要为 output.jsonpFunction
   配置提供一个自定义的名称

5. 自动添加公共路径 Webpack 5 会在可能的情况下自动确定 output.publicPath。

6. Typescript 类型 Webpack 5 从源码中生成 typescript 类型，并通过 npm 包暴露它们

### 构建优化

1. 嵌套的 tree-shaking webpack 现在能够跟踪对导出的嵌套属性的访问。这可以改善重新导出命名空间对象时的 Tree Shaking

2. 内部模块 tree-shaking webpack 5 有一个新的选项 optimization.innerGraph，在生产模式下是默认启用的，它可以对模块中的标志进行分析，找出导出和引用之间的依赖关系。

3. CommonJs Tree Shaking 对 CommonJs 导出和 require() 调用时的导出使用分析

4. 副作用分析 在 package.json 中的 "sideEffects" 标志允许手动将模块标记为无副作用，这就允许在不使用时放弃它们。

webpack 5 也可以根据对源代码的静态分析，自动将模块标记为无副作用

5. 模块合并 模块合并也可以在每个运行时工作，允许每个运行时进行不同的合并

6. 改进代码生成

7. 改进 target 配置

8. 代码块拆分与模块大小

### 性能优化

1. 持久缓存 支持文件系统缓存。它是可选的，可以通过以下配置启用：

```js
module.exports = {
  cache: {
    // 1. 将缓存类型设置为文件系统
    type: "filesystem",

    buildDependencies: {
      // 2. 将你的 config 添加为 buildDependency，以便在改变 config 时获得缓存无效
      config: [__filename],

      // 3. 如果你有其他的东西被构建依赖，你可以在这里添加它们
      // 注意，webpack、加载器和所有从你的配置中引用的模块都会被自动添加
    },
  },
};
```

2. 编译器闲置和关闭 编译器现在需要在使用后关闭。编译器现在会进入和离开空闲状态，并且有这些状态的钩子。插件可能会使用这些钩子来做不重要的工作

在使用 Node.js API 时，一定要在完成工作后调用 Compiler.close。

3. 文件生成 所以 webpack 现在会检查输出目录中现有的文件，并将其内容与内存中的输出文件进行比较。只有当文件被改变时，它才会写入文件。 这只在第一次构建时进行。任何增量构建都会在运行中的 webpack
   进程中生成新的资产时写入文件

### 长期未解决的问题

1. 单一文件目标的代码分割 只允许启动单个文件的目标（如 node、WebWorker、electron main）现在支持运行时自动加载引导所需的依赖代码片段

这允许对这些目标使用 `chunks: "all"` 和 `optimization.runtimeChunk`

2. 解析器更新

### 未来计划

1. 实验特性

- 异步 WebAssembly
- 顶层的 Await

2. 最小 Node.js 版本 最低支持的 Node.js 版本从 6 增加到 10.13.0(LTS)

### 配置变更

### 重大内部变更

1. 缓存插件更新 增加了一个带有插件接口的 Cache 类。该类可用于写入和读取缓存。根据配置的不同，不同的插件可以为缓存添加功能。MemoryCachePlugin 增加了内存缓存功能。FileCachePlugin
   增加了持久性（文件系统）缓存

2. 从数组到集合(Set)

3. 模块热替换

4. 全新的监听 它之前使用的是 chokidar 和原生依赖 fsevents（仅在 macOS 上）。现在它在只基于原生的 Node.js 中的 fs

## 重点

- 缓存
- Tree Shaking
- 拥抱 Node.js
