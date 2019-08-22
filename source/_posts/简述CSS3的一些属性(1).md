---
title: 简述CSS3的一些属性(1)
date: 2017-06-16 13:55:49
tags: [CSS, 前端]
---
##### 写在前面
这篇文章主要讲css3边框
主要讲一下三个属性,分别是border-radius,border-image,box-shadow.它们都没有继承性.

#### border-radius
首先讨论兼容性问题, 除了ie8以下大部分都兼容, 相信这个属性大家也很常用.

1. border-radius其实是缩写, 如果要定义单个角落的radius的话,应该这么写 border-xxx-xxx-radius, 记住先写上下再写左右.如要定义左上的话应该是这样, border-top-left-radius.
2. 它的值有三种, px,em,百分比. 我们假设div的宽高是一致的如200px, 当所设的值是宽高一半时即100px, 就是最大值了, 此时div得border就是一个正圆.
   百分比就是设50%的时候; em比较特殊,要考虑到当前字体大小, 如果默认字体大小是16px时, 那需要7em即7 * 16 = 112才能变成正圆. 当宽高不一致时, 取其中较小值来衡量.另外经过我的测试发现, 三种值的变化过程和变化结果都是不同的, 即使是正圆. 其中100px和50%也有细微的不同.

<!--more-->
3. border-radius还有另一种写法. eg:border-radius:5px 10px/15px 20px;  
   顺序是从左上开始, 然后顺时针. 即左上的值是5px 15px 右上的值是10px 20px 右下的值是5px 15px, 左下10px 20px;  
   另外要提的是,第一个值和第二个值影响两个方向的边框, 如左上的第一个值5px影响左上的上边框.  
   这种写法可扩展, eg:border-radius:5px 10px 15px/20px 25px; 上5px 左右10px 下15px

#### border-image
兼容性问题与border-radius相同,它有五个属性,source,slice,width,outset,repeat

1. border-image-source, 最容易理解, 边框图片地址, 用url()制定, 默认为none, 不设置会用border-style的值;
2. border-image-slice, 只能用数字和%,px和em在这里会不显示; 还有不允许设负值, 将图片分为9块, 不设置fill的话中间为透明.
3. border-image-width, 规定将图像分割为9个部分, 如果不写就由border的值制定.
4. border-image-outset, 规定让边框背景延伸到盒子外, 不能用%.
5. border-image-repeat, 默认stretch,即拉伸的意思, 还有另外两个值, repeat和round,二者显示效果差不多.

#### box-shadow
兼容性最差, ie11才兼容, 其他浏览器也要加前缀, -o-box-shadow, -webkit-box-shadow.

1. 有六个值, x,y,blur,spread,color,inset.  
   x,y设置阴影的位置, 可负值.
   blur 可选, 让阴影模糊.  
   spread 可选, 阴影延伸的尺寸.  
   color 可选, 阴影的颜色.  
   inset 可选, 将阴影设为内部阴影.
2. 可以给每一边阴影不同的颜色, 效果相当于发散模糊的border, 用x,y来控制即可, 注意要用逗号隔开.  
   eg:box-shadow: -10px 0 10px red,10px 0 10px yellow,0 -10px 10px blue,0 10px 10px green; 四边不同颜色的边框.
3. 可以多层box-shadow嵌套, 但是上一层的spread不能大于下一层.  
   eg:box-shadow: 0 0 10px 10px(spread) red, 0 0 10px 20px blue;



