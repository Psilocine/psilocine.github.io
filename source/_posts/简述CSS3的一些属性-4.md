---
title: 简述CSS3的一些属性(4)
date: 2017-08-20 13:40:18
tags: [CSS, 前端]
---
##### 写在前面
这篇文章主要讲transform的3D转换、css3过渡
首先是rotate3d() rotate3d() rotate3d(), 视角perspective perspective-origin, transform-style, backface-visibility
过渡的transition. 今天的属性都无继承性.

#### rotate3d() translate3d() scale3d()
兼容性ie10及以上支持3d, safari和chrome加上厂商前缀即可, opera只能支持2d.
rotateZ()本质是rotate3d(x,y,z)的第三个参数. 斜拉skew没有z轴上的方法, 也没有skew3d()这个方法.
x轴, y轴大家都很了解了, 这个z轴是在哪呢, 其实就是我们视角看向屏幕的方向, 即z轴是与屏幕垂直的, 越靠近我们z轴值越大.
1. rotateZ(), 围绕z轴旋转. 和rotate()效果一模一样. rotateX()围绕x轴旋转, rotateY()围绕y轴, 可以看出3d效果.
2. translateZ(), 结合后面的perspective一起才能起作用, 负值时往屏幕深处移动, 正值往屏幕正前方移动.
3. scaleZ(), 当定义了一个3d变换物体, 这个方法用来制定z方向的拉伸缩小比例, 同样可以负值.

<!--more-->
#### perspective perspective-origin
ie10+, 其他要加-webkit- -moz- 火狐不用加前缀.
1. perspective, 定义浏览器的视角高度,  如同unity3d中设置的camera一样.
2. 用法: perspective: 100px; 当translateZ(101px);时候, 物体将会消失, 因为物体跑到视角后面去了. 
3. perspective-origin, 顾名思义, 就是3d元素的基点位置. 若值为(25% 75%), 就是在左下方, 假设translateZ是正值, 物体会往右上靠近我们.

#### transform-style
ie11+, -moz-, -webkit-
1. 默认值flat,表示子元素平面显示, transform-style: preserve-3d; 表示子元素3d显示.
2. 一般想做一些较复杂的3d变换, 这个属性是必须设置的. 假设我们想做一个立体骰子, 没有设置这个属性的话, 6个面都会叠在一起.

#### backface-visibility
ie10+, -moz-, -webkit-
1. 顾名思义, 背部的显示与否. 默认值为visible. 可设置为hidden, 这样rotateX(180deg) rotateY(180deg)物体都不会显示.
2. 实际上过了90deg后就已经算是背面了, 因此在91deg时候物体就消失不见了.

#### matirx3d()
1. 终极大魔王, 2d的时候6个参数, 3d参数为4*4=16个. 不过千万不要被它的参数所吓到, 其实本质是一样的, 如后4位(10, 20, 30, 1), 其实和2d一样, 是平移的值, 10为x 20为y 30为z轴上平移的值. 
2. matrix3d(1,0,0,0,0,1,0,0,0,0,1,0,0,0,0,1), transform-origin基点默认的情况下, 其实第一个1就是x轴的scale倍数, 第二个1就是y轴的, 第三个1就是z轴的.
3. 呃, 剩下的skew和rotate有空再研究吧. orz

#### transition 过渡
ie10+, Safari需要加前缀-webkit-. 这个属性实用程度远比上述都高. 有四个属性, 不过是这个属性同样可以缩写, 现在拆分变异理解.
1. transition-property. 规定应用过渡的css属性名称, 其实嫌麻烦可以设为all.
2. transition-duration. 定义过渡花费的时间, 小于1秒的小数可以省略前面的零, 如0.3秒写为.3s.
3. transition-timing-function. 规定过渡效果的时间曲线, 默认值是ease, 效果是先慢后快再慢. 还有linear, 效果是速度保持一致过渡等, 其实时间设置补偿的前提下, 效果都差不多.
4. transition-delay. 规定过渡效果何时开始, 默认为0, 其实就是延时作用, 一般不会修改这个属性.
5. 上述可以缩写为, transition: width 1s linear 2s; 然后再给所设物体写上伪类hover设置变化的width即可.
```javascript
    div1 {
      width: 100px;
      height: 100px;
      border: 1px solid #000;
      transition: width 1s linear;
      -webkit-transition: width 1s linear;
    }
    div1:hover {
      width: 200px;
    }
```
这样, 当我们鼠标hover到div1时候, 其宽度就会在1s内平缓变为200px;