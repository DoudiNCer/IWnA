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

