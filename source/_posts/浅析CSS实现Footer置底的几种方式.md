---
title: 浅析CSS实现Footer置底的几种方式
date: 2017-11-05 22:40:51
tags: [CSS,前端]
---
##### 写在前面
Footer置底就是让footer部分始终能在浏览器窗口的最底部.
1. 当页面内容超过vh时候, footer能够被推倒页面底部;
2. 当页面内容不够vh时, footer能保持在浏览器窗口底部.

### 将内容部分的底部外边距设为负数
把内容content部分最小高度设为100%. 当content部分高度比浏览器高度小时, content负边距能让footer保持在浏览器窗口底部.
注意负边距数值应和.push, .footer高度一致
<!-- more -->
```css
html, body {
  height: 100%;
  margin: 0;
}

.content {
  min-height: 100%;

  /* 等于footer的高度 */
  margin-bottom: -50px;
}
.footer, .push {
  height: 50px;
}
```
```html
<body>
  <div class='content'>
    content
    <div class='push'></div>
  </div>
  <div class='footer'></div>
</body>
```

### 页脚顶部外边距设为负数
和第一种方法大同小异, 需理解margin顶部底部负值的差异. 
注意如果content有背景或背景颜色, 用margin-bottom更佳, 因为用padding-bottom, footer会与背景重叠.
```css
html, body {
  height: 100%;
  margin: 0;
}
.wrapper {
  min-height: 100%;
}
.content {
  padding-bottom: 50px; /* margin-bottom ? */
}
.footer {
  margin-top: -50px; /* 和padding-bottom数值一致 */
}
```
```html
<body>
  <div class='wrapper'>
    <div class='content'>
      content
    </div>
  </div>
  <div class='footer'></div>
</body>
```

### calc()设置内容高度
上述两种方式都需要额外的标签来实现, 使用css3的计算函数能够轻松实现
缺点是只兼容高版本浏览器.
```css
.content {
  min-height: calc(100vh - 50px); /* 注意(-)之间空格, 后者是footer高度 */
}
.footer {
  height: 50px;
}
```
```html
<body>
  <div class="content">
    content
  </div>
  <div class="footer"></div>
</body>
```

### flexbox弹性布局
以上三种方法的footer高度都是已知的, 或者说是固定的, 不利于页面布局. 因为一旦内容、页脚超出固定高度就会破坏布局.
flex同样不支持低版本ie.
```css
html {
  height: 100%;
}
body {
  min-height: 100%;
  display: flex;
  flex-direction: column;
}
.content {
  flex: 1;
}
```
```html
<body>
  <div class='content'>
    content
  </div>
  <div class='footer'></div>
</body>
```

### Grid网格布局
也是强大的布局, 但是目前兼容性最差, 只能在chrome和ff上使用.
```css
html {
  height: 100%;
}
body {
  min-height: 100%;
  display: grid;
  grid-template-rows: 1fr auto;
}
.footer {
  grid-row-start: 2;
  grid-row-end: 3;
}
```
```html
<body>
  <div class='content'>
    content
  </div>
  <div class='footer'></div>
</body>
```