---
title: JS 数据类型
tags: JavaScript
categories: JavaScript高级程序设计
abbrlink: 53bf3c33
date: 2019-10-29 15:40:46
---
<!-- 2019/10/29 16：10 纪念博客终于搭起来了，虽然磕磕碰碰，但是开始了3年研究生生活，可视化、前端、后台 要学的东西暂时有点乱，但是慢慢来 -->
ECMAScript 的语法大量借鉴了C及其他类C语言（如Java和Perl）的语法。数据类型：Undefined、Null、Boolean、Number、String、Object
<!-- more -->

## 变量

{% blockquote %}
ECMAScript 的变量是松散类型的，所谓松散类型就是可以用来保存任何类型的数据。换句话说，
每个变量仅仅是一个用于保存值的占位符而已。定义变量时要使用 var 操作符（注意 var 是一个关键
字），后跟变量名（即一个标识符），变量没有类型，数据才有类型！
{% endblockquote %}

```js
    var message;
    var message = "hi";
    message = 100; // 有效，但不推荐 修改变量值的同时修改值的类型
```

## 数据类型

{% blockquote %}
基本数据类型：Undefined、Null、Boolean、Number、String
引用数据类型：Object
{% endblockquote %}
Object 本质上是由一组无序的名值对组成。

## typeof

鉴于 ECMAScript 是松散类型的，因此需要有一种手段来检测给定变量的数据类型——typeof
{% blockquote %}
 "undefined"——如果这个值未定义；
 "boolean"——如果这个值是布尔值；
 "string"——如果这个值是字符串；
 "number"——如果这个值是数值；
 "object"——如果这个值是对象或 null； 
 "function"——如果这个值是函数。
{% endblockquote %}

## Undefined类型

Undefined类型只有一个值，即特殊的undefined。在使用 var 声明变量但未对其加以初始化时，这个变量的值就是undefined。以及对未初始化的变量执行typeof操作会返回undefined值，而对未声明的变量执typeof操作同样会返回undefined值。除了可以对未声明的变量执行typeof操作之外，在非严格模式下还可以对其进行delete操作。除了这两种情况之外，对未声明的变量进行任何其他操作，都会抛出错误。

## Null

Null 类型是第二个只有一个值的数据类型，这个特殊的值是 null。从逻辑角度来看，null 值表示一个空对象指针

```js
typeof null === 'object'    // true
null instanceof Object      // false
null == undefined           // true
Boolean(null) === false     // true
Boolean([]) === true        // true
Boolean({}) === true        // true
```

## Boolean

Boolean 类型只有两个字面值：true 和 false。这两个值与数字值不等同，因此 true 不一定等于 1，而 false 也不一定等于 0。Boolean类型的字面值true和false是区分大小写的。也就是说，True和False都不是Boolean值，只是标识符。

## Number

{% blockquote %}
八进制字面量在严格模式下是无效的，会导致支持的JavaScript引擎抛出错误。
浮点数值的最高精度是17位小数，但在进行算术计算时其精确度远远不如整数。
绝对值超过Number.MAX_VALUE的值会被自动转换为infinity或-infinity。
可以使用isFinite()函数来确定数值是否为无穷。如果不是无穷，则返回true。
NaN与自身都不相等，可以使用isNaN()或Number.isNaN()确定数值是否为NaN。在传入数据非数值时，isNaN()会首先调用Number()将其转换为数值类型再进行判断，而Number.isNaN()则会直接返回false。
Number()用于把任何类型数据转换为数值。
parseInt()和parseFloat()用于把字符串转换为数值。
parseInt()在转换字符串时比Number()好用，但是最好为parseInt()提供第二个参数（转换的基数2、8、10、16）。
{% endblockquote %}

## String

{% blockquote%}
任何字符串的长度都可以通过访问其length属性取得，包含双子节字符的可能不准确；
字符串是不可变的，一旦创建就不能改变，要改变某个变量保存的字符串，首先要销毁原来的字符串然后再用新的替代；
数值、布尔值、对象和字符串值都有toString()方法，并且数值的toString()方法可以接收一个参数（数值的基数），但是null和undefined没有；
在不知道要转换值的类型时，可以使用String()方法。
{% endblockquote %}

## Object

ECMAScript 中的对象其实就是一组数据和功能的集合。对象可以通过执行 new 操作符后跟要创建
的对象类型的名称来创建。而创建 Object 类型的实例并为其添加属性和（或）方法，就可以创建自定
义对象：

```js

var o = new Object();

```

Object 的每个实例都具有下列属性和方法。
{% blockquote %}
constructor：保存着用于创建当前对象的函数。
hasOwnProperty(propertyName)：用于检查给定的属性在当前对象实例中（而不是在实例的原型中）是否存在。其中，作为参数的属性名（propertyName）必须以字符串形式指定（例如：o.hasOwnProperty("name")）。
isPrototypeOf(object)：用于检查传入的对象是否是传入对象的原型。
propertyIsEnumerable(propertyName)：用于检查给定的属性是否能够使用 for-in 语句来枚举。与 hasOwnProperty()方法一样，作为参数的属性名必须以字符串形式指定。
toLocaleString()：返回对象的字符串表示，该字符串与执行环境的地区对应。
toString()：返回对象的字符串表示。
valueOf()：返回对象的字符串、数值或布尔值表示。通常与 toString()方法的返回值相同。
{% endblockquote %}

## Symbol

ES6 引入了一种新的原始数据类型Symbol，表示独一无二的值。它是 JavaScript 语言的第七种数据类型。Symbol 值通过Symbol函数生成。这就是说，对象的属性名现在可以有两种类型，一种是原来就有的字符串，另一种就是新增的 Symbol 类型。凡是属性名属于 Symbol 类型，就都是独一无二的，可以保证不会与其他属性名产生冲突。

```js

let s = Symbol();
typeof s    // "symbol"
```

注意，Symbol函数前不能使用new命令，否则会报错。这是因为生成的 Symbol 是一个原始类型的值，不是对象。也就是说，由于 Symbol 值不是对象，所以不能添加属性。基本上，它是一种类似于字符串的数据类型。
Symbol函数可以接受一个字符串作为参数，表示对 Symbol 实例的描述，主要是为了在控制台显示，或者转为字符串时，比较容易区分。

## 语句

### do-while

```js
do { 
 statement 
} while (expression);
```

### for-in

for-in 语句是一种精准的迭代语句，可以用来枚举对象的属性。

```js
for (let propName in window) { 
 document.write(propName); 
}
```

## 函数

ECMAScript 函数不能像传统意义上那样实现重载。如果在 ECMAScript 中定义了两个名字相同的函数，则该名字只属于后定义的函数。