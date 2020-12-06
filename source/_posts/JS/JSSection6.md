---
title: 第六章 面向对象的程序设计
tags: JavaScript
categories: JavaScript高级程序设计
abbrlink: bb5d40f2
date: 2020-03-28 14:59:46
---
面向对象（Object-Oriented，OO）的语言有一个标志，那就是它们都有类的概念，而通过类可以创建任意多个具有相同属性和方法的对象。
<!-- more -->

## 理解对象

### 属性类型

ECMAScript 中有两种属性：数据属性和访问器属性。

#### 数据类型

{% blockquote %}

- [[Configurable]] ：表示能否通过`delete`删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性修改为访问器属性。直接在对象上定义的属性，它们的这个特性默认值为 true 。
- [[Enumerable]] ：表示能否通过`for-in`循环返回属性。直接在对象上定义的属性，它们的这个特性默认值为 true 。
- [[Writable]] ：表示能否修改属性的值。直接在对象上定义的属性，它们的这个特性默认值为`true`。
- [[Value]] ：包含这个属性的数据值。读取属性值的时候，从这个位置读；写入属性值的时候，把新值保存在这个位置。这个特性的默认值为`undefined`。
{% endblockquote %}

要修改属性默认的特性，必须使用ES5的`Object.defineProperty()`方法。这个方法接收三个参数：属性所在的对象、属性的名字和一个描述符对象。其中描述符对象的属性必须是：`configurable`、`enumerable`、`writable`和`value`。
可通过`getOwnPropertyDescriptor`来查看这些属性描述符的默认值

```js
var obj = { test: 1}
var desc = Object.getOwnPropertyDescriptor(obj, 'test')
console.log(desc)   // {value: 1, writable: true, enumerable: true, configurable: true}

Object.defineProperty(obj, 'demo', {
    configurable: false
})
desc = Object.getOwnPropertyDescriptor(obj, 'demo')
console.log(desc)   // {value: undefined, writable: false, enumerable: false, configurable: false}
```

关于`writable`：当`writable`为`false`时，在非严格模式下通过赋值语句修改属性值，赋值操作将被忽略；在严格模式下则会抛出错误。但是如果通过`Object.defineProperty()`方法修改value特性则不会有任何问题。

```js
var obj = { test: 1}
Object.defineProperty(obj, 'test', {
    writable: false
})
obj.test = 2
console.log(obj)       // {test: 1}
Object.defineProperty(obj, 'test', {
    value: 3
})
console.log(obj)       // {test: 3}
```

关于`configurable`：当`configurable`为`false`时，不允许删除属性，不允许修改属性的`enumerable`、`configurable`，不可以将`writable`由`false`修改为`true`，但是可以将`writable`由`true`修改为`false`，也可以修改属性的`value`特性。如果对这个属性调用`delete`，则在非严格模式下什么也不会发生，而在严格模式下会导致错误。

```js
var obj = { test: 1}
Object.defineProperty(obj, 'test', {
    configurable: false
})
obj.test = 2
console.log(obj)       // {test: 1}
Object.defineProperty(obj, 'test', {
    value: 3
})
console.log(obj)       // {test: 3}

Object.defineProperty(obj,'test', {
    configurable:true
})  //TypeError: Cannot redefine property: test
```

在调用`Object.defineProperty()`方法时，如果不指定，`configurable`、`enumerable`和`writable`特性的默认值都是`false`。

```js
Object.defineProperty(obj,'value',{
    value:1
})  
desc=Object.getOwnPropertyDescriptor(obj,'value')
console.log(desc) //{ value: 1, writable: false, enumerable: false, configurable: false }
obj.value=2  // 在configurable和writable均为false时，尝试修改属性值
console.log(obj.value)  //1
Object.defineProperty(obj,'value',{
    value:2    //TypeError: Cannot redefine property: value
})  
```

当`writable`和`configurable`均为`false`时，不允许通过任何方式修改属性值，直接赋值或者通过value都不可以！

#### 访问器属性

访问器属性不包含数据值；它们包含一对儿`getter`和`setter`函数（不过，这两个函数都不是必需的）。在读取访问器属性时，会调用`getter`函数，这个函数负责返回有效的值；在写入访问器属性时，会调用setter 函数并传入新值，这个函数负责决定如何处理数据。访问器属性有如下 4 个特性。
{% blockquote %}

- [[Configurable]] ：表示能否通过`delete`删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性修改为数据属性。对于直接在对象上定义的属性，这个特性的默认值为true。
- [[Enumerable]] ：表示能否通过`for-in`循环返回属性。对于直接在对象上定义的属性，这个特性的默认值为`true`。
- [[Get]] ：在读取属性时调用的函数。默认值为`undefined`。
- [[Set]] ：在写入属性时调用的函数。默认值为`undefined`。

{% endblockquote %}

访问器属性不能直接定义，必须使用`Object.defineProperty()`来定义。

