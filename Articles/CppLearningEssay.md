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

## 面向对象

&emsp;&emsp;在以前，我们直接操作各种基本数据类型，一步一步达到我们的目的。后来发现操作大量的基本类型不太方便，就有了各种复合类型，比如数组、结构体……我们能不能把某些操作结构体的函数打包进结构体，并且限制对其内部变量的操作呢？于是，类与对象出现惹！~~好惹，以后没对象就自己new一个。~~

### 封装

#### 类的定义

&emsp;&emsp;为了限制对对象属性的访问、保护数据、降低代码耦合性，我们将一个类的属性和方法封装起来，并加上访问修饰符以限制访问，如：

```c++
// Circle.h
class Circle{
    private:
        static const double Pi = 3.14159;   // const和static貌似不用考虑顺序
        double Radius;
    public:
        Circle();
        Circle(int);        // 构造函数
        void setRadius(double);
        double getCircumference(void);
        double getSize(void);
        ~Circle();          // 析构函数
}
// Circle.cpp
Circle::Circle(){
    Radius = 1;
}
Circle::Circle(double Radius){
    this->Radius = Radius;
}
void Circle::setRadius(double Radius){
    this->Radius = Radius;   // 为了避免歧义，此处使用this指针代表当前对象
}
double Circle::getSize(void){
    return Size * Size * Pi;
}
double Circle::getCircumference(void){
    return Size * Pi * 2.0;
}
Circle::~Circle(){}
```

&emsp;&emsp;在类的方法中，可使用zhis指针使用调用该方法的对象。

&emsp;&emsp;若不显式定义构造函数和析构函数，会自动生成无参构造函数和析构函数；若提供了含参构造函数，需要提供无参构造函数。

#### 访问修饰符

- public：在任何地方都可见
- protected：在类外不可见，在子类中可见
- private：仅在类内函数可见

> 若不提供访问修饰符，则为private

#### 友元

&emsp;&emsp;类的友元函数是定义在类外部，但有权访问类的所有私有（private）成员和保护（protected）成员。尽管友元函数的原型有在类的定义中出现过，但是友元函数并不是成员函数。

&emsp;&emsp;我们可以在类内使用friend声明一个友元函数：

```c++
friend void getSize();
```

&emsp;&emsp;或将一个类声明为另一个类的友元类，此时该类的所有方法都属于该类的友元函数：

```c++
friend class CircleUtils;   // 此时CircleUtils类的所有方法均可操作该类
```

### 继承

&emsp;&emsp;继承是面向对象编程的重要内容之一，阐述了类与类之间的逻辑关系。

```c++
class Flower :: public Plants{}
```

&emsp;&emsp;C++ 的继承有公有（ public ）继承、保护继承（ protected ）继承和私有（ private ）继承，简单来说，公有继承中子类“原封不动”获得父类属性和方法（“is-a”），保护继承中父类公有属性和方法在子类变为保护，私有继承中父类的属性和方法在子类中全部为私有（“has-a”）。

&emsp;&emsp;C++允许多继承（即子类可继承多个超类），但这也导致了可能的命名冲突，此时可通过显式指明属性或方法或使用虚基类（使用virtual修饰继承的类）。

### 多态

&emsp;默认情况下，超类与子类出现重名属性或方法时，会根据对象指针的类型决定使用的是哪个属性或方法：

```c++
sub *subClass = new sub();
super *superClass = subClass;   
superClass->func();             // 调用超类方法
subClass->func();               // 调用子类方法
subClass->super::func();        // 调用超类方法
```

&emsp;&emsp;这种现象是由于编译时就确定了方法指针。

#### 虚函数与方法重写

&emsp;&emsp;有时我们希望根据对象本身的类型调用方法，即让子类覆盖超类的方法，这时可以将超类需要被重写的方法变为虚函数（使用 **virtual** 关键字修饰）。

```c++
virtual void paintEvent(QPaintEvent *event);
```

> - 当超类的一个函数被标记为虚函数时，其子类的该函数也为虚函数且无法取消该属性。
> - 方法重写属于运行时多态。

#### 接口与抽象类

&emsp;&emsp;接口用于描述类是行为和功能，接口不需要实现这些功能。

&emsp;&emsp;C++没有接口类型，C++通过抽象类实现接口的效果。抽象类指含有纯虚函数的类，不可被直接实例化。纯虚函数指没有函数体的函数：

```c++
class StudentService{
    public:
        virtual Student* checkInfo(int id) = 0;
        virtual bool addStudent(Student* stu) = 0;
}
class StudentServiceImpl : public StudentService{}
```

> 不能创建抽象类的对象，但可以声明抽象类的指针。

#### 方法重载

&emsp;&emsp;广义的多态还包括方法重载和运算符重载，方法重载是指一组名称和返回值类型相同，参数（形参的类型数量排列顺序，不包括形参名）和函数体不同的函数，此时可根据传入参数不同调用不同的方法。

