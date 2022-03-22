---
date: 20210410

author: matthew

keywords: 函数式，高阶组件，属性代理，反向继承，装饰器模式，受控组件

---

[TOC]

## React 中的高阶组件及其应用场景

### 什么是高阶组件

如果一个函数 接受一个或多个组件作为参数并且返回一个组件 就可称之为 高阶组件 下面就是一个简单的高阶组件：

```js
const HigherOrderComponent = (WrappedComponent) => <WrappedComponent/>
```

### React 中的高阶组件

React 中的高阶组件主要有两种形式：属性代理 和 反向继承

#### 属性代理（Props Proxy）

最简单的属性代理实现：

```js
// 无状态
function HigherOrderComponent(WrappedComponent) {
    return props => <WrappedComponent {...props} />;
}

// 有状态
function HigherOrderComponent(WrappedComponent) {
    return class extends React.Component {
        render() {
            return <WrappedComponent {...this.props} />;
        }
    };
}
```

可以发现，属性代理其实就是 一个函数接受一个`WrappedComponent `组件作为参数传入，并返回一个继承了`React.Component`组件的类，且在该类的 render()
方法中返回被传入的`WrappedComponent `组件。

那我们可以利用属性代理类型的高阶组件做一些什么呢？

因为属性代理类型的高阶组件返回返回继承自`React.Component`的标准组件。 因此，在 React 标准组件中可以做什么，高阶组件也可以做，比如：

- 操作 props

```js
import React from 'react'

const App = (props) => {
    const {userInfo} = props;
    return (
        <div className="App">
            <h1>Learn React</h1>
            <p>
                {JSON.stringify(userInfo, null, 2)}
            </p>
        </div>
    );
}

/**
 * 在高阶组件里操作props
 * @param WrappedComponent
 * @constructor
 */
const HigherOrderComponent = (WrappedComponent) => {
    return class extends React.Component {
        constructor(props) {
            super(props);
            this.state = {
                userInfo: null
            }
        }

        componentDidMount() {
            fetch('https://jsonplaceholder.typicode.com/users/1')
                .then(response => response.json())
                .then(data => this.setState({userInfo: data}))
        }

        render() {
            const {userInfo} = this.state;
            const newProps = {
                ...this.props,
                userInfo
            }
            return (
                <WrappedComponent {...newProps} />
            );
        }
    }
}

export default HigherOrderComponent(App);
```

- 抽离 state

```js
import React from 'react'

const App = (props) => {
    const {name: {value, onChange}} = props;
    return (
        <div className="App">
            <h1>Learn React</h1>
            <input type="text" value={value} onChange={(e) => onChange(e.target.value)}/>
        </div>
    );
}

/**
 * 在高阶组件里抽离 state
 * @param WrappedComponent
 * @constructor
 */
const withOnChange = (WrappedComponent) => {
    return class extends React.Component {
        constructor(props) {
            super(props);
            this.state = {
                name: ''
            }
        }

        onChange = (newName) => {
            console.log('onChange', newName)
            this.setState({name: newName})
        }


        render() {
            const {name} = this.state;
            const newProps = {
                name: {
                    value: name,
                    onChange: this.onChange
                }
            }
            return (
                <WrappedComponent {...newProps} />
            );
        }
    }
}


export default withOnChange(App);
```

- 通过 ref 访问到组件实例 有时会有需要访问 DOM element （使用第三方 DOM 操作库）的时候就会用到组件的 ref 属性。它只能声明在 Class 类型的组件上，而无法声明在函数（无状态）类型的组件上。

ref 的值可以是字符串（不推荐使用）也可以是一个回调函数，如果是回调函数的话，它的执行时机是：

组件被挂载后（`componentDidMount`），回调函数立即执行，回调函数的参数为该组件的实例。 组件被卸载（`componentDidUnmount`）或者原有的 ref
属性本身发生变化的时候，此时回调函数也会立即执行，且回调函数的参数为 null。 如何在 高阶组件 中获取到`WrappedComponent `组件的实例呢？答案就是可以通过 `WrappedComponent `组件的 ref
属性，该属性会在组件 `componentDidMount` 的时候执行 ref 的回调函数并传入该组件的实例：

