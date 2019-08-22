---
title: 浅析axios
date: 2018-04-11 22:50:57
tags: [VUE,前端]
---
##### 写在前面
vue官方已经确定不要维护vue-resource, 转而推荐axios. axios有何力量使得官方都推荐使用呢?

#### 最简单的axios例子
```JavaScript
  axios('api'); // 默认get请求
```
<!-- more -->
#### 几则例子
```JavaScript
// 1. api + params
axios.get('/user?name=psilo').then(() => {
  // 请求成功处理
}).catch(() => {
  // 请求失败处理
});
// 2. api
axios.get('/user', {
  params: {
    name: 'psilo'
  }
}).then(() => {}).catch(() => {});

// 请求方法
axios.request(config)
axios.get(url[, config])
axios.delete(url[, config])
axios.head(url[, config])
axios.post(url[, data[, config]])
axios.put(url[, data[, config]])
axios.patch(url[, data[, config]])

// 也可以把请求方法method, 请求地址url写在config里
axios({
  method: 'post', // 不写默认get方法
  url: '/user/12345',
  data: {
    firstName: 'Psilo',
    lastName: 'Lau'
  }
});
```

#### 并发请求
```javascript
function getUserAccount() {
  return axios.get('/user/12345');
}

function getUserPermissions() {
  return axios.get('/user/12345/permissions');
}

axios.all([getUserAccount(), getUserPermissions()]) // axios.all([iterable])
  .then(axios.spread(function (acct, perms) { // axios.spread(callback)
    // 两个请求现在都执行完成
  }));
```

#### 一些请求配置
```JavaScript
  // `baseURL` 将自动加在 `url` 前面，除非 `url` 是一个绝对 URL。
  // 它可以通过设置一个 `baseURL` 便于为 axios 实例的方法传递相对 URL
  baseURL: 'https://some-domain.com/api/',

  // `transformRequest` 允许在向服务器发送前，修改请求数据
  // 只能用在 'PUT', 'POST' 和 'PATCH' 这几个请求方法
  // 后面数组中的函数必须返回一个字符串，或 ArrayBuffer，或 Stream
  transformRequest: [function (data) {
    // 对 data 进行任意转换处理

    return data;
  }],

  // `transformResponse` 在传递给 then/catch 前，允许修改响应数据
  transformResponse: [function (data) {
    // 对 data 进行任意转换处理

    return data;
  }],

  // `timeout` 指定请求超时的毫秒数(0 表示无超时时间)
  // 如果请求话费了超过 `timeout` 的时间，请求将被中断
  timeout: 1000,
```

#### 响应结构
```JavaScript
{
  // `data` 由服务器提供的响应
  data: {},

  // `status` 来自服务器响应的 HTTP 状态码
  status: 200,

  // `statusText` 来自服务器响应的 HTTP 状态信息
  statusText: 'OK',

  // `headers` 服务器响应的头
  headers: {},

  // `config` 是为请求提供的配置信息
  config: {}
}
```

#### 配置的优先顺序
1. 在 lib/defaults.js 找到的库的默认值.
2. 实例的 defaults 属性.
3. 请求的 config 参数.
```JavaScript
// 使用由库提供的配置的默认值来创建实例
// 此时超时配置的默认值是 `0`
var instance = axios.create();

// 覆写库的超时默认值
// 现在，在超时前，所有请求都会等待 2.5 秒
instance.defaults.timeout = 2500;

// 为已知需要花费很长时间的请求覆写超时设置
instance.get('/longRequest', {
  timeout: 5000
});
```

#### 拦截器
在请求或响应被 then 或 catch 处理前拦截它们.
```JavaScript
// 添加请求拦截器
axios.interceptors.request.use(function (config) {
    // 在发送请求之前做些什么
    return config;
  }, function (error) {
    // 对请求错误做些什么
    return Promise.reject(error);
  });

// 添加响应拦截器
axios.interceptors.response.use(function (response) {
    // 对响应数据做点什么
    return response;
  }, function (error) {
    // 对响应错误做点什么
    return Promise.reject(error);
  });
```