```js
var book = {
_year: 2004,
edition: 1
};
Object.defineProperty(book, "year", {
    get: function(){
        return this._year;
    },
    set: function(newValue){
        if (newValue > 2004) {
            this._year = newValue;
            this.edition += newValue - 2004;
        }
    }
});
book.year = 2005;
console.log(book); //{ _year: 2005, edition: 2 }
```

以上代码创建了一个 book 对象，并给它定义两个默认的属性： _year 和 edition 。 _year 前面的下划线是一种常用的记号，用于表示只能通过对象方法访问的属性。而访问器属性 year 则包含一个getter 函数和一个 setter 函数。getter 函数返回 _year 的值，setter 函数通过计算来确定正确的版本。因此，把 year 属性修改为 2005 会导致 _year 变成 2005，而edition变为 2。这是使用访问器属性的常见方式，即设置一个属性的值会导致其他属性发生变化。

不一定非要同时指定`getter`和`setter`。只指定`getter`意味着属性是不能写，尝试写入属性会被忽略。在严格模式下，尝试写入只指定了`getter`函数的属性会抛出错误。类似地，只指定`setter`函数的属性也不能读，否则在非严格模式下会返回`undefined`，而在严格模式下会抛出错误。

可以通过`Object.defineProperty()`实现数据属性与访问器属性的转换，但是切记不能同时指定数据属性和访问器属性，这样会抛出错误！

### 定义多个属性

ECMAScript 5定义了一个`Object.defineProperties()`方法来一次定义多个属性。
第一个对象是要添加和修改其属性的对象，第二个对象的属性与第一个对象中要添加或修改的属性一一对应。

```js
var book = {}
Object.defineProperties(book, {
    _year: {
        value
    },
    edition: {
        value: 1
    },
    year: {
        get: function () {
            return this._year
        },
        set: function (newValue) {
            if (newValue > 2004) {
                this._year = newValue
                this.edition += newValue - 2004
            }
        }
    }
})
```

### 读取属性的特性

ECMAScript 5的`Object.getOwnPropertyDescriptor()`方法，可以取得给定属性的描述符。这个方法接收两个参数：属性所在的对象和要读取其描述符的属性名称。返回值是一个对象，如果是访问器属性，这个对象的属性有`configurable`、`enumerable`、`get`和`set`；如果是数据属性，这个对象的属性有`configurable`、`enumerable`、`writable`和`value`。

## 创建对象

### 工厂模式

工厂模式是软件工程领域一种广为人知的设计模式，这种模式抽象了创建具体对象的过程（本书后面还将讨论其他设计模式及其在JavaScript中的实现）。考虑到在ECMAScript 中无法创建类，开发人员就发明了一种函数，用函数来封装以特定接口创建对象的细节。

```js
function createPerson(name, age, job){
    var o = new Object();
    o.name = name;
    o.age = age;
    o.job = job;
    o.sayName = function(){
        console.log(this.name);
    };
    return o;
}
var person1 = createPerson("Nicholas", 29, "Software Engineer");
var person2 = createPerson("Greg", 27, "Doctor");
console.log(person1)
person1.sayName() //Nicholas
```

工厂模式的问题在于它没有解决对象识别的问题（即怎样知道一个对象的类型）。

### 构造函数模式

ECMAScript中的构造函数可用来创建特定类型的对象。像 Object 和 Array 这样的原生构造函数，在运行时会自动出现在执行环境中。此外，也可以创建自定义的构造函数，从而定义自定义对象类型的属性和方法。

```js
function Person(name, age, job){
    this.name = name;
    this.age = age;
    this.job = job;
    this.sayName = function(){
        console.log(this.name);
    };
}
var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");
console.log(person1 instanceof Person)  // true
console.log(person2 instanceof Person)  // true
console.log(person1 instanceof Object); //true
```

在`person1`和`person2`中分别保存着`Person`的不同实例，这两个对象都有一个`constructor`属性，该属性指向`Person`。对象的`constructor`属性最初是用来标识对象类型的，但是提到检测对象类型，还是`instanceof`操作符更可靠一些。创建自定义的构造函数意味着将来可以将它的实例标识为一种特定的类型；而这正是构造函数模式胜过工厂模式的地方。
这个例子中创建的所有对象既是`Object`的实例，同时也是`Person`的实例。因为所有对象均继承自`Object`（详细内容稍后讨论）。

构造函数模式与工厂模式的区别:
{% blockquote %}

- 没有显式地创建对象；
- 直接将属性和方法赋给了 this 对象；
- 没有 return 语句。
{% endblockquote %}

此外，还应该注意到函数名Person使用的是大写字母P。按照惯例，构造函数始终都应该以一个大写字母开头，而非构造函数则应该以一个小写字母开头。这个做法借鉴自其他OO语言，主要是为了区别于ECMAScript中的其他函数；因为构造函数本身也是函数，只不过可以用来创建对象而已。

