---
title: 我搭建java后台遇到的一些难题
date: 2017-11-23 23:53:07
tags: [JAVA,后端]
---
##### 写在前面
在做一个前后端分离项目, 需要自己搭建后端, 此文章记录我艰辛的填坑之路

* idea 16版本里terminal的git commit
git commit -m '不能用单引号', 报错. 用双引号即可

* 安装gem. 国内gem被和谐掉, 需要代替原镜像源报错
Error fetching https://ruby.taobao.org/: 原因是taobao gems 源已经下架, 需要换成http://gems.ruby-china.org/

* yum和apt-get
centos下是yum, ubuntu才是apt-get, 看到apt-get命令找不到不要怀疑自己, 可能只是系统不一样

<!-- more -->

* gem安装报错Error installing xxx:
这是因为装了ruby没装ruby-devel, 再装个ruby-devel即可

* gem安装指定版本
和yum, npm 不一样, gem不是通过@来指定, 是gem install xxx -v 版本号

* db.driverLocation在windows下的小问题
db.driverLocation: windows下路径 '\'需要转义 如 E:\\xxx,或 E:/也行. 另外文件名后缀要补全, 笔者就是因为双击文件名复制导致'.jar'没有复制上, 疯狂报错(uri is not registered).

* node-sass安装
十分坑, 用registry淘宝源没用, 会提示转回node的git地址下载. 经查询才知道需要 SASS_BINARY_SITE=https://npm.taobao.org/mirrors/node-sass/ npm install node-sass@版本号

* ftp远程连接500 oops
1. 在安装了ftp的前提下, 需确认服务器的防火墙没有关掉20,21端口. 这里提一句, 安装ftp前要确定没有原本有无ftp, 不然你会start ftp一直失败, 原因是已经有一个ftp版本占据了21端口. 言归正传, 进入iptables看有没关掉端口, 改完需重启防火墙.
2. 端口开启, 这时候要检查下ftp账号是不是有误.
3. 账号无误, 去nginx配置文件看是不是路径有问题.

* sql-where语句
如果是多个条件语句的话, 不能用逗号分隔, 只能用 'and' , 不然会报语法错误.

* java.lang.NoSuchMethodException: com.mmall.pojo.User.(init)
很迷的错误, 网上都是说加个无参构造即可, 如下. 可是情况是已经有无参构造还报错, 最后是把无参里面的注释全部删掉, 即可?..
```java
public class User{
  ...
  public User() {
    super();
  }
  ...
}
```

* 500错误 Unknown column 'xxx' in 'field list';
两个原因会导致, 一可能是因为表没有这个xxx这个字段, 其二则为表字段顺序要和插入表的顺序一致.

* Your local changes to the following files would be overwritten by merge:
git pull时发生错误, 原因是在本地有修改过文件, git不知道该不该覆盖.
1. 如果打算覆盖, 可以git reset --hard 上个版本, 然后再 git pull
2. 或者git stash后再git pull

* Column 'id' cannot be null
数据表的id不能自增, 由于不小心用navicat修改了id的类型, 导致错误. 确定不是后端出错后, 重新导入一张新表, 错误解决.


* 前后端分离, 后端成功测试接口方法, 前端测试却400错误
原因是因为前端传输过来的数值不能使用Integer来接收, 可以将相应的字段类型改成BigDecimal, 因为前端表单传输即使写的是数字传递时也会变成String字符串, eg: "level":5 => "level": "5". 导致后端存不了数值报错.

* npm ERR! code ELIFECYCLE
npm run dist时候报错, 原因可能是因为node_modules里面有东西更新, 解决方法是把整个文件夹删掉, 清除缓存npm cache clean, 再安装一遍即可. 网上也有说法是权限不够.
```html
12 error Linux 2.6.32-642.6.2.el6.x86_64
13 error argv "/usr/local/node-v4.4.7-linux-x64/bin/node" "/usr/local/node-v4.4.7-linux-x64/bin/npm" "run" "dist"
14 error node v4.4.7
15 error npm  v2.15.8
16 error code ELIFECYCLE
17 error admin-fe@1.0.0 dist: `WEBPACK_ENV=online webpack -p`
17 error Exit status 2
18 error Failed at the admin-fe@1.0.0 dist script 'WEBPACK_ENV=online webpack -p'.
18 error This is most likely a problem with the admin-fe package,
18 error not with npm itself.
...
```

