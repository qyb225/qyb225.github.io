---
layout: post
title:  "Linux 进程控制 - exit 与 _exit 的区别"
date:   2018-03-06 17:10
categories: Linux
permalink: /linux/exit
---

exit 和 _exit 都用来终止进程。但它们的区别在于 _exit 直接将控制权返回给内核，而 exit 则先要进行一些缓冲区的清理工作，再将控制权返回给内核。也就是说，当调用 _exit 时，缓冲区中的数据会丢失。

---

**例**：

exit

```c
#include <stdio.h>
#include <stdlib.h>

int main() {
	printf("exit\n");
	printf("buffer");
	exit(0);
}
```

程序输出:

```
exit
buffer
```

---

_exit

```c
#include <stdio.h>
#include <unistd.h>

int main() {
	printf("_exit\n");
	printf("buffer");
	_exit(0);
}
```

程序输出：

```
_exit
```

缓冲区中的内容并没有输出。