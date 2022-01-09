---
data: 2021-04-11

title: How to build an Electron app using Create React App and Electron Builder

keywords: Electron, React - Create React App, Rescripts, Electron Builder，Electron 入门

translated by: matthew

category:
  - Electron

author: Randy Findley

source: https://finbits.io/blog/electron-create-react-app-electron-builder/
---

# 如何使用 Create React App 和 Electron Builder 构建 Electron 应用

![Electron-React](https://finbits.io/images/blog/electron-react.jpg)

最近我决定做一个桌面应用来下载和保存我的 Google Photos。因为我曾被丢失所有照片折磨够了。虽然 Google 本身有一些选项可以配置实现，但是实践下来貌似都有问题。

如果你想使用这款应用，你可以在这里下载 [OSX](https://finbits.io/downloads/photosdownloader-0.1.1.dmg)
或者 [WIN](https://finbits.io/downloads/photosdownloader-0.1.1.exe)。

技术栈上我选择 Electron 和 React,因为这使得我的工作很有趣,而且最终效果不错.

在这篇博客中,我会分享一些配置和我踩过的坑。

我刚开始开发的时候，我参考了这两篇博客 [1](https://medium.com/@kitze/️-from-react-to-an-electron-app-ready-for-production-a0468ecb1da3)
和 [2](https://medium.freecodecamp.org/building-an-electron-application-with-create-react-app-97945861647c)，感谢。

好的，结下来我们一起来学学如何构建一个使用 Create React App 开发的 Electron 应用，然后在使用 Electron Builder 进行打包分发。

我们先来了解一下整个技术栈然后再开始。如果你想跳过，可以直接看[源码](https://github.com/rgfindl/electron-cra-boilerplate)

## 技术栈

- [Electron](https://electronjs.org/)
- [React - Create React App](https://github.com/facebook/create-react-app)
- [Rescripts](https://github.com/harrysolovay/rescripts)
- [Electron Builder](https://github.com/electron-userland/electron-builder)

[Electron](https://electronjs.org/) 是一个使用 JavaScript, HTML, and CSS 等 Web 技术创建原生应用的框架。在我们的案例中，我们使用的是 React

[React](https://reactjs.org/) 是构建用户界面的 JavaScript 库......

为了让我们的 React 项目配置更容易，我们使用[Create React App](https://github.com/facebook/create-react-app)快速创建 React 项目。Create React
App (CRA)很棒，因为它节省了时间，并消除了配置难题。

Create React App 是一个可以让你在构建应用时获得先发优势的工具，由 Facebook 开发者创建。它节省了安装和配置的时间。你只需要简单地运行一个命令，Create React App 就会帮你安装好用于启动项目的工具

[Rescripts](https://github.com/harrysolovay/rescripts) 可以让你无需`eject`也可以自定义 CRA。

弹出(`eject`) CRA 是你应该避免的操作，因为这将意味着你再也不会受益于 CRA 的未来更新。

[Electron Builder](https://github.com/electron-userland/electron-builder) 用于打包我们的桌面应用进行分发。

## 项目搭建

使用 Create React App 快速创建项目

```shell
npx create-react-app my-app
cd my-app
```

安装 Electron

```shell
yarn add electron electron-builder --dev
```

和一些我们需要的工具

```shell
yarn add wait-on concurrently --dev
yarn add electron-is-dev
```

创建`public/electron.js`文件，内容如下：

```js
const electron = require("electron");
const app = electron.app;
const BrowserWindow = electron.BrowserWindow;

const path = require("path");
const isDev = require("electron-is-dev");

let mainWindow;

function createWindow() {
  mainWindow = new BrowserWindow({ width: 900, height: 680 });
  mainWindow.loadURL(
    isDev
      ? "http://localhost:3000"
      : `file://${path.join(__dirname, "../build/index.html")}`
  );
  if (isDev) {
    // Open the DevTools.
    //BrowserWindow.addDevToolsExtension('<location to your react chrome extension>');
    mainWindow.webContents.openDevTools();
  }
  mainWindow.on("closed", () => (mainWindow = null));
}

app.on("ready", createWindow);

app.on("window-all-closed", () => {
  if (process.platform !== "darwin") {
    app.quit();
  }
});

app.on("activate", () => {
  if (mainWindow === null) {
    createWindow();
  }
});
```

在`package.json`文件的`scripts` 项添加如下命令：

```shell
"electron-dev": "concurrently \"BROWSER=none yarn start\" \"wait-on http://localhost:3000 && electron .\""
```

这个`script`将会在启动 Electron 之前 等待应用在`localhost:3000`运行起来。

在`package.json`文件的`main` 项添加如下内容：

```json
"main": "public/electron.js",
```

现在，你的 `package.json`应该是这样的

```json
{
  "name": "my-app",
  "version": "0.1.0",
  "private": true,
  "dependencies": {
    "electron-is-dev": "^1.0.1",
    "react": "^16.8.3",
    "react-dom": "^16.8.3",
    "react-scripts": "2.1.5"
  },
  "main": "public/electron.js",
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject",
    "electron-dev": "concurrently \"BROWSER=none yarn start\" \"wait-on http://localhost:3000 && electron .\""
  },
  "eslintConfig": {
    "extends": "react-app"
  },
  "browserslist": [">0.2%", "not dead", "not ie <= 11", "not op_mini all"],
  "devDependencies": {
    "concurrently": "^4.1.0",
    "electron": "^4.0.6",
    "electron-builder": "^20.38.5",
    "wait-on": "^3.2.0"
  }
}
```

这时，你可以通过以下命令以开发模式启动你的项目

```shell
yarn electron-dev
```

你应该会看到下图这样
![electron-boilerplate](https://finbits.io/images/blog/electron-boilerplate.png)
如果你看到了，恭喜你可以进行接下来的开发了。🤩

Now, if you need to access the `fs` module like I did, you’ll quickly hit the `Module not found` error.
See [here](https://stackoverflow.com/questions/35428639/how-can-i-use-fs-in-react-with-electron). 如果你需要使用`fs`
模块，你可能很快就会遇到`Module not found`
这样的错误。[参考](https://stackoverflow.com/questions/35428639/how-can-i-use-fs-in-react-with-electron)

要解决这个问题，我们需要设置`Webpack`的`target`为`electron-renderer`。但是哦我们不想弹出(eject) CRA
去做这件事。所以我们使用[Rescripts](https://github.com/harrysolovay/rescripts)。下面教你如何使用。

首先，安装`Rescripts`

```shell
yarn add @rescripts/cli @rescripts/rescript-env --dev
```

然后，将 `package.json`里面的`scripts` 项从

```shell
"start": "react-scripts start",
"build": "react-scripts build",
"test": "react-scripts test",
```

改为

```shell
"start": "rescripts start",
"build": "rescripts build",
"test": "rescripts test",
```

现在新建 `.rescriptsrc.js`文件，内容如下：

```js
module.exports = [require.resolve("./.webpack.config.js")];
```

最后新建`.webpack.config.js`，内容如下：

```js
// define child rescript
module.exports = (config) => {
  config.target = "electron-renderer";
  return config;
};
```

现在就可以无需担心地使用`fs` 模块了

## 开始打包

非常棒，现在我们可以准备打包我们的项目了

首先，安装 Electron Builder 和 Typescript

```shell
yarn add electron-builder typescript --dev
```

默认情况下 CRA 会使用绝对路径构建修改`index.html`文件。这会使得 Electron 加载出错。这需要一些配置来修正。

将 `package.json`中的`homepage`项的值设为

```json
"homepage": "./",
```

接下来我们添加给`package.json`的`scripts` 项添加`electron-pack`命令用于打包构建(build)结果

```json
"postinstall": "electron-builder install-app-deps",
"preelectron-pack": "yarn build",
"electron-pack": "build -mw"
```

`"postinstall": "electron-builder install-app-deps"` 将会确保你的原生依赖总是会匹配 `electron` 的版本

`"preelectron-pack": "yarn build"` 用于构建(build) CRA 项目

`"electron-pack": "build -mw"` 用于打包 `Mac (m) `和 `Windows (w)`应用

在我们可以运行这个命令之前在`package.json`里给 Electron Builder 配置如下内容：

```json
"author": {
"name": "Your Name",
"email": "your.email@domain.com",
"url": "https://your-website.com"
},
"build": {
"appId": "com.my-website.my-app",
"productName": "MyApp",
"copyright": "Copyright © 2019 ${author}",
"mac": {
"category": "public.app-category.utilities"
},
"files": [
"build/**/*",
"node_modules/**/*"
],
"directories": {
"buildResources": "assets"
}
}
```

你可以在在 [这里](https://www.electron.build/configuration/configuration)查看在所有的 Electron Builder 配置项

你可能还会创建`assets` 文件夹以存放应用的图标(icon)。格式可以参考 [这里](https://www.electron.build/icons)

最终整个 `package.json` 内容如下：

```json
{
  "name": "my-app",
  "description": "Electron + Create React App + Electron Builder",
  "version": "0.1.0",
  "private": true,
  "author": {
    "name": "Your Name",
    "email": "your.email@domain.com",
    "url": "https://your-website.com"
  },
  "build": {
    "appId": "com.my-website.my-app",
    "productName": "MyApp",
    "copyright": "Copyright © 2019 ${author}",
    "mac": {
      "category": "public.app-category.utilities"
    },
    "files": ["build/**/*", "node_modules/**/*"],
    "directories": {
      "buildResources": "assets"
    }
  },
  "dependencies": {
    "electron-is-dev": "^1.0.1",
    "react": "^16.8.3",
    "react-dom": "^16.8.3",
    "react-scripts": "2.1.5"
  },
  "homepage": "./",
  "main": "public/electron.js",
  "scripts": {
    "start": "rescripts start",
    "build": "rescripts build",
    "test": "rescripts test",
    "eject": "react-scripts eject",
    "electron-dev": "concurrently \"BROWSER=none yarn start\" \"wait-on http://localhost:3000 && electron .\"",
    "postinstall": "electron-builder install-app-deps",
    "preelectron-pack": "yarn build",
    "electron-pack": "build -mw"
  },
  "eslintConfig": {
    "extends": "react-app"
  },
  "browserslist": [">0.2%", "not dead", "not ie <= 11", "not op_mini all"],
  "devDependencies": {
    "@rescripts/cli": "^0.0.10",
    "@rescripts/rescript-env": "^0.0.5",
    "concurrently": "^4.1.0",
    "electron": "^4.0.6",
    "electron-builder": "^20.38.5",
    "typescript": "^3.3.3333",
    "wait-on": "^3.2.0"
  }
}
```

现在我们准备好打包应用。运行下面的命令：

```shell
yarn electron-pack
```

最终你会在 `dist` 目录看到打包结果

好了，该你上手实践了。

源码在 [这里](https://github.com/rgfindl/electron-cra-boilerplate)

希望这这篇博客对你有所帮助。 Bye!
