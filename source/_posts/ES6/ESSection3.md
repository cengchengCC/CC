---
title: 第三章 字符串的扩展及其新增方法
tags: ES6
categories: ECMAScript 6 入门
abbrlink: f141f1f2
date: 2020-05-20 18:19:29
---
本章介绍 ES6 对字符串的改造和增强。
<!-- more -->

## 字符的 Unicode 表示法

ES6 允许采用\uxxxx形式表示一个字符，其中xxxx表示字符的 Unicode 码点。但是，这种表示法只限于码点在\u0000~\uFFFF之间的字符。超出这个范围的字符，必须用两个双字节的形式表示。

```js
"\u0061"        // "a"
"\uD842\uDFB7"  // "𠮷"
"\u20BB7"      // " 7"
"\u{20BB7}"    // "𠮷"
```

在ES6里只要将码点放入大括号，就能正确解读该字符。
有了这种表示法之后，JavaScript 共有`6`种方法可以表示一个字符。

```js
'\z' === 'z'  // true
'\172' === 'z' // true
'\x7A' === 'z' // true
'\u007A' === 'z' // true
'\u{7A}' === 'z' // true
```

## 字符串的遍历器接口

ES6为字符串添加了遍历器接口（详见《Iterator》一章），使得字符串可以被`for...of`循环遍历。

```js
for (let codePoint of 'foo') {
  console.log(codePoint)
}
// "f"
// "o"
// "o"
```
