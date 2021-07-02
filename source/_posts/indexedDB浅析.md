---
title: IndexedDB浅析
date: 2021-03-25 11:21:02
tags: [前端, JS]
---

##### 写在前面

目前支撑的业务有大量数据存储在WebStorage的情况，考虑到WebStorage容量的限制，研究了一下浏览器提供的本地数据库————IndexedDB

#### IndexedDB概述

IndexedDB是一种底层的异步API，是一种在用户浏览器中持久存储数据的方法。它允许您在不考虑网络的可用性，创建具有丰富查询能力的可离线Web应用程序。对于在客户端存储大量的结构化数据和不需要持久在线网络连接的应用程序中很有用。  

通俗地说，IndexedDB 就是浏览器提供的本地数据库，它可以被网页脚本创建和操作。IndexedDB 允许储存大量数据，提供查找接口，还能建立索引。就数据库类型而言，IndexedDB 不属于关系型数据库（不支持 SQL 查询语句），更接近 NoSQL 数据库（简单好使）。  

值得一提的是，过去的竞争规范（WebSQL）数据库是关系型数据库（复杂），但是已经被W3C废弃。

<!-- more -->

##### 为何IndexedDB会诞生

现有的浏览器数据储存方案，都不适合存储大量数据：

1. Cookie的大小不超过4KB，且每次请求都会发送回服务器；
2. LocalStorage约在5MB左右（各家浏览器不同，IE浏览器约3M左右），且不提供搜索功能，不能建立自定义索引。

为了解决痛点，IndexedDB就应邀诞生了。

##### IndexedDB目前的兼容性（2021.03）

![兼容性](兼容性.jpg)

##### IndexedDB的特性

1. 键值对储存  
IndexedDB内部采用对象仓库（object store）存放数据。所有类型的数据都可以直接存入，包括JavaScript对象。对象仓库中，数据以“键值对”的形式保存，每一个数据记录都有对应的主键，主键是独一无二的，不能有重复。

2. 异步  
IndexedDB操作时不会锁死浏览器，用户依然可以进行其他操作，这与LocalStorage形成对比，后者的操作是同步的。异步设计是为了防止大量数据的读写，拖慢网页的表现。值得注意的是，IndexedDB也有同步API，目前已从规范中移除。

3. 支持事务  
IndexedDB支持事务（transaction），这意味着一系列操作步骤之中，只要有一步失败，整个事务就都取消，数据库回滚到事务发生之前的状态，不存在只改写一部分数据的情况。

4. 同源策略  
IndexedDB受到同源限制，每一个数据库对应创建它的域名。网页只能访问自身域名下的数据库，而不能访问跨域的数据库。

5. 储存空间大  
IndexedDB的储存空间比LocalStorage大得多，理论上取决于硬盘大小：为可用磁盘空间的50%。

6. 支持二进制储存  
IndexedDB不仅可以储存字符串，还可以储存二进制数据（ArrayBuffer对象和Blob对象，理论上支持任意格式的数据）。

##### IndexedDB的局限性

以下情况不适合使用IndexedDB：

* 多语言混合存储。国际化支持不好，需要自己处理。
* 和服务器端数据库同步。得自己写同步代码。
* 全文搜索。IndexedDB接口没有类型SQL语句中LIKE的功能。

以下情况，数据库可能被清除：

* 用户请求清除数据。
* 浏览器处于隐私模式。最后退出浏览器的时候，数据会被清除。
* 硬盘等存储设备的容量到限或损坏。

#### IndexedDB的核心用法

IndexedDB API是强大的，也是复杂的，涉及不少概念。它把不同的实体，抽象成一个个对象接口。学习这个API，就是学习它的各种对象接口。

* indexedDB：挂载在window的构造函数
* IDBRequest：操作请求
* IDBDatabase：数据库对象
* IDBObjectStore：对象仓库
* IDBTransaction：事务
* IDBIndex：索引
* IDBCursor：指针
* IDBKeyRange：主键集合


##### indexedDB对象：
* open(数据库名称, 数据库版本号 = 1)方法：用于打开数据库，如果不存在，会新建该数据库。异步回调（success、error、upgradeneeded、blocked）

* deleteDatabase(数据库名称)方法：删除一个数据库。异步回调（success、error）
 
* cmp(i, j)方法：比较两个主键的大小。返回整数（i === j为0、i > j为1、i < j为-1）

![indexedDB对象](indexedDB对象.jpg)

##### IDBRequest对象：
操作请求，表示打开的数据库连接，数据库的操作都是通过这个对象执行的。indexedDB.open()和deleteDatabase()会返回这个对象。

##### IDBDatabase对象：
通过IDBRequest对象的回调result属性获得。后面对数据库的操作，都通过这个对象完成。

* objectStoreNames(对象仓库名称)：包含当前数据库所有object store的名字，可以使用contains方法检查数据库是否包含某个对象仓库
* createObjectStore(名, 对象仓库的可配置属性)：创建存放数据库的对象仓库，返回IDBObjectStore对象，
* deleteObjectStore(对象仓库名称)：删除指定的对象仓库 
* transaction(对象仓库名称，读取权限 = “readonly”)：返回一个IDBTransaction事务对象
* close()：关闭数据库连接

