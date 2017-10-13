---
layout: post
title:  "C++ 声明那些事"
date:   2017-09-27 20:00
categories: C++
permalink: /cpp/declaration
---

# 数组、指针、const、函数和它们的神奇的操作

## 一、常规操作

### 1. const int* ptr

在这里， const 修饰 int*，指的是指针 ptr 指向的内容是 const 常量，不能被改变。我们称 ptr 是**指向常量的指针**。

---

### 2. int const *ptr

**int const \*ptr** 与 **const int\* ptr** 意义完全相同。

---

### 3. int* const ptr

在这里，const 修饰 ptr，ptr 是个**指针常量**，即指针不能指向初始化外的其它地方，但指向的内容可以改变。

---

### 4. const int* const ptr

ptr 是一个指向常量的常量指针。

---

## 二、奇技淫巧

### 写在前面

为了避免被打，以下技巧请谨慎使用。

---

### 1. int (*ptr)[10]

**int (\*ptr)[10]**：先读括号里的内容，\*ptr 指的是 ptr 可以进行解引用操作，是一个指针；接下来看右边，可知 ptr 是一个指向的数组指针；最后看左边，可知数组中的元素为 10。所以 **ptr 是一个指向 10 个元素数组的指针**。

**int (&ref)[10]**：与上面类似，**ref 是一个 10 个元素的数组的引用**。

**e.g.1**：

```cpp
int nums[5] = {0, 1, 2, 3, 4};

int (*ptr)[5] = &nums;
int (&ref)[5] = nums;
```

**e.g.2**：

```cpp
void print(int (&nums)[5]) {
    for (auto elem : nums) {
        cout << elem << " ";
    }
}

void modify(int (*numsPtr)[5]) {
    int i = 0;
    while (i < 5) {
        (*numsPtr)[i] *= 2;
        ++i;
    }
}

int main() {
    int array[5] = {0, 1, 2, 3, 4};
    modify(&array);
    print(array);

    return 0;
}
```

这种方法的优势在于，传入的数组大小必须严格与形参声明的大小相等，否则编译器就会报错。当然它的劣势是严格限制大小导致了很差的程序扩展性。

当然对于 ptr 来说，还有一个很有用的作用，就是传递多维数组。

```cpp
void fn(int (*matrix)[10], int rowSize);  // rowSize * 10 的二维数组

//等价声明
void fn(int matrix[][10], int rowSize);
```

---

### 2. 返回指向数组的指针的函数

我们知道，数组无法被拷贝，所以函数无法返回数组。不过，函数可以返回数组的指针或者引用。

话不多说看下面这个震撼到我的函数声明：

```cpp
int (*func(int param))[10];
```

一步一步解释它：

* **func(int param)** 表示调用 func 函数需要一个 int 类型的参数。

* **(\*func(int param))** 表示我们可以对函数的调用结果进行解引用操作，即返回一个指针。

* **(\*func(int param))[10]** 表示函数返回结果是一个 10 个元素的数组。

* **int (\*func(int param))[10]** 表示数组中的元素是 int 类型。

综合起来：**func接受一个参数，返回一个大小为 10 个元素的 int 数组的指针**。

---

C++11标准下，也可以用这种写法：

```cpp
auto func(int param)->int(*)[10];
```

---

**e.g.**：

```cpp
int odd[5] = {1, 3, 5, 7, 9};
int even[5] = {0, 2, 4, 6, 8};

int (*selectPtr(int i))[5] {
    return (i % 2) ? &odd : &even;
}

// auto selectPtr(int i)->int(*)[5] {
//     return (i % 2) ? &odd : &even;
// }

int main() {
    int (*ptr)[5] = selectPtr(4);

    for (auto elem : *ptr) {
        cout << elem << " ";
    }
    cout << endl;
    /*输出 0 2 4 6 8*/

    return 0;
}
```

---

### 3. 函数指针

声明一个函数指针：

```cpp
int (*fnPtr)(int param);
```

相同返回值和形参的函数可以赋值给这个函数指针。

**e.g.**：

```cpp
int max(int a, int b) {
    return (a > b) ? a : b;
}

int min(int a, int b) {
    return (a > b) ? b : a;
}

//函数指针作为形参
int choose(int a, int b, int (*fnPtr)(int a, int b)) {
    return fnPtr(a, b);
}

int main() {
    int (*fnPtr)(int, int); //声明函数指针

    fnPtr = max;
    cout << fnPtr(1, 2) << endl;
    fnPtr = min;
    cout << fnPtr(1, 2) << endl;

    cout << choose(1, 2, max) << endl;
    cout << choose(1, 2, min) << endl;
    
    return 0;
}
```

### 4. 返回函数指针的函数

```cpp
int (*func(int param))(int, int);
```

指的是 func 接收一个 int 的参数，返回一个函数指针，这个函数指针指向“接收两个 int 作为参数，返回 int 的函数”。

等价于：

```cpp
auto func(int param)->int (*)(int, int);
```

---

### 5. 返回函数指针的函数的函数指针

```cpp
int (*(*ptr)(int))(int, int);
```

ptr 是一个函数指针，ptr 指向的函数是：接收一个 int 的参数，返回一个函数指针，这个函数指针指向“接收两个 int 作为参数，返回 int 的函数”。

```cpp
auto func(int param)->int (*)(int, int);

int (*(*ptr)(int))(int, int);
ptr = func;
```
