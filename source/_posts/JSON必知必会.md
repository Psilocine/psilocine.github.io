---
title: JSON必知必会
date: 2017-09-13 20:19:04
tags: JSON
---
##### 写在前面
文章标题和O'Reilly动物书同名, 记录这本书的一些重点(个人觉得)

### 什么是JSON
JSON是一种数据交换格式

### JSON语法
1. JSON基于JavaScript对象字面量, 用{}或[]包裹.
2. JSON采用(键:值)来表示, 键值对可以是字符串, 数字, 布尔值, null, 数组, 对象. 字符串必须用双引号包裹起来, 不能用单引号, 也不能用引号.
3. 正确的JSON语法. 一般为了JSON的可移植性, 键最好不要在字符串中使用空格或特殊字符即[a-zA-Z0-9]其他的字符. 如"my girl"写成"myGirl", "Psilo's blog"写成"psilosBlog".
4. JSON文件的后缀是.json

### JSON的数据类型

* 对象数据类型

<!--more-->
```json
{
  "person": {
    "name": "Psilo",
    "age": 23,
    "head": {
      "hair": {
        "color": "black",
        "length": "short"
      },
      "eyes": "brown"
    }
  }
}
```

* 字符串类型, 需要转义的字符还是要转义, 另外JSON中没有制表符\t和换行符\n

```json
{
  "animal": "cat"
  "promo": "Say \"Hello\" to China." 
}
```

* 数字类型, json中的数字可以是正数, 小数, 负数或者指数. 指数常常用科学表示法来表示.

```json
{
  "widgetInventory": 289,
  "savingsAccount": 22.59,
  "earthsMass": 5.97219e+24
}
```

* 布尔类型, json中用小写的true false表示布尔值.

```json
{
  "tea": false,
  "coffee": true
}
```

* null类型, null表示不存在, 没有. 与0的意义不同, 0是数字.

```json
{
  "person": {
    "name": "psilo",
    "car": null
  }
}
```

* 数组类型, 一个放了一些鸡蛋的容器, 拿走了两个鸡蛋, 就成了第二个json, 把5将最后一个鸡蛋替换掉, 并不会报错, 成了第三个json, 这是因为JavaScript是弱语言, 基本类型可以任意改变

```json
{
  "eggCarton": [
    "egg",
    "egg",
    "egg",
    "egg",
    "egg",
    "egg",
    "egg",
    "egg",
  ]
}
{
  "eggCarton": [
    "egg",
    null,
    "egg",
    "egg",
    "egg",
    "egg",
    null,
    "egg",
  ]
}
{
  "eggCarton": [
    "egg",
    null,
    "egg",
    "egg",
    "egg",
    "egg",
    null,
    5,
  ]
}
```

### JSON Schema
数据交换中的一种虚拟的"合同", JSON Schema是数据接收方的第一道防线, 也是数据发送方节约时间保证数据正确的工具.
```json
// 验证猫的JSON   注意:JSON里不能写注释
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "title": "Cat",
  "properties": {
    "name": {
      "type": "string",
      "minLength": 3,
      "maxLength": 20
    },
    "age": {
      "type": "number",
      "description": "Your cat's age in years.",
      "minimun": 0
    },
    "description": {
      "type": "string"
    }
  },
  "required": [ // JSON必须要有的属性
    "name",
    "age"
  ]
}
不能通过验证的JSON
{
  "name": "Fluffy the greatest cat in thewhole wide world",
  "age": -2,
  "description": "Fluffy loves to sleep all day."
}
名字太长; 年龄小于0.
能通过验证的JSON
{
  "name": "Fluffy",
  "age": 3
}
```

### JSON中的安全问题
跨站请求伪造CSRF和跨站脚本攻击CSS
#### 跨站请求伪造: 利用站点对用户浏览器的信任进行的攻击.
1. 不要使用顶级数组. 顶级数组是合法的JavaScript脚本, 他们可以用script标签链接并使用.
2. 对于不想公开的资源, 仅允许使用HTTP POST方法请求, 而不是GET方法. GET方法可以用过URL来请求, 甚至可以放在script标签中.
```json
[
  {
    "user": "psilo"
  },
  {
    "phone": "555-555-5555"
  }
]
// 将数组存放到对象之中, 这样说的非法的JavaScript, 不会被script变迁加载
{
  "info": [
    {
      "user": "psilo"
    },
    {
      "phone": "555-555-5555"
    }
  ]
}
```
#### 跨站脚本攻击: 通过截取或将站点中所使用的第三方代码更换为恶意脚本, 来对站点进行的一种注入攻击.
1. 使用JSON.parse()来代替eval(). eval()函数会将传入的字符串编译并执行, 这会让你的代码易被攻击. 应仅适用JSON.parse()来解析JSON数据.

