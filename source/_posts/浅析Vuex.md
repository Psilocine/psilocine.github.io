---
title: 浅析Vuex
date: 2018-03-27 15:02:46
tags: [VUE,前端]
---
##### 写在前面
在中大型项目中, 如果不在组件外部管理状态, 可能会过于繁琐且难以维护. 本篇文章记录笔者的学习笔记, 基于官方文档.

#### 最简单的Vuex
```vue
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  }
})
```

#### vuex运行机制
在 Vuex 中, 不能直接改变状态, 比如不能直接改变 store.state.count, 唯一的途径就是显示地提交 (commit) mutation
```vue
store.commit('increment')

console.log(store.state.count) // -> 1
```
<!-- more -->
#### Vuex核心概念
1.state (单一状态树), 这意味着, 每个应用将仅仅包含一个 store 实例
2.getter, 当不止一个组件需要 store.state 中派生的一些状态时, 我们就需要用到getter, 因为去每一个组件里定义相同的方法是麻烦的. getter 和 computed 一样, 会将得到的数据缓存起来.
```vue
// Getter 接受 state 作为其第一个参数：
const store = new Vuex.Store({
  state: {
    todos: [
      { id: 1, text: '...', done: true },
      { id: 2, text: '...', done: false }
    ]
  },
  getters: {
    doneTodos: state => {
      return state.todos.filter(todo => todo.done)
    }
  }
})

// 1.通过属性访问
store.getters.doneTodos // -> [{ id: 1, text: '...', done: true }]
// 2.通过方法访问
getters: {
  // ...
  getTodoById: (state) => (id) => {
    return state.todos.find(todo => todo.id === id)
  }
}
store.getters.getTodoById(2) // -> { id: 2, text: '...', done: false }
```
3.mutation, 不能直接调用, 需要 commit('mutation事件名'). 值得注意的是 Mutation 必须是同步函数
```vue
const store = new Vuex.Store({
  state: {
    count: 1
  },
  mutations: {
    increment (state) {
      // 变更状态
      state.count++
    }
  }
})

// mutation提供额外参数, 称为提交载荷(Payload). 更多情况下, 载荷应该是个对象
mutations: {
  increment (state, n) { // n 为基本类型
    state.count += n
  }
}

mutations: {
  increment (state, payload) { // 作为对象传入
    state.count += payload.amount
  }
}

// 使用常量替代事件类型
// mutation-types.js
export const SOME_MUTATION = 'SOME_MUTATION'

// store.js
import Vuex from 'vuex'
import { SOME_MUTATION } from './mutation-types'

const store = new Vuex.Store({
  state: { ... },
  mutations: {
    // 我们可以使用 ES2015 风格的计算属性命名功能来使用一个常量作为函数名
    [SOME_MUTATION] (state) {
      // mutate state
    }
  }
})
```
4.action, 提交的是 mutation, 而不是直接变更状态. Action 可以包含任意异步操作.
```vue
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  },
  actions: {
    increment (context) {
      context.commit('increment')
    }
  }
})

// action通过store.dispatch触发
store.dispatch('increment')

// 可以组合action, 处理更复杂的异步流程
/ ... /
actions: {
  actionA ({ commit }) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        commit('someMutation')
        resolve()
      }, 1000)
    })
  },
  actionB ({ dispatch, commit }) {
    return dispatch('actionA').then(() => { // 先执行actionA
      commit('someOtherMutation')
    })
  }
}

// 也可以
store.dispatch('actionA').then(() => {
  commit('someOtherMutation')
})

// 也可以利用async, await
// 假设 getData() 和 getOtherData() 返回的是 Promise
actions: {
  async actionA ({ commit }) {
    commit('someMutation', await getData())
  },
  async actionB ({ dispatch, commit }) {
    await dispatch('actionA') // 等待 actionA 完成
    commit('someOtherMutation', await getOtherData())
  }
}
```

5.module, 当应用过大时, 单一的store可能会相当臃肿, Vuex允许我们将 store 分割成模块, 并且每个模块拥有自己的 state, mutation, action, getter 甚至是嵌套子模块
```vue
const moduleA = {
  state: { ... },
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: { ... },
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
  }
})

store.state.a // -> moduleA 的状态
store.state.b // -> moduleB 的状态

// 对于模块内部的mutation和getter, 第一个参数是模块的局部状态对象, 用context.state表示, 第三个参数是根节点状态, 用context.rootState表示; 根getter作为第四个参数传入, 用context.rootGetter
// 模块foo下
getters: {
  // 在这个模块的 getter 中，`getters` 被局部化了
  // 你可以使用 getter 的第四个参数来调用 `rootGetters`
  someGetter (state, getters, rootState, rootGetters) {
    getters.someOtherGetter // -> 'foo/someOtherGetter'
    rootGetters.someOtherGetter // -> 'someOtherGetter'
  },
  someOtherGetter: state => { ... }
},

actions: {
  // 在这个模块中， dispatch 和 commit 也被局部化了
  // 他们可以接受 `root` 属性以访问根 dispatch 或 commit
  someAction ({ dispatch, commit, getters, rootGetters }) {
    getters.someGetter // -> 'foo/someGetter'
    rootGetters.someGetter // -> 'someGetter'

    dispatch('someOtherAction') // -> 'foo/someOtherAction'
    dispatch('someOtherAction', null, { root: true }) // -> 'someOtherAction'

    commit('someMutation') // -> 'foo/someMutation'
    commit('someMutation', null, { root: true }) // -> 'someMutation'
  },
  someOtherAction (ctx, payload) { ... }
}
```

#### 辅助函数
当一个组件需要获取多个状态时, 将这些状态声明为计算属性会有些重复和冗余, 我们可以使用辅助函数来帮助.
1.mapState
```vue
import { mapState } from 'vuex'
import { mapGetters } from 'vuex'
import { mapMutations } from 'vuex'
import { mapActions } from 'vuex'

export default {
  computed: mapState([
    // 映射 this.count 为 store.state.count
    'count'
  ])
}

// 将mapState与局部计算属性混合
computed: {
  / ... /,
  ...mapState({
    / ... /
  })
}
```
2.mapGetters
```vue
mapGetters({
  // 映射 `this.doneCount` 为 `store.getters.doneTodosCount`
  doneCount: 'doneTodosCount'
})

// 混合
computed: {
// 使用对象展开运算符将 getter 混入 computed 对象中
  ...mapGetters([
    'doneTodosCount',
    'anotherGetter',
    // ...
  ])
}
```
3.mapMutations
```vue
methods: {
  ...mapMutations([
    'increment', // 将 `this.increment()` 映射为 `this.$store.commit('increment')`

    // `mapMutations` 也支持载荷：
    'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.commit('incrementBy', amount)`
  ]),

  ...mapMutations({
    add: 'increment' // 将 `this.add()` 映射为 `this.$store.commit('increment')`
  })
}
```
4.mapActions
```vue
methods: {
  ...mapActions([
    'increment', // 将 `this.increment()` 映射为 `this.$store.dispatch('increment')`

    // `mapActions` 也支持载荷：
    'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.dispatch('incrementBy', amount)`
  ]),

  ...mapActions({
    add: 'increment' // 将 `this.add()` 映射为 `this.$store.dispatch('increment')`
  })
}
```