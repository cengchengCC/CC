---
title: this
tags: JavaScript
categories: JS进阶
abbrlink: feee8227
date: 2020-08-04 21:21:43
---
<!-- this -->
<!-- more -->

## this 简介

当对象方法需要使用对象中的属性，可以使用`this`关键字：

```js
let user = {
    name : 'CC',
  say(){ console.log(`hello ${this.name}`)}
}

user.say(); // "hello leo"
```

## 箭头函数的this

箭头函数比较特别，没有自己的`this`，如果有引用`this`的话，则指向外部正常函数。

```js
let user = {
    name : 'CC',
  say : () => {
    console.log(`hello ${this.name}`);
  },
  hello(){
        let fun = () => console.log(`hello ${this.name}`);
    fun();
    }
}

user.say();   // hello      => say() 外部函数是 window
user.hello(); // hello leo  => fun() 外部函数是 hello
```

## call/apply/bind

函数原型链中的`apply`，`call`和`bind`方法是 JavaScript 中相当重要的概念，与`this`关键字密切相关。当我们想把`this`值绑定到另一个环境中，就可以使用这些方法实现：

### 基础对比

语法：

```js
fun.call(thisArg, param1, param2, ...)
fun.apply(thisArg, [param1,param2,...])
fun.bind(thisArg, param1, param2, ...)
```

#### return

- `call`/`apply`: `fun`执行的结果
- `bind`:return `fun`的拷贝，并有指定的`this`和初始参数

#### params

`thisArg`:

{% blockquote %}

- `fun`的`this`指向`thisArg`对象
- 非严格模式下：`thisArg`指定为`null`、`undefined`，`fun`中的`this`指向`window`对象.
- 严格模式下：`fun`的`this`为`undefined`
- 值为原始值(数字，字符串，布尔值)的`this`会指向该原始值的自动包装对象，如 `String`、`Number`、`Boolean`
{% endblockquote %}

`param1`、`param2`：传给`fun`的参数

{% blockquote %}

- 如果`param`不传或为 `null`/`undefined`，则表示不需要传入任何参数.
- `apply`第二个参数为数组，数组内的值为传给`fun`的参数。
{% endblockquote %}

#### 调用对象

调用`apply`，`call`和`bind`必须是个函数，它们是定义在`Function`对象上的三个方法。

```js
Object.prototype.toString.call(data)
```

#### 作用

改变函数执行时的`this`指向

#### 区别

`call`与`apply`的唯一区别:

- 传给`fun`的参数写法不同:`apply`是第2个参数，这个参数是一个数组：传给`fun`参数都写在数组中。`call`从第2~n的参数都是传给`fun`的。

`call`/`apply`与`bind`的区别

- `call`/`apply`改变了函数的this上下文后马上执行该函数
- `bind`则是返回改变了上下文后的函数,不执行该函数

返回值：

- `call`/`apply` 返回`fun`的执行结果
- `bind`返回`fun`的拷贝，并指定了`fun`的this指向，保存了`fun`的参数。

### call/apply/bind的理念——借用方法

A对象有个方法，B对象因为某种原因也需要用到同样的方法，相比于单独为B对象扩展一个方法呢，借用 A 对象的方法更节省内存。借助已实现的方法，改变方法中数据的this指向，减少重复代码。

### 应用场景

`call`/`apply`的应用场景：需要借用方法


## 参考链接

参考链接1：https://www.imooc.com/article/290456
参考链接2：https://www.webhek.com/post/javascript-bind.html
