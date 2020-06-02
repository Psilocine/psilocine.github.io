---
title: 关于Flex布局的使用指南
date: 2018-02-23 23:56:51
tags: [CSS, 前端]
---

##### 写在前面

flex 布局, 在移动端也是一大布局利器之一, 跟笔者复习一遍吧.

#### 基本布局

```css
<div class='father'> // flex container
  <div calss='son son-1'>1</div> // flex item
  <div calss='son son-2'>2</div>
</div>

.father {
  display: flex;
}
```

#### flex container 属性

flex 规定 flex container 的横向为主轴(main axis), 纵向为交叉轴(cross axis)
1.flex-direction 指定 flex item 是如何布局的. 默认为 row, 即 flex item 从左至右横向排列

```css
.father {
  ...;
  flex-direction: row / row-reverse / column / column-reverse;
                        (横向右至左) (纵向上至下) (纵向下至上)
}
```

<!-- more -->

2.flex-wrap 指定 flex-item 能否自动换行. 默认为 nowrap, 即子元素不换行排列

```css
.father {
  ...;
  flex-wrap: nowrap / wrap / wrap-reverse ;
                  (多行显示) (多行显示,从下至上显示成多行)
}
```

3.flex-flow 是 flex-direction 和 flex-wrap 的缩写

```css
.father {
  ...;
  flex-flow: row nowrap;
}
```

4.jusitfy-content 指定 flex item 在主轴线上的对齐方式, 即水平方向. 默认为 flex-start, 子元素靠左对齐.

```css
.father {
  ...;
  justify-content: flex-start / flex-end / center / space-between / space-around;
                                (靠右对齐)  (居中)
  space-between: 等间距排列, firstChild和lastChild靠两侧边缘, 如图一
  space-around: 等间距排列, firstChild和lastChild与两侧边缘也有间隔, 如图二
}
```

图一 space-between![space-between](1_space-between.jpg)
图二 space-around![space-around](1_space-around.jpg)

5.align-items 指定 flex item 在交叉轴上的对齐方式, 即垂直方向. 默认为 stretch, 子元素如无定义高度, 将被拉伸以适应父容器(默认 flex-direction 为 row)

```css
.father {
  ...;
  align-items: stretch / center / flex-start / flex-end / baseline (垂直对齐) (在交叉轴起始对齐)
    (于底对齐) (按照子元素里的文本基线对齐);
}
```

flex-start![flex-start](2_flex-start.jpg)
baseline![baseline](2_baseline.jpg)

6.align-content 指定交叉轴上多余空间 默认值为 stretch

```css
.father {
  ...;
  align-content: stretch / center / flex-start / flex-end / space-between / space-around
  center: (居中对齐, 没有多余空间)
  flex-start: (在交叉轴起始对齐,没有行行多余空间)
  flex-end: (于底对齐,没有多余空间)
  space-between: (各行之间留有空白)
  space-around: (各行之前之间之后都留有空白)
}
```

stretch![stretch](3_stretch.jpg)
space-around![space-around](3_space-around.jpg)

#### flex item 属性

1.order 指定排列顺序, 可正可负, 值越小排越前. 默认都是 0

```css
.son-2 {
  order: -1 // 排在son-1前面;
}
```

2.flex-grow 当父元素存在剩余空间时, 可以用来指定子元素的拉伸比例. 默认值为 0, 负值无效
![flex-grow](4_flex-grow.jpg)
3.flex-shrink 当父元素空间不足时, 可以用来指定子元素的收缩比例. 负值无效, 默认为 1, 即会收缩.
如果设置为 0, 就不收缩将保持原来的大小.
![flex-shrink](5_flex-shrink.jpg)
4.flex-basis 为子元素分配初始大小, 默认值为 auto

```css
.son-2 {
  ...,
  flex-grow: 0;
  flex-skrink: 0;
  flex-basis: 400px; // 结合上两个属性, 该元素400px宽度不会变化.
}
```

5.flex 是 flex-grow flex-skrink flex-basis 的缩写, 此属性比较常用

```css
.son-1 {
  flex: 0 0 100px;
}
.son-2 {
  flex: 1 1 auto;
}
// son-1固定宽度100px, son-2自适应
```

6.align-self 指定单个 flex-item 允许更改交叉轴的对齐方式, 即垂直方向.
![align-self](6_align-self.jpg)

#### 注意事项

1.伪元素::first-line ::first-letter 在 flex-container 不起作用
2.float clear vertical-align 在 flex-item 不起作用
