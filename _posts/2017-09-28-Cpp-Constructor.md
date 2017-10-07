---
layout: post
title:  "再探 C++ 构造函数"
date:   2017-09-28 23:00
categories: C++
permalink: /cpp/constructor
---


# 深入探索 C++ 构造函数

## 1. 构造函数初始值列表

假设有一个 Person 类，它有私有变量 name, id, age.

现在有两种构造方式：

```cpp
//Constructor 1
Person::Person(std::string& pName, std::int pAge) {
    name = pName;
    age = pAge;
}

//Constructor2
Person::Person(std::string& pName, std::int pAge): 
               name(pName), age(pAge) {}
```

第二种初始化方式，我们称为用初始值列表进行初始化，是一种对需要初始化的变量直接进行初始化的方式。

再来看看第一种初始化的方式，它的本质其实是先将变量 name, age 进行默认初始化，然后再进行赋值。

```cpp
//Constructor1 等价于
Person::Person(std::string& pName, std::int pAge):
               name(""), age(0) {
    name = pName;
    age = pAge;
}
```

所以，如果构造函数仅仅是用于初始化值的话，最好使用初始化列表进行初始化。

---

## 2. 委托构造函数

C++11 提供了委托构造函数，就是设置一个构造函数负责初始化所有的变量，其余的一些构造函数只需要调用这个构造函数即可。相当于其它的构造函数把自己的构造函数“委托”给了另一个构造函数一样。

还是以刚才的 Person 类为例：

```cpp
//这个构造函数负责初始化所有的变量
Person::Person(std::string& pName, std::int pAge): 
               name(pName), age(pAge) {}

//其它的构造函数委托给上面的构造函数
Person::Person(std::string& Person::Person(): Person(pName, 0) {}
Person::Person(int pAge): Person("no name", pAge) {}
Person::Person(): Person("no name", 0) {}
```

---


## 3. 转换构造函数

转换构造函数的作用是将一个其他类型的数据转换成一个类的对象｡

当一个构造函数只有一个参数，而且该参数又不是本类的 const 引用时，这种构造函数称为转换构造函数。

举个例子，假设一个复数类 Complex，两个私有变量： 实部 real，虚部 image.

```cpp
class Complex {
private:
    double real;
    double image;

public:
    //构造函数
    Complex(double r, double i): real(r), image(i) {}

    //默认构造
    Complex(): Complex(0, 0) {}

    //重载加号
    Complex operator+(Complex& a);
};

```

---

### 3.1 隐式转换

我们希望的是，输入一个 double 的实数，它可以自动转换为一个虚部 image 为 0 的复数。所以这个时候，我们需要转换构造函数：

```cpp
Complex::Complex(double r): Complex(r, 0) {}
```

这时候，用一个 Complex 和一个 double 变量相加，double 变量会被**隐式转换**为 Complex 类型。

```cpp
//合法
Complex(2, 1) + 3.0;
```

---

### 3.2 抑制隐式转换 explicit

但有的时候，我们不希望进行隐式转换，这个时候，我们需要在构造函数之前加入 **explicit** 关键字。

```cpp
class Complex {
private:
    double real;
    double image;

public:
    //构造函数
    Complex(double r, double i): real(r), image(i) {}

    //默认构造
    Complex(): Complex(0, 0) {}

    //抑制隐式转换
    explicit Complex(double r): Complex(r, 0) {}

    //重载加号
    Complex operator+(Complex& a);
};

```

印制了隐式转换，就只能进行显示构造，否则会报错。

```cpp
//不合法, 无法隐式转换
Complex(2, 1) + 3.0;

//合法, 可以显式转换
Complex(2, 1) + Complex(3.0);
```

---

## 4. 聚合类

如果一个类，没有 private, protected 变量，没有基类，没有 virtual，没有自定义的构造函数，则它是一个聚合类，可以直接用列表初始化。

```cpp
struct A() {
    int a;
    std::string b;
};

//初始化
A a = { 5, "Hello" };
```