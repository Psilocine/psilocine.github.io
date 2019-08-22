---
title: 简述CSS3的一些属性(3)
date: 2017-08-18 13:26:12
tags: [CSS, 前端]
---
##### 写在前面
这篇文章主要讲css3字体、transform的2D转换
首先是字体@font-face, 2D转换的translate(), rotate(), scale(), skew(), transform-origin, 还有大魔王matrix().
2D转换的属性都没有继承性.

#### @font-face
ie9及以上都支持, 但只支持.eot后缀字体. font-face的出现, 字体就没必要在用户用户端上提前安装好了.

    @font-face {
      font-family: fontName; // 一个变量名
      src: url("fontTrueName.ttf"), // 字体名
      url("fontTrueName.eot");
    }
    div {
      font-family: fontName; // 引用字体的变量名即可
    }
<!--more-->
注意, 如果要用字体的不同字体, 如粗细, 还需要再添加一个@font-face, 而且src的字体也要是新的粗字体, 还要加上font-weight: bold等, 而且变量名一定要一样.
#### transform
ie10+, chrome,safari加-webkit-, ie9加-ms-(仅适用于2D转换), 厂商前缀都加载transform上. eg:-ms-transform: translate()

* transform-origin
1. 两个参数, 用法: transform-origin: x y; 初始值是50% 50%; 因此是需要变换的中心点, 下面都说基点.
2. 可以规定变换的基点, 如0% 50%, 就是x轴0, y轴是变换物体高度的一半, 基点就是正左边缘点, 所有变换都会以正左为中心点旋转, 斜拉, 缩放(位移也是, 虽然看不出来).
3. 参数除了百分数, 也可以用px像素, 也可以直接用left, right,(x轴) top, bottom,(y轴) center(x, y均可)来指定. 一般如果基点要求不是非常苛刻, 用left, right这些比较简单明了.

* translate() 位移
1. 两个参数, 用法 transform: translate(10px, 20px); 表示向右移10像素, 向下移动20像素.
2. 如果只有一个参数, 那只在x轴移动即左右移动, 所以这个属性也能赋予负值.

* rotate() 旋转
1. 一个参数, 用法 transform: rotate(90deg); 表示顺时针旋转90度, 单位是deg(度 Degress的缩写), 可负值.
2. 基点如果不是默认的话, 围绕所设基点位置旋转, 而且基点可值大于旋转物体的宽高值, 即设200% 200%, 会围绕变换物体以外的点旋转.

* scale() 缩放
1. 两个参数, 用法 transform: scale(2, 1); 表示宽度变为原来的两倍, 高度一倍不变.
2. 只有一个参数时, 表示同时给定宽高的倍数. 可以负值, scale(-1, -1)相当于rotate(180deg)
3. 如果基点不是默认, 如上述0% 50% 正左基点, 那么x轴上的缩放倍数, 如scale(2, 1)物体的左边位置不变, 往右扩展一倍. 读者可自行测试.

* skew() 斜拉
1. 两个参数, 用法 transform: skew(0deg, 0deg); skew(180deg, 180deg)和skew(0deg, 0deg)效果一样.
2. 180deg一个循环, 当参数是90deg时候, 变换物体将被拉伸至消失, 想象一下高中时期的tan(90°), 因此两个参数有一个是90deg, 物体都会消失不见.
3. 第一个参数表示左右偏移, 给定度数是正数时, 容器会往左上和右下横向拉扯. 负数相反. 第二个参数为正数时, 往左上和右下纵向拉扯. 负数相反.
4. 如果基点不默认, 那基点位置将不变, 位置不动的情况下拉扯.

* 大魔王matrix()
1. 写在前面, 其实上述介绍的四种方法, 无论拉伸斜拉还是啥, 本质都是调用matrix()方法实现.
2. matrix() 有6个参数, 别怕, 下面慢慢分析. 假设参数分别是a, b, c, d, e, f
```
      a c e   
    [ b d f ]
      0 0 1
```
将6参看成矩阵, 结合之前的transform-origin的两个参数x y来看
```
      a c e    x        ax + cy + e 
    [ b d f ][ y ] => [ bx + dy + f ] // 矩阵相乘 
      0 0 1    1         0 + 0 + 1
```
ax + cy + e代表水平坐标, bx + dy + f代表垂直坐标.
3. 位移 eg matrix(1, 0, 0, 1, 30, 30) 基点默认时的坐标是(0, 0),即物体中心点. ax + cy + e => e = 30, bx + dy + f => f = 30, 相当于translate(30px, 30px). 上述可证明matrix后两位的参数分别表示, 水平偏移距离, 垂直偏移距离.
2. 缩放 eg matrix(s, 0, 0, s, 0, 0) 基点默认, ax + cy + e => sx , bx + dy + f => sy, 相当于scale(sx, sy).
3. 旋转 设旋转角度为α, eg matrix(cosα, sinα, -sinα, cosα, 0, 0)基点默认. ax + cy + e => xcosα - ysinα , bx + dy + f => xsinα + ycosα. 一般情况下, 用rotate(αdeg)就可以了, 方便简单.
4. 拉伸 eg matrix(1, tan(αy), tan(αx), 1, 0, 0). ax + cy + e => x + ytan(αx) , bx + dy + f => xtan(αy) + y. 相当于skew(αx deg, αy deg). αx表示x轴倾斜的角度.