要创建`Person`的新实例，必须使用`new`操作符。以这种方式调用构造函数实际上会经历以下 步骤：

{% blockquote %}

- 创建一个新对象；
- 将构造函数的作用域赋给新对象（因此 this 就指向了这个新对象）；
- 执行构造函数中的代码（为这个新对象添加属性）；
- 返回新对象。
{% endblockquote %}

构造函数与其他函数的唯一区别，就在于调用它们的方式不同。不过，构造函数毕竟也是函数，不存在定义构造函数的特殊语法。任何函数，只要通过`new`操作符来调用，那它就可以作为构造函数。

```js
// 当作构造函数使用
var person = new Person("Nicholas", 29, "Software Engineer");
person.sayName(); //"Nicholas"
// 作为普通函数调用
Person("Greg", 27, "Doctor"); // 添加到 window
window.sayName(); //"Greg"
// 在另一个对象的作用域中调用
var o = new Object();
Person.call(o, "Kristen", 25, "Nurse");
o.sayName(); //"Kristen"
```

构造函数模式虽然好用，但也并非没有缺点。使用构造函数的主要问题就是每个方法(例子中的sayName)都要在每个实例上重新创建一遍，但是有的方法是所有实例都应该共享的，没有创建多次的必要。

```js
function Person(name, age, job){
this.name = name;
this.age = age;
this.job = job;
this.sayName = sayName;
}
function sayName(){
console.log(this.name);
}
var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");
```

即使将`sayName()`函数的定义转移到了构造函数外部,新问题又来了：在全局作用域中定义的函数实际上只能被某个对象调用，这让全局作用域有点名不副实。而更让人无法接受的是：如果对象需要定义很多方法，那么就要定义很多个全局函数。

### 原型模式

我们创建的每个函数都有一个`prototype`（原型）属性，这个属性是一个指针，指向一个对象，而这个对象的用途是包含可以由特定类型的所有实例共享的属性和方法。 `prototype`就是通过调用构造函数而创建的那个对象实例的原型对象。使用原型对象的好处是可以让所有对象实例共享它所包含的属性和方法。换句话说，不必在构造函数中定义对象实例的信息，而是可以将这些信息直接添加到原型对象中。

```js
function Person () {}
Person.prototype.name = 'Nicholas'
Person.prototype.age = 29
Person.prototype.job = 'Software Engineer'
Person.prototype.sayName = function () {
    console.log(this.name)
}

var person1 = new Person()
person1.sayName()   // Nicholas
var person2 = new Person()
person2.sayName()   // Nicholas
console.log(person1.sayName === person2.sayName)    // true
```

#### 原型对象

