---
title: go-learn
published: 2026-09-22
pinned: false
description: go-learn
tags: [go, stack]
image: 'api'
category: go
slug: go-learning
draft: false
---

## 教程地址

https://www.bilibili.com/video/BV1gf4y1r79E

## SDK Download

https://golang.google.cn/dl/

## Go优势

- 编译部署简单
- 不依赖其他库
- 静态类型语言 天生并发

- 强大标准库：runtime、高效GC、标准库
- OOP+多系统

## Golang适合用来做什么

1、云计算基础设施领域 代表项目：docker、kubernetes、etcd、consul、cloudflare CDN、七牛云存储等。

2、基础后端软件 代表项目：tidb、influxdb、cockroachdb等。

3、微服务 代表项目：go-kit、micro、monzo bank的typhon、bilibili等。

4、互联网基础设施 代表项目：以太坊、hyperledger等。

## Hello Go

```go
package main

import (
	"fmt"
	"time"
)

func main()  {
	fmt.Println("Hello go")
	time.Sleep(1 * time.Second)
}
```

## 变量

```go
package main

import (
	"fmt"
)

var globalState bool = false

func main()  {
	var (
		x int = 100
		y int = 200
	)
	showPoint := false
	fmt.Println("y = ", y, "\nshowPoint = ", showPoint)
	fmt.Printf("Type of x = %T\n", x)
}
```

```
go run hello.go
y =  200
showPoint =  false
Type of x = int
```

## 常量

```go
const (
	a, b = iota + 1, iota + 2
	c, d	

	e, f = iota * 2, iota * 3
    g, h
)
```

## 函数

```go
package main

import "fmt"

// 普通函数
func getUserId(method string, url string) int {
	return 2022811900	
}
// 多返回值函数
func getPoint(x int, y int) (int, int) {
	return 10, 20
}
// 多返回值 + 形参初始化
func getPoint2(x int, y int) (z, o int) {
	z = 100
	o = 200
	return 
}

func main()  {
	userId := getUserId("GET", "/user")
	fmt.Println(userId)
	x, y := getPoint(1, 2)
	fmt.Println("X:", x, "\nY:", y)
	x, y = getPoint2(1, 2)
	fmt.Println("X:", x, "\nY:", y)
}
```

## 包

一个"包" = 一个目录。一个目录里只能有同一个 package 名。import 的路径 = 模块名 + 子目录路径。

```
└───module-test
    │   go.mod
    │   hello.go
    └───lib
            lib.go
```

lib.go

> 包有一个init方法，被引入时，会调用。执行在main方法之前。

```go
package lib

import "fmt"

func GetUserAPI()  {
	fmt.Println("Lib API")
}

func init()  {
	fmt.Println("Lib Init")
}
```

hello.go

```go
package main

import "module-test/lib"

func main() {
	lib.GetUserAPI()
}
```

go.mod

```go
module module-test
go 1.26.1
```

run

```
go run .\hello.go
Lib Init
Lib API
```

### 匿名导入

> 匿名导入的包会运行init方法，且不使用也不会报错

```go
package main

import _"module-test/lib"

func main() {
	// lib.GetUserAPI()
}
```

### 导入当前文件

> 使用`.`可以导入到当前文件，函数可以直接调用 **不推荐**

```go
package main

import ."module-test/lib"

func main() {
	GetUserAPI()
}
```

## 指针

```go
package main

import "fmt"

func swap(value1 *int, value2 *int) {
	fmt.Println("In Swap Function Value1 Address: ", &value1, "     Value=", value1)
	fmt.Println("In Swap Function Value2 Address: ", &value2, "     Value=", value2)
	temp := 0;
	temp    = *value1
	*value1 = *value2
	*value2 = temp
}

func main() {
	value1 := 10
	value2 := 20
	fmt.Println("Before Swap Value1 Address: ", &value1, "     Value=", value1)
	fmt.Println("Before Swap Value2 Address: ", &value2, "     Value=", value2)
	swap(&value1, &value2)
	fmt.Println("After Swap Value1 Address: ", &value1, "     Value=", value1)
	fmt.Println("After Swap Value2 Address: ", &value2, "     Value=", value2)
}
```

