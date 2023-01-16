# Go 学习笔记

&emsp;&emsp;社会在发展，技术在革新。某“不作恶”的谷歌整了个新的后端开发语言——Go。

## Go 语言介绍

&emsp;&emsp;Go （ GoLang ）是一门新兴的静态类型语言，对并发友好。具有 GC 机制，多用于后端开发。但是 Go 目前还有许多缺点和问题，如第三方库可靠性不足、无泛型、无异常处理机制等

&emsp;&emsp;Go 的开发环境需要添加两个环境变量`GOROOT`（指向 Go 的安装路径，如`/usr/lib/go`）和`GOPATH`（指向 Go 的工作目录，如`~/go`），修改一个环境变量：

```shell
# /etc/profile.d/go.sh
export GOROOT=/usr/lib/go
export GOPATH=$HOME/go
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
```

## 基础语法

&emsp;&emsp;一个简单的 Go 程序如下：

```go
package main    // 当前文件所在的包

import "fmt"    // 导入外部包

func main() {
   /* 
       这是多行注释
   */
   fmt.Println("Hello, World!")
    // 介是单行注释
}
```

### 变量

&emsp;&emsp;Go 是一门静态强类型语言，声明变量的方式如下：

```go
// 显式声明，不初始化赋值0
var var0 double = 3.14
var var1, var2 int = 1, 2
var (
    v0 int = 0;
    v1 double = 2.7
)
// 根据赋值自动确定类型
var var2 = 2.0
// 省略 var，不可用于声明全局变量
name := "Go"
```

&emsp;&emsp;可使用`const`定义常量，可使用`const`与`iota`方便地定义枚举：

```go
const ca int = 1
// iota 初始为0，逐行递增
const (
    a, b = iota, iota+1            // 0, 1 
    c, d                           // 1, 2
    e, f = iota * 10, iota * 20    // 20, 40
    g, h                           // 30, 60
)
```

> 0. GoLang 是一门静态强类型语言，因此已经赋值的变量的类型已经确定，无法赋予其他类型的值。

### 分支循环

#### 条件判断

&emsp;&emsp;GoLang 的 `if else`和 C++ 的类似，但 GoLang 的条件语句不需要括号，如：

```go
if 3 % 2 == 0 {
    fmt.Println("3 是偶数")
} else if 3 % 2 == 1 {
    fmt.Println("3 是奇数")
} else {
    fmt.Println("3 是啥？猫猫不造诶")
}
```

#### 循环

&emsp;&emsp;GoLang 没有`while`循环和 `do while`循环，只有各式各样的`for`循环：

&emsp;&emsp;没有任何其他条件的是死循环：

```go
i := 0
for {
    fmt.Println("i = ", i)
    i++
}
```

&emsp;&emsp;可以使用类似 C++ 的 for 循环结构：

```go
for i := 0; i < 5 i++ {
    fmt.Println("i = ", i)
}
```

实际生产中起始条件和循环后置操作常常省略，可以使用类似`while`循环的循环：

```go
i := 0
for i < 5 {
    fmt.Println("i = ", i)
    i++
}
```

&emsp;&emsp;与 C++ 相同，GoLang 也可以使用 `continue`和`break`。

#### switch case

&emsp;&emsp;GoLang 的 `switch case`与 C++ 类似：

```go
switch score / 10 {
    case 10:
        fmt.Println("恭喜你考了100分，等级为A！")
    case 9:
        fmt.Println("你的等级为A")
    case 8:
        fmt.Println("你的等级为B")
    case 7:
        fmt.Println("你的等级为C")
    case 6:
        fmt.Println("你的等级为D")
    case 5, 4, 3, 2, 1, 0:
        fmt.Println("很遗憾，你没有及格")
}
```

&emsp;&emsp;GoLang 的`switch case`可以使用任意的变量类型，也可以省略 switch 后的变量，每个 case 作为一个独立的分支判断（但只执行第一个符合条件的）。

### 函数

&emsp;&emsp;与多数编程语言不同， Go 支持具有多个返回值的函数。函数的定义格式如下：

```go
// 单个返回值
func sum(a int, b int) int{
    return a + b
}
// 多个返回值
func swap(a int, b int) (int, int){
    return b, a
}
// 另一种写法
func swap(a int, b int) (ra int, rb int){
    ra = b
    rb = a
    return
}

func main(){
    a, b := 1,2
    println("a = ", a, ", b = " b)
    a, b = swap(a, b)
    println("a = ", a, ", b = " b)
}
```

&emsp;&emsp;在 Go 中可在函数任意位置使用`defer`关键字指定函数执行完毕后（return后）要做的事情，这些操作会按从后向前的顺序执行，类似压栈与弹栈。

