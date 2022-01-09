---
title: 自动化测试
date: 2020-12-08 22:12:00
tags:
  - 测试框架
  - Cypress

catrgory:
  - 测试相关
---

#### 为什么需要自动化测试

#### 为什么选择 Cypress

##### Pupper

##### Seleniuum

##### Cypress 特点

- Pro：界面美观友好
- Pro：支持模拟手机
- Pro：每一步操作截图
- Pro：全程录屏
- Pro：支持 debug，随时暂停
- Pro：自动等待 UI 更新，减少异步代码

#### Cypress 怎么做

- 关键在快速安装 cypress
- vscode
  - 打开 cypress 测试窗口：`node_modules\.bin\cypress open`
- webstorm
  - 安装 cypress 插件，然后会识别测试文件，可以单个或者多个测试
  - 安装 reporter：`npm install -D cypress-intellij-reporter`
  - 命令行打开浏览器窗口测试：`cypress run --no-exit --headed --spec "**/*.spec.js"`
  - 全局安装了的话直接：`cypress open`
