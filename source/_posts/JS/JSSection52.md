---
title: 第五章 引用类型
tags: JavaScript
categories: JavaScript高级程序设计
abbrlink: 71de0ab2
date: 2020-07-30 11:48:09
---
<!-- more -->

## RegExp类型

ECMAScript 通过`RegExp`类型来支持正则表达式。使用下面类似Perl的语法，就可以创建一个正则表达式。

```js
var expression = / pattern / flags;
```

其中的模式（pattern）部分可以是任何简单或复杂的正则表达式，可以包含字符类、限定符、分组、向前查找以及反向引用。每个正则表达式都可带有一或多个标志（flags），用以标明正则表达式的行为。

正则表达式的匹配模式支持下列 3 个标志。

- `g`：表示全局（global）模式，即模式将被应用于所有字符串，而非在发现第一个匹配项时立即停止；
- `i`：表示不区分大小写（case-insensitive）模式;
- `m`：表示多行（multiline）模式，即在到达一行文本末尾时还会继续查找下一行中是否存在与模式匹配的项。

```js
/*
* 匹配字符串中所有"at"的实例
*/
var pattern1 = /at/g;
/*
* 匹配第一个"bat"或"cat"，不区分大小写
*/
var pattern2 = /[bc]at/i;
/*
* 匹配所有以"at"结尾的 3 个字符的组合，不区分大小写
*/
var pattern3 = /.at/gi;
```

模式中使用的所有元字符都必须转义。正则表达式中的元字符包括：`( [ { \ ^ $ | ) ? * + .]}`

```js
/*
* 匹配第一个"bat"或"cat"，不区分大小写
*/
var pattern1 = /[bc]at/i;
/*
* 匹配第一个" [bc]at"，不区分大小写
*/
var pattern2 = /\[bc\]at/i;
/*
* 匹配所有以"at"结尾的 3 个字符的组合，不区分大小写
*/
var pattern3 = /.at/gi;
/*
* 匹配所有".at"，不区分大小写
*/
var pattern4 = /\.at/gi;
```

前面举的这些例子都是以字面量形式来定义的正则表达式。另一种创建正则表达式的方式是使用`RegExp`构造函数，它接收两个参数：一个是要匹配的字符串模式，另一个是可选的标志字符串。

```js
/*
* 匹配第一个"bat"或"cat"，不区分大小写
*/
var pattern1 = /[bc]at/i;
/*
* 与 pattern1 相同，只不过是使用构造函数创建的
*/
var pattern2 = new RegExp("[bc]at", "i");
```