```c++
void func(int arga, char argb);
void func(int arga);
void func();
```

#### 运算符重载

&emsp;&emsp;除了函数重载，C++还支持运算符重载，即根据操作对象类型不同使用不同的运算法则：

```c++
class Double{
    double D;
    public:
        double operator+(double d){
            return this->D + d;
        }
}
```

&emsp;&emsp;运算符重载也有一定的限制：

> - 不是所有运算符都可以重载，如`\+ - * / % ^ & | ~ ! = < > += -= *= /= %= ^= &= |=  << >> <<= >>= == != <= >= && || ++ -- , ->* -> ()（函数调用） []（下标运算符）  new new[] delete delete[]`可以重载，`. ?: .* ->* :: sizeof #`不可以重载
> - 重载不能改变运算符的优先级、结合性和用法（操作数数量和位置），因此运算符重载函数不能有默认的参数，否则就改变了运算符操作数的个数。
> - 运算符重载函数既可以作为类的成员函数，也可以作为全局函数。
>   - 作为成员函数时，将第一个参数作为调用函数的对象，因此参数列表和会少一个参数。
>   - 作为全局函数时，参数列表中的参数即为操作数，其中需要有至少一个对象，注意使用友元解决访问控制问题
> - 箭头运算符`->`、下标运算符`[ ]`、函数调用运算符`( )`、赋值运算符`=`只能以成员函数的形式重载
> - 重载自增（++)、自减（--）运算符时，为了区分前置和后置运算，约定后置运算符的参数列表中有一个int。

## IO

### IO基本概念

- IO：输入输出。以程序的视角看，程序获取数据即为“输入”，程序将数据传输到外部即为“输出”。
- 流：输入/输出操作可以看作是数据的有向流动，即数据从一个设备（源）流向另一个设备（目标）。在 C++ 中进行输入输出的是流对象，如 cin/cout 都是内置的输入输出对象。

### C++流类库

&emsp;&emsp;C++ 的流类库提供了大量的IO流，所有的流类都继承自 ios 。

#### 缓冲区

&emsp;&emsp;在某些情况下，为了提高效率，并不需要立刻读取所有输入或立刻输出需要输出的数据，便将数据放到缓冲区内。缓冲区类的基类为 streambuf ，用于文件的缓冲区为继承自 streambuf 的 filebuf ，用于字符流的缓冲区为 strstreambuf 。

#### 字节流类

&emsp;&emsp;实际上，所有的流都是对字节流的扩展。基本流类 istream 、 ostream 继承自 ios 基类， iostream 继承自istream 和 ostream ，支持对字节数据进行读写操作。

#### 文件流类

&emsp;&emsp;fstreambase 类继承自 ios 基类，提供了文件读写的基础功能，是文件操作流的公共基类。 ifstream （ ofstream ）继承自 istream （ ostream ）和 emsp;fstreambase，fstream 继承自 ifstream 和 ofstream ，可对文件进行读写操作。

#### 字符流

&emsp;&emsp;strstreambase 继承自 ios 基类，提供了对字符流的操作……（同上）

### 格式化输入输出

#### 使用ios成员方法格式化输入/输出

&emsp;&emsp;ios使用格式化标志字_M_flags存储格式化标志，相关格式化标志定义在bits/ios_base.h中，包括：

| 枚举            | 含义                                                       |
| --------------- | ---------------------------------------------------------- |
| ios::left       | 输出数据在本域宽范围内向左对齐                             |
| ios::right      | 输出数据在本域宽范围内向右对齐                             |
| ios::internal   | 数值的符号位在域宽内左对齐，数值右对齐，中间由填充字符填充 |
| ios::dec        | 设置整数的基数为10                                         |
| ios::oct        | 设置整数的基数为8                                          |
| ios::hex        | 设置整数的基数为16                                         |
| ios::showbase   | 强制输出整数的基数(八进制数以0打头，十六进制数以0x打头)    |
| ios::showpoint  | 强制输出浮点数的小点和尾数0                                |
| ios::uppercase  | 在以科学记数法格式E和以十六进制输出字母时以大写表示        |
| ios::showpos    | 对正数显示“+”号                                            |
| ios::scientific | 浮点数以科学记数法格式输出                                 |
| ios::fixed      | 浮点数以定点格式(小数形式)输出                             |
| ios::unitbuf    | 每次输出之后刷新所有的流                                   |
| ios::stdio      | 每次输出之后清除stdout, stderr                             |

&emsp;&emsp;使用这些格式化标志的方法如下：