```js
import React, {Component, createRef} from 'react'

class App extends Component {
    constructor(props) {
        super(props);
        this.state = {
            value: ''
        }
    }

    onChange = (e) => {
        this.setState({value: e.target.value})
    }

    componentDidMount() {
        console.log('App3 componentDidMount')
    }

    render() {
        const {value} = this.state
        return (
            <div className="App">
                <h1>Learn React</h1>
                <input type="text" value={value} onChange={this.onChange}/>
            </div>
        );
    }
}

/**
 * 在高阶组件里通过 ref 访问到组件实例
 * @param WrappedComponent
 * @constructor
 */
const HigherOrderComponent = (WrappedComponent) => {
    return class extends React.Component {

        componentDidMount() {
            console.log('HigherOrderComponent componentDidMount', this.instance.state, this.instance.onChange)
        }

        render() {
            return (
                // 注意：不能在函数组件上使用 ref 属性，因为无状态组件没有实例。
                <WrappedComponent {...this.props} ref={(obj) => this.instance = obj}/>
            );
        }
    }
}

export default HigherOrderComponent(App);
```

- 用其他元素包裹 `WrappedComponent`
  给  `WrappedComponent` 组件包一层背景色为` #f80 `的 div 元素：

```js
function withBackgroundColor(WrappedComponent) {
    return class extends React.Component {
        render() {
            return (
                <div style={{backgroundColor: '#f80'}}>
                    <WrappedComponent {...this.props} />
                </div>
            );
        }
    };
}
```

#### 反向继承（Inheritance Inversion）

```js
function HigherOrderComponent(WrappedComponent) {
    return class extends WrappedComponent {
        render() {
            return super.render();
        }
    };
}
```

反向继承其实就是 一个函数接受一个 `WrappedComponent` 组件作为参数传入，并返回一个继承了该传入 `WrappedComponent` 组件的类，且在该类的` render() `
方法中返回 `super.render() `方法

会发现其属性代理和反向继承的实现有些类似的地方，都是返回一个继承了某个父类的子类，只不过属性代理中继承的是`React.Component`，反向继承中继承的是传入的组件 `WrappedComponent`

那么，反向继承可以做什么？

- 操作 state 高阶组件中可以读取、编辑和删除`WrappedComponent` 组件实例中的 state。甚至可以增加更多的 state 项，但是这么做可能会导致 state 难以维护及管理

```js
function withLogging(WrappedComponent) {
    return class extends WrappedComponent {
        render() {
            return (
                <div>
                    <h2>Debugger Component</h2>
                    <p>state:</p>
                    <pre>{JSON.stringify(this.state, null, 2)}</pre>
                    <p>props:</p>
                    <pre>{JSON.stringify(this.props, null, 2)}</pre>
                    {super.render()}
                </div>
            );
        }
    };
}
```

- 渲染劫持（`Render Highjacking`） 通过渲染劫持，我们可以：
    + 有条件地展示元素树（element tree）
    + 操作由 render() 输出的 React 元素树
    + 在任何由 render() 输出的 React 元素中操作 props
    + 用其他元素包裹传入的组件`WrappedComponent`（同**属性代理**）

### 高阶组件存在的问题

#### 静态方法丢失

```js
 因为原始组件被包裹于一个容器组件内，也就意味着新组件会没有原始组件的任何静态方法：
// 定义静态方法
WrappedComponent.staticMethod = function () {
}
// 使用高阶组件
const EnhancedComponent = HigherOrderComponent(WrappedComponent);
// 增强型组件没有静态方法
typeof EnhancedComponent.staticMethod === 'undefined' // true
所以必须将静态方法做拷贝：

function HigherOrderComponent(WrappedComponent) {
    class Enhance extends React.Component {
    }

    // 必须得知道要拷贝的方法
    Enhance.staticMethod = WrappedComponent.staticMethod;
    return Enhance;
}

但是这么做的一个缺点就是必须知道要拷贝的方法是什么，不过
React
社区实现了一个库
hoist - non - react - statics
来自动处理，它会
自动拷贝所有非
React
的静态方法：

import hoistNonReactStatic from 'hoist-non-react-statics';

function HigherOrderComponent(WrappedComponent) {
    class Enhance extends React.Component {
    }

    hoistNonReactStatic(Enhance, WrappedComponent);
    return Enhance;
}
```

#### refs 属性不能透传

一般来说高阶组件可以传递所有的 props 给包裹的组件`WrappedComponent`，但是有一种属性不能传递，它就是 ref。与其他属性不同的地方在于 React 对其进行了特殊的处理。

如果你向一个由高阶组件创建的组件的元素添加 ref 引用，那么 ref 指向的是最外层容器组件实例的，而不是被包裹的 `WrappedComponent`组件。

不过，React 为我们提供了一个名为`React.forwardRef`的 API 来解决这一问题：

