### 生命周期

父子组件生命周期和 Vue 一样

- 创建阶段
  - constructor：通常的操作为初始化 state 状态或者在 this 上挂载方法
  - render：用于渲染 DOM 结构，可以访问组件 state 与 prop 属性
  - componentDidMount：组件挂载到真实 DOM 节点后执行，其在 render 方法之后执行。此方法多用于执行一些数据获取，事件监听等操作
- 更新阶段
  - shouldComponentUpdate：告知组件本身基于当前的 props 和 state 是否需要重新渲染组件，默认情况返回 true
  - render
  - componentDidUpdate：组件更新结束后触发
- 卸载阶段
  - componentWillUnmount：组件卸载前，清理一些注册是监听事件，或者取消订阅的网络请求等

### React 事件中为何 bind this

- JS 中的 this 是由函数调用者调用的时候决定的。
- 箭头函数体内的 this 对象，就是定义时所在的对象，而不是使用时所在的对象。

### 事件绑定

- React16 事件绑定到 document
- React17 事件绑定到 root 组件上
- 有利于多个 React 版本共存，例如微前端

### 受控组件和非受控组件

- 两者的区别就在于组件内部的状态是否是全程受控的
- 受控组件的状态全程响应外部数据的变化
- 非受控组件只是在初始化的时候接受外部数据，然后就自己在内部维护状态了

### super() 和 super(props) 区别

- 在 React 中，类组件基于 ES6，所以在 constructor 中必须使用 super
- 在调用 super 过程，无论是否传入 props，React 内部都会将 porps 赋值给组件实例 porps 属性中
- 如果只调用了 super()，那么 this.props 在 super() 和构造函数结束之间仍是 undefined

### setState 本质是同步

- 在组件生命周期或 React 合成事件中，setState 是异步
- 在 setTimeout 或者原生 dom 事件中，setState 是同步
- setState 是同步的执行，state 都是同步更新，因为考虑到性能，多次修改 state，只进行一次 DOM 渲染
- 即，在微任务 Promise.then 之前，state 已经计算完了

### setState 合并

- setState 第一个参数是对象时，会被合并
- setState 第一个参数是函数时，不会被合并
- setState 第二个参数是一个回调函数，用于可以实时的获取到更新之后的数据

### 函数组件

- 纯函数，输入 props，输出 JSX
- 没有实例，没有生命周期，没有 state
- 不能扩展其他方法

### Portals 使用场景

> 组价挂载到指定 DOM 元素内

```javascript
return ReactDOM.creatPortal(
  <div>{{this.props.children}}</div>,
  document.body
)
```

### context 使用场景

- 公共信息传递给每个组件
- 用 props 太繁琐
- 用 redux 小题大做

### React 中如何避免不必要的 render

- shouldComponentUpdate：对 state 和 props，确定是否要重新渲染，默认情况下返回 true
- PureComponent：，通过对 props 和 state 的浅比较结果来实现 shouldComponentUpdate
- React.memo：如果需要深层次比较，这时候可以给 memo 第二个参数传递比较函数