### JavaScript中的XMLHttpRequest与Web API
XMLHttpRequest负责在客户端发起请求, 而Web API负责在服务端返回响应

#### JavaScript中的XMLHttpRequest对象
```JavaScript
// XMLHttpRequest对象用法
var xml = new XMLHttpRequest();
xml.open(method, url, async(可选), user(可选), password(可选));
xml.send();

// XMLHttpRequest对象的一些属性
// 可以在代码中给它负值为一个函数
onreadystatechange 
/* 返回一个0-4的值, 用来表示状态码
 * 0表示open()函数还没有执行.
 * 1表示open()已执行, 但send()函数还没有执行
 * 2表示send()函数已执行切头部和状态码都可以获取了
 * 3表示头部已经收到, 但响应体正在解析中
 * 4表示请求完成, 包括响应头和响应体的内容都已经接受到了
*/
readyState
// 返回HTTP状态码 如200
status
// 当请求成功时, 该属性会包含作为文本的响应体
responseText 

// eg
var xml = new XMLHttpRequest();
var url = "http://api.openweathermap.org/data/2.5/weather?lat=35&lon=139";

xml.onreadystatechange = function() {
  if(xml.readyState == 4 && xml.status == 200) {
    var myObject = JSON.parse(xml.responseText);
    var myJson = JSON.stringify(myObject);
  }
}

xml.openk("GET", url, true);
xml.send();
```
很明显, url的地址和请求的地址不太可能是同一域名, 显然这违背了同源策略, 所以我们需要资源共享. 有些开发人员可以持续多年通过JavaScript的AJAX技术向公共API发送请求, 而不会收到同源策略的影响, 这是因为这些公共API的开发者在他们的服务器上实现了跨域资源共享CORS. 这些服务器会在响应头额外加上一些带有Access-Control-Allow前缀的属性. 如Access-Control-Allow-Origin:*; 定义了任意域名都是允许使用的. 之前跨站请求伪造也可以通过在其响应头加入具体站点域名来禁止其他站点去获取资源, 这样script里的站点地址也就失效了.

#### JSONP
带有padding的JSON. 将JavaScript加入JSON文档, 内联与JSON文档的JavaScript调用一个函数, 函数参数是JSON. 函数参数提供了一种将数据传递给函数的方式. JSON-P还需要服务端的不少支持,  因为JSON资源必须包含JavaScript内联..

### JSON与客户端框架
框架可以节省时间, 让我们更专注于功能的构建. 
#### jQuery
jQuery是一种允许开发者专注于操作DOM构建功能的抽象化工具. 如JSON.parse(), 在老版本的IE, Firefox, chrome并不支持. jQuery帮我们解决了大部分兼容问题.
```JavaScript
var myAnimal = JSON.parse('{ "animal": "cat"}'); // 有兼容问题

// jQuery内置的方法来解析JSON
var myAnimal = jQuery.parseJSON('{"animal": "cat"}');

// 用JQuery创建新的XMLHttpRequest对象, 并从OpenWeatherMap API 获取JSON
var url = "http://api.openweathermap.org/data/2.5/weather?lat=35&lon=139";
$.getJSON(url, function(data) {
  // 对天气数据操作
})
```
#### AngularJS
AngularJS是为DOM操作服务的抽象化工具, 专注单页Web应用的开发. 是基于实现MVC构架概念的框架. 
```JavaScript
/* MVC
 * JSON是模型
 * HTML是试图
 * 控制器是使用AngularJS语法来定义和操作与模型和试图间的交互的JavaScript文件.
 */
// 从OpenWeatherMap API获取天气数据
angular.module('myApp', []).controller('myAppController', function($scope, $http) {
  $http.get("http://api.openweathermap.org/data/2.5/weather?lat=35&lon=139").success(function(data, status, headers, config) {
    $scope.weatherData = data;
  });
});
```
从OpenWeatherMap API返回的JSON数据会被$http反序列化, 然后将名为wetherData的对象呗添加到全局作用域. 这样就能通过HTML试图的插值语法将其作为数据模型进行绑定了.