### 包与模块

&emsp;&emsp;在 Go 中使用`import`导入包，导入后可使用`包名.函数名`调用包内的函数，如：

```go
import . fmt
import (
    _ "MyLib/Lib1"
    lib "MyLib/Lib2"
)

func main(){
    fmt.Printf("Hello world!")
}
```

&emsp;&emsp;可为包设置别名，如上面的“lib”，或设置别名为`.`将该包内的函数作用域改为当前包。

&emsp;&emsp;导入包时会自动调用其`init()`函数，有时我们只需要这个初始化函数，可将其包名设置为`_`来抑制包未使用的错误。

### 数据类型

#### slice

&emsp;&emsp;Go 中普通数组的使用方法如下：

```go
var myArray [10]int
myArray = [10]int{1, 2, 3, 4}
for i := 0; i < len(myArray); i++ {
	println(myArray[i])
}
for index, value := range myArray {
	println("index = ", index, ", value = ", value)
}
```

&emsp;&emsp;将这种数组作为函数实参时，函数必须指定数组长度，且为深拷贝传递，非常不适合开发，因此我们需要“动态数组”——切片（slice）：

```go
var mySlice = []int{1, 2, 3, 4}
mySlice = append(mySlice, 5, 6)
for _, value := range mySlice {
	println("value = ", value)
}
var slice0 = make([]int, 3, 5)
// 类型、初始化范围、容量，可不指定容量
slice0[2] = 2
fmt.Printf("len = %d, cap = %d, value = %v\n", len(slice0), cap(slice0), slice0)
slice0 = append(slice0, 5)
// slice0[3] = 5 不可行，因为未初始化
fmt.Printf("len = %d, cap = %d, value = %#v\n", len(slice0), cap(slice0), slice0)
slice0 = append(slice0, 1, 9, 5)
// 昂会增加到原来的两倍，可用范围不翻倍
fmt.Printf("len = %d, cap = %d, value = %#v\n", len(slice0), cap(slice0), slice0)
```

&emsp;&emsp;类似 Python的列表（List），Go 的 slice 也可以“切片”（名称怪怪的）:

```go
var slice = []int{1, 2, 3, 4, 5}
fmt.Printf("slice[1:3] = %v\n", slice[1:3])
// slice[1:3] = [2 3]
fmt.Printf("slice[:3] = %v\n", slice[:3])
// slice[:3] = [1 2 3]
fmt.Printf("slice[1:] = %v\n", slice[1:])
// slice[1:] = [2 3 4 5]
```

> 0. slice[start:end]：参数表示下标，左开右闭区间
> 1. 可以使用大于最大下标的参数来截取到末尾
> 2. 不支持负数下标

&emsp;&emsp;对 slice 的操作是操作引用，可以使用`copy(slice2, slice)`将 slice 的内容拷贝给 slice2

#### map

&emsp;&emsp;GoLang 的 map 就是个 map，使用方法如下：

```go
myMap := make(map[string]int, 3)
myMap2 := map[string]int{
	"code":   1,
	"height": 180,
	"weight": 60,
}
// 赋值
myMap["code"] = 1
// 删除
delete(myMap, "code")
```

> 0. map 中的数据不会自动排列，其顺序是随机的
> 1. 也可以使用 range 来遍历 map

## 面向对象编程

&emsp;&emsp;GoLang 的 OOP 有亿点原始……

### 结构体

&emsp;&emsp;结构体的定义方法如下：

```go
type double float64
type Cat struct {
	name   string
	age    int
	height double
	weight double
	gender bool
}
func main(){
    var monroe Cat
	monroe.age = 29
	monroe.name = "Monroe"
	monroe.height = 180
	monroe.weight = 60
	monroe.gender = false
	fmt.Println(monroe)
}
```

### 封装与继承

&emsp;&emsp;Go 使用以下格式将方法“绑定”到类上：

```go
func (this Cat) getName() string {
	return this.name
}

func (this *Cat) setName(newName string) {
	this.name = newName
}

func (this Cat) show() {
	fmt.Println("喵喵喵")
}
```

> 对对象进行修改操作的方法要使用指针，否则修改的是对象的拷贝

&emsp;&emsp;Go 中没有“类”一级的可见性，包内小写字母开头的属性和方法在包外可见，小写字母开头的包外不可见。

&emsp;&emsp;Go 真正践行了“extend”的含义——扩充，Go 中“继承”的表示方式如下：

```go
type JavaCat struct {
	Cat
	score int
}

func (this JavaCat) show() {
	fmt.Println("Java喵Java喵Java喵")
}
```