```js
import React from 'react'

class App extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            data: []
        }
    }


    render() {
        return (
            <div className="App">
                <h1>Learn React</h1>
                <p>
                    {JSON.stringify(this.state, null, 2)}
                    {JSON.stringify(this.props, null, 2)}
                </p>
            </div>
        );
    }
}


function withLogging(WrappedComponent) {
    class Enhance extends WrappedComponent {
        UNSAFE_componentWillReceiveProps(nextProps) {
            console.log('Current props', this.props);
            console.log('Next props', nextProps);
        }

        render() {
            const {forwardedRef, ...rest} = this.props;
            // 把 forwardedRef 赋值给 WrappedComponent 的 ref
            return <WrappedComponent {...rest} ref={forwardedRef}/>;
        }
    }

    // React.forwardRef 方法会传入 props 和 ref 两个参数给其回调函数
    // 所以这边的 ref 是由 React.forwardRef 提供的
    return React.forwardRef((props, ref) => {
        return <Enhance {...props} data={123} forwardRef={ref}/>
    });
}


export default withLogging(App)

```

#### 反向继承不能保证完整的子组件树被解析

React 组件有两种形式，分别是 class 类型和 function 类型

我们知道反向继承的渲染劫持可以控制`WrappedComponent `的渲染过程，也就是说这个过程中我们可以对 elements tree、state、props 或 render() 的结果做各种操作

如果渲染 elements tree 中包含了 function 类型的组件的话，这时候就不能操作组件的子组件了

### 高阶组件的约定

高阶组件带给我们极大方便的同时，我们也要遵循一些 约定：

#### props 保持一致

高阶组件在为子组件添加特性的同时，要尽量保持原有组件的 props 不受影响，也就是说传入的组件和返回的组件在 props 上尽量保持一致。

不要改变原始组件`WrappedComponent`
不要在高阶组件内以任何方式修改一个组件的原型，思考一下下面的代码：

```js
function withLogging(WrappedComponent) {
    WrappedComponent.prototype.componentWillReceiveProps = function (nextProps) {
        console.log('Current props', this.props);
        console.log('Next props', nextProps);
    }
    return WrappedComponent;
}

const EnhancedComponent = withLogging(SomeComponent);
```

会发现在高阶组件的内部对 `WrappedComponent`进行了修改，一旦对原组件进行了修改，那么就失去了组件复用的意义，所以请通过 纯函数（相同的输入总有相同的输出） 返回新的组件：

```js
function withLogging(WrappedComponent) {
    return class extends React.Component {
        componentWillReceiveProps() {
            console.log('Current props', this.props);
            console.log('Next props', nextProps);
        }

        render() {
            // 透传参数，不要修改它
            return <WrappedComponent {...this.props} />;
        }
    };
}
```

这样优化之后的`withLogging `是一个 纯函数，并不会修改 `WrappedComponent`组件，所以不需要担心有什么副作用，进而达到组件复用的目的。

#### 透传不相关 props 属性给被包裹的组件 `WrappedComponent`

```js
function HigherOrderComponent(WrappedComponent) {
    return class extends React.Component {
        render() {
            return <WrappedComponent name="name" {...this.props} />;
        }
    };
}
```

#### 不要在 render() 方法中使用高阶组件

```js
class SomeComponent extends React.Component {
    render() {
        // 调用高阶函数的时候每次都会返回一个新的组件
        const EnchancedComponent = enhance(WrappedComponent);
        // 每次 render 的时候，都会使子对象树完全被卸载和重新
        // 重新加载一个组件会引起原有组件的状态和它的所有子组件丢失
        return <EnchancedComponent/>;
    }
}
```

#### 使用 compose 组合高阶组件

```js
// 不要这么使用
const EnhancedComponent = withRouter(connect(commentSelector)(WrappedComponent))；
// 可以使用一个 compose 函数组合这些高阶组件
// lodash, redux, ramda 等第三方库都提供了类似 `compose` 功能的函数
const enhance = compose(withRouter, connect(commentSelector))；
const EnhancedComponent = enhance(WrappedComponent)；
```

因为按照 约定 实现的高阶组件其实就是一个纯函数，如果多个函数的参数一样，所以就可以通过 compose 方法来组合这些函数。

使用 compose 组合高阶组件使用，可以显著提高代码的可读性和逻辑的清晰度。 包装显示名字以便于调试 高阶组件创建的容器组件在 React Developer Tools 中的表现和其它的普通组件是一样的。

为了便于调试，可以选择一个显示名字，传达它是一个高阶组件的结果。

