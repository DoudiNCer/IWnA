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

&emsp;&emsp;GoLang 也支持匿名函数，常用于下面要讲的`GoRoutine`：

```go
func(q int){
    result := 1
    for i := 1; i <= q; i++{
        result *= i
    }
    fmt.Println(i, "! = ", result)
}(5)
```

&emsp;&emsp;也可以将匿名函数赋值给一个变量，如：

```go
f := func(i int){
    fmt.Println(i)
}
```

&emsp;&emsp;GoLang 函数也支持可变参数，如

```go
func sum(vals ...int) int {
    total := 0
    for _, val := range vals {
        total += val
    }
    return total
}
```



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

### 系统与进程

&emsp;&emsp;GoLang 的 `os`包提供了许多与操作系统与进程有关的功能，如：

- 获取命令行参数，请用`os.Args`

    ```go
    for arg := range os.Args {
        println(arg)
    }
    ```

- 操作环境变量，请用`os.Getenv()`和`os.Setenv()`:

    ```go
    println(os.Getenv("LANG"))
    os.Setenv("USER", "朱孟璐")
    ```

- 运行终端命令，要这样做：

    ```go
    output, err := exec.Command("curl", "127.0.0.1").CombinedOutput()
    if err != nil {
        println("exec error", err)
    }
    println(string(output))
    ```

- 还有什么`Getuid()`、`Chmod()`之类的……

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

### JSON

&emsp;&emsp;JavaScript 对象表示法（JSON）是一种广泛用于发送和接收结构化信息的标准协议。GoLang 标准库提供了对 JSON 和其他标准（如 XML）的编解码支持。

&emsp;&emsp;为了自定义对象和 JSON 之间的相互转换，需要给结构体加上结构体标签，如：

```
type Cat struct {
    Name string     `json:"name"`
    Age  int        `json:"age"`
    Height double   `json:"height"`
    Weight double   `json:"weight"`
    Gender bool     `json:"gender"`
}
```

&emsp;&emsp;将结构体或结构体 slice 转换成 json 的过程称为编组（marshaling），通过`json.Marshal()`对对象进行编组：

```go
data, err := json.Marshal(cat)
if err != nil {
    log.Fatalf("JSON marshaling failed: %s", err)
    return
}
fmt.Printf("%s\n", data)
```

&emsp;&emsp;或者使用`json.MarshalIndent()`获取格式化后的 json：

```go
marshal, err := json.MarshalIndent(cat, "", "    ")
// 其第二个和第三个参数表示每行的前缀和缩进
```

&emsp;&emsp;使用`json.Unmarshal()`将 json 解组为对象：

```go
var cat Cat
err = json.Unmarshal(marshal, &cat)
if err != nil {
    log.Fatalf("JSON unmarshaling failed: %s", err)
    return
}
```

> 0. 只能编组/解构导出的属性
> 1. 若不提供 json 结构体标签，将使用属性名作为 key
> 2. 在结构体标签中添加`omitempty`可在值为零时不编组该属性

## 并发

&emsp;&emsp;GoLang 原生支持并发。

### Goroutine

