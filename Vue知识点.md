### 生命周期

- beforeCreate： 创建空白的 vue 实例；data、method 尚未被初始化，不可使用。
- created： vue 实例初始化完成，完成响应式绑定；data、method 初始化完成，可以被调用；尚未开始渲染模板。
- beforeMount： 编译模板，调用 render 生成 vdom；还没开始渲染 DMO。
- mounted： 完成 DOM 渲染，组件创建完成。
- beforeUpdate： data 发生改变，准备更新 DOM。
- updated： data 发生改变，且 DOM 更新完成（在 updated 中修改 data，可能会导致死循环）。
- beforeUnmount： 组件进入销毁阶段，还未销毁；可移除解绑一些全局事件、自定义事件。
- unmounted： 组件已被销毁，所有子组件也都被销毁。

### keep-alive

- onActivated：缓存组件被激活
- onDeactivated：缓存组件被隐藏

### $nextTick

> 在下次 DOM 更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的 DOM

我们可以理解成，Vue 在更新 DOM 时是异步执行的。当数据发生变化，Vue 将开启一个异步更新队列，视图需要等队列中所有数据变化完成之后，再统一进行更新

Vue 什么时候操作 DOM 比较合适？

- mounted 和 updated 都不能保证子组件全部挂载完成
- 使用$nextTick 渲染 DOM

### slot 插槽

- 默认插槽

  - 子组件用<slot>标签来确定渲染的位置，标签里面可以放 DOM 结构，当父组件使用的时候没有往插槽传入内容，标签内 DOM 结构就会显示在页面

  - 父组件在使用的时候，直接在子组件的标签内写入内容即可

- 具名插槽

  - 子组件用 name 属性来表示插槽的名字，不传为默认插槽
  - 父组件中在使用时在默认插槽的基础上加上 slot 属性，值为子组件插槽 name 属性值

  ```html
  <!-- 子组件 -->
  <template>
    <slot>插槽后备的内容</slot>
    <slot name="content">插槽后备的内容</slot>
  </template>
  <!-- 父组件 -->
  <child>
    <template v-slot:default>具名插槽</template>
    <!-- 具名插槽⽤插槽名做参数 -->
    <template v-slot:content>内容...</template>
  </child>
  ```

- 作用域插槽

  - 子组件在作用域上绑定属性来将子组件的信息传给父组件使用，这些属性会被挂在父组件 v-slot 接受的对象上

  - 父组件中在使用时通过 v-slot:（简写：#）获取子组件的信息，在内容中使用

  ```html
  <!-- 子组件 -->
  <template>
    <slot name="footer" testProps="子组件的值">
      <h3>没传footer插槽</h3>
    </slot>
  </template>
  <!-- 父组件 -->
  <child>
    <!-- 把v-slot的值指定为作⽤域上下⽂对象 -->
    <template v-slot:default="slotProps">
      来⾃⼦组件数据：{{slotProps.testProps}}
    </template>
    <!-- or -->
    <template #default="slotProps">
      来⾃⼦组件数据：{{slotProps.testProps}}
    </template>
  </child>
  ```

### 动态组件

```html
<!-- currentTab 改变时组件也改变 -->
<component :is="tabs[currentTab]"></component>
```

在上面的例子中，被传给 :is 的值可以是以下几种：

- 被注册的组件名
- 导入的组件对象

### 异步组件

- import() 函数
  ```javascript
  components: {
    FormComp: () => import("../XXX/FormComp")
  }
  ```
- 按需加载，异步加载大组件

### 双向绑定原理

1. new Vue()首先执行初始化，对 data 执行响应化处理，这个过程发生 Observe 中
2. 同时对模板执行编译，找到其中动态绑定的数据，从 data 中获取并初始化视图，这个过程发生在 Compile 中
3. 同时定义⼀个更新函数和 Watcher，将来对应数据变化时 Watcher 会调用更新函数
4. 由于 data 的某个 key 在⼀个视图中可能出现多次，所以每个 key 都需要⼀个管家 Dep 来管理多个 Watcher
5. 将来 data 中数据⼀旦发生变化，会首先找到对应的 Dep，通知所有 Watcher 执行更新函数

### 双向数据绑定 v-model 的实现原理