```js
const getDisplayName = WrappedComponent => WrappedComponent.displayName || WrappedComponent.name || 'Component';

function HigherOrderComponent(WrappedComponent) {
    class HigherOrderComponent extends React.Component {/* ... */
    }

    HigherOrderComponent.displayName = `HigherOrderComponent(${getDisplayName(WrappedComponent)})`;
    return HigherOrderComponent;
}
```

### 高阶组件的应用场景

#### 权限控制

利用高阶组件的**条件渲染** 特性可以对页面进行权限控制，权限控制一般分为两个维度：**页面级别** 和**页面元素级别**

```js
// HOC.js
import React from "react";

const defaultMessageMap = {
    'Admin': <div>您没有权限查看该页面，请联系管理员！</div>,
    'VIP': <div>您未购买该模块，请联系销售试用！</div>,
    '': null
}

/**
 * 权限控制
 * @param role
 */
const withAuth = role => (WrappedComponent) => {
    return class extends React.Component {
        state = {
            auth: false,
            role: ''
        }

        async UNSAFE_componentWillMount() {
            try {
                const {role: currentRole} = await fetch('//localhost:9090/userInfo').then(response => response.json())
                this.setState({
                    auth: currentRole === role,
                    role: currentRole
                });
            } catch {
                this.setState({
                    auth: false,
                });
            }

        }


        render() {
            if (this.state.auth) {
                return <WrappedComponent {...this.props} />;
            }
            return defaultMessageMap[this.state.role]
        }
    };
}


export default withAuth


// pages/PageA.js
import React, {Component} from 'react';
import withAuth from "../HOC";

/**
 * 管理员专用
 */
class PageA extends Component {
    render() {
        return (
            <div>
                <h2>管理员专用</h2>
            </div>
        );
    }
}

export default withAuth('Admin')(PageA);


// pages/PageB.js
import React, {Component} from 'react';
import withAuth from "../HOC";

/**
 * VIP专用
 */
class PageB extends Component {
    render() {
        return (
            <div>
                <h2>VIP专用</h2>
            </div>
        );
    }
}

export default withAuth('VIP')(PageB);

// pages/PageC.js
import React, {Component} from 'react';
import withAuth from "../HOC";

/**
 * 普通用户使用
 */
class PageC extends Component {
    render() {
        return (
            <div>
                <h2>普通用户使用</h2>
            </div>
        );
    }
}

export default PageC;

```

#### 组件渲染性能追踪

借助父组件子组件生命周期规则捕获子组件的生命周期，可以方便的对某个组件的渲染时间进行记录

```js
import React from 'react'

/**
 * 利用【反向继承】实现的一个高阶组件，功能是计算被包裹组件的渲染时间
 * @param WrappedComponent
 */
function withTiming(WrappedComponent) {
    return class extends WrappedComponent {
        constructor(props) {
            super(props);
            this.start = 0;
            this.end = 0;
        }

        UNSAFE_componentWillMount() {
            super.UNSAFE_componentWillMount && super.UNSAFE_componentWillMount();
            this.start = Date.now();
        }

        componentDidMount() {
            super.componentDidMount && super.componentDidMount();
            this.end = Date.now();
            console.log(`${WrappedComponent.name} 组件渲染时间为 ${this.end - this.start} ms`);
        }

        render() {
            return super.render();
        }
    };
}


class App extends React.Component {
    render() {
        return (
            <div className="App">
                <h1>Learn React</h1>
            </div>
        );
    }
}


export default withTiming(App)

```

#### 页面复用

假设我们有三个页面 `pageA`，` pageB`，`pageC` 分别渲染`users列表`，`posts列表`, `comments列表`，普通写法可能是下面这样：