&emsp;&emsp;`GoRoutine`是一种轻量化的线程，由GoLang运行时调度，同一个程序中的所有 goroutine 共享同一个地址空间。只需在调用函数前添加`go`关键字便可创建一个 goroutine 来运行该函数：

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
ch := make(chan int)        // 无缓冲区双向
ch1 := make(chan int, 2)    // 有缓冲区双向
chi := make(<-chan int)     // 只读
cho := make(chan<- int)     // 只写
```

&emsp;&emsp;使用`<-`将数据写入 channel 或从 channel 读取数据，使用`range`遍历 channel 中的数据

> 0. 对于不带缓冲区的通道，接受方获取数据前发送方会阻塞
> 1. 对于带缓冲区的通道，缓冲区满后发送方写入数据会导致写入线程阻塞

&emsp;&emsp;通道不再使用后，可通过`close()`函数来关闭，从而避免通道已空但接收方尝试读取导致阻塞的问题。

> 0. 在通道未初始化时关闭、重复关闭、关闭后发送共和发送时关闭会导致 panic

### 线程同步

&emsp;&emsp;`sync`包提供了基础的线程同步锁，创建一个互斥锁的方式如下：

```go
var lock sync.Mutex
```

&emsp;&emsp;该互斥锁使用`Lock()`和`Unlock()`方法对编辑共享内存的代码块加锁，保证线程同步。

&emsp;&emsp;有时候我们需要在主线程执行到某个位置时开启多个子线程执行其他任务，且在这些子线程全部结束前阻塞主线程，这时我们就需要`WaitGroup`：

```go
var wg sync.WaitGroup
```

&emsp;&emsp;WaitGroup有三个方法：`Add(delta int)`用于计数器增加 delta ；`Done()`用于表示一个线程完成；`Wait()`用于阻塞主线程。



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

## 编译器与内存管理

### 自动内存管理

&emsp;&emsp;自动内存管理的一些相关概念如下：

> - Mutator：业务线程
> - Collector： GC 线程
> - Serial GC：只有一个 collector
> - Parallel GC：支持多个 collector 同时运行的 GC 算法
> - Concurrent GC： mutator 和 collector 可同时运行的 GC 算法

&emsp;&emsp;评价一个 GC 算法的好坏，主要在以下几点：

> - 安全性（Safety）：不能回收存活的对象
>
> - 吞吐率（Throughput）：
>     $$
>     1-\frac{GC时间}{程序总执行时间}
>     $$
>     
> - 暂停时间（Pause Time）：Stop The World（STW）

&emsp;&emsp;常见的垃圾回收思路有追踪垃圾回收和引用计数。追踪垃圾回收的步骤包括标记根对象->标记可达对象（求指针指向关系的传递闭包）->清理不可达对象；引用计数的中心思想是记录每个对象所关联的指针数目（指向该对象的指针数目），当其降为0时即可清理该对象（这让我想到了 Linux 中`ls -l`看到的链接数）。和追踪垃圾回收相比，引用计数有内存管理过程平摊、实现简单等优点，但也有开销大、清理不彻底（如环状引用）等缺点。

&emsp;&emsp;在追踪垃圾回收清理中，清理不可达对象的方式包括：

> - 将存活对象复制到另外的内存中（Copying GC）
> - 将死亡对象的内存标记为“可分配”（Mark-Sweep GC）
> - 移动整理存活对象（Mark-Compact GC）

### Go 的内存分配

&emsp;&emsp;Go 的 heap 空间的分配过程大致如下：

> - 调用系统调用`mmap()`向操作系统申请一块内存
> - 将这块内存分割为大块
> - 将大块分为特定大小的小块
> - 对于含有指针的对象，进行 scan mspan；反之进行 noscan mspan。

&emsp;&emsp;Go

### 编译器优化和静态分析

&emsp;&emsp;GoLang 代码编译的大致过程如下：

- 词法分析：生成词素（lexeme）
- 语法分析：生成语法树
- 语义分析：生成类型信息、语义检查
- 中间代码分析：生成与平台无关的 Intermediate Representation （IR）
- 代码优化：对 IR 进行与平台无关的优化
- 代码生成：生成目标代码

&emsp;&emsp;静态分析是指不执行代码，推导程序行为、分析程序性质。常见的静态分析包括控制流（Control flow）分析（分析程序执行流程）和数据流（Data flow）分析（数据在控制流上的传递）。静态分析有助于简化代码，提高程序运行效率。

&emsp;&emsp;内联（Inline）是指将被调用函数的函数体（callee）的副本替换到被调用的位置上，GoLang编译器默认对简短的函数进行了内联。对简短的函数进行内联可减少函数调用的开销，提高程序运行效率；但是函数内联会增大程序体积、降低 instruction cache 效率。

&emsp;&emsp;逃逸分析是指代码中指针的动态作用域。对于没有逃逸的对象（作用范围在函数内）可直接在 stack 为其分配内存，降低 GC 压力。

## RedisGo

&emsp;&emsp;Redis 是 常用的非关系型数据库系统之一，常用于缓存、分布式系统、消息系统等。关于 Redis 的使用可查看[Redis 学习笔记](Articles/RedisLearningEssay.md)

&emsp;&emsp;GoLang 中操作 Redis 的常用工具有`redisgo`和`go-redis`，接下来以 redisgo 为例讲述 Redis 的操作。

> 0. 官方文档：http://godoc.org/github.com/gomodule/redigo/redis
> 1. 项目地址：https://github.com/gomodule/redigo

### 安装&连接 Redis

&emsp;&emsp;安装 redisgo 模块：

```bash
go install github.com/gomodule/redigo/redis@latest
```

&emsp;&emsp;创建连接：

```go
conn, err := redis.Dial("tcp", "127.0.0.1:6379", redis.DialPassword("123456"))
if err != nil {
    fmt.Println("Connect to redis error", err)
    return
}
defer conn.Close()
```

### 数据操作

&emsp;&emsp;使用`Do()`方法执行 Redis 操作，该方法的参数为操作和参数数组，如：

```go
_, err := conn.Do("set", "name", "猫猫")
do, err := conn.Do("get", "name")
```

&emsp;&emsp;该方法的第一个返回值为`interface{}`，需要手动转换：

```go
name, _:= redis.String(do, err)
fmt.Println(name)
```

> 0. 上述转换可简化为`name, _:= redis.String(conn.Do("get", "name"))`
> 1. 返回值类型转换方法第二个参数的原理是：若其不为空`err != err`，则返回`nil. err`，反之进行后续处理

### 事务操作

&emsp;&emsp;除了使用`Do()`操作 Redis，还可以使用`Send()`、`Flush()`和`Receive()`更灵活地操作。

&emsp;&emsp;`Send()`将操作写入客户端输出缓冲区；`Flush()`将输出缓冲区中的操作发往服务端执行；`Receive()`依次从客户端输入缓冲区取出服务端发送的结果：

```go
conn.Send("get", "name")
conn.Send("get", "age")
conn.Flush()
name, _ := redis.String(conn.Receive())
age, _ := redis.Int(conn.Receive())
fmt.Printf("name: %s, age: %d", name, age)
```

&emsp;&emsp;`Do()`结合了上述三个方法，首先将传入操作写入输出缓冲区，然后刷新输出缓冲区，最后获取输出缓冲区最后一个返回值作为方法返回值。

&emsp;&emsp;因此，使用事务的一个例子如下：

```go
conn.Send("MULTI")
conn.Send("set", "name", "Monroe")
conn.Send("set", "age", 30)
conn.Send("incr", "num")
do, err := conn.Do("EXEC")
```

> 0. 输入缓冲区为空时`Receive()`会导致阻塞

### Redis 连接池

&emsp;&emsp;在上述示例中，每次创建一个连接，完成操作后便将其关闭，极大浪费了系统资源。为了提高连接利用率，我们引入连接池来管理与 Redis 的连接，在需要时取出已经存在的未使用连接，实现连接复用。

&emsp;&emsp;创建连接池的方法如下：

```go
pool := &redis.Pool{
    MaxIdle:     5,   // 最大空闲连接数
    MaxActive:   0,   // 最大连接数，0表示无限制
    IdleTimeout: 100, // 最大空闲时间
    Dial: func() (redis.Conn, error) {
        return redis.Dial("tcp", "127.0.0.1:6379")
    },
}
```

&emsp;&emsp;获取连接使用连接池的`Get()`方法：

```go
conn := pool.Get()
```

&emsp;&emsp;关闭连接池使用`Close()`方法：

```go
pool.Close()
```

> 0. 关闭连接池后获取连接不会返回异常，但在进行后续操作时会产生异常
> 1. 从连接池获取的连接应使用`Close()`方法关闭以归还连接

## Web三件套

&emsp;&emsp;GoLang 的 Web 三件套包括`Gorm`、`Kitex`和`Hertz`。其中 Gorm 是一个拥有众多扩展的 ORM 框架；Kitex 是一个高性能的微服务框架；Hertz是一个 HTTP 框架。

### Gorm

> 官方文档/网站：https://gorm.cn/

#### 连接&初始化

&emsp;&emsp;目前 Gorm 支持 MySQL 、 SQLite 、 SQL Server 、PostgreSQL 四种数据库系统，下面以MySQL为例进行介绍：

&emsp;&emsp;导入依赖&数据库连接：

```go
import (
  "gorm.io/driver/mysql"
  "gorm.io/gorm"
)

