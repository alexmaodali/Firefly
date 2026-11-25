---
title: C-Hash-Table And Shadow Data Learn
published: 2026-11-23
pinned: false
description: C-Hash-Table And Shadow Data Learn
tags: [c]
image: 'api'
category: c
slug: c-hash-table-and-shadow-data
---

## 参考

[Insane Shadow Data Trick in C](https://www.youtube.com/watch?v=gtk3RZHwJUA)

## STB_DS

```c
#include<stdio.h>

#define STB_DS_IMPLEMENTATION
#include "stb_ds.h"

typedef struct {
  const char *key;
  size_t value;
} Item;

Item *table = NULL;

int main() {
  shput(table, "foo", 69);
  shput(table, "bar", 123);
  shput(table, "baz", 999);
  table[shgeti(table, "bar")].value = 666;
  for(int i=0; i < shlen(table); ++i) {
    printf("%s => %zu\n", table[i].key, table[i].value);
  }

  // foo => 69
  // bar => 666
  // baz => 999

  return 0;
}
```

## 简易版

```c
#include<stdio.h>
#include<stdlib.h>

typedef struct {
  size_t count;
  size_t capacity;
} Header;

int main() {
  int *numbers;
  size_t init_capacity = 256;
  Header *header = malloc(sizeof(Header) + sizeof(*numbers) * init_capacity);
  // [Header][numbers]
  // ^
  // data
  header->count = 0;
  header->capacity = init_capacity;
  numbers = (int*)(header + 1);
  // [Header][numbers]
  //         ^
  //         number
  numbers[header->count++] = 234;
  printf("%d\n", numbers[0]); // 234
  printf("%d\n", numbers[1]); // 0
  printf("%d\n", numbers[2]); // 0
    
  printf("header addr: %p\n", header);
  printf("numbers addr: %p\n", numbers);
  printf("back header: %p\n", (Header*)numbers - 1);
    
  return 0;
}
```

## 图解

```
因为 numbers = (int*)(header + 1); 
所以 header  = (Header*)number - 1
```

```
header      = 0xB790C0
numbers     = 0xB790D0
back header = 0xB790C0

0xB790C0  ┌─────────────── Header ───────────────┐
          │ count (8B)  │ capacity (8B)          │
0xB790D0  └──────────────────────────────────────┘
          │ numbers[0] │ numbers[1] │ numbers[2] …
```

## 抽象成函数

```c
#include<assert.h>
#include<stdio.h>
#include<stdlib.h>

typedef struct {
  size_t count;
  size_t capacity;
} Header;

int *arr_init(size_t init_capacity) {
  Header *header = malloc(sizeof(Header) + sizeof(int)*init_capacity);
  header->count = 0;
  header->capacity = init_capacity;
  return (int*)(header + 1);
}

void arr_push(int* arr, int value) {
  Header *header = (Header*)arr - 1;
  assert(header->count < header->capacity);
  arr[header->count++] = value;
}

size_t arr_len(int* arr) {
  return ((Header*)arr - 1)->count;
}

int main() {
  int *numbers = arr_init(256);
  arr_push(numbers, 123);
  arr_push(numbers, 456);
  arr_push(numbers, 789);
  for(int i = 0; i < arr_len(numbers); i++) {
    printf("%d\n", numbers[i]);
  }
  return 0;
}
```

## 抽象成宏定义

```c
#include<assert.h>
#include<stdio.h>
#include<stdlib.h>

typedef struct {
  size_t count;
  size_t capacity;
} Header;

#define init_capacity 1

#define arr_push(arr, value) \
  do {  \
    if(arr == NULL) { \
      Header *header = malloc(sizeof(Header) + sizeof(*arr)*init_capacity); \
      header->count = 0;\
      header->capacity = init_capacity;\
      arr = (void*)(header + 1);\
    }\
    Header *header = (Header*)(arr) - 1;\
    if(header->count >= header->capacity) {\
      header->capacity *= 2;\
      header = realloc(header, sizeof(*arr)*header->capacity + sizeof(Header));\
      arr = (void*)(header + 1);\
    }\
    assert(header->count < header->capacity);\
    (arr)[header->count++] = (value);\
  } while(0)

// void arr_push(int* arr, int value) {
//   Header *header = (Header*)arr - 1;
//   assert(header->count < header->capacity);
//   arr[header->count++] = value;
// }

#define arr_len(arr) ((Header*)(arr) - 1)->count
// size_t arr_len(int* arr) {
//   return ((Header*)arr - 1)->count;
// }

#define arr_free(arr) free((Header*)(arr) - 1)

int main() {
  float *numbers = NULL;
  arr_push(numbers, 123); // 123.000000
  arr_push(numbers, 456);
  arr_push(numbers, 789);
  for(int i = 0; i < arr_len(numbers); i++) {
    printf("%f\n", numbers[i]);
  }
  arr_free(numbers);
  return 0;
}
```