```js
// pages/PageA.js
import React, {Component} from 'react';

/**
 * users列表
 */
class PageA extends Component {
    constructor(props) {
        super(props);
        this.state = {
            data: []
        }
    }

    async componentDidMount() {
        try {
            const data = await fetch('https://jsonplaceholder.typicode.com/users?id_lte=10').then(response => response.json())
            this.setState({data})

        } catch {
            this.setState({data: []})
        }
    }

    render() {
        const {data} = this.state;
        return (
            <div>
                <h2>users列表</h2>

                <pre> {JSON.stringify(data, null, 2)}</pre>
            </div>
        );
    }
}

export default PageA;

// pages/PageB.js
import React, {Component} from 'react';

/**
 * posts列表
 */
class PageB extends Component {
    constructor(props) {
        super(props);
        this.state = {
            data: []
        }
    }

    async componentDidMount() {
        try {
            const data = await fetch('https://jsonplaceholder.typicode.com/posts?id_lte=10').then(response => response.json())
            this.setState({data})

        } catch {
            this.setState({data: []})
        }
    }

    render() {
        const {data} = this.state;
        return (
            <div>
                <h2>posts列表</h2>

                <pre>{JSON.stringify(data, null, 2)}</pre>
            </div>
        );
    }
}

export default PageB;


// pages/PageC
import React, {Component} from 'react';

/**
 * comments 列表
 */
class PageC extends Component {
    constructor(props) {
        super(props);
        this.state = {
            data: []
        }
    }

    async componentDidMount() {
        try {
            const data = await fetch('https://jsonplaceholder.typicode.com/comments?id_lte=10').then(response => response.json())
            this.setState({data})

        } catch {
            this.setState({data: []})
        }
    }

    render() {
        const {data} = this.state;
        return (
            <div>
                <h2>comments列表</h2>

                <pre>{JSON.stringify(data, null, 2)}</pre>
            </div>
        );
    }
}

export default PageC;

```

页面少的时候可能没什么问题，但是假如随着业务的进展，在这个页面展示更多类型的数据，就会写很多的重复代码，所以我们需要重构一下

```js
// HOC.js
import React from "react";

const withFetching = fetchingFunc => WrappedComponent => {
    return class extends React.Component {
        state = {
            data: [],
        }

        async componentDidMount() {
            try {
                const data = await fetchingFunc();
                console.log('data', data)
                this.setState({
                    data,
                });

            } catch {
                this.setState({data: []})
            }
        }

        render() {
            return <WrappedComponent data={this.state.data} {...this.props} />;
        }
    }
}

export default withFetching

// pages/PageA.js
import React, {Component} from 'react';
import withFetching from "../HOC";

/**
 * users列表
 */
class PageA extends Component {
    // constructor(props) {
    //     super(props);
    //     this.state = {
    //         data: []
    //     }
    // }
    //
    // async componentDidMount() {
    //     try {
    //         const data = await fetch('https://jsonplaceholder.typicode.com/users?id_lte=10').then(response => response.json())
    //         this.setState({data})
    //
    //     } catch {
    //         this.setState({data: []})
    //     }
    // }

    render() {
        // const {data} = this.state;
        const {data} = this.props;
        return (
            <div>
                <h2>users列表</h2>

                <pre> {JSON.stringify(data, null, 2)}</pre>
            </div>
        );
    }
}

// export default PageA;
const fetchingFunc = () => fetch('https://jsonplaceholder.typicode.com/users?id_lte=10').then(response => response.json())

export default withFetching(fetchingFunc)(PageA);

// pages/PageB.js
import React, {Component} from 'react';
import withFetching from "../HOC";

/**
 * posts列表
 */
class PageB extends Component {

    render() {
        // const {data} = this.state;
        const {data} = this.props;
        return (
            <div>
                <h2>posts列表</h2>

                <pre>{JSON.stringify(data, null, 2)}</pre>
            </div>
        );
    }
}

// export default PageB;
const fetchingFunc = () => fetch('https://jsonplaceholder.typicode.com/posts?id_lte=10').then(response => response.json())

export default withFetching(fetchingFunc)(PageB);


// pages/PageC.js
import React, {Component} from 'react';
import withFetching from "../HOC";

/**
 * comments 列表
 */
class PageC extends Component {
    // constructor(props) {
    //     super(props);
    //     this.state = {
    //         data: []
    //     }
    // }
    //
    // async componentDidMount() {
    //     try {
    //         const data = await fetch('https://jsonplaceholder.typicode.com/comments?id_lte=10').then(response => response.json())
    //         this.setState({data})
    //
    //     } catch {
    //         this.setState({data: []})
    //     }
    // }

    render() {
        // const {data} = this.state;
        const {data} = this.props;
        return (
            <div>
                <h2>comments列表</h2>

                <pre>{JSON.stringify(data, null, 2)}</pre>
            </div>
        );
    }
}

// export default PageC;
const fetchingFunc = () => fetch('https://jsonplaceholder.typicode.com/comments?id_lte=10').then(response => response.json())

export default withFetching(fetchingFunc)(PageC);

```

#### 数据校验

#### 异常处理

#### 统计上报

### 总结

1. 高阶组件 不是组件，是 一个把某个组件转换成另一个组件的 函数
2. 高阶组件的主要作用是 代码复用
3. 高阶组件是 装饰器模式在 React 中的实现