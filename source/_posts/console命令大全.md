---
title: console命令大全
date: 2018-05-10 14:45:18
tags: 前端
---
##### 写在前面
console.log, console.time相信大家都不陌生, 平时除了打断点就是打log来调试, 可谓是居家旅行必备神器. 今天就来扒扒除了以上常见的还有哪些可能其貌不扬的命令.

#### log
1. log是笔者平日里用得最多的命令了, 如下图一, 可以直接打印出我们需要的变量; 可以定死内容打印出来用来知道是否执行至此; 可以结合字符串更清晰地在控制台找到输出内容.
<!-- more -->
![log_1](log_1.jpg)
2. 此命令大家很熟悉了, 只有一点需要注意: 结合字符串时, 除了和对象共用会出现[object Object]而显示不出正确内容外, 和其他的类型都能良好搭配. 这是因为object类型和string类型相加时, 会触发toString()方法(其他类型也会.
![log_4](log_4.jpg)
除了log外, 还有info, warn, error, 其实和log本质是一样的, 只不过在输出是加个icon和背景色.
![log_2](log_2.jpg)
在Ff火狐下, info也有图标.
![log_3](log_3.jpg)
3. log还支持占位符, 如下图. 但只支持字符串（%s）、整数（%d或%i）、浮点数（%f）和对象（%o）
![log_5](log_5.jpg)

#### time
1. 此命令可以用来知道一段代码所耗时间, 需要结合console.timeEnd()来闭合代码段. 需要注意的是, 两行命令的参数变量名需要一样, 如下是test, 才能正确判断代码的起始结束位置
![time_1](time_1.jpg)

#### group
1. 该命令结合console.groupEnd()可以起到分组的作用.
![group_1](group_1.jpg)
2. 有时候代码log太多会眼花缭乱, 有了这个可以快速定位到自己想看的log.
![group_2](group_2.jpg)
3. 需要注意的有两点, 结束命令不用像time指令加参数; 其二是如果把"group"换成"groupCollapsed", 则默认为折叠运行结果.

#### dir
1. 用来查看对象的信息, 但在chrome浏览器下好像会展示折叠的状态, 因此和log似乎没差? 在Ff下, dir命令可以自动展开, 比较一目了然.
![dir_2](dir_2.jpg)
![dir_3](dir_3.jpg)
2. 如果查看Dom元素, console.log()以HTML的形式输出, 而console.dir()则会以JSON对象的形式输出.(在chrome下, 实测在Ff下都是以json对象输出)
![dir_1](dir_1.jpg)

#### assert
1. console.assert()类似于单元测试中的断言, 当表达式为false, 输出错误并抛出一个异常. 但是如果你的代码有多个assert命令, 会显得难以区分. 因此assert还支持传第二个参数, 当有输出时依仗第二个参数的值.
![assert_1](assert_1.jpg)
![assert_2](assert_2.jpg)

#### count
1. 调试代码时，我们经常需要知道一段代码被执行了多少次(当然可以用log), 但是count命令存在的本质就是为了计数. 还有log如果是在一起的话, 会累加成一行.
2. count命令也可以传值来作为label, 当然不传也会有个default的label.
![count_1](count_1.jpg)

#### table
1. table命令能轻松将json表格化, 让开发者更直观地看到数据. 在chrome浏览器下, table head还支持正序倒序的排列, 十分方便.
![table_1](table_1.jpg)
2. 值得注意的是, 在chrome下如果json没有该属性会显示空, 值是"", 显示"", 只有在值是undefined才会显示undefined; 而在Ff下, 没有属性显示undefined, 值是""显示空, 值是undefined显示undefined.
![table_2](table_2.jpg)
![table_3](table_3.jpg)

#### trace
console.trace()用来追踪函数的调用轨迹, 可谓是神器, 特别是维护大型项目的时候.
![trace_1](trace_1.jpg)