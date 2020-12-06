---
title: 第二十三章 编程风格
tags: ES6
categories: ECMAScript 6 入门
abbrlink: d3d8527f
date: 2020-07-24 10:55:23
---
本章探讨如何将 ES6 的新语法，运用到编码实践之中，与传统的 JavaScript 语法结合在一起，写出合理的、易于阅读和维护的代码。
<!-- more -->

## 字符串

静态字符串一律使用单引号或反引号，不使用双引号。动态字符串使用反引号。

```js
// bad
const a = "foobar";
const b = 'foo' + a + 'bar';

// acceptable
const c = `foobar`;

// good
const a = 'foobar';
const b = `foo${a}bar`;
```

## 数组

使用扩展运算符（...）拷贝数组。

```js
// bad
const len = items.length;
const itemsCopy = [];
let i;

for (i = 0; i < len; i++) {
  itemsCopy[i] = items[i];
}

// good
const itemsCopy = [...items];
```

使用 `Array.from`方法，将类似数组的对象转为数组。

```js
const foo = document.querySelectorAll('.foo');
const nodes = Array.from(foo);
```