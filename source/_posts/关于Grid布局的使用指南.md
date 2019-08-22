---
title: 关于Grid布局的使用指南
date: 2017-12-16 23:17:19
tags: [CSS,前端]
---
##### 写在前面
之前讨论过footer置底的几种方法, 里面谈及到能通过Grid布局实现. 而最近各大浏览器又频频宣布对Grid的支持, 可想而知Grid会是继Flex的强大后起之秀.
正好笔者也对Grid了解甚少, 正好来个粗略掌握.

#### 术语
下面例子属性看不懂不要急, 我会慢慢解释
```html
/* 第一个例子 */
.container {
  display: grid;
  grid-template-rows: 100px 100px 100px;
  grid-template-columns: 150px 150px 150px;
  text-align: center;  
  font-size: 40px;
}
.item {
  border: 1px solid #000;
  display: inline-grid;
  align-items: center;
}

<div class="container">
  <div class="item item-1">1</div>
  <div class="item item-2">2</div>
  <div class="item item-3">3</div>
  <div class="item item-4">4</div>
  <div class="item item-5">5</div>
  <div class="item item-6">
    <span class="sub-item">6</span>
  </div>
</div>
```
<!-- more -->
* <font size=4>网格容器(Grid Container)</font>
应用 display: grid布局 的元素就是网格容器, 上述div.container为网格容器

![A Grid Item](origin.jpg)
* <font size=4>网格项(Grid Item)</font>
网格容器的第一级子元素, 上述的div.item都是, span.sub-item则不是, 其实很好理解, 如果Grid布局影响所有子元素, 那布局就乱了套.

* <font size=4>网格线(Grid Line)</font>
细心的同学在上面的图片已经看出, Grid布局就是通过纵横线来定位, 即图中虚线. div.container属性有grid-template来初始网格线的数量. 而grid-template模板的意思, 上述规定有三行/row, 每行高100px; 三列/column, 每列宽150px, 四横线四纵线一共绘制成九格, 网格项按顺序排列. 横纵线起始为1.  
值得注意的是, 图中虚线是查看dom就能看到的, 而且如果所设grid-template少于网格项的话, 如两行两列, 结果会遵循列数, 宽度不变, 高度只能靠内容撑起了.

* <font size=4>网格单元格(Grid Cell)</font>
相邻横网格线和相邻列网格线围成的区域就是网格单元格, 上诉指出的div.item-6就是网格单元格.

![A Grid Area](grid-area.jpg)
* <font size=4>网格区域(Grid Area)</font>
那不相邻的网格横纵线组成的区域, 就是网格区域, 如图4 5 6彩色区域, 即为行网格线2到3, 列网格线1到4所划区域.

#### 属性
* <font size=4>grid-template</font>
用来定义网格的列和行, 可以是像素值, 百分比, fr(等份网格容器中可用空间, eg: grid-template-rows: 1fr 1fr; 会二等分). 也可以auto留出自适应长度.
```css
.container{
    grid-template-columns: 40px auto 50px;
    grid-template-rows: 25% auto 100px;
}
```
![Grid Template: auto](grid-template_auto.jpg)
同时支持缩写, grid-template: <rows> / <columns> 上面两行代码可写成 grid-template: 25% auto 100px / 40px auto 50px

* <font size=4>grid-template-areas</font>
网格项通过 grid-area 属性来定义名字模板, 这时候就能在grid-template-areas里来引用名字模板.
```html
/* 在第一个例子基础上 */
.container {
  grid-template-areas: 
    "header header header"
    ". main ."
    "footer footer footer";
}
.item-1 {
  grid-area: header;
  background: red;
}
.item-2 {
  grid-area: footer;
  background: green;
}
.item-3 {
  grid-area: main;
  background: orange;
}
```
![Grid Template Areas](grid-template-areas.jpg)
可以看出, "."代表空的网格单元格, 三个grid-area布完局后, 多余的网络项也按顺序布局, 所以才有div.item-4, div.item-5填充在"."里, 同时也能看出多余的网络项高度只能由内容撑起. grid-template-areas属性的出现, 结合grid-template-rows/columns能很方便实现aside/main等复杂布局.

* <font size=4>grid-gap</font>
用来制定Grid Cell单元格的间距, grid-gap: (grid-row-gap:行与行之间间隙) (grid-column-gap:列与列间隙), 以上是缩写形式, 也可以单独定义.
```html
/* 在第一个例子基础上 */
.container {
  grid-row-gap: 20px;
  grid-column-gap: 10px;
}
```
![Grid Gap](grid-gap.jpg)
如上图可见Grid Cell被隔开了指定距离.


