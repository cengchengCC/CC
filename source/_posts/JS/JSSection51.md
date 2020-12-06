---
title: 引用类型 Array
tags: JavaScript
categories: JavaScript高级程序设计
abbrlink: 8b81097c
date: 2020-03-23 11:48:09
---
Array 和Object类型是 ECMAScript 中最常用的类型。而且，ECMAScript 中的数组与其他多数语言中的数组有着相当大的区别。虽然 ECMAScript 数组与其他语言中的数组都是数据的有序列表，但与其他语言不同的是，ECMAScript 数组的每一项可以保存任何类型的数据。ECMAScript 数组的大小是可以动态调整的，即可以随着数据的添加自动增长以容纳新增数据。
<!-- more -->
## 创建数组

1.利用Array构造函数

``` js
var colors = new Array(args)
var colors = Array(args)
```

其中args可以为空，也可以为一个数值或是字符串，当为数值时，生成一个长度为该数值的数组，当为字符串时，生成一个包含 1 项，即该字符串的数组；其中，new可以省略
2.利用数组字面量表示法进行创建

``` js
var colors = ['1','2','3']
```

其中数组中最后一项不要为逗号，如为逗号将产生一个长度不定的数组(IE8及以前)

## 数组索引

在读取和设置数组的值时，利用方括号并提供相应的数字索引，数字索引是从0开始：

```js
var colors = ["red", "blue", "green"]; // 创建一个包含 3 个字符串的数组
colors[2] = "black"; // 修改第三项
```

数组的 length 属性很有特点——它不是只读的。因此，通过设置这个属性，可以从数组的末尾移除项或向数组中添加新项。

```js
var colors = ["red", "blue", "green"];
colors.length = 2;
alert(colors[2]); //undefined
colors[colors.length] = "black"; // （在位置 2 ）添加一种颜色
```

## 检测数组

对于一个网页，或者一个全局作用域而言，使用 instanceof 操作符就能得到满意的结果：

```js
if (value instanceof Array){
    console.log(true)//对数组执行某些操作
}
```

instanceof 操作符的问题在于，它假定只有一个全局执行环境。如果网页中包含多个框架，那实际上就存在两个以上不同的全局执行环境，从而存在两个以上不同版本的 Array 构造函数。如果你从一个框架向另一个框架传入一个数组，那么传入的数组与在第二个框架中原生创建的数组分别具有各自不同的构造函数。

为了解决上述问题，ECMAScript5新增了Array.isArray()方法。这个方法的目的是最终确定某个值到底是不是数组，而不管它是在哪个全局执行环境中创建的。

```js
if (Array.isArray(value)){
    //对数组执行某些操作
}
```

## 转换方法

所有对象都具有 toLocaleString() 、 toString() 和 valueOf() 方法。其中，调用数组的 toString() 方法会返回由数组中每个值的字符串形式拼接而成的一个以逗号分隔的字符串。而调用 valueOf() 返回的还是数组。实际上，为了创建这个字符串会调用数组每一项的 toString() 方法。

```js
var colors = ["red", "blue", "green"]; // 创建一个包含 3 个字符串的数组
alert(colors.toString()); // red,blue,green
alert(colors.valueOf()); // red,blue,green
alert(colors); // red,blue,green
```

由于 alert() 要接收字符串参数，所以它会在后台调用 toString() 方法，由此会得到与直接调用 toString() 方法相同的结果。

使用 join() 方法，则可以使用不同的分隔符来构建这个字符串。 join() 方法只接收一个参数，即用作分隔符的字符串，然后返回包含所有数组项的字符串。

```js
var colors = ["red", "green", "blue"];
alert(colors.join("||")); //red||green||blue
```

## 栈方法

ECMAScript 数组也提供了一种让数组的行为类似于其他数据结构的方法。push() 方法可以接收任意数量的参数，把它们逐个添加到数组末尾，并返回修改后数组的长度。pop() 方法则从数组末尾移除最后一项，减少数组的 length 值，然后返回移除的项。

