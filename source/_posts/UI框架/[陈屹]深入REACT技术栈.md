---
title: 深入REACT技术栈 阅读笔记

tag: React

category:
  - UI框架

author: matthew

date: 2021-02-03
---

## React 事件系统

### 描述性定义

1. React 基于 Virtual DOM 实现的一个 SyntheticEvent （合成事件）层
2. 比如我们在 JSX 里给一个按钮添加的 onClick 处理方法就是 React 合成事件
3. 它完全符合 W3C 标准，不存在浏览器兼容问题
4. 与原生浏览器事件接口一样，同样支持事件冒泡
5. 所有事件都自动绑定到最外层，如果需要访问原生事件对象，可以使用 nativeEvent 属性

#### 合成事件如何绑定？

1. 在 React 组件中，每个方法的上下文都会指向该组件的实例，即自动绑定 this 为当前组件
2. 在使用 ES6 class 或者 函数组件的时候，需要手动绑定
3. 手动绑定方法有：bind 绑定，constructor 绑定，箭头函数这三种

#### 合成事件代理机制

1. React 并不会把事件处理函数直接绑定到真实节点上，而是绑定在最外层，使用一个统一的事件监听器，这个事件监听器维持了一个映射来保存所有组件内部的事件监听和处理函数
2. 当组件挂载或者卸载时，只是在这个统一的事件监听器上插入或者删除一些对象
3. 当组件事件发生时，首先被这个统一的事件监听器处理，然后再映射里找到真正的事件处理函数并调用

#### 可以在 React 中使用原生事件吗？

1. 可以的。虽然 React 事件好处多多，但是并没有实现所有的应用场景，比如 resize 事件
2. 在 React 中有三种方法使用原生事件

```jsx
class Event extends Component {
  btnRef = createRef();

  handleClick(e) {
    console.log("handleClick", e.target);
  }

  componentDidMount() {
    // 原生事件refs
    // this.refs.button.addEventListener('click', this.handleClick);
    // callback
    // this.btn.addEventListener('click', this.handleClick);
    // createRef
    this.btnRef.current.addEventListener("click", this.handleClick);
  }

  render() {
    return (
      <div>
        {/* <button type="button" ref="button">Test button</button> */}
        {/* <button type="button" ref={obj => this.btn = obj}>Test button</button> */}
        <button type="button" ref={this.btnRef}>
          Test button
        </button>
      </div>
    );
  }
}
```

#### 对比 React 合成事件和 JavaScript 原生事件

1. 事件传播和阻止事件传播

- 浏览器原生 DOM 事件传播分三个阶段：事件捕获阶段，目标处理阶段，事件冒泡阶段
- 但是事件捕获阶段兼容性并不好，所以 React 并没有实现事件捕获阶段，只实现了事件冒泡
- 阻止原生事件需要使用`e.preventDefault()`和`e.cancelBubble = true`，React 事件系统中只需使用`e.preventDefault()`即可

2. 事件类型 React 合成事件的事件类型是原生事件类型的子集
3. 事件绑定方式

- 原生事件绑定有三种

  ```js

  ```

// 直接在 DOM 元素中绑定：
<button onclick="alert(1);">Test</button>
// 在 JavaScript 中，通过为元素的事件属性赋值的方式实现绑定： el.onclick = e => { console.log(e); } // 通过事件监听函数来实现绑定： el.addEventListener('
click', () => { }, false); el.attachEvent('onclick', () => { });

````

- 相比而言，React 合成事件的绑定方式则简单得多：`<button onClick={this.handleClick}>Test</button> `

4. 事件对象 原生DOM事件对象在W3C标准和IE标准下实现有所不同，儿在React合成事件系统中，不存在兼容问题

## CSS Modules是什么

1. CSS模块化解决方案主要有两类：Inline和MOdule
2. CSS模块化过程中遇到了如下问题：
  - 全局污染
  - 命名混乱
  - 依赖管理不彻底
  - 无法共享变量
  - 代码压缩不彻底
3. CSS模块化方案实践发展：SMACSS->OOCSS->BEM
4. webpack的css-loader支持模块化CSS
5. CSS Modules 结合 React 实践

```js
/* dialog.css */
.
root
{
}
.
confirm
{
}
.
disabledConfirm
{
}

/* dialog.js */
import React, {Component} from 'react';
import classNames from 'classnames';
import CSSModules from 'react-css-modules';
import styles from './dialog.css';

class Dialog extends Component {
  render() {
      const cx = classNames({
          confirm: !this.state.disabled,
          disabledConfirm: this.state.disabled,
      });
      return (
          <div styleName="root">
              <a styleName={cx}>Confirm</a>
          </div>
      );
  }
}

export default CSSModules(Dialog, styles);
````

6. 这篇文章写的差不多：https://zhuanlan.zhihu.com/p/100133524

## 组件间通信

#### 父组件到子组件

props

#### 子组件到父组件

1. 回调函数
2. 自定义事件机制

#### 无嵌套关系的组件间

自定义事件机制，比如 EventEmitter

#### 跨级组件通信

context

## 组件间抽象

#### minxin

不推荐使用：https://github.com/tcatche/tcatche.github.io/issues/53

#### decorator

core-decorators stage-2

#### 高阶组件

1. 实现高阶组件两种方法：属性代理和反向继承
2. 具体参考：[高阶组件及其应用场景](https://github.com/Matthrews/rfzhu_blogs/blob/main/技术/React/React中的高阶组件及其应用场景.md)

## 自动化测试

[Jest](https://jestjs.io/)
[Enzyme](https://enzymejs.github.io/enzyme/)
[GitHub Actions](https://github.com/Matthrews/edb/actions/new)
