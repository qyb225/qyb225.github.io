---
layout: post
title:  "C++ 声明那些事"
date:   2017-09-27 20:00
categories: C++
permalink: /cpp/declaration
---

# 声明的神奇操作

## const int* ptr

在这里， const 修饰 int*，指的是指针 ptr 指向的内容是 const 常量，不能被改变。我们称 ptr 是**指向常量的指针**。

---

## int const *ptr

**int const \*ptr** 与 **const int\* ptr** 意义完全相同。

---

## int* const ptr

在这里，const 修饰 ptr，ptr 是个**指针常量**，即指针不能指向初始化外的其它地方，但指向的内容可以改变。

---

## const int* const ptr

ptr 是一个指向常量的常量指针。

---

## int (*ptr)[10] 与 int (&ref)[10]

**int (\*ptr)[10]**：先读括号里的内容，\*ptr 指的是 ptr 是一个指针，接下来看右边，可知 ptr 是一个指向的数组指针，最后看左边，可知数组中的元素为 10。所以 **ptr 是一个指向 10 个元素数组的指针**。

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