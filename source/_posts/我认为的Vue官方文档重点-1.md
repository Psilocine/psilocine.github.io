---
title: 我认为的Vue官方文档重点(1)
date: 2018-01-15 16:18:09
tags: [VUE,前端]
---
##### 写在前面
最近想系统学习一下Vue, 有时候看官方文档太庞大复杂, 会失去看下去的欲望, 不妨来看看我整理的一些知识. 当然前提是你懂基本的Vue语法和结构
文章大体会根据Vue官方文档(教程2.X)提供的学习路线
本文从 Vue实例 -> 组件

### Vue实例
1.Vue是数据驱动的, 但如果在创建实例后再去给Vue添加属性的话, 新添加的属性是不具备响应式的
<!--more-->
```JavaScript
<p>{{name}}</p>
<p>{{age}}</p>
<p>{{sex}}</p>

new vm = new Vue({
  ...
  data () {
    name: 'Psilo',
    age: '24'
  },
  ...
})

// 因为还没有sex属性, 只渲染出前两个标签

vm.sex = '男'
// 添加属性成功, 但是渲染无效.
// 查看后发现原因是因为name有setter和getter, 后添加的sex没有
// 解决方法 1
Vue.set(vm, 'sex', '男')
// 解决方法 2
vm.$set(vm, 'sex', '男')
```
![response](1_newPropertyHaventGetterAndSetter.jpg)

### 模板语法
1.v-once只渲染一次, 适合渲染包含大量静态内容的标签
```javascript
<p v-once>我不会变化: {{msg}}</p>
// msg改变, 上述标签内容也不会改变
```
2.v-html用法, 例如data有个属性包含html标签, 直接用双花括号渲染会原封不动将html标签渲染成字符串, v-html会将HTML标签渲染成html标签, 详情见下图. 但是官方并不推荐用, 因为容易导致xss攻击
```javascript
<p>{{text}}</p>
<p v-html='text'></p>

new Vue({
  data () {
    text: '<span style="color: red">This should be red.</span>'
  }
})
```
![vHtmlUsage](2_vHtmlUsage.jpg)

3.双花括号里面能包括单个表达式
```JavaScript
{{ number + 1 }}
{{ ok? 'Yes'; 'No' }}
{{ message.split('').reverse().join('') }}
// 还支持Math和Date
{{ Math.floor(number) }}
```
4.利用v-bind响应式更新attribute
```javascript
<a v-bind:href='url'>...</a>
<a :href='url'>...</a>

<p class='active' :class='classObject'>...</p> // 最后渲染出三个class的p标签

<p :style='styleObject'>...</p> // 渲染出行内样式

// 也可以是组数
<p :style='['styleObject', 'styleObjectB']'>...</p>

new Vue({
  data () {
    url: 'https://psilocine.github.io',
    classObject: {
      'text-red': true,
      'text-bold': true
    },
    styleObject: {
      color: 'red',
      font-weight: 'bold'
    },
    styleObjectB: {
      font-size: '20px'
    }
  }
})

// url变化时, a标签的href也动态变化
```

### 计算属性和侦听器
1.在计算属性computed定义的方法和在methods里定义的方法有什么不同? computed里的方法适合性能开销大的计算, 因为它会缓存起来, 如果相应变量不改变, 它就只会计算一次, 后续的调用从缓存拿; methods每次都会重复算
```javascript
new Vue({
  ...,
  computed: {
    now () {
      return Date.now()
    }
  },
  ...
})
// 计算属性里的now方法将不再更新，因为 Date.now() 不是响应式依赖
```
2.计算属性默认只有getter, 不过也可以定义setter
```javascript
computed: {
  fullName: {
    // getter
    get: function () {
      return this.firstName + ' ' + this.lastName
    },
    // setter
    set: function (newValue) {
      var names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
```

### Class 与 Style 绑定
1.见模板语法的第3点, 另多补充三元表达式
```JavaScript
<div v-bind:class="isActive ? activeClass : ''"></div>
```
2.v-bind:style添加css属性, 会自动侦测并添加浏览器前缀, 和postcss的autoprefixer相似

### 条件渲染
1.v-if也能给多个元素加, 和template标签配合即可. 最终的渲染结果不包括template标签
```html
<template v-if='condition'>
  <p>hello</p>
  <p>world</p>
  <p>!!!!!</p>
</template>
```
![template_code](3_template_code.jpg)
![template_show](3_template_show.jpg)

