---
layout: post
title:  "Linux 进程控制 - fork()"
date:   2018-03-06 16:00
categories: Linux
permalink: /linux/fork
---

# Linux 中的 fork 与 vfork

**引言**：每个进程都有一个非负整形表示的进程 ID，一般称为 pid。进程 ID 总是唯一的，但是当一个进程结束后，它的进程 ID 可以被复用。

Linux 中，pid 为 0 的进程是调度进程，是内核的一部分，因此也称为系统进程； pid 为 1 的进程为 init 进程，由内核调用，通常读取与系统有关的初始化文件，init 进程不会终止。

---

## 一、fork

fork 函数用于让现有进程创建一个子进程。

---

### 1.1 基本用法

```c
#include <unistd.h>
pid_t fork(); //父进程返回子进程 pid, 子进程返回 0.
```

在程序执行到 fork() 的时候，当前进程创建一个子进程。父进程返回值为子进程的 pid，而子进程返回值为 0。

子进程和父进程继续执行剩下的指令，而子进程是父进程的副本，拥有父进程全部的进程存储空间的副本：包括数据段，堆栈甚至 I/O 缓冲区。而正文段则是共享的。

由于通常 fork 之后往往跟着 exec 函数，也就是那些复制父进程的东西往往在后面根本不会使用，前面的复制工作成了无用功。因此现在通常使用写时复制技术 (Copy-On-Write, COW)，子进程在创建之后，先与父进程共享数据段、堆栈等数据，内核将父子进程的权限改为只读。当父子进程中的任何一个需要修改这些区域，内核为子进程复制其副本。

---

**例**：fork 后子进程对变量的修改不影响父进程。

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int globvar = 10;

int main() {
    int var = 100;
    pid_t pid;

    printf("before fork\n");
    pid = fork();
    if (pid == 0) {
        //子进程修改变量
        ++globvar;
        ++var;
    } else if (pid > 0) {
        //父进程 sleep 保证子进程先输出
        sleep(2);
    }
    printf("pid = %d, globvar = %d, var = %d\n",
           getpid(), globvar, var);
    exit(0);
}
```

输出：

```
before fork
pid = 15540, globvar = 11, var = 101
pid = 15539, globvar = 10, var = 100
```

---

### 1.2 缓冲区复制问题


值得注意的是，fork 函数会令子进程复制父进程当前的 I/O 缓冲区。Linux 标准输出的缓冲区会在出现换行符 `\n` 时刷新并显示在屏幕上。因此如果当前缓冲区未被刷新，则执行 fork 时会复制缓冲区的数据。

还是用上面的为例，修改仅仅是 fork 函数调用前的输出函数 printf 函数不要换行符：

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int globvar = 10;

int main() {
    int var = 100;
    pid_t pid;

    printf("[before fork buffer] ");
	//此时缓冲区未被刷新，将被后面 fork 创建的子进程复制

    pid = fork();
    if (pid == 0) {
        //子进程修改变量
        ++globvar;
        ++var;
    } else if (pid > 0) {
        //父进程 sleep 保证子进程先输出
        sleep(2);
    }
    printf("pid = %d, globvar = %d, var = %d\n",
           getpid(), globvar, var);
    exit(0);
}
```

程序输出为：

```
[before fork buffer] pid = 16616, globvar = 11, var = 101
[before fork buffer] pid = 16615, globvar = 10, var = 100
```

---

## 二、vfork

### 2.1 vfork 与 fork 的异同

vfork 函数的调用和返回与 fork 完全相同，而两者语义不同。

vfork 同样用于创建一个新的子进程，而这个子进程的目的就是用 exec 函数执行一个新的程序。而在 exec 函数或者 exit 函数调用之前，它是直接在父进程的环境中运行的，不会进行复制操作。在此期间如果子进程修改了数据，可能会带来未知后果。

vfork 和 fork 的另一个区别是：vfork 保证子进程先于父进程运行，在它调用 exec 或者 exit 后父进程才可能运行，才此之前，内核会让父进程处于休眠状态。

---

**例**：子进程修改变量后退出，父进程的变量数据被修改。

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int globvar = 10;

int main() {
    int var = 100;
    pid_t pid;

    printf("before vfork\n");
    pid = vfork();

    if (pid == 0) {
        //Child
        ++globvar;
        ++var;
        _exit(0);
    }
    //Parent
    printf("pid = %d, globvar = %d, var = %d\n", 
           pid, globvar, var);
    exit(0);
}
```

输出：

```
before vfork
pid = 19167, globvar = 11, var = 101
```