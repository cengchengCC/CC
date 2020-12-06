---
title: 第四章 变量、作用域和内存问题
tags: JavaScript
categories: JavaScript高级程序设计
abbrlink: ddd2d36c
date: 2020-03-27 14:59:46
---
JavaScript变量松散类型的本质，决定了它只是在特定时间用于保存特定值的一个名字而已。由于不存在定义某个变量必须要保存何种数据类型值的规则，变量的值及其数据类型可以在脚本的生命周期内改变。
<!-- more -->
## 基本类型和引用类型的值

*ECMAScript*变量可能包含两种不同数据类型的值：基本类型值和引用类型值。基本类型值指的是简单的数据段，而引用类型值指那些可能由多个值构成的对象。
引用类型的值是保存在内存中的对象。与其他语言不同，JavaScript不允许直接访问内存中的位置，也就是说不能直接操作对象的内存空间。在操作对象时，实际上是在操作对象的引用而不是实际的对象。为此，引用类型的值是按引用访问的。（复制保存着对象的某个变量时，操作的是对象的引用。但在为对象添加属性时，操作的是实际的对象。）

### 动态属性

不能给基本类型的值添加属性，尽管这样做不会导致任何错误。

```js
var name = "Nicholas";
name.age = 27;
alert(name.age); //undefined
```

### 复制变量值

从一个变量向另一个变量复制基本类型的值，会在变量对象上创建一个新值，然后把该值复制到为新变量分配的位置上。复制引用类型的值时，同样也会将存储在变量对象中的值复制一份放到为新变量分配的空间中。但是这个值的副本实际上是一个指针，而这个指针指向存储在堆中的一个对象。复制操作结束后，两个变量实际上将引用同一个对象。

```js
var num1 = 5;
var num2 = num1;
console.log(num1,num2); //5,5

var obj1 = new Object();
var obj2 = obj1;
obj1.name = "Nicholas";
console.log(obj2.name); //"Nicholas"
```

### 传递函数

**ECMAScript**中所有函数的参数都是按值传递的。
在向参数传递基本类型的值时，被传递的值会被复制给一个局部变量（即命名参数，或者用**ECMAScript**的概念来说，就是`arguments`对象中的一个元素）。在向参数传递引用类型的值时，会把这个值在内存中的地址复制给一个局部变量，因此这个局部变量的变化会反映在函数的外部。

```js
function addTen(num) {
    num += 10;
    return num;
}
var count = 20;
var result = addTen(count);
console.log(count); //20，没有变化
console.log(result); //30

function setName(obj) {
    obj.name = "Nicholas";
    obj = new Object();
    obj.name = "Greg";
}
var person = new Object();
setName(person);
console.log(person.name); //"Nicholas"
```

### 检测类型

{% blockquote %}

- `typeof`:检测基本数据类型,如果变量的值是`null`，则`typeof`操作符会返回`object`
- `instacnceof`:用于测试构造函数的prototype属性是否出现在对象的原型链中
- `Object.prototype.toString.call()`：返回一个表示类型的字符串，详情请看[MDN官网](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/toString)
{% endblockquote %}

```js
var toString = Object.prototype.toString;
console.log(toString.call(new Date)); // [object Date]
```

~~根据规定，所有引用类型的值都是`Object`的实例。因此，在检测一个引用类型值和`Object`构造函数时，`instanceof`操作符始终会返回`true`~~

```js
var a = Object.create(null)
console.log(a instanceof Object)    // false
```

## 执行环境及作用域

{% blockquote %}

- 执行环境(execution context):每个函数都有自己的执行环境，定义了变量或函数有权访问的其他数据，决定了它们各自的行为,全局执行环境是最外围的一个执行环境。
- 变量对象（variable object）:执行环境中定义的所有变量和函数都保存在这个对象中，我们在编程时无法访问这个对象,但解析器在处理数据时会在后台使用它。如果执行环境是一个函数，则将其活动对象作为变量对象。活动对象在最开始时只包含一个变量（函数的参数），即arguments对象（箭头函数没有arguments对象……而且不鼓励使用arguments对象）；
- 作用域：一套设计良好的、用来存储变量并能够根据名称方便地查找这些变量的规则（《你不知道的JavaScript（上卷）》）；
- 作用域链：从当前执行环境的变量对象出发，将外层每一个执行环境的变量对象串联起来，保证对当前执行环境有权访问的所有变量和函数的有序访问。作用域链本质上是一个指向变量对象的指针列表，它只引用但不实际包含变量对象。
{% endblockquote %}

标识符解析是沿着作用域链一级一级地搜索标识符的过程。搜索过程始终从作用域链的前端开始，然后逐级地向后回溯，直至找到标识符为止（如果找不到标识符，通常会导致错误发生）。

## 垃圾收集

JavaScript具有自动垃圾收集机制，也就是说，执行环境会负责管理代码执行过程中使用的内存。
**待扩展**
