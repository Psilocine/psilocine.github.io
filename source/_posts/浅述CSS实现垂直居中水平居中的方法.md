---
title: 浅述CSS实现垂直居中水平居中的方法
date: 2017-07-25 12:14:08
tags: [CSS,前端]
---
##### 写在前面
最近看面试题有看到类似实现方式

#### 1. 第一种, 也是我最常用的.
    {
      position: absolute;
      left: 0; top: 0; bottom: 0; right:0;
      margin: auto;
    }
这里的margin: auto, 相当于margin-top: 0等四边; 此方法支持ie8-10, 也支持图片居中; 缺点就是要声明宽高;

#### 2. 负外边距.
<!--more-->
    {
      position: absolute;
      width: 300px; height: 200px;
      top: 50%; left: 50%;
      margin-top: -100px;
      margin-left: -150px;
    }

这里的margin负值, 还涉及到另一个问题, 就是top, left和 bottom right 的负值有不一样的效果.  
假设a、b、c是三个inline-block的100px正方形div, 处于中间的b, 设margin-left: -50px的效果是将a的右半边覆盖, 相当于b、c往左平移了50px, 而margin-right: -50px的效果是让c向左平移50px, 将b的右半部分覆盖. 上下同理.  
值得注意的是, 当居中的元素有padding时, 负外边距也要加上padding计算, 不然会偏右下.

#### 3. transform变形  
    {
      position: absolute;
      top: 50%; left: 50%;
      transform: translate(-50%, -50%);
    }
兼容性的话, ie8以下不支持. 而且属性需加浏览器厂商前缀. 如-webkit-transform.
这个方法是最近才学习的方法, 用到css3属性. 和上面方法不一样, 当居中的元素有padding时, 不会影响; 当有margin时, 此方法也会偏右下.

#### 4. display: table方法
    <div class='table'>
      <div class='table-cell'>
        <div class='center-block'>
          
    .table {
      display: table;
    }
    
    .table-cell {
      display: table-cell;
      vertical: middle;
    }
    
    .center-block {
      width: 50%;
      margin: 0 auto;
    }
该方法的缺点就是需要比较多的标签和css, 好处是不用设宽高也能居中. display属性, ie8及以上都支持.

#### 5. 行内块元素
    <div class='box'>
      <div class='center-block'>
    .box {
      text-align: center; //先水平居中
    }
    
    .box:after, .center-block {
      display: inline-block;
      vertical-align: middle;
    }

    .box:after {
      height: 100%;
      content: '';
    }
    
    .center-block {
      width:
      height:
    }
这个方法可能居中的位置并不是那么精确. 需要负外边距来调整. 而且需要一个容器. 此方法支持ie7.

#### 6. calc计算居中
    {
      position: absolute;
      width: 100px;
      height: 100px;
      top: calc(50% - 50px); //运算符前后都需要空格, 不然报错.
      left: calc(50% - 50px);
    }
calc 50%减去宽高的一半就行了, 简单明了. 缺点是不兼容低版本ie, 需要ie9+.

#### 7. Flex布局
    <div class='box'>
      <div class='center-block'>
    
    .box {
      display: flex;
      align-items: center;  //垂直
      justify-content: center;  //水平
    }
    .center-block {
      width:
      height:
    }
也是简单暴力的一种方法, 但是我们知道一条规则, 越简单兼容性就越差, 低版本ie肯定是不支持的, ie9都不支持. 而且属性需加浏览器厂商前缀.