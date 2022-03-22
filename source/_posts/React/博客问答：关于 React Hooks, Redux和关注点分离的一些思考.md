---

title: 博客问答：关于 React Hooks, Redux和关注点分离的一些思考

date: 2021-02-14

tag: javascript, react, redux, hooks, tradeoffs

author: Redux maintainer: [Mark Erikson](https://github.com/markerikson)

source: https://blog.isquaredsoftware.com/2019/07/blogged-answers-thoughts-on-hooks/

---

## [译]博客问答：关于 React Hooks, Redux 和关注点分离的一些思考

### 简介

最近看到很多关于 React Hooks 的使用问题，特别是 React-Redux Hooks 的使用。一下是一些最常见的问题

- 如何测试使用 Redux 的组件？
- hooks 和 context 的使用是否有利于关注点分离？
- 组件里面写一堆 hooks and callbacks 会不会导致混乱？

实际上我对 Hooks 带来的权衡有很多想法。
