---
layout: post
title:  "依赖注入 | AngularJS"
date:   2017-08-06 12:40
categories: AngularJS
permalink: /angularjs/dependency-injection
---

# Dependency Injection (依赖注入)

> 大多数面向对象编程语言，在调用一个类的时候，先要实例化这个类，生成一个对象。如果你在写一个类，过程中要调用到很多其它类，甚至这里的其它类，也要“依赖”于更多其它的类，那么可以想象，你要进行多少次实例化。这就是“依赖”的意思。

> 依赖注入，全称是“依赖注入到容器”， 容器（IOC容器）是一个设计模式，它也是个对象，你把某个类（不管有多少依赖关系）放入这个容器中，可以“解析”出这个类的实例。

在AngularJS中，如我们创建的controller中的$scope就是一个依赖注入。通过解析 Arguments 的各项实现。

可参见：[Angular/injector.js 源代码](https://github.com/angular/angular.js/blob/master/src/auto/injector.js)

---

此时controller有三种写法：

### 写法1：无法minify

```js
var app = angular.module("mainApp", []);

app.controller("theController", theController);

function theController($scope, $filter) {
    //...
}
```

### 写法2：可minify

```js
var app = angular.module("mainApp", []);

app.controller("theController", theController);
theController.$inject = ["$scope", "$filter"];

function theController($scope, $filter) {
    //...
}
```

### 写法3：可minify
```js
var app = angular.module("mainApp", []);

app.controller("theController", ["$scope", "$filter", theController]);

function theController($scope, $filter) {
    //...
}
```

附：[JavaScript Minifier](https://javascript-minifier.com/)