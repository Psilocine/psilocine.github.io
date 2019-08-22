---
title: 我认为的Vue官方文档重点(2)
date: 2018-03-03 23:46:09
tags: [VUE,前端]
---
##### 写在前面
文章大体会根据Vue官方文档(教程2.X)提供的学习路线
本文从 进入/离开 & 列表过渡 -> 状态过渡

### 进入/离开 & 列表过渡
1.想要vue的过渡transition标签有效, 必须和下列其一搭配
```html
1. v-if（条件渲染）
2. v-show（条件展示）
3. 动态组件
4. 在组建的根节点上，并且被vue实例DOM方法触发，如appendTo方法把组件添加到某个根节点上
```

2.过渡的类名, 会有6个class切换
```JavaScript
// 这里的v 是过渡标签name里的值 如 <transition name='fade'> 则为 fade-enter
1. v-enter: 进入过渡的开始状态
2. v-enter-active: 过渡的状态, 在元素整个过渡过程中作用
3. v-enter-to: 进入过渡的结束状态
4. v-leave: 离开过渡的开始状态
5. v-leave-active: 过渡的状态, 在元素整个过渡过程中作用
6. v-leave-to: 离开过渡的结束状态
```
<!-- more -->
![transition](2_transition.jpg)
如上图所示, 因此v-enter-to和v-leave定义时可以一块写, 如果你想方便一点的话
```css
<transition name="fade">
  <p v-if="show">hello</p>
</transition>

/* 进入和离开都是一样的过渡效果 */
.fade-enter-active,
.fade-leave-active {
  transition: all .3s ease;
}

/* 进入和离开结束的位置 */
.fade-enter,
.fade-leave-to {
  transform: translateX(10px);
  opacity: 0;
}
```

3.自定义类名, 可以用于配合第三方css动画库, 如Animate.css
```JavaScript
enter-class
enter-active-class
enter-to-class
leave-class
leave-active-class
leave-to-class

<transition name='fade' 
            enter-active-class='animated tada'
            leave-active-class='animated bounceOutRight'>
```


4.transition的v-bind:duration属性, 是显性的过渡持续时间, 设置时间如果比transition-duration大, 则按照原本的时间来, 如果小, 那按照duration的时间来
```html
<transition name='fade' :duration='1000'>
```
5.JavaScript钩子, 可以单独使用. 值得注意的是在 enter 和 leave 中, 回调函数 done 是必须的; 推荐对于仅使用 JavaScript 过渡的元素添加 v-bind:css="false", Vue 会跳过 CSS 的检测. 这也可以避免过渡过程中 CSS 的影响
```html
<transition
  v-on:before-enter="beforeEnter"
  v-on:enter="enter"
  v-on:after-enter="afterEnter"
  v-on:enter-cancelled="enterCancelled"

  v-on:before-leave="beforeLeave"
  v-on:leave="leave"
  v-on:after-leave="afterLeave"
  v-on:leave-cancelled="leaveCancelled"
>
  <!-- ... -->
</transition>

methods: {
  beforeEnter: function (el) {
    // ...
  },
  // 此回调函数是可选项的设置
  // 与 CSS 结合时使用
  enter: function (el, done) {
    // ...
    done()
  },
  afterEnter: function (el) {
    // ...
  },
  enterCancelled: function (el) {
    // ...
  },
  beforeLeave: function (el) {
    // ...
  },
  // 此回调函数是可选项的设置
  // 与 CSS 结合时使用
  leave: function (el, done) {
    // ...
    done()
  },
  afterLeave: function (el) {
    // ...
  },
  // leaveCancelled 只用于 v-show 中
  leaveCancelled: function (el) {
    // ...
  }
}
```

6.列表过渡, 使用transition-group组件, 结合v-for
```html
1. 可以通过tag特性更换为其他元素
2. 内部元素总是需要提供唯一的key属性值
<transition-group name='fade' tag='p'>
  <span v-for='item in items' :key='item'>{{item}}</span>
</transition-group>

假设items是[0, 1, 2, 3], 则最终渲染出4个p标签
```

### 状态过渡
针对数据元素本身的动效. 数字和运算, 颜色的显示, SVG节点的位置, 元素的大小和其他的属性. 可以结合第三方库来实现切换元素的过渡状态

### 混入
混入 (mixins) 是一种分发 Vue 组件中可复用功能的非常灵活的方式
1.当组件和混入对象含有同名选项(data里的属性, methods里的方法, components里的组件, directives里的自定义指令)时, 组件数据覆盖混入对象数据
```JavaScript
var mixin = {
  data () {
    return {
      message: 'hello',
      foo: 'abc'
    }
  },
  methods: {
    hello () {
      console.log('from mixin')
    }
  }
}

var vm = new Vue({
  mixins: [mixin],
  data () {
    return {
      message: 'no hello',
    }
  },
  created () {
    console.log(this.$data) // { message: 'no hello', foo: 'abc' } mixin的message属性被组件属性覆盖
  },
  methods: {
    hello () {
      console.log('from vue')
    }
  }
})

vm.hello() // 'from vue' // mixin的method被覆盖
```

### 自定义指令
1.当页面加载时, 让一个元素获得焦点
```html
<input v-focus type='text' />

// 全局注册
Vue.directive('focus', {
  inserted (el) {
    el.focus()
  }
})

// 局部注册
new Vue({
  ...,
  directives: {
    focus: {
      inserted (el) {
        el.focus()
      }
    }
  }
})
```

### 渲染函数 & JSX