&emsp;&emsp;使用方法如下：

```
maomao := JavaCat{Cat{"猫猫", 19, 1.80, 80, true}, 80}
maomao.show()
maomao.Cat.show()
```

### 多态

&emsp;&emsp;Go 中接口的定义方式如下：

```go
type Animal interface {
	show()
	getName() string
	setName(newName string)
}
```

&emsp;&emsp;Go 中实现一个接口无需显式声明，重写其所有函数即可。

&emsp;&emsp;Go 真正实现了“一切皆对象”——就连所谓“基本数据类型”也实现了空接口`interface{}`。为了判断一个变量`arg`的类型是否为`string`，可使用“类型断言”：

```go
value, ok := arg.(string)
if ok {
	fmt.Println("arg 是 string, arg = ", value)
} else {
	fmt.Println("arg 不是 string")
}
```

### 反射

&emsp;&emsp;Go 中一个对象包括其值和实例类型两个部分，Go 的变量类型分为 static type 和 concrete type，前者指 GoLang 内置的变量类型；后者为 interface 可以指向的类型。

&emsp;&emsp;`reflect`包提供的`TypeOf()`和`ValueOf()`方法可以在运行时获取一个变量的类型和值：

```go
zml := "朱孟璐"
fmt.Println("Type: ", reflect.TypeOf(zml))
fmt.Println("Value: ", reflect.ValueOf(zml))
```

### 结构体标签

&emsp;&emsp;可以给结构体的属性添加键值对类型的标签：

```go
type double float64
type Cat struct {
	name   string `info:"name"`
	age    int    `info:"age" min:"0"`
}
```

## 并发

&emsp;&emsp;GoLang 原生支持并发。

### Goroutine

&emsp;&emsp;`GoRoutine`是一种轻量化的线程，由GoLang运行时调度。是只需在调用函数前添加`go`关键字便可创建一个 goroutine 来运行该函数：

```go
func hello(s string) {
	fmt.Println("Hello ", s, "!")
}
func main() {
	animal := []string{"可爱的狗狗", "猫猫", "熊熊", "鼠鼠(努力学习版)", "鲨鲨", "鼠鼠(仓鼠版)"}
	for _, name := range animal {
		go hello(name)
	}
	time.Sleep(time.Second)
}
```

### 线程通信

&emsp;&emsp;GoLang建议“通过通信实现共享内存”而不是“通过共享内存进行通信”，因为使用共享内存进行通信难免需要枷锁，这回降低程序性能。GoLang 通过“通道”（`channel`）进行 goroutine之间的通信。

&emsp;&emsp;创建 channel 使用`make`：

```go
ch := make(chan int)        // 无缓冲区
ch1 := make(chan int, 2)    // 有缓冲区
```

&emsp;&emsp;使用`<-`将数据写入 channel 或，使用`range`遍历 channel 中的数据：

## 依赖管理

&emsp;&emsp;GoLang 的依赖管理大致经历了 `GOPATH`、`Go Vender`和`Go Module`三个阶段：

&emsp;&emsp;在早期，使用`$GOPATH`环境变量来表示系统级项目的工作目录，其下有`bin`（存储编译好的二进制文件）、`pkg`（存储中间产物）和`src`（项目源码）。

&emsp;&emsp;后来，在每个项目的根目录下添加`vender`文件夹用于存储依赖，在`vender`下找不到的依赖再到`GOPATH`下寻找。

&emsp;&emsp;如今，我们采用"Go Module"来管理依赖，在项目根目录下或每个`package`下添加`go.mod`文件来定义项目依赖的库及其版本。`go.mod`的大致结构如下：

```go
module DoudiNCer/GoLearning
go 1.15
require (
    cats/cat v1.0.1
    cat/monroe v1.0.0-19930111030830-5pyx5a2f55KQCg // indirect
)
```

> 0. 常见的版本号有语义化版本号和基于commit的伪版本号
>
>    语义化版本号的结构为`主版本号.次版本号.修订版本号`
>
>    伪版本号的格式为`语义化版本号前缀-tag的时间戳-tag的12位哈希值`
>
> 1. `// indirect`字面意思，表示间接依赖
>
>    `+incomputable`用于兼容 Go Module 出现以前版本号过高的依赖

&emsp;&emsp;下载依赖时，首先会依次到`$GOPROXY`定义的镜像站下载，如果找不到，才会到原始项目托管平台下载。

&emsp;&emsp;下载依赖使用`go get`，管理项目使用`go mod`，集体用法可参照`go help get `和`go help mod`，此处不再赘述。
