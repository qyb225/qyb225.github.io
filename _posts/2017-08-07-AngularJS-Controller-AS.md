---
layout: post
title:  "Controller AS Synax | AngularJS"
date:   2017-08-07 10:00
categories: AngularJS
permalink: /angularjs/controller-as
---

#  Controller 'AS' 背后发生了什么

---

## 一、ng-controller 的 prototype 继承

### 1.1 JavaScript 的prototype继承：

在JavaScript中，每个 Object 都会有一个 **\__proto\__** 的属性，称为“原型”，如果调用了Object 没有的属性，则会沿着“原型链”向上查找。

语句：

```js
var A = Object.create(B);
```

该语句会将B对象属性添加至A的**\__proto\__**中，所以A可以沿着原型链访问到B的属性，这就是 **prototype继承** ，如果A中定义了B已有的属性，则masking了B中的属性，不会沿着原型链上升，直接覆盖原属性；如果A中没有的属性之中的属性被修改，则B中一样被修改，例如：

```js
var B = {
    value: "parent",
    obj: { objValue: "parent obj" }
};

var A = Object.create(B); //prototype inherit

A.value = "child";  //Masking
A.obj.objValue = "child obj";  //Modify B, too

console.log(A.value); //"child"
console.log(B.value); //"parent"
console.log(B.obj.objValue); //"child obj"
```

参考我的另一篇博客 [JavaScript Prototype 继承](../javascript/prototype)

### 1.2 嵌套 ng-controller 的 $scope prototype 继承

如下两个 controller ：

```html
<div ng-controller="ParentController">
    <div ng-controller="ChildController">
        <!--Something-->
    </div>
</div>
```

其中，ChildController 的 scope 原型继承了 ParentController 的 scope，因此两个scope里的行为和原生js完全相同。

---

## 二、Controller AS Synax

如下两个 controller ：

```html
<div ng-controller="Controller1 as ctrl1"> 
    <div ng-controller="Controller2 as ctrl2">
        { { ctrl1.prop } }
        { { ctrl2.prop } }
    </div>
</div>
```
**controller as ctrl** 的语法，相当于在 controller 的 scope添加了 scope.ctrl 属性，且 ctrl 为 controller 本身的实例，因此上述的 HTML ，angular实际上执行了这样的 js 代码：

```js
$scope1.ctrl1 = new Controller1(...);
$scope2 = Object.create($scope1);
$scope2.ctrl2 = new Controller2(...);
```

因为 Controller2 scope 原型继承了 Controller1 scope，因此 Controller2 的 scope中也可以沿着原型链找到 ctrl1 和 ctrl1.prop。

app.js的controller可以这样写：

```js
app.controller("Controller", function () {
    var ctrl = this;
    ctrl.prop = "xxx";
    /*this.prop = "xxx";*/
});

```
