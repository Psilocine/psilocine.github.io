---
title: 从线性渐变属性探究 CSS 数据类型的重要性
date: 2021-09-13 10:38:59
tags: [CSS]
---

## 为什么要关注 CSS 数据类型

首先要知道一个前提，过去 CSS 按照大版本的方式进行迭代，如果有一个 CSS 特性存在争议，就会使该版本的发布时间延后。例如 CSS2.1 的规范在 2002 年开始制定，而一直到 2011 年 4 月才成为标准，效率十分低下。

为了加速没有争议的 CSS 特性的标准化，W3C 的 CSS 工作组决定将 CSS 模块化。带来的好处就是浏览器支持的特性越来越多，越来越快。

所以以后就没有 CSS4 这一说了，其实 CSS3 也已经解体了。

短时间内大量的 CSS 新特性出现，恰恰如果按之前靠死记 key-value 的形式，这将是坏处。好在 CSS 新的特性有了明确的 CSS 数据类型划分，因此只需要记忆 CSS 数据类型即可

<!-- more -->


## 什么是 CSS 数据类型

一个 CSS 数据类型和用法由三部分组成：

* 数据类型名称：保守估计有50个，如 gradient、color
* keyword：关键字，如 to、from、at、ease、auto
* 符号：分为字面符号，组合符号，数量符号

举个例子， `<gradient>` 的线性渐变 `linear-gradient` 的语法：
```javascript
linear-gradient(
    [ <angle> | to <side-or-corner> ,]? <color-stop-list>
)
```

由上得知，线性渐变的语法包括了三个 CSS 数据类型，包括 `<angle>`、`<side-or-corner>` 和 `<color-stop-list>`。只要我们了解这三个数据类型的用法，哪怕没用过线性渐变属性，也能轻松应对。

