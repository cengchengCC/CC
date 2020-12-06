---
title: 第一部分 变化检测
tags: 深入浅出Vue
categories: Vue.js
abbrlink: 7465b203
date: 2020-10-28 00:00:00
---

<!-- more -->

## Object

JS中检测对象的变化有两种：`Object.defineProperty`和ES6的`proxy`

```js
function defineReactive (data, key, val) {
    Object.defineProperty(data, key, {
        enumerable: true,
        configurable: true,
        get: function () {
            return val
        },
        set: function (newVal) {
            if(val === newVal){
                return
            }
            val = newVal
        }
    })
}
```

### 收集依赖

## 参考链接

[参考链接1](https://github.com/berwin/Blog)
