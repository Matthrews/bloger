---
date: 2021-04-12

title: Electron Forge 入门

source: https://www.electronforge.io/

translated by: matthew

category:
  - Electron

description: Electron Forge是一个集创建，发布和安装现代 Electron 应用于一身的完备工具链。
---

# Electron Forge 入门

> Electron Forge 是一个集创建，发布和安装现代 Electron 应用于一身的完备工具链。

---

## 基本使用

首先初始化一个项目

```bash
# with yarn
yarn create electron-app my-app
# with npm
npx create-electron-app my-app
```

现在你的 `my-app`文件夹下面就有了一个超级小的 Electron 应用模板。进入 `my-app`文件夹，启动项目，你就可以开始开发了。

```bash
# with yarn
cd my-app
yarn start
# with npm
cd my-app
npm start
```

## 构建发行版

现在你可以把你的应用和全世界分享。当你运行`make` 脚本时， Electron Forge
会生成对应平台的发行版。了解你可以构建的发行版类型，请参考[Makers ](https://www.electronforge.io/config/makers)文档。

```bash
# with yarn
yarn make
# with npm
npm run make
```

## 高阶使用

有了一个入门级的应用，你就可以构建发行版了。了解更多高阶功能，可以参考：

[Publishers](https://www.electronforge.io/config/publishers)

[Debugging your app](https://www.electronforge.io/advanced/debugging)

[Webpack support](https://www.electronforge.io/config/plugins/webpack)

[Writing your own makers, publishers and plugins](https://www.electronforge.io/advanced/extending-electron-forge)
