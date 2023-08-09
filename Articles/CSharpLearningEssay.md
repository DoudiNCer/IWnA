# C# 学习笔记

&emsp;&emsp;C#（读作`See Sharp`）是一门 .NET 框架下的面向对象的编程语言，现主要用于 Windows 桌面应用程序开发等。

## .NET

&emsp;&emsp;.NET（读作`Dot Net`）是微软推出的一套完整的软件生态框架，早期是 Windows 独占的 .NET Framework，后来推出了开源、跨平台的 .NET Core。

## C# 基础

&emsp;&emsp;一个单文件版 C# “Hello world”程序的结构大致如下：

```c#
// Hello_World.cs
/*
  这是多行注释
 */
// 使用 “Syatem” 命名空间
using System;

// 下面的代码位于 “Hello_World” 命名空间
namespace Hello_World {
    class Program {
        static void Main(string[] args) {
            Console.WriteLine("Hello Monroe!");
        }
    }
}
```

&emsp;&emsp;使用`csc Hello_World.cs `编译上面的代码，得到一个名为`Hello_World.exe`的 PE32 可执行文件。我们可以在 Windows 或 Linux（安装 .NET Core Runtime 的情况下）下运行该文件。

> 其实，C# 和 C++ 挺像的，你可以这么写：
>
> ```c#
> using System;
> 
> Console.WriteLine("Hello Monroe!" );
> ```

### 变量与数据类型

&emsp;&emsp;C# 中变量类型分为值类型、对象类型和指针类型。值类型（Value types）包括各种“基本数据类型”，如`int`、`bool`、`char`等；引用类型（Reference types）包括各种对象和动态（Dynamic）类型；指针（Pointer）与C/C++的指针类似，但属于不安全的代码，不建议使用。本节主要脱落值类型变量。

&emsp;&emsp;C# 中值类型变量的定义和初始化的方式与 C++ 类似：

```c#
int age;
age = 18;
// 十进制小数
decimal pi = 3.14M;
```

&emsp;&emsp;可以使用`const`修饰一个变量使其变为不可修改的常变量，但需要在定义时为其赋值，如：

```c#
const int age = 19;
```

&emsp;&emsp;当将一个小容量（低精度）变量转换为一个大容量（高精度）类型的变量时，可进行安全地隐式转换，如：

```c#
int age = 18;
long Age = age;
```

&emsp;&emsp;否则，需要进行可能有损失的强制类型转换：

```c#
double age = 18.6;
int Age = (int)age; // Age = 18
```

