---
title: 浅析Js的for循环方法
date: 2017-10-15 17:09:23
tags: [前端, JS]
---

##### 写在前面
Js的for方法有几种了, 你有没有仔细思考过? 性能如何?
除了原生的前测试循环for(;;), 还有es5(for-in),(forEach) 以及es6新方法(for-of)

#### 普通for循环: for(;;)
```javascript
const arr = [1, 2, 3];
for(let i = 0; i < arr.length; i++) {
  console.log(arr[i]);
}
// 当length是个不变的定值时, 可以赋予变量来提高性能
for(let i = 0, len = arr.length; i < len; i++) {
  console.log(arr[i]);
}
```
其实JavaScript的Array的索引是String类型, 即arr[i]的i并不是number, 而是string. arr[1]是系统把1转成String的'1'才能使用arr[1].

<!-- more -->

#### for-in
```javascript
const arr = [1, 2, 3];
for(let i in arr) {
  console.log(arr[i]);
}
```
for-in更适合来遍历对象, 而不是数组, 事实也确实如此, for-in循环遍历是对象的属性, 如下
```javascript
// arr
const arr = [1, 2, 3];
arr.abc = 'abc';
for(let i in arr) {
  console.log('arr[' + i + '] = ' + arr[i]);
}
// output
arr[0] = 1 // 此处的索引0是String类型
arr[1] = 2
arr[2] = 3
arr[abc] = "abc"

// obj
const psilo = {
  age = 18;
  sex = 'male';
}
for(let i in psilo) {
  console.log('psilo[' + i + '] = ' + psilo[i]);
}
// output
psilo[age] = 18
psilo[sex] = "male"
```
需要注意的是, for-in遍历的顺序是不确定的. 第二点就是只能遍历可枚举的属性(enumerable == true).
没错还有第三点, 就是for-in不仅能遍历自身属性, 还是遍历原型链属性的方法
```javascript
Array.prototype.one = 'one';
const arr = [1, 2, 3];
for(let i in arr) {
  console.log('arr[' + i + '] = ' + arr[i]);  
}
// output 
arr[0] = 1 
arr[1] = 2
arr[2] = 3
arr[one] = "one"
```
因此如果是数组遍历就使用普通for循环. 除了稀疏数组.
```javascript
const arr = [];
arr[0] = 1;
arr[1000] = 2;
arr[100000] = 3;
// 这使用普通for循环显然是不合理的
for(let i in arr) {
  console.log(arr[i]); // 1 2 3 不会出现undefined
}
```
但是因为for-in会遍历属性和原型, 所以性能损耗也是巨大的, 速度比普通for循环慢几倍不止.

### forEach
```JavaScript
const arr = [1, 2, 3];
arr.forEach(i => console.log(i));
arr.forEach(i => console.log(i + 1)); // 返回2, 3, 4. 不改变原数组
```
forEach的弊端就是不能break和return, 循环中途不能操作. 性能方面也不如普通for循环

### for-of
```JavaScript
const arr = [1, 2, 3];
for(let i of arr) {
  console.log(i);
}
// output
1
2
3
```
es6引进这个方法的目的十分明确, forEach不能中断; for-in遍历顺序随机且适合遍历对象
1. for-of能中断循环
```javascript
const arr = [1, 2, 3];
for(let i of arr) {
  if(i === 2) break;
  console.log(i);
}
// output
1
```
2. for-of不仅支持数组, 还支持类数组如nodelist
3. 支持Map, Set对象遍历
```JavaScript
let set = new Set([1, 2, 3]);
for(let i of set) {
  console.log(i)
}
// output
1
2
3
```
4. 不支持普通对象的循环