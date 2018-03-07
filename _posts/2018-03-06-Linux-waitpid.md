---
layout: post
title:  "Linux 进程控制 - waitpid"
date:   2018-03-06 19:00
categories: Linux
permalink: /linux/wait
---

# 介绍 wait/waitpid 函数

当一个进程终止时，内核会向其父进程发送 SIGCHLD 信号。而调用了 wait/waitpid 函数的进程会有以下行为：

* 如果它所有的子进程都在运行，则阻塞。
* 如果一个子进程已经终止，正等待父进程获取其终止状态，则取得该子进程终止状态立即返回。
* 如果它没有任何子进程，则返回。

值得注意的是，如果父进程并没有调用 wait/waitpid 获取子进程的终止状态，且父进程还没有结束（子进程没有被 init 收养），那么当子进程结束后，它的进程描述符仍然保存在系统中，这就成了僵尸进程。

```c
#include <sys/wait.h>
pid_t wait(int *statloc);
pid_t waitpid(pid_t pid, int *statloc, int options);
```

---

## 一、wait

wait 比较简单，它只有一个参数 **statloc**，也就是 **status location** 的意思，提供一个地址，用于存放终止的子进程的终止状态。

当然，如果不关心终止状态，可以将此参数指定为空指针。

---

## 二、waitpid

waitpid 是 wait 的扩充，能够实现对子进程回收更加精准的控制。

waitpid 有三个参数，其中 statloc 参数和的 statloc 参数完全相同，现在关心其剩余两个参数 pid 和 options。

---

### 2.1 pid 参数

pid 参数使用有以下几种情况：

* **pid == -1**: 等待任意子进程，此时与 wait 等效。
* **pid > 0**: 等待进程 ID 与 pid 相等的子进程。
* **pid == 0**: 等待组 ID 等于调用进程组 ID 的任一子进程。
* **pid < -1**： 等待组 ID 等于 pid 绝对值的任一子进程。

---

### 2.2 options 参数

options 参数可以进一步控制 waitpid 的操作，甚至让 waitpid 不再阻塞等待，它的参数为以下两种的位运算值 (WNOHANG \| WUNTRACED)：

* **WNOHANG**: Wait No Hang 的意思，如果 pid 指定的子进程没有退出，则 waitpid 立即返回 0，不再阻塞。
* **WUNTRACED**: 比较少使用，若某实现支持作业控制，而由 pid 指定的任一子进程已经处于停止状态，并且其状态从未报告过，则返回其状态。

---

WNOHANG 例：

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/wait.h>

int main() {
    pid_t pid;

    pid = fork();
    if (pid == 0) {
        sleep(0.5);
        printf("child exit.\n");
        _exit(0);
    }
    while (waitpid(pid, NULL, WNOHANG) == 0) {
        printf("My child doesn't exit.\n");
    }
    printf("My child exit!\n");
    exit(0);
}
```

子进程退出之前休眠 0.5s，在此之前父进程调用 waitpid 未被阻塞，而是立即返回 0.

程序输出：

```
//...
My child doesn't exit.
My child doesn't exit.
child exit.
My child doesn't exit.
My child doesn't exit.
My child doesn't exit.
My child doesn't exit.
My child doesn't exit.
My child exit!
```


