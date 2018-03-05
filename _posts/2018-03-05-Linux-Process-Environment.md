---
layout: post
title:  "Linux 进程环境"
date:   2018-03-05 17:40
categories: Linux
permalink: /linux/process-environment
---

# 进程执行、终止与存储空间分布

## 一、 main 函数的执行

C 程序总是从 main 函数开始执行，函数原型为：

```c
int main(int argc, char *argv[]);
```

当内核执行 C 程序时（使用一个 exec 函数），在调用 main 之前先调用一个特殊得到启动例程，可执行二进制程序文件将此启动例程地址作为其起始地址然后执行。执行前，启动例程从内核取得命令行参数和环境变量值，按上述方式调用 main 函数。

---

## 二、进程终止

共有 8 种方式使进程终止：

**正常终止:**

1. 从 main 返回
2. 调用 exit
3. 调用 _exit 或 _Exit
4. 最后一个线程从其启动例程返回
5. 从最后一个线程调用 pthread_exit

**异常终止:**

1. 调用 abort
2. 接收到某信号后终止
3. 最后一个线程对取消请求做出响应

*暂时针对线程的 3 种终止方式暂时不考虑*。

---

### 2.1 退出函数

exit、_exit、_Exit 三个函数用于正常终止一个程序：_exit 和 _Exit 立即进入内核，exit 先执行一些清理操作再进入内核。

```cpp
#include <stdlib.h>
void exit(int status);
void _Exit(int status);

#include <unistd.h>
void _exit(int status);
```

其中，exit 函数总是执行一个标准 I/O 库的清理关闭操作，使得输出缓冲中的所有数据都被冲洗。

3 个函数都带有一个 **int status** 的参数，称为**终止状态**。

以下情况下，终止状态的值未定义：

* main 执行了一个无返回值的 return 语句。

* main 没有声明返回类型为整形。

* 调用上述 exit 函数不带初始状态。

在 main 函数中返回一个 int 值和用该 int 值调用 exit 是等价的，所以在 main 函数中：

**return 0;** 等价于 **exit(0);**

---

### 2.2 函数 atexit

按照 ISO C 的规定，一个进程可以登记至多 32 个函数，这些函数将由 exit 自动调用。这些函数为 **终止处理函数 (exit handler)**，使用 atexit 函数可以登记这些函数。

```c
#include <stdlib.h>
int atexit(void (*func)(void));
//成功则返回 0
```

exit 调用这些函数的顺序与它们登记的顺序相反，同一个函数登记多次，也会被调用多次。

例：

```c
#include <stdlib.h>
#include <stdio.h>

static void exit1();
static void exit2();

int main() {
    if (atexit(exit2) != 0) {
        printf("can't register exit2\n");
    }
    if (atexit(exit1) != 0) {
        printf("can't register exit1\n");
    }
    if (atexit(exit1) != 0) {
        printf("can't register exit1\n");
    }

    printf("main is done\n");
    return 0;
}

static void exit1() {
    printf("exit1 handler\n");
}

static void exit2() {
    printf("exit2 handler\n");
}
```

程序运行结果：

```
$./a.out
main is done
exit1 handler
exit1 handler
exit2 handler
```

---

## 三、存储空间

### 3.1 C 程序的存储空间布局

C 程序由下列几部分组成：

* 正文段：这是由 CPU执行的机器指令部分。通常，正文段是可以共享的，因此经常执行的程序在在存储器中只需要一个副本。另外，正文段常常是只读的，防止程序被意外修改。

* 数据段：包含了程序中明确初始化的变量，例如，C程序中任何函数之外的声明，如： *int init = 0;*

* BSS 段：又称为未初始化段，在程序开始执行前，内核将此段中年的数据初始化为 0 或空指针。通常存放函数外未初始化的声明，如： *int i;*

* 栈：局部作用域中的变量以及每次函数调用时所需保存的信息都放在此段中。每次函数调用时，其返回地址以及调用者的环境信息都保存在栈中。然后，最近被调用的函数在栈上为其自动、临时变量自动分配存储空间，递归也因此可以工作。函数每次递归调用自身时，就会有一个新的栈帧。

* 堆：通常在堆中进行动态存储分配，堆位于BSS段和栈之间。

---

### 3.2 存储空间分配函数

1. malloc: 分配指定字节数的存储区，此存储区未初始化。
2. calloc: 为指定数量 n，指定长度 size 的对象分配存储空间，空间中每一位初始化为 0.
3. relloc: 增加或者减少以前分配过的空间的长度，新增区域内的初始值不确定。

```c
#include <stdlib.h>

void *malloc(size_t size);
void *calloc(size_t n, size_t size);
void *relloc(void *ptr, size_t new_size);

void free(void *ptr);
```

使用上面三个函数分配的地址都要用 free 释放，否则内存泄露。