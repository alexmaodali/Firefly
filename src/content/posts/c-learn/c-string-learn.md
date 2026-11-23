---
title: C-String Learn
published: 2026-11-22
pinned: false
description: C-String Learn
tags: [c]
image: 'api'
category: c
slug: c-string-learn
---

 ## 参考

https://youtu.be/y8PLpDgZc0E?si=oLStaO1FtblVu1SS

## C String基本知识

```c
#include<stdio.h>

int main() {
	// bbbbbbbbbbbbbbbbbbb 内存中的比特
	// Hello, World0bbbbbb 字符串以0结尾
	// ^									 ^表示字符串的起点
	// s
	char *s = "Hello, World";
	// 内存中字符串s所占的大小
	// x64 -> 8bit
	size_t len = sizeof(s); 
	printf("\n%s\n", s);    // Hello, World
	// 72 为什么这里是72 因为是char类型 且字符串起始指向了H
	printf("\n%ld\n", *s); 
	printf("\n%ld\n", s);   // address of the str
	printf("\n%s\n" , s+1); // ello, World
	printf("\n%zu\n", len); // 8
}
```

s + 1的操作就相当于把字符串起始的指针指向了下一个地址

```c
	// bbbbbbbbbbbbbbbbbbb 内存中的比特
	// Hello, World0bbbbbb 字符串以0结尾
	//  ^									 ^表示字符串的起点
	// s+1
```

下面这种操作是不允许的，你只能只读内存中的字符串，但绝不能修改他们

```c
	char *s = "Hello, World";
	int n = strlen(s);
	s[n-1] = 0;
```

复制一个字符串

```c
	char *s = strdup("Hello, World");
```

## StringView的设计

现代String的设计通常包含两个integer，一个指向字符串地址起始，一个记录字符串所占的长度

```c
	// bbbbbbbbbbbbbbbbbbb 内存中的比特
	// Hello, World0bbbbbb 字符串以0结尾
	// ^          ^									 ^表示字符串的起点
	// s
```

```c
#include<stdio.h>
#include<string.h>

typedef struct {
	const char *data; // 字符串
	size_t count; // 长度
} String_View;

// 将c字符串转成StringView类型
String_View sv(const char *cString) {
	return (String_View) {
		.data = cString,
		.count = strlen(cString),
	};
}

// 向后移动指针 字符长度-1
void sv_chop_left(String_View *sv, size_t n) {
	if(n > sv->count) n = sv->count;
	sv->count -= n;
	sv->data  += n;
}

// chop n 个字符
void sv_chop_right(String_View *sv, size_t n) {
	// 如果cnt=0 那么chop 0 合理
	// 如果chop cnt大于了字符串的长度 那么只取字符串的长度进行chop
	if(n > sv->count) n = sv->count;
	sv->count -= n;
}

int main() {
	String_View s = sv("Hello, World");
	sv_chop_right(&s, 2);
	sv_chop_left(&s, 3);
	// .*s 按照指定个数打印字符
	printf("%.*s\n", s.count, s.data); // lo, Wor
}
```

### Trim

```c
// ...
void sv_trim_left(String_View *sv) {
	while(sv->count > 0 && isspace(sv->data[0])) {
		sv_chop_left(sv, 1);
	}
}

void sv_trim_right(String_View *sv) {
	while(sv->count > 0 && isspace(sv->data[sv->count-1])) {
		sv_chop_right(sv, 1);
	}
}

void sv_trim(String_View *sv) {
	sv_trim_left(sv);
	sv_trim_right(sv);
}

int main() {
	String_View s = sv("    Hello, World     ");
	sv_trim(&s);
	printf("\n|%.*s|\n", s.count, s.data); // |Hello, World|
}
```

### chop by delim

```c
String_View sv_chop_by_delim(String_View *sv, char delim) {
	size_t i = 0;
	while (i < sv->count && sv->data[i] != delim) {
		i += 1;
	}
	// Hello, World
	//      ^
	// 012345 
	// [Hello] [ World]
	String_View result = {
		.data = sv->data,
		.count = i,
	};

    // 修改原字符串
	if(i < sv->count) {
		sv_chop_left(sv, i + 1);
	} else {
		sv_chop_left(sv, sv->count);
	}

	return result;
}

int main() {
	String_View s = sv("Hello, World");
	String_View hello = sv_chop_by_delim(&s, ',');
	printf("\n|%.*s|\n", s.count, s.data); // [ World]
	printf("\n|%.*s|\n", hello.count, hello.data); // [Hello] 
}
```

### printf 宏定义sv的打印格式

字符串中间，使用两个`"`号，编译器还是可以解析，所以可以将`.*s`抽象成宏

```c
int main() {
	String_View s = sv("Hello, "
		
		
		
		"World");
	String_View hello = sv_chop_by_delim(&s, ',');
	printf("\n|"
		
		"%."
		
		
		"*s|\n", s.count, s.data);                 // [ World]
	printf("\n|%.*s|\n", hello.count, hello.data); // [Hello] 
}
```

所以你有了

```c
#define fmt "%.*s"
#define arg(s) (s).count, (s).data

int main() {
	String_View s = sv("Hello, World");
	String_View hello = sv_chop_by_delim(&s, ',');
	printf("\n|"fmt"| == |"fmt"|\n", arg(s), arg(hello)); // | World| == |Hello|
}
```

### 打印文件内容

利用字符串打印文件内容

```c
int main() {
	FILE *f = fopen(__FILE__, "rb"); //read binary
	size_t capacity = 1024*1024;
	char *buffer = malloc(capacity);
	size_t size = fread(buffer, 1, capacity, f);
	printf("size=%zu\n", size); // 文件字节大小数

	String_View s = {
		.data = buffer, // 文件内容已经被读入buffer了
		.count = size,
	};

	while (s.count > 0) {
		String_View line = sv_chop_by_delim(&s, '\n');
		printf(fmt"\n", arg(line));
	}

	return 0;
}
```

### 统计文件字符个数

```c
String_View sv_chop_by_type(String_View *sv, int (*istype)(int c)) {
	size_t i = 0;
	while (i < sv->count && !istype(sv->data[i])) {
		i += 1;
	}
	// Hello, World
	//      ^
	// 012345 
	// [Hello] [ World]
	String_View result = {
		.data = sv->data,
		.count = i,
	};

	if(i < sv->count) {
		sv_chop_left(sv, i + 1);
	} else {
		sv_chop_left(sv, sv->count);
	}

	return result;
}

#define fmt "%.*s"
#define arg(s) (s).count, (s).data

int main() {
	FILE *f = fopen(__FILE__, "rb"); //read binary
	size_t capacity = 1024*1024;
	char *buffer = malloc(capacity);
	size_t size = fread(buffer, 1, capacity, f);
	printf("size=%zu\n", size); // 文件字节大小数

	String_View s = {
		.data = buffer, // 文件内容已经被读入buffer了
		.count = size,
	};

	size_t word_count = 0;
	while (s.count > 0) {
		sv_trim_left(&s);
		if(s.count == 0) break;
		String_View word = sv_chop_by_type(&s, isspace);
		printf(fmt"\n", arg(word));
		word_count++;
	}
	
	printf("work_count = %zu\n", word_count); // work_count = 324

	return 0;
}

```

