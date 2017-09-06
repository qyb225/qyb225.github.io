---
layout: post
title:  "Service, Factory and Provider | AngularJS"
date:   2017-08-09 15:40
categories: AngularJS
permalink: /angularjs/service-factory-provider
---

#  Service, Factory and Provider

---

## 1. Service

用途：不同 controller 之间交换数据，完成逻辑。 单例模式

语法：

```js
/*注册service*/
app.service("Service", function () {
    var innerValue = "xxx";
    
    /*逻辑函数*/
    this.someLogic = function () {
         doSomething(innerValue);
    };
    
    /*共享数据*/
    this.getData = function () {
        return innerValue;
    };
});

/*
 * 注入controller，
 * 单例Service可以注入不同controller实现共享
 */
app.controller("Controller", ["Service", function (Service) {
    var viewModel = this;
    viewModel.data = Service.getData();
    
    viewModel.anotherLogic = function () {
        Service.someLogic();
    };
}]);
```

---

## 2. Factory

Factory提供不同的 service 实例（产品），每个实例都是独立互不影响的。

```js
/*第一种，Factory 为之后函数实例*/
app.factory("Factory1", function () {
    return function (arg) {
        return new Service(arg);
    };
});

/*第二种，可返回Object*/
app.factory("Factory2", function () {
    return function (arg) {
        return {
            getService1: function () {
                return new Service1(arg);
            },
            getService2: function () {
                return new Service2(arg);
            }
        };
    };
});
```
Factory 会注册进入controller，Factory即时注册 function的一个实例。service大致如下： 



```js
function Service(arg) {
    var innerValue = "xxx";
    
    /*逻辑函数*/
    this.someLogic = function () {
         doSomething(innerValue);
    };
    
    /*数据*/
    this.getData = function () {
        return innerValue;
    };
}
```

注入controller

```js
app.controller("Controller1", ["Factory1", function (Factory1) {
    var viewModel = this;
    var service = Factory1();
    viewModel.data = service.getData();
    
    viewModel.anotherLogic = function () {
        service.someLogic();
    };
}]);

app.controller("Controller2", ["Factory2", function (Factory2) {
    var viewModel = this;
    var service = Factory2.getService1();
    viewModel.data = service.getData();
    
    viewModel.anotherLogic = function () {
        service.someLogic();
    };
}]);

```

---

## 3. Provider

**Provider** 是唯一一种你可以传进 .config() 函数的 service。当你想要在 service 对象启用之前，先进行模块范围的配置，那就应该用 provider。

```js
var Service = function (arg) {
    var innerValue = "xxx";
    
    /*逻辑函数*/
    this.someLogic = function () {
         doSomething(innerValue);
    };
    
    /*数据*/
    this.getData = function () {
        return innerValue;
    };
};

/*注册Provider*/
app.provider("myService", function () {
    var provider = this;

    provider.prop = "xxx";

    provider.$get = function () {
        return new Service(provider.prop);
    };
});

/*在所有controller之前执行*/
app.config(["myServiceProvider", function (myServiceProvider) {
    myServiceProvider.prop = "yyy";
}]);

/*provider service 注入controller*/
app.controller("Controller", ["myService", function (myService) {
    /*myService 即为调用了$get之后的对象，调用Service的方法*/
    var viewModel = this;
    viewModel.data = myService.getData();
    
    viewModel.anotherLogic = function () {
        myService.someLogic();
    };
}]);
```