![attr](https://ningning-1259809020.cos.ap-chengdu.myqcloud.com/%E5%8E%9F%E5%9E%8B%E7%9A%84%E6%9E%84%E9%80%A0%E5%87%BD%E6%95%B0%E4%B8%8E%E5%AE%9E%E4%BE%8B%E4%B9%8B%E9%97%B4%E7%9A%84%E5%85%B3%E7%B3%BB.png)

上图展示了`Person`构造函数、`Person`的原型属性以及`Person`现有的两个实例之间的关系。在此，`Person.prototype`指向了原型对象，而`Person.prototype.constructor`又指回了`Person`。原型对象中除了包含`constructor`属性之外，还包括后来添加的其他属性。Person 的每个实例——`person1`和`person2`都包含一个内部属性，该属性仅仅指向了`Person.prototype`；换句话说，它们与构造函数没有直接的关系。
这两个实例都不包含属性和方法,调用`person1.sayName()`是通过查找对象属性的过程来实现的。每当代码读取某个对象的某个属性时，都会执行一次搜索，目标是具有给定名字的属性。搜索首先从对象实例本身开始。如果在实例中找到了具有给定名字的属性，则返回该属性的值；如果没有找到，则继续搜索指针指向的原型对象，在原型对象中查找具有给定名字的属性。如果在原型对象中找到了这个属性，则返回该属性的值。

{% blockquote %}

- `isPrototypeOf()`：用于测试一个对象是否存在于另一个对象的原型链上；
- `hasOwnProperty()`:用于检查给定的属性在当前对象实例中（而不是在实例的原型中）是否存在，该方法是从`Object`继承而来的；
- `Object.getPrototypeOf()`：返回指定对象的原型（对象内部`[[Prototype]]`属性的值）。

{% endblockquote %}

```js
console.log(Person.prototype.isPrototypeOf(person1)); //true

console.log(person1.hasOwnProperty("name")); //false
person1.name = "Greg";
console.log(person1.hasOwnProperty("name")); //true
delete person1.name;
console.log(person1.name); //"Nicholas"——来自原型
console.log(person1.hasOwnProperty("name")); //false

console.log(Object.getPrototypeOf(person1) == Person.prototype); //true
```

#### 属性设置和屏蔽

这段待添加 ————————《你不知道的JavaScript（上卷）》 5.1.2
<https://aadonkeyz.com/posts/bb5d40f2/>

当用赋值语句给实例对象设置已经在原型链上层存在的同名属性时，会有以下三种情况：

虽然可以通过对象实例访问保存在原型中的值，但却不能通过对象实例重写原型中的值。如果我们在实例中添加了一个属性，而该属性与实例原型中的一个属性同名，那我们就在实例中创建该属性，该属性将会屏蔽原型中的那个属性。

```js
function Person(){
}
Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function(){
console.log(this.name);
};
var person1 = new Person();
var person2 = new Person();
person1.name = "Greg";
console.log(person1.name); //"Greg" —— 来自实例
console.log(person2.name); //"Nicholas" —— 来自原型
delete person1.name;
console.log(person1.name); //"Nicholas" ——来自原型
```

当为对象实例添加一个属性时，这个属性就会屏蔽原型对象中保存的同名属性；换句话说，添加这个属性只会阻止我们访问原型中的那个属性，但不会修改那个属性。即使将这个属性设置为`null`，也只会在实例中设置这个属性，而不会恢复其指向原型的连接。不过，使用`delete`操作符则可以完全删除实例属性，从而让我们能够重新访问原型中的属性。

#### 原型与`in`操作符

有两种方式使用`in`操作符：单独使用和在`for-in`循环中使用。在单独使用时，`in`操作符会在通过对象能够访问给定属性时返回`true`，无论该属性存在于实例中还是原型中。

```js
console.log("name" in person1); //true
```

使用`for-in`循环时，返回的是所有能够通过对象访问的、可枚举的（`enumerated`）属性，其中既包括存在于实例中的属性，也包括存在于原型中的属性。屏蔽了原型中不可枚举属性（即将`[[Enumerable]]`标记为`false`的属性）的实例属性也会在`for-in`循环中返回，因为根据规定，所有开发人员定义的属性都是可枚举的——只有在IE8及更早版本中例外。

要取得对象上所有可枚举的实例属性，可以使用ECMAScript5的`Object.keys()`方法。这个方法接收一个对象作为参数，返回一个包含所有可枚举属性的字符串数组。

```js
var keys = Object.keys(Person.prototype);
console.log(keys); //"name,age,job,sayName"
var p1 = new Person();
p1.name = "Rob";
p1.age = 31;
var p1keys = Object.keys(p1);
console.log(p1keys); //"name,age"
```

如果想要得到所有实例属性，无论它是否可枚举，都可以使用`Object.getOwnPropertyNames()`方法。

```js
var keys = Object.getOwnPropertyNames(Person.prototype);
console.log(keys); //"constructor,name,age,job,sayName"
```

#### 更简单的原型语法

为减少不必要的输入，也为了从视觉上更好地封装原型的功能，更常见的做法是用一个包含所有属性和方法的对象字面量来重写整个原型对象。

```js
function Person(){
    }
Person.prototype = {
    name : "Nicholas",
    age : 29,
    job: "Software Engineer",
    sayName : function () {
            console.log(this.name);
        }
};

var friend = new Person()
console.log(friend instanceof Object)       // true
console.log(friend instanceof Person)       // true
console.log(friend.constructor === Object)  // true
console.log(friend.constructor === Person)  // false

Object.defineProperty(Person.prototype, 'constructor', {
    enumerable: false,
    value: Person
})
```

最终结果相同，但有一个例外：`constructor`属性不再指向`Person`了。本质上完全重写了默认的`prototype`对象，因此`constructor`属性也就变成了新对象的`constructor`属性（指向`Object`构造函数），不再指向`Person`函数。此时，尽管 `instanceof`操作符还能返回正确的结果，但通过`constructor`已经无法确定对象的类型。

#### 原型的动态性

由于在原型中查找值的过程是一次搜索，因此我们对原型对象所做的任何修改都能够立即从实例上反映出来——即使是**先创建了实例后修改原型**也照样如此。

```js

function Person () {}
var friend = new Person ()
Person.prototype.sayHi = function () {
    console.log('hi')
}

friend.sayHi()      // hi
```

尽管可以随时为原型添加属性和方法，并且修改能够立即在所有对象实例中反映出来，但如果是重写整个原型对象，那么情况就不一样了。调用构造函数时会为实例添加一个指向最初原型的`[[Prototype]]`指针，而把原型修改为另外一个对象就等于切断了构造函数与最初原型之间的联系。请记住：实例中的指针仅指向原型，而不指向构造函数。

```js
function Person () {}

var friend = new Person()

Person.prototype = {
    name: 'Nicholas',
    age: 29,
    job: 'Software Engineer',
    sayName: function () {
        console.log(this.name)
    }
}
Object.defineProperty(Person.prototype, 'constructor', {
    enumerable: false,
    value: Person
})

console.log(friend instanceof Object)       // true
console.log(friend instanceof Person)       // false

friend.sayName()              // Uncaught TypeError: friend.sayName is not a function
```

![attr](https://blog-images-1258719270.cos.ap-shanghai.myqcloud.com/%E3%80%8AJavaScript%E9%AB%98%E7%BA%A7%E7%A8%8B%E5%BA%8F%E8%AE%BE%E8%AE%A1%E3%80%8B/6-3%E9%87%8D%E5%86%99%E5%8E%9F%E5%9E%8B%E5%AF%B9%E8%B1%A1.png)

重写原型对象切断了现有原型与任何之前已经存在的对象实例之间的联系；它们引用的仍然是最初的原型。

#### 原生对象的原型

原型模式的重要性不仅体现在创建自定义类型方面，就连所有原生的引用类型，都是采用这种模式创建的。所有原生引用类型（`Object`、`Array`、`String`，等等）都在其构造函数的原型上定义了方法。

```js
console.log(typeof Array.prototype.sort); //"function"
console.log(typeof String.prototype.substring); //"function"
```

#### 原生对象的问题

原型模式也不是没有缺点。首先，它省略了为构造函数传递初始化参数这一环节，结果所有实例在默认情况下都将取得相同的属性值。虽然这会在某种程度上带来一些不方便，但还不是原型的最大问题。原型模式的最大问题是由其共享的本性所导致的。
原型中所有属性是被很多实例共享的，这种共享对于函数非常合适。对于那些包含基本值的属性，通过在实例上添加一个同名属性，可以隐藏原型中的对应属性。然而，对于包含引用类型值的属性来说，问题就比较突出了。

```js
function Person(){
}
Person.prototype = {
constructor: Person,
name : "Nicholas",
age : 29,
job : "Software Engineer",
friends : ["Shelby", "Court"],
sayName : function () {
console.log(this.name);
}
};
var person1 = new Person();
var person2 = new Person();
person1.friends.push("Van");
console.log(person1.friends); //"Shelby,Court,Van"
console.log(person2.friends); //"Shelby,Court,Van"
console.log(person1.friends === person2.friends); //true
```

### 组合使用构造函数模式和原型模式

创建自定义类型的最常见方式，就是组合使用构造函数模式与原型模式。构造函数模式用于定义实例属性，而原型模式用于定义方法和共享的属性。结果，每个实例都会有自己的一份实例属性的副本，但同时又共享着对方法的引用，最大限度地节省了内存。另外，这种混成模式还支持向构造函数传递参数；可谓是集两种模式之长。

```js
function Person(name, age, job){
    this.name = name;
    this.age = age;
    this.job = job;
    this.friends = ["Shelby", "Court"];
}
Person.prototype = {
    constructor : Person,
    sayName : function(){
        console.log(this.name);
    }
}
var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");
person1.friends.push("Van");
console.log(person1.friends); //"Shelby,Count,Van"
console.log(person2.friends); //"Shelby,Count"
console.log(person1.friends === person2.friends); //false
console.log(person1.sayName === person2.sayName); //true
```

这种构造函数与原型混成的模式，是目前在ECMAScript中使用最广泛、认同度最高的一种创建自定义类型的方法。可以说，这是用来定义引用类型的一种默认模式。

### 动态原型模式

动态原型模式是致力于解决**独立的构造函数和原型**的一个方案，它把所有信息都封装在了构造函数中，而通过在构造函数中初始化原型（仅在必要的情况下），又保持了同时使用构造函数和原型的优点。换句话说，可以通过检查某个应该存在的方法是否有效，来决定是否需要初始化原型。

```js
function Person (name, age, job) {
    // 属性
    this.name = name
    this.age = age
    this.job = job

    // 方法
    if (typeof this.sayName !== 'function') {
        Person.prototype.sayName = function () {
            console.log(this.name)
        }
    }
}

var friend = new Person('Nicholas', 29, 'Software Engineer')
friend.sayName()    // Nicholas
```

**使用动态原型模式时，不能使用对象字面量重写原型**。前面已经解释过了，如果在已经创建了实例的情况下重写原型，那么就会切断现有实例与新原型之间的联系。

### 寄生构造函数模式

通常，在前述的几种模式都不适用的情况下，可以使用寄生（**parasitic**）构造函数模式。这种模式的基本思想是创建一个函数，该函数的作用仅仅是封装创建对象的代码，然后再返回新创建的对象；但从表面上看，这个函数又很像是典型的构造函数。

```js
function Person (name, age, job) {
    var o = new Object()
    o.name = name
    o.age = age
    o.job = job
    o.sayName = function () {
        console.log(this.name)
    }
    return o
}

var friend = new Person('Nicholas', 29, 'Software Engineer')
friend.sayName()    // Nicholas
```

在这个例子中，`Person`函数创建了一个新对象，并以相应的属性和方法初始化该对象，然后又返回了这个对象。除了使用`new`操作符并把使用的包装函数叫做构造函数之外，这个模式跟工厂模式其实是一模一样的。构造函数在不返回值的情况下，默认会返回新对象实例。而通过在构造函数的末尾添加一个`return`语句，可以重写调用构造函数时返回的值。

关于寄生构造函数模式，有一点需要说明：首先，返回的对象与构造函数或者与构造函数的原型属性之间没有关系；也就是说，构造函数返回的对象与在构造函数外部创建的对象没有什么不同。为此，不能依赖`instanceof`操作符来确定对象类型。由于存在上述问题，我们建议在可以使用其他模式的情况下，不要使用这种模式。

### 稳妥构造函数模式

道格拉斯·克罗克福德（Douglas Crockford）发明了JavaScript中的稳妥对象（durable objects）这个概念。所谓稳妥对象，指的是没有公共属性，而且其方法也不引用`this`的对象。稳妥对象最适合在一些安全的环境中（这些环境中会禁止使用`this`和`new`），或者在防止数据被其他应用程序（如 Mashup程序）改动时使用。稳妥构造函数遵循与寄生构造函数类似的模式，但有两点不同：一是新创建对象的实例方法不引用`this`；二是不使用`new`操作符调用构造函数。按照稳妥构造函数的要求，可以将前面
的 Person 构造函数重写如下。

```js
function Person (name, age, job) {
    // 创建要返回的对象
    var o = new Object()
    // 可以在这里定义私有变量和函数
    // 添加方法
    o.sayName = function () {
        console.log(name)
    }
    return o
}

var friend = Person('Nicholas', 29, 'Software Engineer')
friend.sayName()    // Nicholas
// console.log(friend.sayName())
let desc =Object.getOwnPropertyDescriptor(friend,'name')
```

变量`friend`中保存的是一个稳妥对象，而除了调用`sayName()`方法外，没有别的方式可以访问其数据成员。即使有其他代码会给这个对象添加方法或数据成员，但也不可能有别的办法访问传入到构造函数中的原始数据。

### 小结

![attr](https://blog-images-1258719270.cos.ap-shanghai.myqcloud.com/《JavaScript高级程序设计》/创建对象模式总结.png)

## 继承

继承是OO语言中的一个最为人津津乐道的概念。许多OO语言都支持两种继承方式：接口继承和实现继承。接口继承只继承方法签名，而实现继承则继承实际的方法。如前所述，由于函数没有签名，在 ECMAScript 中无法实现接口继承。ECMAScript 只支持实现继承，而且其实现继承主要是依靠原型链来实现的。

### 原型链

基本思想是利用原型让一个引用类型继承另一个引用类型的属性和方法。简单回顾一下构造函数、原型和实例的关系：每个构造函数都有一个原型对象，原型对象都包含一个指向构造函数的指针，而实例都包含一个指向原型对象的内部指针。那么，假如我们让原型对象等于另一个类型的实例，结果会怎么样呢？显然，此时的原型对象将包含一个指向另一个原型的指针，相应地，另一个原型中也包含着一个指向另一个构造函数的指针。假如另一个原型又是另一个类型的实例，那么上述关系依然成立，如此层层递进，就构成了实例与原型的链条。这就是所谓原型链的基本概念。

```js
function SuperType () {
    this.property = true
}
SuperType.prototype.getSuperValue = function () {
    return this.property
}

function SubType () {
    this.subproperty = false
}
SubType.prototype = new SuperType()
SubType.prototype.getSubValue = function () {
    return this.subproperty
}

var instance = new SubType()
console.log(instance.getSuperValue())   // true
```

![attr](https://blog-images-1258719270.cos.ap-shanghai.myqcloud.com/%E3%80%8AJavaScript%E9%AB%98%E7%BA%A7%E7%A8%8B%E5%BA%8F%E8%AE%BE%E8%AE%A1%E3%80%8B/6-4%E5%8E%9F%E5%9E%8B%E9%93%BE.png)

调用`instance.getSuperValue()`会经历三个搜索步骤：
{% blockquote %}
1）搜索实例；
2）搜索`SubType.prototype`；
3）搜索`SuperType.prototype`，最后一步才会找到该方法。在找不到属性或方法的情况下，搜索过程总是要一环一环地前行到原型链末端才会停下来。
{% endblockquote %}

可以通过两种方式来确定原型和实例之间的关系:`instanceof`操作符，只要用这个操作符来测试实例与原型链中出现过的构造函数，结果就会返回`true`。
`isPrototypeOf()`方法。同样，只要是原型链中出现过的原型，都可以说是该原型链所派生的实例的原型，因此`isPrototypeOf()`方法也会返回`true`。

```js
console.log(instance instanceof Object); //true
console.log(instance instanceof SuperType); //true
console.log(instance instanceof SubType); //true
```

```js
console.log(Object.prototype.isPrototypeOf(instance)); //true
console.log(SuperType.prototype.isPrototypeOf(instance)); //true
console.log(SubType.prototype.isPrototypeOf(instance)); //true
```

子类型有时候需要重写超类型中的某个方法，或者需要添加超类型中不存在的某个方法。但不管怎样，给原型添加方法的代码一定要放在替换原型的语句之后。

```js
//继承了 SuperType
SubType.prototype = new SuperType();
// 添加新方法
SubType.prototype.getSubValue = function (){
    return this.subproperty;
};
// 重写超类型中的方法
SubType.prototype.getSuperValue = function (){
    return false;
};
```

**通过原型链实现继承时，不能使用对象字面量创建原型方法。**

原型链的第一个问题类似于上面介绍的原型模式的问题——共享着同一引用类型。
它的第二个问题是在创建子类型的实例时，不能向超类型的构造函数中传递参数——没有办法在不影响所有对象实例的情况下，给超类型的构造函数传递参数。

### 使用构造函数

在解决原型中包含引用类型值所带来问题的过程中，借用构造函数（有时候也叫做伪造对象或经典继承），在子类型构造函数的内部调用超类型构造函数，通过使用`apply()`和`call()`方法也可以在（将来）新创建的对象上执行构造函数。

```js
function SuperType(){
    this.colors = ["red", "blue", "green"];
}
function SubType(){
    // 继承了 SuperType
    SuperType.call(this);
}
var instance1 = new SubType();
instance1.colors.push("black");
console.log(instance1.colors); //"red,blue,green,black"
var instance2 = new SubType();
console.log(instance2.colors); //"red,blue,green"
console.log(instance2 instanceof SubType)      //true
console.log(instance2 instanceof SuperType)     //flase
console.log(SubType.prototype.isPrototypeOf(instance2))     //true
console.log(SuperType.prototype.isPrototypeOf(instance2))   //flase
```

相对于原型链而言，借用构造函数有一个很大的优势，即可以在子类型构造函数中向超类型构造函数传递参数。

```js

function SuperType (name) {
    this.name = name
}

function SubType () {
    // 继承了SuperType，同时还传递了参数
    SuperType.call(this, 'Nicholas')

    // 实例属性
    this.age = 29
}

var instance = new SubType()
console.log(instance.name)      // Nicholas
console.log(instance.age)       // 29
```

如果仅仅是借用构造函数，那么也将无法避免构造函数模式存在的问题——方法都在构造函数中定义，因此函数复用就无从谈起了。而且，在超类型的原型中定义的方法，对子类型而言也是不可见的，结果所有类型都只能使用构造函数模式。考虑到这些问题，借用构造函数的技术也是很少单独使用的。

### 组合继承

组合继承（combination inheritance），有时候也叫做伪经典继承，指的是将原型链和借用构造函数的技术组合到一块，从而发挥二者之长的一种继承模式。其背后的思路是使用原型链实现对原型属性和方法的继承，而通过借用构造函数来实现对实例属性的继承。这样，既通过在原型上定义方法实现了函数复用，又能够保证每个实例都有它自己的属性。

```js
function SuperType (name) {
    this.name = name
    this.colors = ['red', 'blue', 'green']
}
SuperType.prototype.sayName = function () {
    console.log(this.name)
}

function SubType (name, age) {
    // 继承属性
    SuperType.call(this, name)

    this.age = age
}
// 继承方法
SubType.prototype = new SuperType()
SubType.prototype.sayAge = function () {
    console.log(this.age)
}

var instance1 = new SubType('Nicholas', 29)
instance1.colors.push('black')
console.log(instance1.colors)       // ["red", "blue", "green", "black"]
instance1.sayName()                 // Nicholas
instance1.sayAge()                  // 29

var instance2 = new SubType('Greg', 27)
console.log(instance2.colors)       // ["red", "blue", "green"]
instance2.sayName()                 // Greg
instance2.sayAge()                  // 27
```

两个实例上的`colors`属性屏蔽了原型链上的同名属性,两个不同的 SubType 实例既分别拥有自己属性——包括`colors`属性，又可以使用相同的方法。
组合继承避免了原型链和借用构造函数的缺陷，融合了它们的优点，成为 JavaScript 中最常用的继承模式。而且，`instanceof`和`isPrototypeOf()`也能够用于识别基于组合继承创建的对象。

### 原型式继承

道格拉斯·克罗克福德在 2006年写了一篇文章，题为 Prototypal Inheritance in JavaScript （JavaScript中的原型式继承）。想法是借助原型可以基于已有的对象创建新对象，同时还不必因此创建自定义类型。

```js
function object(o){
    function F(){}
    F.prototype = o;
    return new F();
}
```

在`object()`函数内部，先创建了一个临时性的构造函数，然后将传入的对象作为这个构造函数的原型，最后返回了这个临时类型的一个新实例。从本质上讲，`object()`对传入其中的对象执行了一次浅复制。

ECMAScript 5通过新增`Object.create()`方法规范化了原型式继承。这个方法接收两个参数：一个用作新对象原型的对象和（可选的）一个为新对象定义额外属性的对象。在传入一个参数的情况下，`Object.create()`与`object()`方法的行为相同。

```js
var person = {
    name: "Nicholas",
    friends: ["Shelby", "Court", "Van"]
};
var anotherPerson = Object.create(person);
anotherPerson.name = "Greg";
anotherPerson.friends.push("Rob");
var yetAnotherPerson = Object.create(person);
yetAnotherPerson.name = "Linda";
yetAnotherPerson.friends.push("Barbie");
console.log(person.friends); //"Shelby,Court,Van,Rob,Barbie"
```

`Object.create()`方法的第二个参数与`Object.defineProperties()`方法的第二个参数格式相同：每个属性都是通过自己的描述符定义的。以这种方式指定的任何属性都会覆盖原型对象上的同名属性。

```js
var person = {
    name: "Nicholas",
    friends: ["Shelby", "Court", "Van"]
};
var anotherPerson = Object.create(person, {
    name: {
    value: "Greg"
    }
});
console.log(anotherPerson.name); //"Greg"
```

只想让一个对象与另一个对象保持类似的情况下，原型式继承是完全可以胜任的。和使用原型模式一样，引用类型值的属性始终都会共享相应的值。

### 寄生式继承

寄生式（parasitic）继承是与原型式继承紧密相关的一种思路，并且同样也是由克罗克福德推而广之的。寄生式继承的思路与寄生构造函数和工厂模式类似，即创建一个仅用于封装继承过程的函数，该函数在内部以某种方式来增强对象，最后再像真地是它做了所有工作一样返回对象。

```js

function object (o) {
    function F () {}
    F.prototype = o
    return new F()
}

function createAnother (original) {
    var clone = object(original)    // 通过调用函数创建一个新对象
    clone.sayHi = function () {     // 以某种方式来增强这个对象
        console.log('hi')
    }
    return clone                    // 返回这个对象
}

var person = {
    name: 'Nicholas',
    friengd: ['Shelby', 'Court', 'Van']
}
var anotherPerson = createAnother(person)
anotherPerson.sayHi()       // hi
```

### 寄生组合式继承

前面说过，组合继承是`JavaScript`最常用的继承模式；不过，它也有自己的不足。组合继承最大的问题就是无论什么情况下，都会调用两次超类型构造函数：一次是在创建子类型原型的时候，另一次是在子类型构造函数内部。没错，子类型最终会包含超类型对象的全部实例属性，但我们不得不在调用子类型构造函数时重写这些属性。

```js

function SuperType (name) {
    this.name = name
    this.colors = ['red', 'blue', 'green']
}
SuperType.prototype.sayName = function () {
    console.log(this.name)
}

function SubType (name, age) {
    SuperType.call(this, name)              // 第二次调用SuperType()

    this.age = age
}

SubType.prototype = new SuperType()        // 第一次调用SuperType()
SubType.prototype.sayAge = function () {
    console.log(this.age)
}
```

有两组`name`和`colors`属性：一组在实例上，一组在`SubType`原型中。这就是调
用两次`SuperType`构造函数的结果。好在我们已经找到了解决这个问题方法——寄生组合式继承。
**照片待添加JS书173页**

所谓寄生组合式继承，即通过借用构造函数来继承属性，通过原型链的混成形式来继承方法。其背后的基本思路是：不必为了指定子类型的原型而调用超类型的构造函数，我们所需要的无非就是超类型原型的一个副本而已。本质上，就是使用寄生式继承来继承超类型的原型，然后再将结果指定给子类型的原型。寄生组合式继承的基本模式如下所示：

```js

function inheritPrototype (subType, superType) {
    var prototype = Object(superType.prototype)     // 创建对象
    prototype.constructor = subType                 // 增强对象
    subType.prototype = prototype                   // 指定对象
}

function SuperType (name) {
    this.name = name
    this.colors = ['red', 'blue', 'green']
}
SuperType.prototype.sayName = function () {
    console.log(this.name)
}

function SubType (name, age) {
    SuperType.call(this, name)

    this.age = age
}

inheritPrototype(SubType, SuperType)

SubType.prototype.sayAge = function () {
    console.log(this.age)
}
instance1=new SubType('1',11)
instance1.sayAge() //11
```

这个例子的高效率体现在它只调用了一次`SuperType`构造函数，并且因此避免了在 `SubType.prototype`上面创建不必要的、多余的属性。与此同时，原型链还能保持不变；因此，还能够正常使用`instanceof`和`isPrototypeOf()`。开发人员普遍认为寄生组合式继承是引用类型最理想的继承范式。

### 小结

[!attr](https://blog-images-1258719270.cos.ap-shanghai.myqcloud.com/%E3%80%8AJavaScript%E9%AB%98%E7%BA%A7%E7%A8%8B%E5%BA%8F%E8%AE%BE%E8%AE%A1%E3%80%8B/%E7%BB%A7%E6%89%BF%E6%80%BB%E7%BB%93.png)