```c++
fmtflags setf(flags);            // 设置指定的格式化标志flags
fmtflags unsetf(flags);          // 取消指定的格式化标志flags
fmtflags setf(flags,filed);      // 先清除filed、然后设置flags和filed的交集
fmtflags flags(flags);           // 将_M_flags设置为flags
int width(int len);     // 修改_M_width的值，设置改数据字段宽度为len
char fill(char ch);     // 修改_M_fill，设置填充字符为ch
int percision(int num); // 修改_M_percision，修改定点数精度为num
// 以及四者的同名无参方法，用于获取当前值
```

> - 以上方法在`bits/ios_base.h`中定义
> - 格式化标志字使用类似掩码的原理，多个标志使用按位或（“|”）连接，类似的还有接下来的文件打开方式。

#### 使用操纵算子格式化输入输出

&emsp;&emsp;除了使用ios基类提供的方法，还可使用操纵符（操纵算子）进行输入输出的格式化。

- showbase（noshowbase）：显示（不显示）数值的基数前缀
- showpoint（noshowpoint）：显示小数点（存在小数部分时才显示小数点）
- showpos（noshowpos）：在非负数中显示（不显示）+
- skipws（noskips）：输入数据时，跳过（不跳过）空白字符
- uppercase（nouppercase）：十六进制显示为0X（0x），科学计数法显示E(e)
- dec / oct / hex：十进制/八进制/十六进制
- left / right：设置数据输出对齐方式为：左/右 对齐
- fixed：以小数形式显示浮点数
- scientitific：用科学计数法显示浮点数
- flush：刷新输出缓冲区
- ends：插入空白字符，然后刷新ostream缓冲区
- endl：插入换行字符，然后刷新ostream缓冲区
- ws：跳过开始的空白

> 以上操纵算子在`bits/ios_base.h`中定义

- setfill(ch)：设置ch为填充字符
- setprecision(n)：设置精度为n位有效数字
- setw(w)：设置数据的输出宽度为w个字符
- setbase(b)：基数设置为b（b=8，16等）进制

> 以上操纵符函数在`iomanip`中定义

#### 自定义输入输出行为

&emsp;&emsp;可通过类外友元函数重载运算符“<<”和“>>”自定义对某些类的输入输出，如：

```c++
friend ostream& operator <<(ostream& stream, COMPLEX& obj);
```

&emsp;&emsp;还可自定义操作符，如：

```c++
istream& operaSign(istream& stream){
  ….;
  return stream;
}
```

### 标准输入输出

&emsp;&emsp;C++定义了八个用于标准输入输出的对象，即 cin（标准输入）、cout （标准输出）、cerr（标准错误输出）、clog（也属于标准错误输出，但有缓冲区）及其对应的支持宽字符的 wcin 、wcout 、 wcerr 和 wclog 。其中 cin 、 cout 和 clog 均使用了缓冲区，cerr无缓冲区。

### 文件操作

&emsp;&emsp;在C++中使用文件流来操作文件：

```c++
fstream file;
file.open(filename, mode, access);
file.close();
```

#### 文件打开方式

| 文件打开方式   | **说  明**                                                   |
| -------------- | ------------------------------------------------------------ |
| ios::in        | 以输入方式打开文件，即读文件（ifstream类对象默认方式）       |
| ios::out       | 以输出方式打开文件，即写文件（ofstream类对象默认方式）       |
| ios::app       | 以添加方式打开文件，新增加的内容添加在文件尾                 |
| ios::ate       | 以添加方式打开文件，新增加的内容添加在文件尾，但下次添加时则添加在当前位置 |
| ios::trunc     | 如文件存在就打开并清除其内容，如不存在就建立新文件           |
| ios::binary    | 以二进制方式打开文件（默认为文本文件）                       |
| ios::nocreate  | 打开已有文件，若文件不存在，则打开失败                       |
| ios::noreplace | 若打开的文件已经存在，则打开失败                             |

### 文件访问隔离

- filebuf::openport ：共享方式
- filebuf::sh_none：独占方式，不允许共享
- filebuf::sh_read：允许读共享
- filebuf::sh_write：允许写共享

### 文件操作函数

&emsp;&emsp;对于文本文件流，使用`get()`和`put()`读取单个字符；对于二进制文件流，使用`read()`和`write()`读写指定字节长度的数据。

&emsp;&emsp;使用`tellg()`/`tellp()`获取文件流读/写指针位置，使用`seekp()`/`seekg()`修改文件流读写指针的位置。

## 泛型

&emsp;&emsp;有些时候，我们会用相同的方法处理不同类的数据，问了减少代码量，可以使用泛型的思想来忽略具体的数据类型，便捷地进行程序设计。

&emsp;&emsp;在C++中通过模板来实现泛型编程，即通过实例化模板来自动生成符合给定类型的方法或类。模板的开头一般是这样：

```c++
template <typename T>
// 此处typename可替换为class
```

&emsp;&emsp;C++ 的泛型是根据模板在需要的时候创建一个该类型的类/方法，和 Jvav 的类型擦除相比没啥需要注意的，但C++的泛型会导致编译后的代码量增大。

