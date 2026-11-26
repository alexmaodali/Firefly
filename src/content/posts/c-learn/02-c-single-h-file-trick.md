---
title: C-Single-H-File-Trick
published: 2026-11-26
pinned: false
description: C-Single-H-File-Trick
tags: [c]
image: "api"
category: c
slug: c-single-h-file-trick
---

## STB单文件H

众所周知啊，C的函数必须定义在使用的前面。

一般来讲，`void hello(const char *name)`这种函数预定义的内容需要写到`.h`文件里面

然后具体的实现需要写到`.c`文件里面，然后这个 `.c` 文件需要 `#include "hello.h"`

这种方式来进行使用

```c
#include<stdio.h>

void hello(const char *name);

void hello(const char *name) {
  printf("Hello, %s\n", name);
}

int main()
{
  hello("Alex");
  return 0;
}
```

那么 STB库里面的单文件copy下来加上一个宏定义就可以使用

这种方式是如何做到的呢？

```c
#include<stdio.h>

void hello(const char *name);

#if defined(HELLO_IMPLEMENTATION)

void hello(const char *name) {
  printf("Hello, %s\n", name);
}

#endif // HELLO_IMPLEMENTATION
```

答案很简单，就是使用这种条件编译的方式来添加的

使用时，定义对应的宏定义即可

```c
#define HELLO_IMPLEMENTATION
#include "hello.h"

int main()
{
  hello("Alex");
  return 0;
}
```

## reference

- <https://github.com/nothings/stb>