### JSON与NoSQL
SQL: 关系型数据库, 使用表格, 行和列来以结构化形式存储数据的. NoSQL: 顾名思义, 不是关系型数据库, 它可以是键值对存储.
#### CouchDB: 使用JSON文档存储数据的文档存储数据库的NoSQL数据库
在关系型数据库中, 对于一个账户对应多个地址这种一对多的关系, 需要执行联合查询, 以便把数据放在一起. 有了CouchDB, 数据就不需要因为它们之间的关系而被分开存储, 也不需要再读取时进行重组.
```json
{
  "firstName": "Bob",
  "lastName": "Barker",
  "age": "91",
  "addresses": [
    {
      "street": "123 fake st",
      "city": "somewhere"
    },
    {
      "street": "456 fakey ln",
      "city": "some place"
    }
  ]
}
```
由于CouchDB使用文档来存储数据, 因此从数据库查询一个账户时, 得到的直接就是一个结构化文档, 没有必要进行重组. 另一个好处是有利于数据的变化, 有些数据会随着时间而发生变化, 如电话号码, 当账户将来又有了新的号码, 只需要将其加入存储电话的数据即可

#### CouchDB API
对于HTTP, 我们是通过URL的方式来请求资源的, 从CouchDB API中请求的资源是一份JSON文档
```json
// 本地名为accounts的数据库
// http://localhost:8080/accounts
{
  "db_name": "accounts",
  "doc_count": 3,
  "doc_del_count": 0,
  "update_seq": 7,
  "purge_seq": 0,
  "compact_running": false,
  "disk_size": 29999,
  "data_size": 1222,
  "instance_start_time": "1432493477586600",
  "disk_format_version": 6,
  "committed_update_seq": 7
}
// 其中的"doc_count"表示数据库中包含多少份文档. 这里是3份, 可以通过每个文档的位移标识符来对其进行查询,可以通过http://localhost:8080/accounts/_all_docs这一URL来获取行标识符数组. 如标识符是3636fa3c716f9dd4f7407bd6f7000552. 访问http://localhost:8080/accounts/3636fa3c716f9dd4f7407bd6f7000552可得以下JSON资源
{
  "_id": "3636fa3c716f9dd4f7407bd6f7000552",
  "_rev": "1-8a9527cbfc22e28984dfb3a3e6062635",
  "firstName": "Billy",
  "lastName": "Bob",
  "age": "91",
  "addresses": [
    {
      "street": "123 fake st",
      "city": "somewhere"
    },
    {
      "street": "456 fakey ln",
      "city": "some place"
    }
  ]
}
// 上述是从CouchDB数据库请求数据, 下面说说如何向数据库发送数据
// 通过向http://localhost:8080/accounts/这一URL post数据来实现
// 通过post方法请求 http://localhost:8080/accounts/的HTTP请求头
POST /accounts/ HTTP/1.1
Host: localhost:8080
Content_type: application/json
Cache-control: no-cache
// 通过post方法请求 http://localhost:8080/accounts/ 的HTTP请求体
{
  "firstName": "Psilo",
  "lastName": "Lau",
  "age": "18",
  "addresses": [
    {
      "street": "1234 fake st",
      "city": "somehwhere"
    },
    {
      "street": "4567 fakey ln",
      "city": "some place"
    }
  ]  
}
// http请求成功后, CounchDB API会将JSON格式的相应信息发送给你, 其中包含了新创建的文档的标识符.
{
  "ok": true,
  "id": "3asd564sa6451654v6sd4v6d4asv6",
  "rev": "1-as564ff6sd31v6asd54v6ads4v"
}
// 这时候可以用心的标识符来创建URL, 并从账户数据库中请求JSON文档.
// http://localhost:8080/accounts/3asd564sa6451654v6sd4v6d4asv6的响应
{
  "_id": "3asd564sa6451654v6sd4v6d4asv6",
  "_rev": "1-as564ff6sd31v6asd54v6ads4v",
  "firstName": "Psilo",
  "lastName": "Lau",
  "age": "18",
  "addresses": [
    {
      "street": "1234 fake st",
      "city": "somehwhere"
    },
    {
      "street": "4567 fakey ln",
      "city": "some place"
    }
  ]
}
// 如果希望更新刚刚插入的JSON文档, 可以通过在post请求改子欧元deURL时, 将"_id" "_rev"加入请求体来实现, 成功后, API会响应一个说明状态的JSON文档, 其中会包含更新后的"rev"键值对.
```

