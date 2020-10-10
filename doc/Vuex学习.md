# Vuex学习
### Vuex 是什么？
一个` Vuex `应用的核心就是` store`（仓库），包含着你的应用中大部分的状态 (`state`)。`Vuex` 和单纯的全局对象有以下两点不同：
- `Vuex` 的状态存储是**响应式**的。当` Vue` 组件从 `store` 中读取状态的时候，若` store` 中的状态发生变化，那么相应的组件也会相应地得到高效更新。（和`Vue`组件中的`data`数据有着相同的**响应式**功能）

- **不能直接改变 `store` 中的状态**。改变 store 中的状态的唯一途径就是**显式地提交 (`commit`) `mutation`**。这样使得我们可以方便地跟踪每一个状态的变化，从而让我们能够实现一些工具帮助我们更好地了解我们的应用。
### 状态管理与单项数据流
#### 从一个简单的 Vue 计数应用开始：
```js
new Vue({
  // state
  data () {
    return {
      count: 0
    }
  },
  // view
  template: `
    <div>{{ count }}</div>
  `,
  // actions
  methods: {
    increment () {
      this.count++
    }
  }
})
```
这个状态自管理应用包含以下几个部分：
- state，驱动应用的数据源；
- view，以声明方式将 state 映射到视图；
- actions，响应在 view 上的用户输入导致的状态变化。
#### 单向数据流指只能从一个方向来修改状态。
![alt 单向数据流](https://vuex.vuejs.org/flow.png)
但是，当我们的应用遇到多个组件共享状态时，单向数据流的简洁性很容易被破坏：
- 多个视图依赖于同一状态。
- 来自不同视图的行为需要变更同一状态。

多个组件会共享状态时，组件间（兄弟组件、父子组件、无关系的组件）通信变的不容易。我们把**共享状态抽取出来**，用**单向数据流**的方式会变得更加方便。
### Vuex 集中式的状态管理模式
#### `Vuex`规定了一些需要遵守的规则：
- 应用层级的状态应该集中到**单个 `store` 对象**中。

- 提交` mutation` 是更改状态的**唯一**方法，并且这个过程是**同步**的。

- **异步**逻辑都应该封装到 `action` 里面。

#### 对于大型应用，我们会希望把 Vuex 相关代码分割到模块中。下面是`store`的项目结构示例：
```
 store
    ├── index.ts                       # 组装模块并导出 store 的地方
    ├── initailState.ts                # 初始化 store 数据的地方
    ├── actions.ts                     # 根级别的 action
    ├── service.ts                     # 处理 axios 请求的地方
    ├── mutations.ts                   # 根级别的 mutation
    └── mutationsTypes.ts              # 导出 mutation 的类型的常量    
```
#### State
Vuex 使用单一状态树——用一个对象就包含了全部的应用层级状态
```js
// initailState.ts 导出了`default`数据
export default {
  count: 0
}
```
`State` 用来存状态, `createStore`函数创建了一个`store`并最终注册到`Vue`根实例中，之后在`Vue`的任何组件中可以通过 `this.$store.state`来访问`state`中的状态
```js
// index.ts
import Vue from 'vue'
import Vuex from 'vuex'
import mutations from './mutations'
import actions from './actions'
import defaultState from './initailState'  

Vue.use(Vuex)

export default function createStore (initailState = {}) {
   return new Vuex.Store({
      state: {
           ...defaultState,
           ...initailState 
      },
      mutations,
      actions
    })
}
```
#### Mutations
在前面说过，状态的改变必须遵循**单向数据流**思想，在任何地方想要改变`State`中的状态，只能通过提交（`commit`）`Mutations`, 来改变。需要注意的是，Mutations 里的修改状态的操作必须是**同步**的。
提交`Mutations`的方法：
```js
store.commit({
  type: 'increment',
  amount: 10
})
```
`type`是`Mutation`事件类型，`amount`是提交至`Mutation`的形参，为了方便管理，一般在`mutationsTypes.ts`中统一定义`type`常量
```js
// mutationsTypes.ts
export const INCREMENT = 'increment'
```
```js
// mutations.ts
import { INCREMENT } from './mutationsTypes.ts'
export default {
   [INCREMENT] (state, amount: number) {
      // 在这里改变 state 的状态
      state.count = amount
   }
}
```
#### Actions
有时候，我们需要**异步地去更改`State`的状态**，例如通过`axios`请求得到数据更改`state`中的状态并更新到页面中，这时候我们需要`actions`，`actions`本质上与`mution`没有区别也是一个`function`, 只不过**actions是通过`dispatch`来派发**，并且是一个**异步执行**的操作，要最终更改`State`的状态，还需要在`actions`中通过**调用 Mutations** 来改状态。

**使用`dispatch`派发`action` :**
```js
store.dispatch({
  // actions 类型名称
  type: 'querySomeThing',
  // 形参
  param: 'ok' 
})
```
```js
// service.ts 中保存了 axios 操作
export const getSomeThing = () => $axios.get('xxxx')
.then( res => res.data )
.catch( e => console.error(e) )
```
```js
// actions.ts
import { getSomeThing } from './service'
import { INCREMENT } from './mutationsTypes.ts'

async querySomeThing ({ state, commit, dispatch }, { param }) { 

  // 逻辑处理 示例
 await res = getSomeThing() 
 if( res.par === param ) {

  // 在 actions 中通过 commit mutaion 来改变 State 中的状态
   commit(INCREMENT, res.count)

 }
 // ...
 // ...

 // 同样的，在 action 中也可以 dispatch 其他的 action
 dispatch({
   type: 'queryAct'
 })
}

async queryAct () {
  // ......
}
```
### 总结
#### Vuex的整体单向数据流程可以概括为：
#### 组件中触发 Action，Action 提交 Mutations，Mutations 修改 State。 
#### 组件根据 State 响应式渲染页面
![alt Vuex](https://vuex.vuejs.org/vuex.png)




