---
title: JS面试系列

tag: 前端面试

author: matthew

date: 20210203

---

## JS面试系列

### defer和asnyc

参考：[带你玩转prefetch, preload, dns-prefetch，defer和async - SegmentFault 思否](https://segmentfault.com/a/1190000011577248)

```js
<script type="module" src="foo.js"></script>
<!-- 等同于 -->
<script type="module" src="foo.js" defer></script>

```

[阮一峰的ES6---Module的加载实现_慕课手记 (imooc.com)](https://www.imooc.com/article/20630?block_id=tuijian_wz)

[webpack解惑：require的五种用法 - 吕大豹 - 博客园 (cnblogs.com)](https://www.cnblogs.com/lvdabao/p/5953884.html)

## **5、介绍下 Set、Map的区别？**

> 应用场景Set用于数据重组，Map用于数据储存Set：　
> （1）成员不能重复
> （2）只有键值没有键名，类似数组
> （3）可以遍历，方法有add, delete,has
> Map:
> （1）本质上是健值对的集合，类似集合
> （2）可以遍历，可以跟各种数据格式转换

[ES6常见面试题总结 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/102442557)

[深入理解 ES6 模块机制 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/33843378)

[JS中的「import」和「require 」 - 简书 (jianshu.com)](https://www.jianshu.com/p/f1e54dde30c8)

## 前端性能优化 24 条建议

https://zhuanlan.zhihu.com/p/121056616

https://zhuanlan.zhihu.com/p/26559480

https://zhuanlan.zhihu.com/p/147178478

[web性能优化的15条实用技巧 (qq.com)](https://mp.weixin.qq.com/s?__biz=MzU2Mzk1NzkwOA%3D%3D&chksm=fc531be6cb2492f04475bac0fecbd1a9f9781ca67f35bd30c320964b24a8cbc2ca3d7bbd5345&idx=1&mid=2247483933&scene=21&sn=c2729ef1fd4a28f4707bb923a5ffae79#wechat_redirect)

[如何理解es6中的import是静态编译执行的？（一说是编译期执行的）？ - 知乎 (zhihu.com)](https://www.zhihu.com/question/265631914)

[CSS预加载Preload - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/32561606)

Preload是为处理当前页面所生，Prefetch是告诉浏览器加载下一页面可能会用到的资源，优先级自然在当前页面之后

[TypeScript 和 JavaScript 究竟哪个更好？_Web_hls33的博客-CSDN博客](https://blog.csdn.net/weixin_42554191/article/details/103889161#:~:text=二者的主要区别为：,TypeScript
是静态类型，js是动态类型（详见强类型、弱类型、静态类型、动态类型的区别）。)

[Telemetry needs to be disabled without voodoo · Issue #10713 · vercel/next.js (github.com)](https://github.com/vercel/next.js/issues/10713)

[如何评价首发于 2020 年 1 月下旬的 JavaScript 依赖管理器 Yarn 2？ - 知乎 (zhihu.com)](https://www.zhihu.com/question/367871981)