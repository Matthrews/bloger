---
title: 图解 Google V8

tag: V8

author: matthew

date: 20210625

---

# 图解 Google V8

## 概论

### 如何学习谷歌高性能 JavaScript 引擎V8？

#### 什么是 V8？
V8 是 JavaScript 虚拟机的一种。我们可以简单地把 JavaScript 虚拟机 理解成翻译程序，将人类能够理解的**编程语言  JavaScript** ，翻译成为机器能够理解的**机器语言**。如下图所示：
![JavaScript引擎](https://static001.geekbang.org/resource/image/8a/a1/8a40fd003baa9be179fe2e55a1be5fa1.jpg)

上图中，中间的“黑盒”就是 JavaScript 引擎 V8。目前市面上有很多种 JavaScript 引擎，诸如 SpiderMonkey、V8、JavaScriptCore 等。而由谷歌开发的开源项目 V8 是当下使用最广泛的 JavaScript 虚拟机，全球有超过 25 亿台安卓设备，而这些设备中都使用了 Chrome 浏览器，所以我们写的 JavaScript 应用，大都跑在 V8 上。

V8 之所以拥有如此庞大的生态圈，也和它许多革命性的设计是分不开的。

在 V8 出现之前，所有的 JavaScript 虚拟机所采用的都是解释执行的方式，这是 JavaScript 执行速度过慢的一个主要原因。而 V8 率先引入了即时编译（JIT）的双轮驱动的设计，这是一种权衡策略，混合编译执行和解释执行这两种手段，给 JavaScript 的执行速度带来了极大的提升。

可以说，V8 的出现，将 JavaScript 虚拟机技术推向了一个全新的高度。

即便 V8 具有诸多优点，但我相信对于大部分同学来说，V8 虚拟机还只是一个黑盒，我们将一段代码丢给这个黑盒，它便会返回结果，并没有深入了解过它的工作原理。

如果只是单纯使用 JavaScript 和调用 Web  API，并不了解虚拟机内部是怎样工作的，在项目中遇到的很多问题很可能找不到解决的途径。比如，有时项目的占用内存过高，或者页面响应速度过慢，又或者使用 Node.js 的时候导致任务被阻塞等问题，都与 V8 的基本运行机制有关。如果你熟悉 V8 的工作机制，就会有系统性的思路来解决这些问题。

另外，V8 的主要功能，就是结合 JavaScript 语言的特性和本质来编译执行它。通过深入地学习 V8，你对 JavaScript 语言本质和设计思想会有很直观的感受。这些设计思想像是更加高级的工具，你掌握了它，就可以提升你的语言使用和架构设计水平。