```js
var colors = new Array(); // 创建一个数组
var count = colors.push("red", "green"); // 推入两项
console.log(count); //2
var item = colors.pop(); // 取得最后一项
console.log(item); //"black"
console.log(colors.length); //2
```

## 队列方法

队列在列表的末端添加项，从列表的前端移除项。模拟队列只需从数组前端取得项的方法——shift() ，它能够移除数组中的第一个项并返回该项，同时将数组长度减1。

```js
var colors = new Array(); //创建一个数组
var count = colors.push("red", "green"); //推入两项
console.log(count); //2
var item = colors.shift(); // 取得第一项
console.log(item); //"red"
```

ECMAScript 还为数组提供了一个 unshift() 方法:在数组前端添加任意个项并返回新数组的长度。

```js
var colors = new Array(); //创建一个数组
var count = colors.unshift("red", "green"); //2
```

## 重排序方法

数组中已经存在两个可以直接用来重排序的方法：reverse()和sort()。reverse() 方法会反转数组项的顺序

```js
var values = [1, 2, 3, 4, 5];
console.log(values.reverse())  \\5,4,3,2,1
```

在默认情况下， sort() 方法按升序排列数组项——即最小的值位于最前面，最大的值排在最后面。为了实现排序， sort() 方法会调用每个数组项的 toString() 转型方法，然后比较得到的字符串，以确定如何排序。即使数组中的每一项都是数值， sort() 方法比较的也是字符串。

```js
var values = [0, 1, 5, 10, 15];
values.sort();
console.log(values); //0,1,10,15,5
```

这种排序方式在很多情况下都不是最佳方案。因此sort()方法可以接收一个比较函数作为参数，以便我们指定哪个值位于哪个值的前面。

```js
var values = [0, 1, 5, 10, 15];
values.sort(compare);
console.log(values); //0,1,5,10,15
function compare(value1,value2){
    if (value1<value2){
        return -1;
    } else if(value1>value2){
        return 1;
    } else if(value1=value2){
        return 0
    }
}
```

对于数值类型或者其 valueOf() 方法会返回数值类型的对象类型，可以使用一个更简单的比较函数。这个函数只要用第二个值减第一个值即可。

```js
function compare(value1, value2){
    return value2 - value1;
}
```

## 操作方法

concat()方法可以基于当前数组中的所有项创建一个新数组。具体来说，这个方法会先创建当前数组一个副本，然后将接收到的参数添加到这个副本的末尾，最后返回新构建的数组。在没有给 concat() 方法传递参数的情况下，它只是复制当前数组并返回副本。如果传递给 concat() 方法的是一或多个数组，则该方法会将这些数组中的每一项都添加到结果数组中。如果传递的值不是数组，这些值就会被简单地添加到结果数组的末尾。

```js
var colors = ["red", "green", "blue"];
var colors2 = colors.concat("yellow", ["black", "brown"]);
console.log(colors); //red,green,blue
console.log(colors2); //red,green,blue,yellow,black,brown
```

注意，concat()方法不会影响原始数组。
slice()能够基于当前数组中的一或多个项创建一个新数组。slice()方法可以接受一或两个参数，即要返回项的起始和结束位置。在只有一个参数的情况下，slice()方法返回从该参数指定位置开始到当前数组末尾的所有项。如果有两个参数，该方法返回起始和结束位置之间的项——但不包括结束位置的项。

```js
var colors = ["red", "green", "blue", "yellow", "purple"];
var colors2 = colors.slice(2);
var colors3 = colors.slice(2,4);
console.log(colors2); //blue,yellow,purple
console.log(colors3); //blue,yellow
```

注意，slice()方法不会影响原始数组。
splice() 的主要用途是向数组的中部插入项：
1、删除：可以删除任意数量的项，只需指定 2 个参数：要删除的第一项的位置和要删除的项数。
2、插入：可以向指定位置插入任意数量的项，只需提供 3 个参数：起始位置、0（要删除的项数）和要插入的项。
3、替换：可以向指定位置插入任意数量的项，且同时删除任意数量的项，只需指定 3 个参数：起始位置、要删除的项数和要插入的任意数量的项。插入的项数不必与删除的项数相等。

