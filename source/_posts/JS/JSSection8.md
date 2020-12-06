---
title: 第八章 BOM
tags: JavaScript
categories: JavaScript高级程序设计
abbrlink: 3970683f
date: 2020-05-11 08:01:38
---
**BOM**（浏览器对象模型）提供了很多对象，用于访问浏览器的功能，这些功能与任何网页内容无关，但是提供了与浏览器的互操作性。<!-- more -->W3C为了把浏览器中 JavaScript最基本的部分标准化，已经将 BOM的主要方面纳入了 HTML5的规范中。

## `window`对象

BOM的核心对象是`window`，它表示浏览器的一个实例。在浏览器中，`window`对象有双重角色，它既是通过JavaScript访问浏览器窗口的一个接口，又是ECMAScript规定的`Global`对象。这意味着在网页中定义的任何一个对象、变量和函数，都以`window`作为其`Global`对象，因此有权访问`parseInt()`等方法。

### 全局作用域

由于`window`对象同时扮演着ECMAScript中`Global`对象的角色，因此所有在全局作用域中声明的变量、函数都会变成`window`对象的属性和方法。

```js
var age = 29;
function sayAge(){
    console.log(this.age);
}
console.log(window.age); //29
sayAge(); //29
window.sayAge(); //29
```

全局变量不能通过 delete 操作符删除，而直接在 window 对象上的定义的属性可以。

```js
var age = 29;
window.color = "red";
//在 IE < 9 时抛出错误，在其他所有浏览器中都返回 false
delete window.age;
//在 IE < 9 时抛出错误，在其他所有浏览器中都返回 true
delete window.color; //returns true
alert(window.age); //29
alert(window.color); //undefined
```

使用`var`语句添加的`window`属性对应的[[Configurable]] 的特性设置为`false`，不可以通过`delete`操作符删除。

尝试访问未声明的变量会抛出错误，但是通过查询`window`对象，可以知道某个可能未声明的变量是否存在。

```js
//这里会抛出错误，因为 oldValue 未定义
var newValue = oldValue;

//这里不会抛出错误，因为这是一次属性查询
//newValue 的值是 undefined
var newValue = window.oldValue;
```

### 窗口关系及框架

## `location`对象

`location`对象既是`window`对象的属性，也是`document`对象的属性；换句话说，`window.location`和`document.location`引用的是同一个对象。`location`对象保存着当前文档的信息，而且它将`URL`解析为独立的片段，让开发人员可以通过不同的属性访问这些片段。
P207

### 位置操作

使用`location`对象可以通过很多方式来改变浏览器的位置。

```js
location.assign("http://www.wrox.com");
```

立即打开新`URL`并在浏览器的历史记录中生成一条记录。如果是将`location.href`或`window.location`设置为一个`URL`值，也会以该值调用`assign()`方法。

```js
window.location = "http://www.wrox.com";
location.href = "http://www.wrox.com";  //最常用的设置
```

此外，修改`location`对象的其他属性也可以改变当前加载的页面。

```js
//假设初始 URL 为 http://www.wrox.com/WileyCDA/

//将 URL 修改为"http://www.wrox.com/WileyCDA/#section1"
location.hash = "#section1";

//将 URL 修改为"http://www.wrox.com/WileyCDA/?q=javascript"
location.search = "?q=javascript";

//将 URL 修改为"http://www.yahoo.com/WileyCDA/"
location.hostname = "www.yahoo.com";

//将 URL 修改为"http://www.yahoo.com/mydir/"
location.pathname = "mydir";

//将 URL 修改为"http://www.yahoo.com:8080/WileyCDA/"
location.port = 8080;
```

当通过上述任何一种方式修改`URL`之后，浏览器的历史记录中就会生成一条新记录。

每次修改`location`的属性（`hash`除外），页面都会以新`URL`重新加载。要禁用这种行为，可以使用`replace()`方法。这个方法只接受一个参数，即要导航到的`URL`；结果虽然会导致浏览器位置改变，但不会在历史记录中生成新记录。

`reload()`重新加载当前显示的页面,调用时不传递任何参数，页面就会以最有效的方式重新加载。如果页面自上次请求以来并没有改变过，页面就会从浏览器缓存中重新加载。如果要强制从服务器重新加载，则需要像下面这样为该方法传递参数`true`。

```js
location.reload(); //重新加载（有可能从缓存中加载）
location.reload(true); //重新加载（从服务器重新加载）
```

位于`reload()`调用之后的代码可能不会执行，所以将`reload()`放在代码的最后一行。

## `navigator`对象

待处理

## `screen`对象

`screen`对象基本上只用来表明客户端的能力，其中包括浏览器窗口外部的显示器的信息，如像素宽度和高度等。

## `history`对象

`history`对象保存着用户上网的历史记录，从窗口被打开的那一刻算起。因为`history`是`window`对象的属性，因此每个浏览器窗口、每个标签页乃至每个框架，都有自己的`history`对象与特定的`window`对象关联。

借由用户访问过的页面列表，同样可以在不知道实际`URL`的情况下实现后退和前进。
使用`go()`方法可以在用户的历史记录中任意跳转，可以向后也可以向前。这个方法接受一个参数，表示向后或向前跳转的页面数的一个整数值。负数表示向后跳转（类似于单击浏览器的“后退”按钮），正数表示向前跳转（类似于单击浏览器的“前进”按钮）。

```js
//后退一页
history.go(-1);

//前进一页
history.go(1);

//前进两页
history.go(2);

//跳转到最近的 wrox.com 页面
history.go("wrox.com");

//跳转到最近的 nczonline.net 页面
history.go("nczonline.net");
```

也可以给`go()`方法传递一个字符串参数，此时浏览器会跳转到历史记录中包含该字符串的第一个位置——可能后退，也可能前进，具体要看哪个位置最近。如果历史记录中不包含该字符串，那么这个方法什么也不做;

`back()`和`forward()`可代替`go()`;

```js
//后退一页
history.back();

//前进一页
history.forward();
```

`history`对象的`length`属性，保存着历史记录的数量。这个数量
包括所有历史记录，即所有向后和向前的记录。对于加载到窗口、标签页或框架中的第一个页面而言，`history.length`等于`0`。

```js
if (history.length == 0){
    //这应该是用户打开窗口后的第一个页面
}
```

{% blockquote %}
当页面的 URL 改变时，就会生成一条历史记录。在 IE8 及更高版本、Opera、Firefox、Safari 3 及更高版本以及 Chrome 中，这里所说的改变包括 URL 中 hash 的变化（因此，设置 location.hash 会在这些浏览器中生成一条新的历史记录）。
{% endblockquote %}

## 小结

浏览器对象模型（BOM）以`window`对象为依托，表示浏览器窗口以及页面可见区域。同时，`window`对象还是`ECMAScript`中的`Global`对象，因而所有全局变量和函数都是它的属性，且所有原生的构造函数及其他函数也都存在于它的命名空间下。

{% blockquote %}

- 使用`location`对象可以通过编程方式来访问浏览器的导航系统。设置相应的属性，可以逐段或整体性地修改浏览器的`URL`。
- 调用`replace()`方法可以导航到一个新`URL`，同时该`URL`会替换浏览器历史记录中当前显示的页面。

{% endblockquote %}
`screen`对象中保存着与客户端显示器有关的信息，这些信息一般只用于站点分析。利用`history`对象判断历史记录的数量，也可以在历史记录中向后或向前导航到任意页面。
