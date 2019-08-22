---
title: 'hexo d时遇到的新错误'
date: 2017-05-21 01:21:14
tags: HEXO
---
##### 写在前面
我发现github pages不能被百度索引, 想解决这一方法, 发现可以用托管到coding.net来解决
按照网络上的步骤, 我在 _config.yml里repo设置了多个仓库以后, hexo d就出现了如下错误

1. permission denied publickey
2. fatal: could not read from remote respository

解决方法网上有很多种, 但是我都失败了. 最后是把.ssh的id公钥私钥删掉, 用ssh-keygen -t rsa -C '你的邮箱名'生成新的key, 在github和coding新建后, 发现还是不行, 把repo里的coding仓库删掉以后即可.
不知道这算不算解决, 不过我还是放弃了同步多个仓库这个想法
<!--more-->

2017年12月13日
1. 一个警告 lf will be replaced by crlf xxx
原因是因为windows和linux平台的空格不一样, lf是linux下的, crlf是windows下的, 我的解决方法是hexo init后即可