```shell
❯ go run .\hello.go
Before Swap Value1 Address:       0x17d72ebca0b0      Value= 10
Before Swap Value2 Address:       0x17d72ebca0b8      Value= 20
In Swap Function Value1 Address:  0x17d72ebc4068      Value= 0x17d72ebca0b0
In Swap Function Value2 Address:  0x17d72ebc4070      Value= 0x17d72ebca0b8
After Swap Value1 Address:        0x17d72ebca0b0      Value= 20
After Swap Value2 Address:        0x17d72ebca0b8      Value= 10
```

## Defer

> defer 后面的语句会在函数结束前执行

```go
func main() {
	defer fmt.Println("Defer 1")
	defer fmt.Println("Defer 2")
	fmt.Println("1")
	fmt.Println("2")
}
```

```shell
1
2
Defer 2
Defer 1
```

1. Defer是压栈，先进后出
2. **Defer函数比Return后执行**

## 数组

```go
package main

import "fmt"

func arrTest1(arr [2]int)  {
	// 固定长度的数组函数参数传递为值传递 不会修改原有数组数据	
	arr[0] = 100 
}

// slice数组是引用传递
func arrTest2(arr []int)  {
	arr[0] = 100 
}

func printArr(arr []int) {
	for index, value := range arr {
		fmt.Println("arr3[",index,"] = ", value)
	}
}

func main() {
	var arr1 [10]int // 10 len arr every element's value equals 0
	arr2 := [2]int{1, 2} // Type: [2]int
	arr3 := []int{1, 2}  // Type: []int
	arrTest2(arr3)
	printArr(arr3)
}
```

### slice

```go
func main() {
    // 这样定义的slice没有分配任何空间 不能进行使用
	var slice []int
	fmt.Println("slice=", slice)
	fmt.Println("slice=", slice[0])
}
```

```
slice= []
panic: runtime error: index out of range [0] with length 0
```

```go
func main() {
	var slice []int
    // 如需使用slice 必须要先使用make开辟空间才行
	slice = make([]int, 3)
	fmt.Println("slice=", slice[0])
}
```

判断slice是否可用

```go
func main() {
	var slice []int
	if slice == nil {
		fmt.Println("Slice Not Avaliable")
	} else {
		fmt.Println("Slice OK")
	}
}
```

### slice常见使用

> slice1和slice2指向同一个地址 若需深拷贝，使用copy函数: copy(target, source)

```go
func main() {
	// cap = 3 扩容时翻倍
	slice1 := []int{1, 2, 3}
	slice2 := slice1[0:2]

	slice2[0] = 10

	fmt.Printf("slice1 结构体地址: %p, slice1=%v\n", &slice1, slice1)
	fmt.Printf("slice2 结构体地址: %p, slice2=%v\n", &slice2, slice2)

	fmt.Printf("slice1底层数组地址: %p\n", slice1)
	fmt.Printf("slice2底层数组地址: %p\n", slice2)

	// cap 取出slice capacity
	var slice3 []int = make([]int, len(slice1), cap(slice1))
	copy(slice3, slice1)
	slice3[0] = 100
	fmt.Println("slice1=", slice1)
	fmt.Println("slice3=", slice3)
}
```

```
slice1 结构体地址: 0x7fe99616078, slice1=[10 2 3]
slice2 结构体地址: 0x7fe99616090, slice2=[10 2]
slice1底层数组地址: 0x7fe99622000
slice2底层数组地址: 0x7fe99622000
slice1= [10 2 3]
slice3= [100 2 3]
```

## map

```go
func main() {
	var score map[string]int = make(map[string]int, 10)
	score["English"] = 80
	fmt.Println(score) // map[English:80]

	// cap 自动分配
	score2 := make(map[string]int)
	score2["History"] = 90
	fmt.Println(score2) // map[History:90]
}
```

> **map在参数传递时，是值传递**

