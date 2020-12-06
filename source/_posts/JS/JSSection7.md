---
title: 第七章 函数表达式
tags: JavaScript
categories: JavaScript高级程序设计
abbrlink: f1587c53
date: 2020-04-11 18:24:47
---
定义函数的方式有两种：一种是函数声明，另一种就是函数表达式。关于函数声明，它的一个重要特征就是**函数声明提升**（function declaration hoisting），意思是在执行代码之前会先读取函数声明。这就意味着可以把函数声明放在调用它的语句后面。<!-- more -->
使用函数表达式创建一个函数并将它赋值给变量`functionName`。这种情况下创建的函数叫做匿名函数（anonymous function）（拉姆达函数），因为`function`关键字后面没有标识符。函数表达式与其他表达式一样，在使用前必须先赋值。

```js
// 函数声明
function name () {}

// 函数表达式
var functionName = function(arg0, arg1, arg2){
    //函数体
};

// 会报错，因为JavaScript引擎会将function关键字当作一个函数声明的开始
function () {}

// 由匿名函数构成的函数表达式
(function () {})
```

能够创建函数再赋值给变量，也就能够把函数作为其他函数的值返回。

## 递归

递归函数是在一个函数通过名字调用自身的情况下构成的，如下所示。

```js
function factorial(num){
    if (num <= 1){
        return 1;
    } else {
        return num * factorial(num-1);
    }
}

var anotherFactorial = factorial;
factorial = null;
console.log(anotherFactorial(4)); //出错！factorial is not a function
```

调用`anotherFactorial()`时，由于必须执行`factorial()`，而`factorial`已经不再是函数，所以就会导致错误。
在这种情况下，`arguments.callee`是一个指向正在执行的函数的指针，可以用它来实现对函数的递归调用.

```js
function factorial(num){
    if (num <= 1){
        return 1;
    } else {
        return num * arguments.callee(num-1);
    }
}
```

通过使用`arguments.callee`代替函数名，可以确保无论怎样调用函数都不会出问题。因此，在编写递归函数时，使用`arguments.callee`总比使用函数名更保险。
但在严格模式下，不能通过脚本访问`arguments.callee`，访问这个属性会导致错误。不过，可以使用命名函数表达式来达成相同的结果。

```js
var factorial = (function f(num){
    if (num <= 1){
        return 1;
    } else {
    return num * f(num-1);
    }
});
```

以上代码创建了一个名为`f()`的命名函数表达式，然后将它赋值给变量 `factorial`。即便把函数赋值给了另一个变量，函数的名字`f`仍然有效，所以递归调用照样能正确完成。这种方式在严格模式和非严格模式下都行得通。
[具体原因](https://aadonkeyz.com//posts/107d86cd/#%E5%87%BD%E6%95%B0%E4%BD%9C%E7%94%A8%E5%9F%9F)

## 闭包

闭包是指有权访问另一个函数作用域中的变量的函数。创建闭包的常见方式，就是在一个函数内部创建另一个函数。

```js
function createComparisonFunction(propertyName) {
    return function(object1, object2){
        var value1 = object1[propertyName];
        var value2 = object2[propertyName];
        if (value1 < value2){
            return -1;
        } else if (value1 > value2){
            return 1;
        } else {
            return 0;
        }
    };
}
```

即使内部函数被返回了，而且是在其他地方被调用了，但它仍然可以访问变量`propertyName`。之所以还能够访问这个变量，是因为内部函数的作用域链中包含`createComparisonFunction()`的活动对象（即执行环境的变量对象）的引用。
当`createComparisonFunction()`函数运行结束后，其作用域链被销毁了，但是它的活动对象并没有，它仍然留在内存中等待着被匿名函数调用。直到匿名函数被销毁后，`createComparisonFunction()`的活动对象才会被销毁。
**第四章函数待扩展**

```js

// 创建函数
var compareNames = createComparisonFunction('name')

// 调用函数
var result = compareNames({name: 'Nicholas'}, {name: 'Greg'})

// 解除对匿名函数的引用（以便释放内存）
compareNames = null
```

### 闭包与变量

作用域链的这种配置机制引出了一个值得注意的副作用，即闭包只能取得包含函数中任何变量的最后一个值。

```js
function createFunctions(){
    var result = new Array();
    for (var i=0; i < 3; i++){
        result[i] = function(){
            return i;
        };
    }
    return result;
}
var array = createFunctions()
array[0]()      // 3
array[1]()      // 3
array[2]()      // 3

// 上面的等价形式
function createFunctions () {
    var result = new Array()

    var i = 0
    result[i] = function () {
        return i
    }
    i = 1
    result[i] = function () {
        return i
    }
    i = 2
    result[i] = function () {
        return i
    }
    i = 3
    return result
}
```

每个函数的作用域链中都保存着`createFunctions()`函数的活动对象，所以它们引用的都是同一个变量 i 。当`createFunctions()`函数返回后，变量`i`的值是`3`，此时每个函数都引用着保存变量`i`的同一个变量对象，所以在每个函数内部`i`的值都是`3`。
可以通过创建另一个匿名函数强制让闭包的行为符合预期:

```js
function createFunctions(){
    var result = new Array();
    for (var i=0; i < 3; i++){
        result[i] = function(num){
            return function(){
                return num;
            };
        }(i);
    }
    return result;
}
var array = createFunctions()
array[0]       // 0
array[1]       // 1
array[2]       // 2
```

定义了一个匿名函数，并将立即执行该匿名函数的结果赋给数组。这里的匿名函数有一个参数`num`，也就是最终的函数要返回的值。在调用每个匿名函数时，我们传入了变量`i`。由于函数参数是按值传递的，所以就会将变量 i 的当前值复制给参数`num`。而在这个匿名函数内部，又创建并返回了一个访问 num 的闭包。这样一来，`result`数组中的每个函数都有自己`num`变量的一个副本，因此就可以返回各自不同的数值。

书中使用函数参数是按值传递的性质，解决这个问题。但是返回的数组中包含的是内部函数的返回值而不是内部函数的引用了。

{% blockquote %}
等介绍到ES6的时候，用`let`可以很轻松的解决这里的问题而毫无副作用。
{% endblockquote %}
[煜哥](https://aadonkeyz.com/posts/f1587c53/)
**待扩展**

### 关于 this 对象

在全局函数中，`this`等于`window`，而当函数被作为某个对象的方法调用时，`this`等于那个对象。不过，匿名函数的执行环境具有全局性，因此其`this`对象通常指向`window`。

```js
var name = "The Window";
var object = {
name : "My Object",
getNameFunc : function(){
return function(){
return this.name;
};
}
};
console.log(object.getNameFunc()()); //"The Window"（在非严格模式下）
```

每个函数在被调用时都会自动取得两个特殊变量：`this`和`arguments`
[this进阶](http://localhost:4000/CC/2020/08/04/JS%E8%BF%9B%E9%98%B6/this/)

## 模仿块级作用域