2.v-if和v-show的区别
```javascript
1. v-if能跟v-else连用, v-show不行
2. v-if能和template标签配合, v-show不行
3. 带有 v-show 的元素始终会被渲染并保留在 DOM 中, v-show 只是简单地切换元素的 CSS 属性 display; v-if如果不成立, 是不在DOM中渲染的
4. v-if 有更高的切换开销, 而 v-show 有更高的初始渲染开销. 如果需要非常频繁地切换, 则使用 v-show 较好; 如果在运行时条件很少改变, 则使用 v-if 较好
```
3.v-if和v-for一起用, v-for比v-if高优先权
```html
<li v-for="todo in todos" v-if="!todo.isComplete">
  {{ todo }}
</li>
上述只传递了未完成的 todos
```

### 列表渲染
1.v-for遍历对象时, 是按Object.keys() 的结果遍历, 但是不能保证它的结果在不同的 JavaScript 引擎下是一致的
2.v-for也可以和v-if一样, 和template标签渲染多个元素

### 事件处理
1.v-on的修饰符
```javascript
// 事件修饰符
.stop // evnet.preventDefault() v-on:click.stop='doThis'
.prevent // event.stopPropagation
.capture // 事件捕获
.once // 执行一次
.self // event.target是当前元素才触发

注意修饰符的顺序也有影响: 
@:click.self.prevent // 阻止自身元素默认行为 
@:click.prevent.self // 阻止所有的点击

// 按键修饰符
@:keyup.13 = 'submit' // keyup且keyCode是13才触发
@.keyup.enter = 'submit' // 同理

.enter
.tab // Tab键, 当focus到视口外失效, 如最后一个元素
.delete (捕获“删除”和“退格”键)
.esc
.space
.up // ↑键
.down
.left
.right

// 系统修饰键
.ctrl
.alt
.shift
.meta // mac对应command, windows对应左下微软标识键

@keyup.ctrl.67='something' // ctrl + c 复制时候触发事件

// 鼠标按键修饰符
.left
.right
.middle
```

### 表单输入绑定
v-model实现双向绑定, 有v-model指令的标签能实时改变绑定的值
```html
v-model实际上是v-bind和v-on的语法糖
<input type='text' :value='message' ref='inp' @input='inputValue'>
<p>{{message}}</p>

...,
data () {
  return {
    message: 'hello'
  },
  methods: {
    inputValue () {
      this.message = this.$refs.inp.value
    }
  }
}

// 不用到ref的更简洁的方法 @input='inputValue($event)'
    inputValue (event) {
      this.message = event.target.value
    }
但这样还是显得太过臃肿, 于是引入v-model
```
1.关于教程里标注的'在文本区域textarea插值并不会生效', 是指的textarea如果是用花括号赋值, 当改变文本区域的内容, 不会实时变化
```html
{{num}}
<input v-model='num'>
<textarea>{{num}} asd</textarea>
<textarea v-model='num'></textarea>

假设num值是1.5
1. 当我们改变有v-model指令的input和textarea标签, 4个值都会实时改变. 看图1 我们加了'change'
2. 当我们改变直接插值的textarea以后, 发现其他三个并没有改变, 从这时候开始, 直接插值的textarea就已经不是双向绑定了. 看图2 我们又多加'111'
3. 如果不在直接插值的文本区域修改属性, 还是可以生效的; 如果修改了, 哪怕不是修改属性值, 如上例子修改的是{{num}} 后面的 asd, 也会断开联系
```
<!--more-->
图1  
![图1](1_textarea_1.jpg)
图2  
![图2](1_textarea_2.jpg)

2.在 iOS 中, select元素这会使用户无法选择第一个选项, 推荐像下面这样提供一个值为空的禁用选项.
```html
  <select v-model="selected">
    <option disabled value="">请选择</option> // 空值选项
    <option>A</option>
    <option>B</option>
    <option>C</option>
  </select>
```

3.对于单选按钮, 复选框和选择框的选项, v-model绑定的值通常是静态字符串
```JavaScript
<select v-model="selected">
  <option v-for="option in options" v-bind:value="option.value">
    {{ option.text }}
  </option>
</select>
<span>Selected: {{ selected }}</span>

new Vue({
  el: '...',
  data: {
    selected: 'A',
    options: [
      { text: 'One', value: 'A' },
      { text: 'Two', value: 'B' },
      { text: 'Three', value: 'C' }
    ]
  }
})

// 为什么明明没改变data里selected的值, 可是渲染出来的{{ selected }}却可以随select选择变化
原因就是上面的话, 选择的option的value会传递给v-model
```

