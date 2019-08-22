---
title: 搭建hexo的坑
date: 2017-05-13 18:51:17
tags: HEXO
---
##### 写在前面
在搭建blog时的一些坑, 写下来方便大家查阅

1.一切都弄好后要部署到github上时, 出现ERROR Deployer not found: git
  需要在输hexo d指令前 先输入 npm install hexo-deployer-git --save, 安装成功后再部署.

2.hexo d部署到一半发生错误, 出现fatal: could not read Username for ‘https://github.com‘: Invalid argument
  我的解决方法是在根目录的_config.yml里deploy的repo设置成了http, 改回SSH即可.