# C++ 学习笔记

&emsp;&emsp;一个 Java 程序猿看着手里那本比 *Core Java* 还大一圈的 ~~C艹 Primer Plus~~ *C++ Primer Plus* 陷入了沉思……

> 注：基于作者本人水平，本笔记省略与C语言相同的部分，请在熟悉C语言后再进行阅读和学习。

## 复合数据类型

&emsp;&emsp;在C语言中，有时我们会将多个基本类型的数据放到一起，组成派生类型来使用，如数组、结构体等。在C++中，我们仍然会使用类似的复合类型，并将其发扬光大，引入面向对象中的“类”。

### 数组

&emsp;&emsp;C++的数组与C的数组使用上基本一致，这里仅列出一些 C++11 中的新特性：

0. 初始化数组可省略“=号：

   ```c++
   int num[4] {1, 2, 3, 4};
   ```

1. 可使用空的大括号填充0：

   ```c++
   int num[4] {};
   ```

### 字符串

&emsp;&emsp;在C语言中没有字符串，我们使用字符数组模拟字符串。而在C++中，我们既可以使用字符数组模拟字符串，也可以使用C++98中添加的string类型（需引入string头文件），string类也可以使用C中的常用方法（需引入cstring头文件）：

```c++
string str;
cin >> str;
cout << str << ": " << strlen(str) << endl;
```

&emsp;&emsp;字符不仅仅有ASCII字符，字符数组也不能只有char数组。C++有wchar_t（占用2-4字节），C++11添加了char16_t（2字节）和char32_t（4字节）以及对UTF-8的支持，分别用

前缀“L”、“u”、“U”和“u8”表示相应的字符串字面量：

```c++
wchar_t wchar[] = L"wchar_t string";
char16_t ch16[] = u"char16_t string";
char32_t ch32[] = U"char32_t string";
```

&emsp;&emsp;C++11还添加了“原始字符串”，以R和自定义定界符包围的字符串中所有符号的特殊作用将被取消，如：

```c++
cout << R"(A "\n" means the end of a line.)";
// 输出“A "\n" means the end of a line.”
cout << R"%$(We can use "()" here !!)%$";        // 自定义定界符不可包含空格、斜线、括号和控制字符。
```

### 指针与引用

&emsp;&emsp;指针是我们的老朋友了，并且在C++中它让然将陪伴我们。在C++中，指针带来了它的弟弟——引用，引用相当于一个变量的别名，在定义时即完成初始化，用法与原变量相同，指向的内存地址不可修改：

```c++
int a;
int &b = a;
cout << a==b;
```