* 脚本的编写以及脚本快捷执行
每次上线一次新的版本都要经过繁琐的行行代码执行, 解放双手的目的是必须的. 编写sh文件就是一种解放方式, 但是每次都需要进入对应的文件夹执行是不爽的事. 
可以进用户根目录的.bash_profile文件, 给对应的脚本赋值一个名字, 以后便可以在任何地方执行脚本
```javascript
cd ~
vim .bash_profile
// .bash_profile
alias '名字'='脚本完整路径'
```
保存退出即可在命令行输入'名字'直接运行脚本

* 测试url映射报错
Error parsing HTTP request header
原因是http, 我测试时蠢了, 写成https.. 查阅了资料, 也有可能是tomcat版本不一致会导致 需注意. 

* 更新数据表时候报错
Duplicate entry 'xxx'(id) for key 'PRIMARY'
原因是使用insert into了又一次id, 使用update即可解决

* 服务器npm run build/dist目的文件夹时候无法运行完, 中途被Killed
还有安装package.json里的依赖的时候也会被Killed, webpack打包也会被Killed. 我猜想应该是服务器内存不够被迫中止. 解决方法是在根目录的opt文件夹下新建一个swap文件夹
```linux
cd /opt 
opt mkdir swap
cd swap
(sudo) dd if=/dev/zero of=swapfile bs=1M count=1024
mkswap swapfile 
swapon swapfile 
```

* Unexpected token < in JSON at position 0
没解决, 网上有说是fileheader注释头应该去掉, 因为json本身不支持注释. 我去掉了注释还是不行, 解决掉下一个错误后, 该错误也神奇消失

* java.io.filenotfoundexception: 路径 (permission denied)
原因是 报错路径 的所属文件夹的权限不够, 更改一下当前的所属用户就可以了

* MySQL check the manual that corresponds to your MySQL server version for the right syntax错误
如果语法检查得很仔细, 感觉没错的话, 就要好好看看是否你的字段用到了关键字, 我用了desc(description描述)来充当描述字段, 结果报这个错误

* MySQL check the manual that corresponds to your MySQL server version for the right syntax错误(二)
可能是sql语句最后一句多加了个逗号

* 低版本ie或360兼容模式报错
'Promise未定义', 这样因为低版本ie不支持es6语法, 安装依赖babel-polyfill, 导入即可

* vim 一个文件时 提示修改不成功
会出现"can't open file for writing", 原因有两个  
1.当前用户的权限不足, 解决方法是打开是加上sudo即可
2.此文件可能正被其他程序或用户使用, 稍等片刻

* 405 Method Not Allowed
方法不被允许, 可能是接口方法指定post, 而你默认用get

* 传值出现java lang错误
java.lang.NullPointerException, 没有判断空的情况, 可能是参数名传错了, 比如userId, 前端发送id过来.

* ftp转图片出现553 Could not create file
可能是因为传输的目的文件权限不够.

* 查询数据库时的问题
1. 比如查询关键字+省市区, 当省市区为空时, 查询所有有关键字的列表出来, 在where查询里加上
```JavaScript
<if test="province!=null">
  and province = #{province}
</if>
```
2. 虽然上面解决了只需要写一个方法的问题, 但是前端传值过来, 还是不能成功显示. 因为方法是get方法, 所传值会在url一起带回. 初步认为是 传回的省市区是空字符串不是null, 于是在前端加上( || null), 还是失败告终; 最后得知上述的if里面test可以多加条件语句, 加上 province != '' 即可!