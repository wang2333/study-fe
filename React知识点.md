### setState 本质是同步

- setState 是同步的执行，state 都是同步更新
- 因为考虑到性能，多次修改 state，只进行一次 DOM 渲染
- 即，在微任务 Promise.then 之前，state 已经计算完了
