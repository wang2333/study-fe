## 生命周期

- beforeCreate： 创建空白的 vue 实例；data、method 尚未被初始化，不可使用。
- created： vue 实例初始化完成，完成响应式绑定；data、method 初始化完成，可以被调用；尚未开始渲染模板。
- beforeMount： 编译模板，调用 render 生成 vdom；还没开始渲染 DMO。
- mounted： 完成 DOM 渲染，组件创建完成。
- beforeUpdate： data 发生改变，准备更新 DOM。
- updated： data 发生改变，且 DOM 更新完成（在 updated 中修改 data，可能会导致死循环）。
- beforeUnmount： 组件进入销毁阶段，还未销毁；可移除解绑一些全局事件、自定义事件。
- unmounted： 组件已被销毁，所有子组件也都被销毁。

### keep-alive 组件

- onActivated：缓存组件被激活
- deactivated：缓存组件被隐藏

### Vue 什么时候操作 DOM 比较合适？

- mounted 和 updated 都不能保证子组件全部挂载完成
- 使用$nextTick 渲染 DOM

### Vue3 Composition API 生命周期的区别？

- 使用 step 代替了 beforeCreate 和 created
- 使用 Hooks 函数的形式，如 mounted 改为 onMounted()

### Vue2 Vue3 React 三者 diff 算法区别？

> Tree Diff 优化
>
> - 只比较同一层级，不跨级比较
> - tag 不同则删除重建（不再去比较内部的细节）
> - 子节点通过 key 区分（key 的重要性）

- Vue2：双端比较
- Vue3：最长递增子序列比较
- React：仅右移

### Vue React 为何循环时需要使用 key？

- vdom diff 算法会根据 key 判断元素是否删除
- 匹配了 key，则只需要移动元素，性能较好
- 为匹配 key，则删除重建，性能较差

### vue-router 三种模式

- Hash：location.hash 推送，window.onhashchange 监听变化
- WebHistory： history.pushState 推送，window.onpopstate 监听路由变化
- MemoryHistory：（v4 之前叫 abstract history)页面路由无变化，浏览器没有前进和后退
