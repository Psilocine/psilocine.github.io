---
title: 简述CSS3的一些属性(2)
date: 2017-08-16 17:49:31
tags: [CSS, 前端]
---
##### 写在前面
这篇文章主要讲css3背景、文本效果
六个属性,背景background-size, background-origin, background-clip, 它们都没有继承性.  
text-shadow, word-wrap, word-break, 都能被继承.

#### background-size
ie9及以上都支持, 主流浏览器支持.

* 四种负值方法, length/%/cover/contain
   length: 设置宽高, px/em/rem都可以使用. 如果只给一个值, 第二个值也就是高会默认auto. 当然你也可以手动background-size:auto 100px; 让宽自动匹配.  
   %: 值得注意的问题是这里的百分比并不是背景图像自身的宽高, 而是承载这个图像的容器的宽高. 100% 100%;就是覆盖整个容器.  
   cover: 保持图像的比例拉伸, 完全覆盖容器. 要注意, 容器如果是宽高比例差很多的话, 只会显示极小一部分, 读者可自行想象.  
   contain: 保持图像的比例大小拉伸, 当一侧到达容器的100%时, 就不在扩大.
   
<!--more-->
#### background-origin, background-clip
ie9及以上都支持, 低版本浏览器需要加厂商前缀.
之所以把这两个css3属性放在一起讲, 主要是因为这两个的显示效果几乎一致. origin是指定从哪里开始绘制背景, clip则控制背景的显示.

1. 二者属性一致, border-box(clip默认值), padding-box(origin默认值), content-box.
   border-box, 顾名思义, 所设背景从border开始显示, 但是如果容器没设border, 和padding-box的显示效果一样, 同理没有padding, content-box和padding-box显示效果一致.(origin,clip都是)
2. 当二者属性同时写时, (background-clip:padding-box; background-origin:border-box;) 你会发现背景怎么从padding开始绘制, 不用质疑, 上述背景肯定是从border开始, 但是clip是控制背景的显示的, 所以只能有padding和content内容区域显示, 但是会少掉左上border宽度的一截  
   读者测试时, 可以将边框dashed, 会清晰很多.
3. clip制定背景可以覆盖到什么位置, 在哪片区域显示, origin指定背景从什么位置开始. 最后要注意clip的默认值是padding.

#### text-shadow
兼容性: ie10+, 属性和[简述CSS3的一些属性(1)](https://psilocine.github.io/2017/06/16/%E7%AE%80%E8%BF%B0CSS3%E7%9A%84%E4%B8%80%E4%BA%9B%E5%B1%9E%E6%80%A7%281%29/)中的box-shadow差不多.

1. 属性: x, y, blur, color.  
   x, y: 水平垂直阴影位置, 往右下移动, 可以负值.  
   blur: 可选, 模糊程度.  
   color: 可选, 默认黑色.
2. 其实这个属性在css2中存在过一段时间, 最后被抛弃了, 不过在css3回来, 可见text-shadow还是很重要的. 主要能用它完成一些讨巧的阴影效果.
3. 与box-shadow一样可以多层嵌套. 用逗号隔开, 确保下层偏移比上层偏移大就可以了.

#### word-wrap, word-break
主流浏览器都兼容二者(ie6也支持)

1. word-wrap属性允许长单词或者url换行, 当有border的容器里面, 不设置word-wrap, 长单词会无视容器宽度而溢出, 这里是长单词本身比容器宽度大的情况.
2. 通过word-wrap: break-word设置, word-wrap的默认值是normal, 让浏览器自行判断是否换行, 当它看到一个单词放不下了, 就会换行, 但是如果换行了还是比容器宽度大, 就是1.情况, 那就只能溢出了.
3. 这时候可以设置word-break: break-all; 二者的区别就是word-break:break-all不将单词换行再拆分, 上述说了word-wrap会先将长单词换行后拆分, 这会导致上一行后面有一大段留白, word-break就能很好解决, 增加浏览时的体验. 如果讲得不透侧, 还是很有必要自行测试一下的.