![666](//github.com/Psilocine/TechMeme/blob/main/meme/common/666.png?raw=true)

### 符号

为了看懂 CSS 数据类型，我们必须先了解符号的意义

字面符号

|  符号  | 名称  | 描述 |
|  ----  | ----  | ---- |
| , | 并列分隔符 |  用来分隔并列值，或者分隔函数的参数值  |
| / | 缩写分隔符 | 用来分隔一个值的多个部分，在CSS缩写用于分离类型相同且属性不同的值。如 font 缩写的 font-size / line-height |

组合符号

|  符号  | 名称  | 描述 |
|  ----  | ----  | ---- |
|  | 并列  | 普通空格字符，表示各部分必须按顺序出现 |
| && | 与 | 各部分必须出现，但可以不按顺序 |
| \|\| | 或 | 各部分至少出现一个，但可以不按顺序 |
| \| | 互斥 | 各部分只能出现一个 |
| [] | 方括号 | 优先级最高 |

数量符号

|  符号  | 名称  | 描述 |
|  ----  | ----  | ---- |
| | 无数量符号 | 出现一次 |
| * | 星号 | 可以出现任意次数 |
| + | 加号 | 可以出现 1 次或多次 |
| ? |问号 |	可以出现 0 次或 1 次 |
| {A, B} |	花括号 |	出现最少 A 次，最多 B 次 |
| # |	井号 |	可以出现一次或多次，但多次出现必须以逗号分隔 |
| ! | 感叹号 |	表示当前分组必须产生一个值，该符号多出现在方括号 [] 的后面 |

![no_technology](https://raw.githubusercontent.com/Psilocine/TechMeme/main/meme/common/common_02.jpg)

## 管中窥豹：`<gradient>`

通过查阅 [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/gradient) 得知，`<gradient>` 类型有四种值：
* 线性渐变 linear gradient
* 径向渐变 radial gradient
* 重复渐变 repeating gradient
* 锥形渐变 conic gradient

由于篇幅问题，本文只讨论线性渐变

### 线性渐变 linear gradient

角度默认值为 to bottom。刚刚提过，语法是
```javascript
Syntax: linear-gradient(
	[ <angle> | to <side-or-corner> ,]? <color-stop-list>
)
```

下面学习涉及的 CSS 数据类型：

`<angle>` 表示角度的大小。有正负符号，数值为0时，单位可以省略。单位有：
* deg（degress, 角度）360deg = 完整的圆
* rad（radians，弧度）2π * rad = 360deg
* turn（turns，圈数）1turn = 360deg
* grad（gradians，百分度）400grad = 360deg

---

`<side-or-corner>` 表示边角类型。主要用在线性渐变中。
* Syntax：[ left | right ] || [ top | bottom ]

---

`<color-stop-list>` 表示多个 `<linear-color-stop>` 的集合。
* Syntax: [ `<linear-color-stop`> [, `<color-hint>`? ]? ]#, `<linear-color-stop>`

---

`<linear-color-stop>` 表示颜色断点，多出现在渐变中。
* Syntax：`<color>` [ `<color-stop-length>` ]?

---

`<color>` 表示色值，可以是：
* 颜色关键字 blue, transparent, etc.
* currentColor
* rgb()、rgba()
* hsl()、hsla()

---

`<color-hint>` 表示渐变在相邻颜色之间如何进行插值，如果省略，颜色过渡的中点是两个颜色之间的中点。

---

`<color-stop-length>` 表示颜色的数值参数。
* Syntax：[ `<percentage>` | `<length>` ]{1, 2}

---

`<percentage>` 表示百分比数值。有正负符号。

---

`<length>` 表示距离值。
* 相对长度单位：em, rem, ch, ex
* 视区相关长度：vh, vw, vmin, vmax
* 绝对长度单位：px, cm, mm, in, pc, pt

![can't learn anymore](https://github.com/Psilocine/TechMeme/blob/main/meme/common/common_06.jpg?raw=true)

不知不觉掌握了好多 CSS 数据类型了呢，想想还觉得挺激动的。有了上面的背书，我们就直接下手看这个属性。

1. `background: linear-gradient(pink, skyblue);` 上述有提到默认值是 to bottom，第一个值 pink 的 `<color-stop-length>` 默认值是 0%，最后一个值 skyblue 默认值为 100%，所以效果是这样的：
![3](https://saas-base.cdnjtzy.com/sns/20210910/3c0167b39676f6f31041f1824ac74abf.png)
等效于 `background: linear-gradient(to bottom, pink 0%, skyblue 100%);`

2. `background: linear-gradient(0.25turn, pink, skyblue);` 就是`to right`
![4](https://saas-base.cdnjtzy.com/sns/20210910/080dbea3e84048f6295ce50d3e7807bd.png)

3. `<color-hint>`
```css
/* 指定了<color-hint>在 50%，所以能见到明显的分割线，不会有渐变的过程 */
background: linear-gradient(0.25turn, pink 50%, 50%, skyblue);
/* 等同于 */
background: linear-gradient(0.25turn, pink 50%, skyblue 50% 100%);
```
![5](https://saas-base.cdnjtzy.com/sns/20210910/edfc7a26742a3b91ed63bfcac4908b2d.png)

4. 拓展
```css
background-image: linear-gradient(217deg, rgba(255,0,0,.8), rgba(255,0,0,0) 70.71%),
linear-gradient(127deg, rgba(0,255,0,.8), rgba(0,255,0,0) 70.71%),
linear-gradient(336deg, rgba(0,0,255,.8), rgba(0,0,255,0) 70.71%);
```
![7](https://saas-base.cdnjtzy.com/sns/20210910/1b7fabfbd77ee67f4fec6f6d48097fd4.png)

从上面的例子，我们可以反推 background-image 语法的数据类型可能有 `<gradient>#`，通过查阅 [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/background-image#bg-image) 得知，确实如此。

![nice](https://github.com/Psilocine/TechMeme/blob/main/meme/common/common_05.jpg?raw=true)

## 结论
CSS 数据类型能够帮助我们快速知道某个新特性的 value，减少错误赋值，可以说是一大利器。

比如我们不认识下面两个属性，但是通过对 `<color>` 的认识，就可以知道该属性能够赋予的 value 有哪些了～
1. accent-color: `<color>`
2. caret-color: `<color>`

![ending](https://github.com/Psilocine/TechMeme/blob/main/meme/common/ending.gif?raw=true)


参考资料：
* [MDN: CSS data types](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Types)
* [张鑫旭](https://www.zhangxinxu.com/wordpress/2019/11/css-value-type/)