```js
var colors = ["red", "green", "blue"];
var removed = colors.splice(0,1); // 删除第一项
console.log(colors); // green,blue
console.log(removed); // red，返回的数组中只包含一项

removed = colors.splice(1, 0, "yellow", "orange"); // 从位置 1 开始插入两项
console.log(colors); // green,yellow,orange,blue
console.log(removed); // 返回的是一个空数组

removed = colors.splice(1, 1, "red", "purple"); // 插入两项，删除一项
console.log(colors); // green,red,purple,orange,blue
console.log(removed); // yellow，返回的数组中只包含一项
```

## 位置方法

indexOf()和lastIndexOf()。这两个方法都接收两个参数：要查找的项和（可选的）表示查找起点位置的索引。其中，indexOf()方法从数组的开头（位置0）开始向后查找， lastIndexOf() 方法则从数组的末尾开始向前查找。

```js
var numbers = [1,2,3,4,5,4,3,2,1];
console.log(numbers.indexOf(4));//3
console.log(numbers.lastIndexOf(4));//5
console.log(numbers.indexOf(4, 4)); //5
console.log(numbers.lastIndexOf(4, 4)); //3

var person = { name: "Nicholas" };
var people = [{ name: "Nicholas" }];
var morePeople = [person];
console.log(people.indexOf(person)); //-1
console.log(morePeople.indexOf(person)); //0
```

## 迭代方法

ECMAScript5为数组定义了5个迭代方法。每个方法都接收两个参数：要在每一项上运行的函数和（可选的）运行该函数的作用域对象——影响 this 的值。传入这些方法中的函数会接收三个参数：数组项的值、该项在数组中的位置和数组对象本身。根据使用的方法不同，这个函数执行后的返回值可能会也可能不会影响方法的返回值。
every()：对数组中的每一项运行给定函数，如果该函数对每一项都返回true，则返回 true。
filter()：对数组中的每一项运行给定函数，返回该函数会返回true的项组成的数组。
forEach()：对数组中的每一项运行给定函数。这个方法没有返回值。
map()：对数组中的每一项运行给定函数，返回每次函数调用的结果组成的数组。
some()：对数组中的每一项运行给定函数，如果该函数对任一项返回 true ，则返回 true。
以上方法都不会修改数组中的包含的值。

```js
var numbers=[1,2,3,4,5,4,3,2,1];
let everyResult=numbers.every((item,index,array)=>(item > 2))
console.log(everyResult) //flase

var someResult = numbers.some((index, item, array)=>(item > 2));
console.log(someResult); //true

var eachResult = numbers.forEach((item, index, array)=>(console.log(item))); //执行某些操作
console.log(eachResult);  

var mapResult = numbers.map((x)=>(x ** 2));
console.log(numbers);
console.log(mapResult); //true


var filterResult = numbers.filter(function(item, index, array){
return (item > 2);
});      //[3,4,5,4,3]
```

## 归并方法

两个归并数组的方法： reduce() 和 reduceRight() 。这两个方法都会迭代数组的所有项，然后构建一个最终返回的值。其中， reduce() 方法从数组的第一项开始，逐个遍历到最后。而 reduceRight() 则从数组的最后一项开始，向前遍历到第一项。这两个方法都接收两个参数：一个在每一项上调用的函数和（可选的）作为归并基础的初始值。传给 reduce() 和 reduceRight() 的函数接收 4 个参数：前一个值、当前值、项的索引和数组对象。这个函数返回的任何值都会作为第一个参数自动传给下一项。第一次迭代发生在数组的第二项上，因此第一个参数是数组的第一项，第二个参数就是数组的第二项。
使用 reduce() 方法可以执行求数组中所有值之和的操作，比如：

```js
var values = [1,2,3,4,5];
var sum = values.reduce(function(prev, cur, index, array){
return prev + cur;
});
alert(sum); //15

var values = [1,2,3,4,5];
var sum = values.reduceRight(function(prev, cur, index, array){
return prev + cur;
});
alert(sum); //15
```