func main() {
  // refer https://github.com/go-sql-driver/mysql#dsn-data-source-name for details
  dsn := "user:pass@tcp(127.0.0.1:3306)/dbname?charset=utf8mb4&parseTime=True&loc=Local"
  db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
}
```

&emsp;&emsp;定义表的结构（Gorm model）：

```go
type UserInfo struct{
    ID      uint    `gorm:"primaryKey;column:id"`
    Name    string  `gorm:"column:name"`
    Phone   string  `gorm:"default:12345678901"`
}
// 自定义表名
func (ui UserInfo) TableName() string {
    return "user_info"
}
```

> 0. 默认使用名为 ID 的键作为主键
> 1. 默认使用结构体名蛇形复述作为表名
> 2. 字段名的蛇形命名作为列名
> 3. 使用`CreatedAt`、`UpdatedAt`作为创建时间、修改时间
> 4. 上述结构体标签用于覆盖上述约定

#### CRUD

##### Create

&emsp;&emsp;使用`Create()`方法插入新数据，如：

```go
// 插入一条数据
db.Create(&UserInfo{
    ID:    1,
    Name:  "Maomao",
    Phone: "12345678901",
})
// 插入多条数据时可直接传入一个切片
```

> 0. 使用`db.Select().Create()`指定要插入的列
>
>     使用`db.Omit().Create()`跳过要插入的列
>
> 1. 支持使用`map[string]interface{}`或`map[string]interface{}{}`插入
>
> 2. 不填写主键且设置主键自增后可从原结构体获取主键值
>
> 3. 可使用`Clauses(clause.OnConflict{DoNothing: true})`指定主键冲突时不进行操作（与`Save()`相反）

##### Read

&emsp;&emsp;可以使用`First()`（主键升序）、`Take()`（数据库内存储顺序）或`Last()`（主键降序）根据条件查询一条数据（`LIMIT 1`），如：

```go
var ui UserInfo
// 查询主键升序第一条
db.First(&ui)
// 对整型主键进行查询（不安全）
db.First(&db, 2)
// 对主键外的字段进行条件查询（不能查主键）
db.First(&ui, "name = ?", "Maomao")
```

&emsp;&emsp;若需要查询的所有结果，使用`Find()`，用法与`First()`类似，如：

```go
var uis []UserInfo
db.Find(&uis, "`name` like ?", "%M%")
```

&emsp;&emsp;查询条件也可拆成多个函数，如上述`Find()`的例子可拆成如下形式：

```go
db.Where("`name` like ?", "%M%").Find(&uis)
```

> 0. `First()`在查询不到数据时会产生`ErrRecondNotFound`错误，`Find()`不会。
> 1. 使用 struct 作为查询条件，会忽略零值

&emsp;&emsp;除了`Where()`，还有`Not()`、`Or()`可对查询结果行添加筛选条件；`Select()`可对查询列添加筛选条件；`Limit()`和`Offset()`对结果条数添加条件等。

##### Update

&emsp;&emsp;可使用`Save()`根据主键更新（包括零值）或插入记录：

```go
newUi := UserInfo{
    ID:    4,
    Name:  "齐大",
    Phone: "12345678901",
}
db.Save(newUi)
```

&emsp;&emsp;更新部分列使用`Update()`，使用`Where()`添加限制条件，使用`Table()`指定表名或使用`Model()`指定表名和主键：

```go
db.Table("user_info").Where("id = ?", "4").Update("name", "齐大大")
```

> 0. 更新多列可使用`struct`或`map[string]interface{}`，但前者之更新非零列。
>
> 1. 可使用`Select()`强制指定要更新的列
>
> 2. 可使用`gorm.Expr()`编写表达式更新，如：
>
>     ```go
>     db.Model(&User{ID: 4,}).Update("age", gorm.Expr("age + ?"), 1)
>     ```

##### Delete

&emsp;&emsp;使用`Delete()`来删除记录：

```go
// 根据主键删除
db.Delete(&UserInfo{}, 4)
// 根据传入对象的主键删除
db.Delete(&ui)
// 根据附加条件删除
db.Where("name = ?", "齐大").Delete(&UserInfo{})
db.Delete(&UserInfo{}, "name = ?", "齐大")
```

&emsp;&emsp;Gorm 拒绝通过不加限制条件来删库，需要添加一些添加条件，如：

```go
// Wrong
db.Delete(&UserInfo{})
// Right
db.Where("1 = 1").Delete(&UserInfo{})
```

#### 事务

&emsp;&emsp;在 Gorm 开启一个事务使用`Begin()`，提交使用`Commit()`，回滚使用`Rollback()`，一个简单的示例如下：

```go
tx := db.Begin()
if err = tx.Create(&UserInfo{Name: "Maomao", Phone: "13261534257"}).Error; err != nil {
    tx.Rollback()
    return
}
if err = tx.Create(&UserInfo{Name: "Monroe", Phone: "13261634257"}).Error; err != nil {
    tx.Rollback()
    return
}
tx.Commit()
```

&emsp;&emsp;上述操作可使用`Transaction()`函数简化：

```go
db.Transaction(func(tx *gorm.DB) error {
    if err = tx.Create(&UserInfo{Name: "Maomao", Phone: "13261534257"}).Error; err != nil {
        return err
    }
    if err = tx.Create(&UserInfo{Name: "Monroe", Phone: "13261634257"}).Error; err != nil {
        return err
    }
    return nil
})
```

#### Hook

&emsp;&emsp;可通过实现 Hook 函数在 CRUD 前后进行自动化操作，如：

```go
func (u *UserInfo) BeforeCreate(tx *gorm.DB) (err error) {
    if len(u.Phone) != 11 {
        return errors.New("Invalid data")
    }
    return nil
}
```

&emsp;&emsp;Gram会使用默认事务执行 Hook 和单条语句，在 Hook 中返回异常会使事务回滚。对于不同的操作，Hook的调用顺序如下：

- Create:
    - 开始事务
    - BeforeSave
    - BeforeCreate
    - 关联前的 save
    - 插入记录至 db
    - 关联后的 save
    - AfterCreate
    - AfterSave
    - 提交或回滚事务

- Read
    - 从 db 中加载数据
    - Preloading (eager loading)
    - AfterFind

- Update
    - 开始事务
    - BeforeSave
    - BeforeUpdate
    - 关联前的 save
    - 更新 db
    - 关联后的 save
    - AfterUpdate
    - AfterSave
    - 提交或回滚事务

- Delete
    - 开始事务
    - BeforeDelete
    - 删除 db 中的数据
    - AfterDelete
    - 提交或回滚事务

#### 性能提升&补充

&emsp;&emsp;为了保证数据安全，Gorm 对于单独的操作开启了默认事务，若不需要关联操作和 Hook 可考虑关闭默认事务，即在创建连接时添加`SkipDefaultTransaction: true`。

&emsp;&emsp;可通过开启 SQL 语句预编译来提升性能，即在创建连接时添加`PrepareStmt: true`

### Kitex

&emsp;&emsp;Kitex 是 GoLang Web 开发中~~常用~~的 RPC框架，本文以`Kitex v0.4.4`为例介绍 Kitex 的使用方法。

> 0. Kitex 的官方文档为：https://www.cloudwego.io/zh/docs/kitex/overview/
> 1. Kitex 当今对 Windows 支持不完善，建议使用 Linux

#### RPC

&emsp;&emsp;RPC（Remote Procedure Call，远程过程调用）是使在调用方不知情的情况下让调用者像调用本地资源一样调用网络资源，是微服务重要的技术之一。

#### 安装配置

&emsp;&emsp;安装 Kitex 和 Thrift Go：

```bash
go install github.com/cloudwego/kitex/tool/cmd/kitex@latest
go install github.com/cloudwego/thriftgo@latest
```

&emsp;&emsp;使用以下命令生成代码

```bash
kitex -module $ModuleName -service $ServiceName $ThriftFile
```

&emsp;&emsp;生成的目录结构大致如下：

```
.
├── build.sh                // 构建脚本
├── go.mod
├── handler.go              // 用于实现接口
├── kitex_gen
│   └── api
│       ├── hello
│       │   ├── client.go
│       │   ├── hello.go
│       │   ├── invoker.go
│       │   └── server.go
│       ├── k-consts.go
│       ├── k-zml.go
│       └── zml.go
├── kitex.yaml
├── main.go                  // 程序入口
├── script
│   └── bootstrap.sh
└── zml.thrift
```

&emsp;&emsp;使用`build.sh`进行项目编译。

#### IDL

&emsp;&emsp;IDL（Interface Description Language，接口定义语言）是用于定义、描述接口的语言。在接下来的开发中，将使用 `Thrift IDL`生成代码来辅助开发。

&emsp;&emsp;一个简单的 Thrift IDL 的示例如下：

```idl
namespace go api

