---
title: vscode extension初探
date: 2021-07-05 17:28:33
tags: [前端, vscode]
---

##### 写在前面
越学习vscode的知识，越觉得vscode这个编辑器的强大，也会感觉越学越多。本文简单地探讨一下vscode的构成和vscode extension的用法。

#### vscode 构成
vscode的特性之一是跨平台。是因为它是基于Electron构建的，主要有三部分组成：

* [Monaco Editor](https://microsoft.github.io/monaco-editor/)：基于web的代码编辑器
* [Extension Host](https://code.visualstudio.com/api/advanced-topics/extension-host)：插件管理
* [Language Server Protocol](https://microsoft.github.io/language-server-protocol/) & [Debug Adapter Protocol](https://microsoft.github.io/debug-adapter-protocol/)：两大协议

还有一个特性是轻量级，因为很多功能都是基于插件实现，插件进程并不影响主进程的渲染速度、UI响应。

![vscode](vscode.png)

![LSP](lsp.png)

![DAP](dap.png)

#### vscode extension 要点
vscode插件[能做什么？（官方）](https://code.visualstudio.com/api/extension-capabilities/overview)
* 使用颜色或图标更改vscode的外观
* 在UI界面中添加自定义组件和视图
* 创建一个Webview来显示自定义构建的web页面
* 支持新的编程语言
* 支持调试特定的程序

在我看来：
* 自定义命令、快捷键、菜单选项
* 自定义跳转、自动补全、悬浮提示
* 自定义设置
* 自定义Webview
* 自定义左侧Tree view
* 自定义颜色、图标主题
* 新增语言语法
* 通知、状态栏

当我们开发vscode插件的时候，只需要注意三个要点，就可以对vscode进行功能增强

* Activation Event

> 插件激活的时机，在package.json中声明。
key为activationEvents，value为数组的一组JSON声明。
值得注意的是，激活的时机可以是多条件的。

* Contribution Points

> 插件配置的声明、增强了vscode的什么功能，在package.json中声明。
key为contributes，value为对象的一组JSON声明

* VS Code API

> 普遍在src/extension.ts(js)。
插件中可以调用的扩展JavaScript API。

##### Activation Event
* onLanguage:`${language}` 打开特定语言的文件时激活

eg: onLanguage:typescript、onLanguage:python

![activation_event_1](activation_event_1.png)

* onCommand:`${command id}` 调用命令时激活

eg: onCommand:paperang.helloworld

![activation_event_2](activation_event_2.png)

* onDebug 在启动调试模式时激活
* workspaceContains 当打开的文件夹至少有一个glob pattern表达式匹配的文件时激活

eg: workspaceContains:`**/.editorconfig`、workspaceContains:`**/.prettierrc.js`
* onFileSystem 当读取的文件或文件夹匹配文件方案时激活

eg: onFileSystem:ftp、onFileSystem:ssh
* onView 左侧栏展开特定view id时激活

eg: onView:outline
* onUri 打开系统级的uri时激活（这个URI协议需要带上vscode 或者 vscode-insiders协议

eg:vscode://vscode.git/init、vscode-insiders://vscode.git/init
* onWebviewPanel 匹配到是viewType的webview时激活（需要配合其他事件、如onCommand来创建webview 

eg: onWebviewPanel:catCoding
* onCustomEditor 匹配到是viewType的自定义编辑器时激活 
* *（start up）vscode启动时激活（尽量不用
* onStartupFinished vscode启动一段时间后激活（和*相比，不会减慢vscode的启动速度

![activation_event_3](activation_event_3.png)

##### Contribution Points
* configuration:object 将插件的配置内容暴露出来，用户可以通过UI界面或setting.json修改
```javascript
title:string 插件的展示名称 eg:paperangExtension -> Paperang Extension
properties:object 配置项集合
  [property]:object 配置项
    type number、string、boolean可以在UI界面展示，其余types只能通过json修改
    default:any
    description:string
    markdownDescription:string
    enum:array
    enumDescriptions:array
```

![contribution_points_example](contribution_points.png)

* commands:Array<Object>
```javascript
command:string
title:string
category:string 便于分组
```

![contribution_points_example](contribution_points_2.png)

* menus:object
```javascript
commandPalette:Array<Object> 全局命令面板
explorer/context 资源管理器上下文菜单
editor/context 编辑器上下文菜单
editor/title 编辑器标题栏右侧
editor/title/context 编辑器标题栏
debug/toolbar 调试工具栏
scm/title source control manager 右侧
view/title 标题栏右侧
view/item/context 
```

![contribution_points_example](contribution_points_3.png)

* keybindings:Array<Object>
```javascript
command:string
key:string
mac:string
when:string
```

![contribution_points_example](contribution_points_4.png)

##### VS Code API
VS Code暴露了大几十种[API](https://code.visualstudio.com/api/references/vscode-api)，下述只罗列几个：
* commands:

registerCommand(command:string, callback) 注册command
executeCommand(command) 执行command

* window:

createStatusBarItem(alignment?, priority?:number) 底部导航栏item
createTreeView(viewId:string, options) 左侧菜单栏
createWebviewPanel(viewType: string, title: string) 创建新的webview
setStatusBarMessage(text: string, hideAfterTimeout: number) 底部状态栏消息
show(Error/Infomation/Warning)Message(message: string, ...items: string[]) 底部右侧弹窗，需要交互关闭

* workspace：

getConfiguration 获取配置对象
eg: vscode.workspace.getConfiguration('pomodoro).get(visible')、
vscode.workspace.getConfiguration().get('pomodoro.visible')

#### 实践和发布
1. 安装官方脚手架yo、generator-code
```terminal
$ npm install -g yo generator-code
$ yo code
```
![cli](cli.png)
![cli-builded](cli-builded.png)

2. 完成插件的开发和测试

i. 番茄钟
![paperang-pomodoro](paperang-pomodoro.gif)
ii. 主题色
![paperang-theme](paperang-theme.gif)

3. 本地打包/发布到vscode插件市场

本地打包：不方便发布到市场的插件。如涉密，团队自用
i. vsce package
ii. Install from VSIX

![local_setup](local_setup.gif)

发布[市场](https://marketplace.visualstudio.com/vscode)：
i. 注册[Microsoft账号](https://login.live.com/)
ii. 创建[Azure DevOps](https://aka.ms/SignupAzureDevOps)组织
iii. 创建Token：(security/Personal Access Tokens
)
iv. 注册成为[发布者](https://aka.ms/vscode-create-publishe)：命令行注册已经失效，需网页注册
v. vsce publish
vi. 过几分钟就可以在市场上搜索到了～