4.输入的修饰符
```javascript
1. (.lazy) v-model在change时才触发更新
2. (.number) 输入的必须是数值类型, 如果不是, 在输入框blur时自动将非数值剔除. 如果已经有值, 那第一个字符应该就为数值, 不然无效
3. (.trim) 过滤用户输入的首位空白字符
```

### 组件
1.组件注册可以全局注也可以局部注册
```JavaScript
// 局部, 一种导入(假设为header) 一种当前作用域定义注册
var footer = {
  template: '<div>i am footer</div>'
}
new Vue({
  ...,
  components: {
    'v-header': header // 导入
    'v-footer': footer // 作用域注册 不常用
  },
  ...
})
// 全局
var footer = {
  template: '<div>i am footer</div>'
}
// 注意 一定要在new之前注册, 否则无效
Vue.component('v-footer', {
  footer
})
new Vue({
  ...
})
```
2.data必须是函数, 这样每个组件都能有独立的状态
3.一般来说只允许父组件向子组件传值(props), 称为props单向数据流, 为了防止子组件无意间改变父组件的状态
```JavaScript
// 子组件
export default {
  props: ['name', 'age'] // 可以是数组
  props: {               // 可以是对象
    name: String, // 验证类型
    age: {        // 验证类型 + 其他验证
      type: Number,
      dafault: 0,
      required: true,
      validator (val) {
        return val >= 0
      }
    }
  }
}

//补充 props 里的 type 支持的属性
String
Number
Boolean
Function
Object
Array
Symbol

// 父组件
<Child name='Psilo' age='24' /> // 设子组件名为 <Child>, 传值成功
```
4.子组件向父组件怎么传值? 自定义事件. 父组件里用v-on:自定义事件名字='父组件方法'; 子组件在监听方法名里用this.$emit('自定义事件名字')
```JavaScript
使用 $on(eventName) 监听事件
使用 $emit(eventName) 触发事件

// 父组件
<div id="counter-event-example">
  <p>{{ total }}</p>
  <button-counter v-on:increment="incrementTotal"></button-counter>
  <button-counter v-on:increment="incrementTotal"></button-counter>
</div>

new Vue({
  el: '#counter-event-example',
  data: {
    total: 0
  },
  methods: {
    incrementTotal: function () {
      this.total += 1
    }
  }
})

// 子组件
<div>
  <button v-on:click='incrementCounter'>{{ counter }}</button>
</div>

export default {
  data: function () {
    return {
      counter: 0
    }
  },
  methods: {
    incrementCounter: function () {
      this.counter += 1
      this.$emit('increment')
    }
  }
}
```

5.非父子组件怎么通信
```JavaScript
// 可以使用一个空的Vue实例来做媒介
var bus = new Vue()

// 需要传递值的组件
bus.$emit('someMethods')

// 需要接受值的组件
bus.$on('someMethods', function (val) {
  // 监听
  // ...
})

// 情况复杂的话需要vuex状态管理
```

6.插槽分发slot标签. 在设计组合使用的组件时, 内容分发API非常有用
7.动态组件
```JavaScript
// 可以通过保留component元素, 并对其 is 特性进行动态绑定.
var vm = new Vue ({
  ...,
  data () {
    return {
      currentView: 'home'
    }
  },
  components: {
    home: {
      template: '<div>home</div>'
    },
    posts: { ... },
    archive: { ... }
  }
})

<component v-bind:is='currentView'>

// 也可以直接绑定到组件
var Home = {
  template: '<div>home</div>'
}

var vm = new Vue ({
  ...,
  data: {
    currentView: Home
  }
})
```
8.keep-alive标签
```html
<keep-alive>
  <... />
</keep-alive>
<!-- 可以保留其内部的状态或者避免重新渲染 -->
```
9.$refs 只在组件渲染完成后才填充, 并且它是非响应式的. 它仅仅是一个直接操作子组件的应急方案——应当避免在模板或计算属性中使用 $refs


前往[第二篇](https://psilocine.github.io/2018/03/03/%E6%88%91%E8%AE%A4%E4%B8%BA%E7%9A%84Vue%E5%AE%98%E6%96%B9%E6%96%87%E6%A1%A3%E9%87%8D%E7%82%B9-2/)