struct Request{
    1: string message
}

struct Response{
    1: string message
}

service Echo {
    Response echo(1: Request req)
}
```

> 0. Thrift IDL 语法 https:/thrift.apache.org/docs/idl
> 1. Proto3 IDL 语法 https://developers.google.com/protocol-buffers/docs/proto3

#### 服务端与客户端

&emsp;&emsp;Kitex 自动生成的服务端的启动代码（main.go下的主函数）如下：

```go
svr := zml.NewServer(new(RemoteMonroeImpl))
err := svr.Run()
if err != nil {
    log.Println(err.Error())
}
```

&emsp;&emsp;此部分可操作的部分为`NewServer()`的后续参数，可通过`server.WithXXX()`的形式指定各种服务端参数（Server Option），如：

```go
addr, _ := net.ResolveTCPAddr("tcp", "0.0.0.0:38080")
svr := zml.NewServer(new(RemoteMonroeImpl), server.WithServiceAddr(addr))
```

&emsp;&emsp;但是我们需要自己编写客户端的调用程序555。获取客户端的方式与获取服务端类似，调用`NewClient()`即可，如：

```go
cli, err := zml.NewClient("zml.remoteMonroe", client.WithHostPorts("127.0.0.1:8888"))
```

&emsp;&emsp;除了像服务端添加服务端参数那样添加客户端参数（Client Option），客户端还支持在每次调用时添加调用参数（Call Option），如：

```go
req := zml.Request{Name: "猫猫"}
hello, err := cli.Hello(context.Background(), &req, callopt.WithConnectTimeout(time.Duration(time.Second)))
```

> 0. Call Option 优先级高于 Client Option

&emsp;&emsp;使用 Thrift 等生成的服务器端逻辑实现代码在`handler.go`中编写。

### Hertz

&emsp;&emsp;Hertz 是一个 GoLang 微服务 HTTP 框架，下面使用`hertz v0.5.1`为例介绍其用法。
> 0. 官方文档：https://www.cloudwego.io/zh/docs/hertz/overview
> 1. 官方代码示例：https://github.com/cloudwego/hertz-examples

#### 安装&配置
&emsp;&emsp;安装`hertz`：
```bash
go install github.com/cloudwego/hertz/cmd/hz@latest
```

&emsp;&emsp;创建项目：

```bash
hz new -mod $ModuleName # 和Kitex一样，在$GOPATH下可不指定 ModuleName
go mod tidy
```

&emsp;&emsp;生成的项目目录结构大致如下：

```
.
├── biz
│   ├── handler
│   │   └── ping.go
│   └── router
│       └── register.go
├── go.mod
├── go.sum
├── main.go
├── router_gen.go
└── router.go
```

#### 路由与处理器

&emsp;&emsp;服务端通过定义路由（router）来分类处理接收到的请求，建议在项目根目录下`router.go`中的`customizedRegister()`函数中定义路由。

&emsp;&emsp;一个简单的 GET 路由示例如下：

```go
r.GET("/ping", handler.Ping)
```

&emsp;&emsp;Hertz 支持静态路由、参数路由和通配路由。静态路由即全字符匹配，如上述示例所示；参数路由指形如`/cat/:name`的路由，匹配到的参数可用于后续处理；通配路由指形如`/image/*path`的路由。其优先级顺序为静态路由 > 命名路由 > 通配路由。

&emsp;&emsp;Hertz 提供了路由分组的功能，通过`Group()`生成分组，如：

```go
userR := r.Group("/user")
adminR := r.Group("admin")
userR.GET("/info", handler.user.GetInfo)
adminR.GET("/info", handler.admin.GetInfo)
```

> 0. 除了常见的`GET`、`POST`、`PUT`、`DELETE`等请求方式，Hertz还提供了`Handle`来自定义请求方式，`Any()`来注册所有请求方式，`StaticFile/Static/StaticFS`来注册静态文件。
> 1. 使用匿名函数注册路由时，若需要处理器名称，需要使用形如`GETEX()`的路由注册函数来指定处理器名称。
> 2. Hertz 提供了 `NoRoute` 与 `NoMethod` 方法用于全局处理 HTTP 404 与 405 请求，使用`NoRoute`需开启`WithHandleMethodNotAllowed`

&emsp;&emsp;Hertz 注册路由时传入的函数即为用于处理业务的处理器（Handler），一个简单的处理器函数如下所示：

```go
func Ping(ctx context.Context, c *app.RequestContext) {
    c.JSON(consts.StatusOK, utils.H{
        "message": "pong",
    })
}
```

#### 参数获取与校验

&emsp;&emsp;Hertz 使用` go-tagexpr `进行参数校验，可使用`RequestContext.BindAndValidate()`对参数进行绑定和校验，也可调用单独的方法进行绑定或校验。

> 0. go-tagexpr 的项目地址：https://github.com/bytedance/go-tagexpr
> 1. 参数校验语法：https://github.com/bytedance/go-tagexpr/tree/master/validator

&emsp;&emsp;首先要定义用于接收参数的结构体，如：

```go
type request struct {
    ver int `path:"version"`
    id  int `query:"id"`
}
```

&emsp;&emsp;用于绑定的 tag 包括`path`、`form`、`query`、`header`、`json`、`raw_body`，校验参数写在`vd`参数中
