---
layout: post
title:  "JavaScript Prototype 继承"
date:   2017-08-08 17:53
categories: JavaScript
permalink: /javascript/prototype
---

# Prototype 继承

在JavaScript中，每个 Object 都会有一个 **\__proto\__** 的属性，称为“原型”，如果调用了Object 没有的属性，则会沿着“原型链”向上查找。

语句：
```js
var A = Object.create(B);
```

该语句会将B对象属性添加至A的**\__proto\__**中，所以A可以沿着原型链访问到B的属性，这就是 **prototype继承** ，如果A中定义了B已有的属性，则 masking了B中的属性，这时B不会沿着原型链向上查询，称之为覆盖。


```js
var parent = {
    value: "parent",
    obj: { objValue: "parent obj" }
};

var child = Object.create(parent); //prototype inherit

child.value = "child";  //Masking
child.obj.objValue = "child obj";  //Notice: Modify B, too

console.log(child.value); //"child"
console.log(parent.value); //"parent"
console.log(parent.obj.objValue); //"child obj"
```

需要注意的是，如果child沿着 prototype chain 直接访问并修改了它继承的对象的属性对象内的值，父对象属性会被修改。如上述程序的这两条语句：

```js
child.obj.objValue = "child obj";  //Notice: Modify B, too

console.log(parent.obj.objValue); //"child obj"
```