### 服务端的JSON
客户端: html css js  
服务端: php asp.net nodejs ruby java go等  
在服务端, 我们通过HTTP想服务端发送资源请求. 服务端会响应一份文档, 当文档是JSON时, 必须要用服务端代码来生成它. 同时, 服务端可能要先接受一个JSON文档才能返回一个JSON文档.

#### 序列化、反序列化与请求JSON
# ASP.NET
ASP.NET是由微软开发的服务端WEB框架, 在ASP.NET解析JSON不想js中那么简单, 需要第三方ASP.NET库. 最流行的库是Json.NET  
有了ASP.NET和Json.NET 可以快速地将ASP.NET对象序列化为JSON. 首先需要一个用于操作的JSON对象.
```java
public class CustomerAccount
{
  public string firstName { get; set; }
  public string lastName { get; set; }
  public string phone { get; set; }
  public Address[] addresses { get; set; }
  public bool famous { get; set;}
}

public class Address
{
  public string street { get; set; }
  public string city { get; set; }
  public string state { get; set; }
  public int zip { get; set; }
}
// 有了表示对象的类, 现在就创建一个新的对象以保存Bob Barker 的信息
CustomerAccount bobsAccount = new CustomerAccount();
bobsAccount.firstName = "Bob";
bobsAccount.lastName = "Barker";
bobsAccount.phone = "555-55-55555";

Address[] addresses;
addresses = new Address[2];
Address bobsAddress1 = new Address();
bobsAddress1.state = "123 fakey st";
bobsAddress1.city = "somewhere";

addresses[0] = bobsAddress1;

Address bobsAddress2 = new Address();
bobsAddress2.state = "456 fakei ln";
bobsAddress2.city = "some place";

addresses[1] = bobsAddress1;

string json = JsonConvert.SerializeObject(bobsAccount);
// 序列化后的ASP.NET CustomerAccount对象
{
  "firstName": "Bob",
  "lastName": "Barker",
  "phone": "555-55-55555",
  "addresses": [
    {
      "street": "123 fake st",
      "city": "somehwhere"
    },
    {
      "street": "456 fakey ln",
      "city": "some place"
    }
  ]
}
// 反序列化JSON
CustomerAccount customerAccount = JsonConvert.DeserializeObject<CustomerAccount>(json);
```

# php
php是一种用于创建动态WEb页面的服务端脚本语言. php代码可以直接嵌入html文档中, 也支持对象数据类型.
```php
<?php 
class Account {
  public $firstName;
  public $lastName;
  public $phone;
  public $addresses;
}

class Address {
  public $street;
  public $city;
}

$address1 = new Address();
$address1->street = "123 fake st";
$address1->city = "somewhere";
$address2 = new Address();
$address2->street = "456 fakey ln";
$address2->city = "some place";

$account = new Account();
$account->firstName = "Bob";
$account->lastName = "Barker";
$account->phone = "555-55-55555";
$account->address = array($address1, address2);

$json = json_encode($account);
?>
//json_encoude($account)返回的结果
{
  "firstName": "Bob",
  "lastName": "Barker",
  "phone": "555-55-55555",
  "addresses": [
    {
      "street": "123 fake st",
      "city": "somehwhere"
    },
    {
      "street": "456 fakey ln",
      "city": "some place"
    }
  ]
}
// 反序列化json 相对的使用json_decode
```
# nodejs
nodejs是服务端的js, 在nodejs不再使用XMLHttpRequest对象, 在nodejs中通过get()函数来请求json
```JavaScript
// 通过向openweathermap API发送请求, 得到的JSON反序列化为JS对象, 然后通过console.log输出(coord)坐标对象 
var http = require('http');
http.get({
  host: 'api.openweathermap.org',
  path: '/data/2.5/weather?q=london,uk'
}, function(response) {
  var body = '';
  response.on('data', function(data) {
    body += data;
  });
  response.on('end', function() {
    var weatherData = JSON.parse(body);
    console.log(weatherData.coord);
  });
});
```