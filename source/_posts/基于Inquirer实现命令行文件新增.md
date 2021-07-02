---
title: 基于Inquirer实现命令行文件新增
date: 2021-04-29 13:58:11
tags: [前端, JS]
---

##### 写在前面
由于我们很多移动端项目是hybrid实现方案的特殊性，导致了多入口项目配置。

当每次新业务需求来临的时候，需要：

1. 手动配置入口文件、新增项目文件夹和文件
2. 从老页面拷贝入口文件代码（统一的全局依赖、组件等）

如果能够把这部分机械工作解放，那是十分好的，因此该脚本诞生。

#### Inquirer概述

##### Inquirer是做什么的？
![Inquirer](Inquirer_icon.png)
Inquirer.js试图为NodeJs做一个可嵌入式的美观的命令行界面，它是非常容易去处理以下几种事情的：

* 提供错误回调
* 询问操作者问题
* 获取并解析用户输入
* 检测用户回答是否合法
* 管理多层级的提示

Inquirer采用问答的命令行形式呈现，问题对象具体配置项：

![example](example.png)

* type：（String）提示的类型。默认值：input-可能的值：input，number，confirm， list，rawlist，expand，checkbox，password，editor
* name：（String）将答案存储在答案哈希中时使用的名称。如果名称包含句点，它将在答案哈希中定义路径。
* message：（String | Function）要打印的问题。如果定义为函数，则第一个参数将是当前查询者会话答案。默认值为name（后面跟冒号）。
* default：（String | Number | Boolean | Array | Function）如果未输入任何内容，则使用默认值，或者返回默认值的函数。如果定义为函数，则第一个参数将是当前查询者会话答案。
* validate：（功能）接收用户输入并回答哈希。true如果该值有效，则应返回，否则返回错误消息（String）。如果false返回，则提供默认错误消息。
* filter：（功能）接收用户输入并回答哈希。返回要在程序内部使用的过滤值。返回的值将添加到Answers哈希中。
* transformer：（功能）接收用户输入，回答哈希和选项标志，并返回转换后的值以显示给用户。转换仅影响编辑时显示的内容。它不会修改答案哈希。
* when：（Function，Boolean）接收当前用户的答案哈希，并应返回true或false取决于是否应询问此问题。该值也可以是一个简单的布尔值。
* choices：（Array | Function）Choices数组或返回choices数组的函数。如果定义为函数，则第一个参数将是当前查询者会话答案。数组值可以是简单的numbers，strings或objects含有一个name（将在一览显示），一个value（在答案散列保存），和一个short（以后选择显示）属性。choices数组也可以包含一个Separator。

> 值得注意的是，每个type类型需要的配置项是不一样的，如type为checkbox时，需要配置choices；type为number时则不需要

#### 打开方式

##### 流程如下
1. npm run new

2. 依次输入7个问题：

* 项目名
* 是否P3项目
* 页面入口
* 页面标题
* 是否需要数学公式
* 是否需要页面loading
* 是否需要页面通信lsbridge

3. 创建成功/创建失败

##### 会遇到的情况
* 创建失败：项目名重复

![situation_1](situation_1.png)
* 创建失败：文件名重复

![situation_2](situation_2.png)
* 创建成功

![situation_3](situation_3.png)

创建成功后，在对应目录写入对应文件和在配置文件（打包的入口entry）配置项目信息
![result](result.png)
![result_2](result_2.png)

#### 代码实现
首先依赖引入inquirer、chalk（命令行美化）、fs（文件读取写入）。

逻辑我们分三部分：
1. 判断项目名是否同名
2. 判断项目是否为P3项目
3. 走完后续问题对象的输入

![code](code_1.png)

isProjectExist方法是去遍历配置入口的所有项目名，一旦命中则退出脚本

![code_2](code_2.png)

判断项目是否为P3项目

![code_3](code_3.png)

setEntry目的是为了获取到正确的文件路径，如果项目都新增在同一个文件夹中，则不需要这一步

![code_4](code_4.png)

走完后续问题对象的输入：
写入文件将默认的模版字符串写入，把需要替换的字符串如图中的`__placeholder__`替换成命令行输入的项目名即可。

