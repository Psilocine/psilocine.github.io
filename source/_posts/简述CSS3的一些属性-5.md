---
title: 简述CSS3的一些属性(5)
date: 2017-08-25 14:10:44
tags: [CSS, 前端]
---
##### 写在前面
这篇文章主要讲css3动画 column 用户界面
动画的@keyframes animation,  column的colunm-count column-gap column-rule,  用户界面的resize box-sizing outline-offset. 都没有继承性.

#### @keyframes 关键帧
兼容ie10+, 其他浏览器需要加厂商前缀.
1. 用法: @keyframes animationName { keyframes-selector { css-styles; } }
   animationName: 必须. 指定关键帧的名称.
   keyframes-selector: 必须, 规定动画持续时间的百分比, 步骤. 可以选0-100%, from(0%), to(100%)
   css-styles: 必须, 一个或多个css样式.
2. 必须与animation配合使用, 下面会给eg.

#### animation
<!--more-->
兼容ie10+, 其他浏览器需要加厂商前缀.
1. 属性有8个. animation-name: 要绑定到选择器的关键帧名称.
   aimation-duration: 指定动画需要的时间, 与transition的duration用法一样, 默认值为0s.
   animation-timing-function: 指定动画如何完成一个周期. 同上述, 默认值为ease;
   animation-delay: 指定动画启动前的延迟. 同上述, 默认值为0;
   animation-iteration-count: 定义动画执行的次数, 默认为1, 常用infinite(无限次).
   animation-direction: 指定动画是否轮流方向播放. 默认值为normal.
   animation-fill-mode: 指定动画不播放时, 或动画播放完成后的位置或样式. 默认值为none.
   animation-play-state: 指定动画是否在运行或已暂停, 默认值为running.
2. 与transition一样可以缩写成 animation: name duration timing-function delay teration-count direction fill-mode play-state. 一般比较常用name duration teration-count三个属性.
```javascript
// 只在chrome或safari测试
div {
  width: 100px; height: 100px;
  background: red; position: relative;
  animation: move 5s infinite;
  -webkit-animation: move 5s infinite;
}
@keyframes {
    0% { top: 0px; background: red; width: 100px; }
  100% { top: 200px; background: yellow; width: 300px; }
}
@-webkit-keyframes {
    0% { top: 0px; background: red; width: 100px; }
  100% { top: 200px; background: yellow; width: 300px; }
}
```
上述是div物体在5s内, 向下运动200px, 背景颜色由red逐渐变成yellow, 宽度也是逐渐变成300px. 速度都是平均, 想要不同的话可以改变timing-function. 值得注意的是, fill-mode和play-state, 当执行次数只有一次的时候, 设置fill-mode为forwards. 会停在动画结尾的位置, 即样式不会变为执行前的样式. 第二就是在动画过程中play-state的值如果变为pause, 物体会停在当前位置, 但是动画执行不会消失, 一旦play-state的值恢复running, 动画继续执行.

那为何有了transition又要设计一个animation出来呢, 二者实现效果相似. 原因是animation和keyframes可以精准控制每一帧的动画效果, 能够更加精细地划分动画, 这是transition所欠缺的. 因此如果是一些较简单的动画效果笔者认为用transition足以.

#### 多列column
column: 兼容ie10+, 除了Opera以外的浏览器都需要加厂商前缀.
1. column-count. 规定元素赢干杯分隔的列数. 如要把一个p标签的文字分成3列: column-count: 3即可. 间隔宽度各大浏览器标准都不一致.
2. column-gap: 规定列与列之间的间隔. column-gap: 40px; column-gap: 4em; 都是可以的, 百分比会失效.
3. column-rule: 规定列与列之间的边框宽度颜色样式. column-rule: 3px solid #000; column-count: 2. 结果为两列间隔存在一宽度为3px的竖线黑色边框.

#### 用户界面
1. resize. ie不兼容, 低版本Firefox需要加-moz-. 指定的元素可以由用户调整宽高, 和textarea很相似. 默认为none. 可以设置为both, 用户即可调整宽高. horizont, 用户可调整物体宽度, vertical, 用户可调整高度.
2. box-sizing. ie8+, 低版本chrome和Firefox需要加-webkit-, -moz-. 指定以某种方式定义某些元素, 以适应指定区域. 默认值为content-box. 效果为指定的样式宽高为多少, 内容区域(盒子模型里的content区)九尾多少, 即不包括padding. 可设置为border-box, 这样就包括padding.
```JavaScript
// 总宽度为 140px 包含两边padding
.div1 {
  width: 100px;
  height: 100px;
  padding: 20px;
  box-sizing: content-box;
}
// 总宽度为 100px 内容区域为60px, width = content区 + padding
.div2 {
  width: 100px;
  height: 100px;
  padding: 20px;
  box-sizing: border-box;
}
```

* outline-offset. 只有ie不兼容. 设置border边缘外的偏移, 与outline共用. 可以理解为border外面又有一个border.
```JavaScript
div {
  ...
  border: 2px solid #000;
  outline: 2px solid #F00;
  outline-offset: 15px;
}
```
在border外距离15px处存在一个红色的2px边框. outline不会影响布局空间, 因此不会影响元素的尺寸.

#### 写在最后
简述css3系列应该算是完结了, 实不相瞒, 当初是想了解box-sizing才看的css3, 结果发现了更多没有注意到的或者是根本没见过的属性, 让我受益良多. 
更多css3属性可以点击[css3汇总](http://www.css88.com/book/css/css3-quicksearch.htm)
希望我能保持热情. 还是那句话, 喜欢的话就坚持吧.