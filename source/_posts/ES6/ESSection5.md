---
title: 第五章 数值的扩展
tags: ES6
categories: ECMAScript 6 入门
abbrlink: d49ae64c
date: 2020-05-20 22:17:18
---
<!-- more -->
# 数值的扩展
## 不同进制表示法
ES6 提供了二进制和八进制数值的新的写法，分别用前缀0b（或0B）和0o（或0O）表示。
```js
0b111110111 === 503 // true
0o767 === 503 // true
```
如果要将0b和0o前缀的字符串数值转为十进制，要使用Number方法。
```js
Number('0b111')  // 7
```
## Number.isFinite()和Number.isNaN()
ES6 在Number对象上，新提供了Number.isFinite()和Number.isNaN()两个方法
```js
Number.isFinite(15); // true
Number.isFinite(0.8); // true
Number.isFinite(NaN); // false
Number.isFinite(Infinity); // false
Number.isFinite(-Infinity); // false
Number.isFinite('foo'); // false
Number.isFinite('15'); // false
Number.isFinite(true); // false
```
如果参数类型不是数值，Number.isFinite一律返回false。Number.isNaN()用来检查一个值是否为NaN。
```js
Number.isNaN(NaN) // true
Number.isNaN(15) // false
Number.isNaN('15') // false
Number.isNaN(true) // false
Number.isNaN(9/NaN) // true
Number.isNaN('true' / 0) // true
Number.isNaN('true' / 'true') // true
```
## Number.parseInt() 和 Number.parseFloat()
ES6 将全局方法parseInt()和parseFloat()，移植到Number对象上面，行为完全保持不变。逐步减少全局性方法，使得语言逐步模块化.
```js
Number.parseInt('12.34') // 12
Number.parseFloat('123.45#') // 123.45
Number.parseInt('12.34') // 12
Number.parseFloat('123.45#') // 123.45
```
## Number.isInteger()
Number.isInteger()用来判断一个数值是否为整数。
```js
Number.isInteger(25) // true
Number.isInteger(25.1) // false
Number.isInteger(25.0) // true
```
JavaScript 内部，整数和浮点数采用的是同样的储存方法，所以 25 和 25.0 被视为同一个值。
对数据精度的要求较高，不建议使用Number.isInteger()： JavaScript里数值存储为64位双精度格式，数值精度最多可以达到 53 个二进制位（1 个隐藏位与 52 个有效位）。如果数值的精度超过这个限度，第54位及后面的位就会被丢弃；如果一个数值的绝对值小于Number.MIN_VALUE（5E-324），即小于 JavaScript 能够分辨的最小值，会被自动转为 0。
```js
Number.isInteger(5E-324) // false
Number.isInteger(5E-325) // true
Number.isInteger(3.0000000000000002) // true
```
## Math