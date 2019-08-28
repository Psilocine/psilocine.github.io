---
title: 微信小程序 && mpVue 部分踩坑实录
date: 2019-06-12 14:50:10
tags: [小程序, 前端]
---
##### 写在前面
下述只是笔者现阶段遇到的问题，时效性不强，后续mpVue更新可能有所出入。欢迎大家留言补充。

### 生命周期相关
- 使用mpvue时，其所有页面的created方法都会在小程序首次加载时执行。

- 原生的生命周期onLoad、onReady方法比created、mounted方法执行的更早，使用前者会相对减少白屏时间。（但mpvue官方不推荐使用原生的生命周期
- 我们熟悉的asyncComputed插件在小程序上有弊端：所有定义过asyncComputed方法的页面都会在小程序首页加载时执行方法，意味着http request更多。
<!-- more -->
### Template相关
- 不支持在template标签里使用部分复杂的Js渲染表达式操作，只支持三元表达式。

- 不支持使用methods定义的函数。
- 不支持加“this”，如data定义了maysa变量，template中“{{this.maysa}}”是不可行的。
- 不支持在使用组件时给其添加样式(style)或者新增class属性。
```Vue
<!--下述情况均无法成功渲染-->
<Child class="maysa-class" />
<Child style="width: 100%" />
```
### MpVue其余部分
- 计算属性computed不能和wx.getStorageSync共用，因为wx.getStorageSync只会取第一次。（认为是没有双向绑定，所以不会实时更新

- 引用文件不支持使用配置的alias。
- 引用文件只支持单文件
```Vue
import { maysa, maysaTech } from "xxx" // error
```
- v-for循环必须加key，多层v-for时index必须是不一样的名字，否则认为索引一样会报错。
- 不支持注册全局组件。
- 不支持ref属性。（其实是支持的，但是data和methods属性均为null，即无法调用任何属性和方法
- 不支持vue-router。
- 不支持v-html属性。
- 不支持过滤器filter。
- 不支持一个组件多个插槽。
- 不支持transition标签。
