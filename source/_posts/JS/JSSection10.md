---
title: 第十章 DOM
tags: JavaScript
categories: JavaScript高级程序设计
abbrlink: ee93c80b
date: 2020-05-17 16:11:53
---

**DOM**（文档对象模型）是针对`HTML`和`XML`文档的一个`API`（应用程序编程接口）。**DOM**描绘了一个层次化的节点树，允许开发人员添加、移除和修改页面的某一部分。<!-- more -->

## 节点层次

DOM可以将任何HTML或XML文档描绘成一个由多层节点构成的结构。节点分为几种不同的类型，每种类型分别表示文档中不同的信息及（或）标记。每个节点都拥有各自的特点、数据和方法，另外也与其他节点存在某种关系。节点之间的关系构成了层次，而所有页面标记则表现为一个以特定节点为根节点的树形结构。

```js
<html>
    <head>
        <title>Sample Page</title>
    </head>
    <body>
        <p>Hello World!</p>
    </body>
</html>
```

文档节点是每个文档的根节点。在这个例子中，文档节点只有一个子节点——`<html>`元素，即**文档元素**。文档元素是文档的最外层元素，文档中的其他所有元素都包含在文档元素中。每个文档只能有一个文档元素。在 HTML页面中，文档元素始终都是`<html>`元素。在XML中，没有预定义的元素，因此任何元素都可能成为文档元素。

可以将这个简单的HTML文档表示为一个层次结构，如下图所示。

![attr](https://blog-images-1258719270.cos.ap-shanghai.myqcloud.com/%E3%80%8AJavaScript%E9%AB%98%E7%BA%A7%E7%A8%8B%E5%BA%8F%E8%AE%BE%E8%AE%A1%E3%80%8B/10-1%E8%8A%82%E7%82%B9%E5%B1%82%E6%AC%A1%E7%BB%93%E6%9E%84.png)

每一段标记都可以通过树中的一个节点来表示：HTML元素通过元素节点表示，特性（`attribute`）通过特性节点表示，文档类型通过文档类型节点表示，而注释则通过注释节点表示。总共有`12`种节点类型，这些类型都继承自一个基类型。

### **Node**类型

DOM1级定义了一个**Node**接口，该接口将由DOM中的所有节点类型实现。这个Node接口在JavaScript中是作为Node类型实现的；除了IE之外，在其他所有浏览器中都可以访问到这个类型。JavaScript中的所有节点类型都继承自Node类型，因此所有节点类型都共享着相同的基本属性和方法。
每个节点都有一个nodeType属性，用于表明节点的类型。节点类型由在Node类型中定义的下列12个数值常量来表示，任何节点类型必居其一：

{% blockquote %}

```js
Node.ELEMENT_NODE (1)；
Node.ATTRIBUTE_NODE (2)；
Node.TEXT_NODE (3)；
Node.CDATA_SECTION_NODE (4)；
Node.ENTITY_REFERENCE_NODE (5)；
Node.ENTITY_NODE (6)；
Node.PROCESSING_INSTRUCTION_NODE (7)；
Node.COMMENT_NODE (8)；
Node.DOCUMENT_NODE (9)；
Node.DOCUMENT_TYPE_NODE (10)；
Node.DOCUMENT_FRAGMENT_NODE (11)；
Node.NOTATION_NODE (12)。
```

{% endblockquote %}

通过比较上面这些常量，可以很容易地确定节点的类型。

```js
if (someNode.nodeType == Node.ELEMENT_NODE){ //在 IE 中无效
    alert("Node is an element.");
}
```

为了确保跨浏览器兼容，最好将`nodeType`属性与数字值进行比较。

```js
if (someNode.nodeType == 1){ // 适用于所有浏览器
    alert("Node is an element.");
}
```

#### 节点关系

### Document类型

JavaScript通过`Document`类型表示文档。在浏览器中，`document`对象是`HTMLDocument`（继承自`Document`类型）的一个实例，表示整个HTML页面。而且，`document`对象是`window`对象的一个属性，因此可以将其作为全局对象来访问。`Document`节点具有下列特征：

> `nodeType`的值为 9；
> `nodeName`的值为 "#document" ；
> `nodeValue`的值为`null`；
> `parentNode`的值为`null`；
> ownerDocument 的值为`null`；
> 其子节点可能是一个`DocumentType`（最多一个）、`Element`（最多一个）、`ProcessingInstruction`或`Comment`。

`Document`类型可以表示`HTML`页面或者其他基于`XML`的文档。不过，最常见的应用还是作为`HTMLDocument`实例的`document`对象。通过这个文档对象，不仅可以取得与页面有关的信息，而且还能操作页面的外观及其底层结构。

#### 文档的子节点