可配置属性：
keyPath：主键
autoIncrement：自增主键，如果没有适合作主键的属性。

##### IDBObjectStore对象
对象仓库。由IDBDatabase.createObjectStore()返回。它是直接操作数据的对象。

值得注意的是，IDBDatabase对象的transaction()返回一个事务对象，事务对象的objectStore()反回IDBObjectStore对象，因此可以采用链式写法

* add(键值, 主键)方法：用于向对象仓库添加数据，返回一个IDBRequest对象。该方法只用于添加数据，如果主键相同则会报错，因此更新数据必须使用put()方法
* put(数据, 主键)方法：用于更新某个主键对应的数据记录，如果不存在则插入一条新的记录。返回一个IDBRequest对象
* clear()方法：删除当前对象仓库的所有记录，返回一个IDBRequest对象 
* delelte(主键)方法：删除指定主键的记录，返回一个IDBRequest对象
* count()方法：用于计算记录的数量，返回一个IDBRequest对象。不带参数时，返回当前对象仓库的所有记录数量。如果主键或IDBKeyRange对象作为参数，则返回对应的记录数量
* getKey()、get(主键)、getAll()、getAllKeys()、index(name)、createIndex()、deleteIndex()、openCursor()、openKeyCursor()，etc。

##### IDBTransaction对象
事务，所有的读写操作都要通过这个对象，权限只有两种（readwrite、readonly）。IDBDatabase.transaction()返回就是事务对象。事务的执行顺序是按照创建的顺序，而不是发出请求的顺序。

* abort()方法：终止当前事务，回滚所有已经进行的变更

* objectStore(name)方法：返回指定名称的对象仓库

##### IDBIndex对象：索引，通过这个对象可以获取数据库里面的记录

##### IDBCursor对象：指针，用来遍历对象仓库或索引的记录。

##### IDBKeyRange对象：主键集合，根据该集合的条件，可以获取数据仓库或索引里面的一组记录


#### IndexedDB的打开方式

比起干撸IndexedDB的API，社区已经有很多优秀而成熟的第三方库。上手简单，对开发者友好：

[localForage](https://localforage.github.io/localForage/)

[dexie](http://www.dexie.org/)

[PouchDB](https://pouchdb.com/)

目前来说，我们并不需要用到IndexedDB的很多API，我们只需要能够覆盖Web Storage的功能就可以了。上述的库对我们来说还是太大太复杂了。

因此，干脆结合上述库的写法，自己动手封装一下API。几乎抹平了使用和学习成本。

* getItem(key, value, callback)
* setItem(key, callback)
* removeItem(key, callback)
* clear(callback)

具体看下述代码：
```javascript
class IndexedDB {
  constructor (name = "miaobao-store") {
    this.ready(name);
  }
  // 初始化
  ready (name) {
    return new Promise((resolve, reject) => {
      if (this.db) {
        resolve(this);
      } else {
        this.objectStoreName = name;
        const request = window.indexedDB.open("MiaoBao", 1);

        request.onsuccess = (e) => {
          this.db = e.target.result;
          resolve(this);
        };

        request.onupgradeneeded = (e) => {
          const db = e.target.result;
          if (!db.objectStoreNames.contains(this.objectStoreName)) {
            db.createObjectStore(this.objectStoreName);
          }
        };

        request.onerror = (e) => {
          reject(e);
        };

        request.onblocked = (e) => {
          reject(e);
        };
      }
    });
  }
  // 赋值
  setItem (key, value, callback) {
    return this.init((success, error) => {
      const request = this.db
        .transaction(this.objectStoreName, "readwrite")
        .objectStore(this.objectStoreName)
        .put(value, key);
      request.onsuccess = () => success(value);
      request.onerror = error;
    }, callback);
  }
  // 取值
  getItem (key, callback) {
    return this.init((success, error) => {
      const request = this.db
        .transaction(this.objectStoreName)
        .objectStore(this.objectStoreName)
        .get(key);
      request.onsuccess = () => success(request.result);
      request.onerror = error;
    }, callback);
  }
  // 清除
  removeItem (key, callback) {
    return this.init((success, error) => {
      const request = this.db
        .transaction(this.objectStoreName, "readwrite")
        .objectStore(this.objectStoreName)
        .delete(key);
      request.onsuccess = () => success(key);
      request.onerror = error;
    }, callback);
  }

  clear (callback) {
    return this.init((success, error) => {
      const request = this.db
        .transaction(this.objectStoreName, "readwrite")
        .objectStore(this.objectStoreName)
        .clear();
      request.onsuccess = () => success(null);
      request.onerror = error;
    }, callback);
  }

  init (request, callback) {
    return new Promise((resolve, reject) => {
      const success = value => {
        if (callback && typeof callback === "function") {
          callback(false, value);
        }
        resolve(value);
      }
      const error = e => {
        if (callback && typeof callback === "function") {
          callback(e);
        }
        reject(e);
      }
      return this.ready(this.objectStoreName).then(() => {
        request(success, error);
      }).catch(error);
    });
  }
}

export default IndexedDB;

```