- input 元素的 value = this.name
- 绑定 input 时间 this.name = $event.target.value
- data 更新触发 re-render

### defineProperty 缺点

1. 深度监听，需要递归到底，一次性计算量大
2. 无法监听新增属性和删除属性

### 如何监听数组变化

监听对象属性时，如果是数组，重写该对象的原型

```javascript
const oldArrayProperty =  Array.prototype
const arrProto = Object.creat(oldArrayProperty)
['push','pop','shift','unshift'...].forEach((methodName)=>{
  arrProto[methodName]=function(){
    // 触发视图更新
    updateView()
    oldArrayProperty[methodName].call(this,...arguments)
  }
})
```

### 性能优化之为什么不要频繁操作 DOM

> 把 DOM 和 JavaScript 各自想象成一个岛屿，它们之间用收费桥梁连接。——《高性能 JavaScript》

- 在浏览器中 DOM 的实现和 ECMAScript 是分离的。
- ES 和 DOM 是两种东西，每次连接都需要消耗性能
- 操作 DOM 会导致重排和重绘，重排会占用、消耗 CPU; 重绘会占用、消耗 GPU

### vdom（snabbdom 库）

- 用 js 模拟 DOM 结构（vnode）
- 新旧 vnode 对比，得出最小的更新范围，最好更新 DOM
- 数据驱动视图的模式下，有效控制 DOM 操作

### Vue2 diff 算法

1.patch 函数前两个参数位为 oldVnode 和 Vnode ，分别代表新的节点和之前的旧节点，主要做了四个判断：

- 没有新节点，直接触发旧节点的 destory 钩子
- 没有旧节点，说明是页面刚开始初始化的时候，此时，根本不需要比较了，直接全是新建，所以只调用 createElm
- 旧节点和新节点自身一样，通过 sameVnode 判断节点是否一样，一样时，直接调用 patchVnode 去处理这两个节点
- 旧节点和新节点自身不一样，当两个节点不一样的时候，直接创建新节点，删除旧节点

2. patchVnode 做了以下操作：

- 新节点是否是文本节点，如果是，则直接更新 dom 的文本内容为新节点的文本内容
- 新节点和旧节点如果都有子节点，则处理比较更新子节点
- 只有新节点有子节点，旧节点没有，那么不用比较了，所有节点都是全新的，所以直接全部新建就好了，新建是指创建出所有新 DOM，并且添加进父节点
- 只有旧节点有子节点而新节点没有，说明更新后的页面，旧节点全部都不见了，那么要做的，就是把所有的旧节点删除，也就是直接把 DOM 删除
- 子节点不完全一致，则调用 updateChildren

3. updateChildren 主要做了以下操作：

- 设置新旧 VNode 的头尾指针
- 新旧头尾指针进行比较，循环向中间靠拢，根据情况调用 patchVnode 进行 patch 重复流程、调用 createElem 创建一个新节点，从哈希表寻找 key 一致的 VNode 节点再分情况操作

### 模板编译

- 模板编译为 render 函数，执行 render 函数返回 vnode
- 基于 vnode 再执行 patch 和 diff
- 使用 webpack vue-loader，会在开发环境下编译模板
- vue.runtime.js 中没有 compiler，需要使用 h 函数来渲染页面

### 组价渲染过程

1. 初次渲染
   - 解析模板为 render 函数（开发环境使用 vue-loader 已完成）
   - 触发响应式，监听 data 属性 getter setter
   - 执行 render 函数，生成 vnode，patch(ele,vnode)
2. 更新过程
   - 修改 data，触发 setter（此前在 getter 中被监听 ）
   - 重新执行 render 函数，生成 newVnode
   - patch(vnode,newVnode)

### vue-router 三种模式

- Hash：location.hash 推送，window.onhashchange 监听变化
- WebHistory： history.pushState 推送，window.onpopstate 监听路由变化
- MemoryHistory：（v4 之前叫 abstract history)页面路由无变化，浏览器没有前进和后退

### 路由守卫

- 全局路由：beforeEach、beforeResolve、afterEach（参数中没有 next）
- 组件内路由：beforeRouterEnter、beforeRouteUpdate、beforeRouteLeave
- 路由独享：beforeEnter
-