```go
package main

import "fmt"

func printScore(mapObj map[string]int) {
	for key, value := range mapObj {
		fmt.Printf("mapObj[%s]=%d\n", key, value)
	}
}

func changeScore(mapObj map[string]int) {
	// update/change
	mapObj["History"] = 100
	// add
	mapObj["Chinese"] = 60
	delete(mapObj, "English")
}

func main() {
	var score map[string]int = make(map[string]int, 10)
	score["English"] = 80
	score["History"] = 90
	printScore(score)
	changeScore(score)
	printScore(score)
}
```

```
mapObj[English]=80
mapObj[History]=90
===================
mapObj[History]=100
mapObj[Chinese]=60
```

## 类型别名type

```go
type Integer int

func main() {
	var score Integer = 100	
	fmt.Println(score)
}
```

## Struct

```go
type UserInfo struct {
	id int
	name string
}

func changeUserName(user *UserInfo) {
	user.name = "Eric"
}

func main() {
	var user UserInfo
	user.id = 10010
	user.name = "Alex"
	fmt.Println(user.id)
	changeUserName(&user)
	fmt.Println(user.name) // Eric
}
```

基本使用

```go
type UserInfo struct {
	age int
	name string
}

func (this *UserInfo) Log() {
	fmt.Println(*this)
}

func (this *UserInfo) SetName(name string) {
	this.name = name
}

func (this *UserInfo) GetName() string{
	return this.name
}

func main() {
	user := UserInfo{ age: 18, name: "Alex" }
	user.SetName("Eric")
	user.Log() // {18 Eric}
	fmt.Println(user.GetName()) // Eric
}
```

> 首字母大写意味着public 小写意味着private

## 类与继承

```go
package main

import "fmt"

type BaseDO struct {
	status int
}

func (this *BaseDO) GetStatus() int {
	return this.status
}

type UserInfo struct {
	age int
	name string
	BaseDO // 匿名嵌入作为继承
}

func (this *UserInfo) Log() {
	fmt.Println(*this)
}

// 重写父类方法
func (this *UserInfo) GetStatus() int {
	this.status = this.status + 10
	newStatus := this.status
	return newStatus
}

func main() {
	user := UserInfo{ age: 18, name: "Alex" }
	user.status = 10
	user.GetStatus()
	user.Log() // {18 Alex {20}}
}
```

## 接口与多态

Go语言中，没有像java那样直白的定义接口的方式。

如果要实现接口，**直接实现里面的内容，不需要额外操作！**

Interface相当于一个指针！所以赋值需要&

```go
package main

import "fmt"

type BaseEntityInterface interface {
	SetField(value string) 
	GetField() string
}

func GetEntityField(entity BaseEntityInterface) {
	entity.GetField()
}

type UserInfo struct {
	age int
	name string
}

func (this *UserInfo) SetField(value string) {
	this.name = value
	fmt.Println("UserInfo SetField: ", this.name)
}

func (this *UserInfo) GetField() string {
	fmt.Println("UserInfo GetField Name = ", this.name)
	return this.name
}

type VideoInfo struct {
	vid string
}

func (this *VideoInfo) SetField(value string) {
	this.vid = value
	fmt.Println("VideoInfo SetField: ", this.vid)
}

func (this *VideoInfo) GetField() string {
	fmt.Println("VideoInfo GetField Vid = ", this.vid)
	return this.vid
}

func main() {
	var entity BaseEntityInterface
	entity = &UserInfo{ 18, "Alex"}
	entity.SetField("Eric")
	GetEntityField(entity)
	entity = &VideoInfo{ "123456" }
	entity.SetField("78901")
	GetEntityField(entity)
}
```

```
UserInfo SetField:  Eric
UserInfo GetField Name =  Eric
VideoInfo SetField:  78901
VideoInfo GetField Vid =  78901
```

## object与类型断言

```go
package main

import "fmt"

type object interface{}

func print(obj object) {
	fmt.Println(obj)

	value, ok := obj.(string)
	if ok {
		fmt.Println("Obj Is String Value=", value)
	} else {
		fmt.Printf("Obj Is Not A String Type=%T\n", value)
	}
}

func main() {
	print("hello world")
	print(12345)
}
```

## 变量Pair