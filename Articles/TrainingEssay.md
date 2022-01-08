# 科协&机器人培训笔记

这是2021届科技协会培训的笔记&2021计算机4班晚自习培训的笔记，~~禁止外传~~视频都发到B站了，也不用保密了。

## 2021.10.4

**BV1sU4y1w7te**

 第一次培训，没记笔记

## 2021.10.10

### **C语言 #0**

**BV1sU4y1w7te**

（从这里开始，我炸了）

#### 基本格式

0. 头文件
   
   ```c
   #include ""    // 先看程序员目录，后看系统库
   #include <>    // 先看系统库，没有才看程序目录
   ```

1. 花括号{}在定义函数时不可省略

#### 数据类型

0. 为什么分？省空间 运算快  

1. 123，0123，\0123都表示八进制123

2. float 1 8 23位分别表示符号、指数和数据 ，double为1 9 52
+ 参考IEEE-754
+ 由于指数不需要做加减法，指数用~~原码~~-127=00000000,1=10000000（原数加127的无符号类型）表示，线性对应便于比较大小。
+ 拆分规则同科学记数法，但是只表示1到2之间的数。
+ 精确到6到7位。
+ [关于C语言float类型的详细介绍](https://blog.csdn.net/hzw05103020/article/details/50626076?utm_source=app&app_version=4.16.0&code=app_1562916241&uLinkId=usr1mkqgl919blen)

#### 标准输入输出

printf()执行顺序  ***未定义*** （历史原因、编译器自己优化），所以尽量别在printf()里做会改变变量值的计算。

修饰符

+ #用于显示八进制和十六进制前缀
+ +强制显示正数的+
+ -u左对齐（与a.b配合使用）
+ \*.\*把*所在地方的数作为参数使用，使代码更灵活或

puts()默认带\n换行，printf()得自己加

缓冲区：

> 产生原因：CPU太快了，输出（输入）比较慢
> 作用：等等再输出（输入）
> 清空条件：
> 
> 1. \n
> 2. return（函数结束）
> 3. 满了

返回值：

> scanf()返回读取“数据”个数
> printf()返回s输出“数据”的个数

输出一个百分号：%%

取余运算生搬a正负号

#### 按位运算

移位：

```c
a=a>>2;    // 二进制移位，相当于除以2
b=b<<2;    // 二进制移位，相当于除以2
```

## 2021.10.13

### **机器人C语言小讲堂 #0**

[高程的课件链接](https://pan.baidu.com/s/1NqTNDfAJvr3_r8Ndw7Isww)提取码：tjut

#### 敲代码常见错误（欢迎补充）：

0. “==”和“=”的区别

```C
if(a=0){
    function1();
}
```

1. scanf()引号里不要加奇怪的东西，比如

```C
scanf("%d,%d",&num1,&num2);    // 你输入3 5试试？只能读到3（可怜的scanf()疯狂寻找那个逗号，但是找不到）
```

#### 代码规范

0. 不要用奇奇怪怪的n名称和无意义的字母
1. 写注释

```C
// 这是单行注释
/*
这是多行注释
*/
```

2. const（常变量）的变量名用大写字母，比如

```C
const int MAX=114514;
```

3. 驼峰命名法（还有其他命名方法）

```C
int mathScore;            // 小驼峰，处第一个单词外其他单词首字母大写。用于变量名
int GetAddress(int num);    // 大驼峰，所有单词首字母大写。用于函数名等
```

c++和++c：

> 共同点：实现变量自增
> 
> 区别：返回值不同，c++返回自增前的变量，++c返回自增后的变量

逻辑运算中不是0的都是1。

~~左移一位相当于乘2，右移一位相当于除以2。~~这个前面有

#### 数据类型之间的转化

0. 类型不同，取精度高的
   
   > char < short < int < long < long long
   > 
   > float < double <long double
   > 
   > signed < 对应的unsigned
   > 
   > int < double

1. 强制类型转换

```C
int a=5;
int b=2;
double c;
c=(double)a/(double)b;    // 这次可以看到2.5了
```

#### 调试

> 我不想调试 qwq

0. DevC++的调试  新建项目 > 打断点  > 下一步……
1. 我更喜欢加一些printf();

#### 循环

0. 三大循环

```C
for(int i=0;i>=0;i++){
    functions();
}
// i++发生在函数体执行后（想想i的值是什么时候变的）
// 变量i属于此for循环所有，
while(1){
    functions();
}
// 
do{
    functions();
}while(0);
// 这个至少执行一次
```

1. break用于打破循环，continue用于跳过本轮循环

#### 数组

0. 数组标号从0开始

```C
int a[6]={0,1,2,3,4};    // 没有a[6],且a[5]为0
for(int i=0;i<6;i++){    // 这里就用起来比较舒服了
    functionns();
}
```

1. 字符数组与字符串

```C
char str1[7]={'z','z','y','y','y','d','s'};    // 字符数组，长度为7
char str2[]="zzyyyds"                        // 字符串，长度为6，str[7]='\0'（字符串结束标志，可以用来判断字符串是否结束。
```

2. 数组和指针的py交易

```c
char ch;
scanf("%c",&ch);        // 必须有取地址符&
char str[10];
scanf("%s",str);        // 这里可以没有取地址符&
scanf("%c",&str[0]);    // 这里也必须有取地址符&
// &str等于str，等于&str[0],即数组str的第一个元素在内存中的地址。而str[0]则可以看作一个char.所以必须有&。

int score[5];
int *myScore=score;
myScore[1]=100;            // 没错，可以这么玩（在这里好像没看出来有什么用）
```

## 2021.10.14

### **C语言 #1**

**BV1Jb4y1Y7US**

#### 判断与循环

不要这样写

```C
if(10<=x<=20){        // 从左往右判断，相当于(10<=x)<=20
    functions();
}
```

由于编译器的优化，会有奇怪的事情发生。看看这个

```C
int x=10;
if(x<5 && x++){}    // ++不会被执行，因为x<5已经不成立，所以自动忽略后面
// 所以尽量不要把会影响变量值的操作扔到条件表达式里
```

前方高能！！

```C
char N='n'^32    // 大小写转换
char n='N'^32    // 这样也可以
```

EOF的使用

> EOF（End of File），文件结尾标记
> 
> EOF仅存在于**文件**结尾，要想在终端输入EOF，Win用Ctrl+Z，MacOS用Ctrl+W，Linux用Ctrl+D。
> 
> ```C
> int num;
> if(scanf("%d",&num)!=EOF){
>     printf("%d",num);
> }
> ```

#### 排序算法

0. 冒泡排序

> 依次判断当前数和它后面的数的大小关系，不符合要求的进行交换
> 
> ```C
> int Sort(int num, int* Num) {
>     int i,j;
>  int temp;
>     for(i = num - 1; i > 0; i--) {            // 进行(数目-1)!轮
>         for(j = 0; j < i; j++) {
>             if(Num[j]<Num[j+1]) {
>                 temp = Num[j];
>                 Num[j] = Num[j+1];
>                 Num[j+1] = temp;
>             }
>         }
>     }
>  return 0;
> }
> ```

1. 选择排序

> 从数组头开始，选择后面比它大（小）的最大（小）的与之交换
> 
> ```C
> int Sort(int num, int* Num) {
>     int i, j;
>     int mNum, mVue;                //max Value，和Vue没啥关系
>     for(i = 0; i < num - 1; i++){
>         mNum = i;    
>         mVue = Num[i];
>         for(j = i + 1; j < Num; j++) {
>             if(mVue < Num[j]) {
>                 mNum = j;
>                 mVue = Num[j];
>             }
>         }
>         if(mVue > Num[i]) {
>             Num[mNum] = Num[i];
>             Num[i] = mVue;
>         }
>     }
> }
> ```

#### 数组

数组+1的问题

```C
int num[i][j][k]=[0];                // 这里i,j和k是数字
num[2][3][4]+1 == &(num[2][3][5])    // +sizeof(int)
num+1 == &(num[1][1][0])            // +j*j*sizeof(int)
&num+1 ==&(num[i-1][j-1][k-1]+1)    // +i*j*k*sizeof(int)，直接越界
```

数组初始化

```C
int num[24]={0};    // 使用{}进行初始化，{}里不够的用0
memset(num,1,4);    // 注意匹配问题，memset是在内存层面进行初始化，要初始化的内容需要手动指定
```

## 2021.10.17

### **C语言 #2**

**BV1Bf4y1g7Hm**

cjb的另一条路

> 0. 可以用来快速“查询”的东西有：数组
> 1. 变长数组谨慎使用（不能初始化、先后问题……）

函数的作用：减少代码量、方便维护、功能封装

#### 内部函数

> ~~定义在系统库而不是头文件~~我干死你DevC++
> 
> 没有头文件函数照样能用，但是数据类型可能有误

#### 自定义函数

0. 返回值

> return 结束当前函数并向调用函数的地方返回一个值
> 
> 返回值必须与函数原型、函数定义匹配。如果返回值类型为void，需要
> 
> ```C
> return ;
> ```

1. 函数的声明与定义
   
   > 用函数原型声明函数，证明n有这个函数（可以没有形式参数，但要有形参类型）
   > 
   > 函数定义才是函数的内容

#### 函数使用

> 参数一一对应，不多不漏
> 
> 变量作用域和生存期问题（里面定义的变量外面不存在，使用指针访问“不安全”）
> 
> 传入的是值，而不是变量（形参和实参的区别）
> 
> 只读不写传变量，要写变量传指针
> 
> 传数组的方式：
> 
> > 直接传：
> > 
> > ```C
> > int foo(a[]);
> > 
> > int a[10]={0};
> > foo(a);                // a==&a==&a[0]
> > ```
> > 
> > 或者用指针（参考DevC++新建项目时main()函数的参数）
> > 
> > （其实这俩没区别）

#### 全局变量与局部变量

|        | 定义方式            | 作用域  | 生存期  | 内存中的位置 |
|:------:|:---------------:|:----:|:----:|:------:|
| 全局变量   | 定义在函数外          | 整个程序 | 整个程序 | 数据区    |
| 局部变量   | 代码块内            | 代码块  | 代码块  | 栈区     |
| 静态局部变量 | static int a;   | 代码块  | 整个程序 | 数据区    |
| 寄存器变量  | register int a; | 代码块  | 代码块  | CPU寄存器 |

#### 变量存储位置

> 代码区：函数二进制代码
> 
> 数据区：各种常量（代码中提供的字符串属于字符串常量）、静态变量、全局变量
> 
> 堆区（大）：malloc()的内存（动态分配）（记得free()）
> 
> 栈区（小）：数据（int、double等）
> 
> 命令行参数区：shell调用程序时提供的参数

#### 函数递归调用

0. 栈
   
   > 访问原则：先进后出（FILO）
   > 
   > 非常小（win下2MB）
   > 
   > 参数入栈顺序：
   > 
   > > printf()函数参数计算顺序不确定，但参数入栈顺序确定，即从右向左（用于实现可变参数函数）

1. 递归

> 函数调用自己的操作，可以用来解决复杂的问题，但会有很大的时间和空间的开销

2. 尾递归

> 在函数结尾递归，类似循环

## 2021.10.19

### **算法 #0**

**BV1ou411d7Po**

深度学习（Deep Learning）：感知-->推理-->知识-->规划

> 自然语言处理
> 
> 计算机视觉
> 
> 机器学习

#### 算法

> Informally, an algorithm is any well-defined computational procedure that takes some value, or set of values, as input and produces some value, or set of values, as output.

0. 算法的要求

> 正确性
> 
> 复杂度小

1. 评判算法优劣：

> f(n)=O(g(n))：∀n>N₀,s.t. f(x)<=c₁g(x)（上界，最坏复杂度）
> 
> f(n)=Ω(g(n))：∀n>N₀,s.t. f(x)>=c₂g(x)（下界，最优复杂度）
> 
> f(n)=ϴ(g(n))：说明O(g(n))=Ω(g(n))

2. 分治：

> 把复杂的问题拆分成小问题
> 
> 比如Karatsuba Multiplication O(n^log3^)
> 
> > z=x*y;
> > 
> > x=xL\*10^m^+xR; y=yL\*10^m^+yR;
> > 
> > zL=xL\*yL; zM=xL\*yR+xR\*yL; xR\*yR
> > 
> > z=zL\*10^2m^+zM*10^m^+zR;
> > 
> > 升级后 
> > 
> > zM=(xL+xR)*(yL+yR)-zL-zR;
> > 
> > z=zL\*10^2m^+zM*10^m^+zR;

3. 快速幂

> a^n^ = (a^n/2^)^2^ = ...
> 
> 递归求幂，时间复杂度为O(log(n))

4. Fibonacci

## 2021.10.23

### **算法 #1 **

**BV1Wh41187gA**

#### 分治的时间复杂度

> T(n) = a * T(n/b) + C * n^d^ 
> 
> T(1) = C
> 
> a：子问题数
> 
> b：分解一次问题规模减小的比例
> 
> C：常数，
> 
> d：每个节点操作次数

#### 二分查找

> 对于**不严格单调**的待查找数组，先判断两个端点，，依次类推再看中间，找出待查找的项或将查找范围减半，查找范围减半后判断新范围中间元素与待查找值的关系，以次类推……
> 
> 时间复杂度：O(log2(n))
> 
> 实现：
> 
> > ```C
> > /*
> > *摘自洛谷P2249答案，返回单调不减数列中某元素第一次出现的位置（1 == 1序号）
> > */
> > 
> > int Find(int ask, int rP, int* array, int* code) {    // array and code is two array,whose values are the number need to be find and its "first code"
> >     int lP = 0;
> >     int mP;
> >     if(array[lP] == ask) {
> >         return code[lP] + 1;
> >     }
> >     if(array[rP] == ask) {
> >         return code[rP] + 1;
> >     }
> >     do{
> >         if ((rP - lP) < 2) {                    // if we don't fnd the number,return -1
> >             return -1;
> >         }
> >         mP = (lP + rP) / 2;
> >         if (array[mP] == ask) {
> >             return     code[mP] + 1;
> >         }else if (array[mP] > ask) {
> >             rP = mP;
> >         }else if (array[mP] < ask) {
> >             lP = mP;
> >         }
> >     }while(1);
> > }
> > ```

#### 归并排序

> 将待排序的数组不断分组直至每个小组内n元素数量不大于2，对最小小组排序，
> 
> 时间复杂度：O(nlog(n))
> 
> 实现：
> 
> > ```C
> > void Sort(int arr[], int len) {
> >     int* a = arr;
> >     int* b = (int*) malloc(len * sizeof(int));
> >     int seg, start;
> >     for (seg = 1; seg < len; seg += seg) {
> >         for (start = 0; start < len; start += seg + seg) {
> >             int low = start, mid = min(start + seg, len), high = min(start + seg + seg, len);
> >             int k = low;
> >             int start1 = low, end1 = mid;
> >             int start2 = mid, end2 = high;
> >             while (start1 < end1 && start2 < end2)
> >                 b[k++] = a[start1] < a[start2] ? a[start1++] : a[start2++];
> >             while (start1 < end1)
> >                 b[k++] = a[start1++];
> >             while (start2 < end2)
> >                 b[k++] = a[start2++];
> >         }
> >         int* temp = a;
> >         a = b;
> >         b = temp;
> >     }
> >     if (a != arr) {
> >         int i;
> >         for (i = 0; i < len; i++)
> >             b[i] = a[i];
> >         b = a;
> >     }
> >     free(b);
> > }
> > ```

#### 取中位数

> 0. 先排序后取中间的，O(nlog(n))
> 1. 随机取a属于S，遍历S找出比a大的、小的和等于a的，然后砍掉不符合的。O(n)

#### 矩阵快速乘法

> 0. 暴力计算
> 1. 分块矩阵
> 2. 无法描述

## 2021.10.27

### **C语言 #3**

程序间传输数据：网络、访问内存

C程序开始不是main()函数，前面有东西

#### 指针

1. 定义和使用

> &：取地址符，后加变量
> 
> *：解引用符，后加内存地址
> 
> %p，用于打印内存地址的占位符，相当于%#lx（x86_64）或%#x（x86_32，x86）
> 
> 定义指针变量：基类型 *变量名（基类型\* 变量名）
> 
> > \*仅对其后面的一个变量名起作用，比如：
> > 
> > ```C
> > int *a, *b, *c;
> > ```
> > 
> > 基类型影响C语言对该指针的看法（比如+1的问题），不影响*和&（同一程序中指针一边大）

2. 指针和数组的py交易
   
   > （前面好像有了）
   > 
   > ```C
   > char str[114514];
   > // 数组名相当于一个指针，指向数组首元素
   > str == &str[0];        
   > str[1] == *(str + 1);
   > 
   > // 指向数组的指针可以当数组来用
   > char *p = str[1];
   > p[1] == str[2];
   > 
   > // （个人理解）二维数组是一个元素类型为一维数组的一维数组
   > int aa[2][5] = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9,};
   > *(aa + 1) == aa[1][0];
   > ```

3. 指针的运算

> + 两个指针相加没有意义，不予考虑。同样不予考虑的还有指针的乘除法。
> 
> + 指针+1表示“下一个”（内存中相邻）相同类型变量的地址（起始地址），同理-1表示“上一个”
> 
> + 相同类型变量地址的差表示两者之间相差“几个”这种变量，不同类型变量相减会导致error。
> 
> + 指针可以自增、自减
>   
>   ```C
>   int a[10];
>   int *p = a;
>   *(a++) == a[0];
>   *a == a[1];
>   ```
> 
> + 还可以这么玩
>   
>   ```C
>   int a[10];
>   1[a] == a[1];
>   ```
> 
> + 指针的比较
> 
> + 不要用+1来获得字符串长度，你得到的是定义的那个数组长度

4. 常量指针&指针常量
   
   > ```C
   > int a = 1;
   > const int *p1 = &a;            // 常量指针，指向的变量被写const,使*p1不能被修改
   > int const *p1 = &a;            // 也可以这么写
   > int *const p2 = &a;            // 指针常量，一个指针被写const，使p2不变
   > const *int p3 = &a;            // 不能这么写
   > ```

5. 空指针和未知类型指针
   
   > ```C
   > int *p0  = NULL;        // 空指针，用于初始化指针，不要用，一用就炸
   > void *p;                // 基类型未知的指针，常用于接收maloc()返回值
   > ```

#### 堆区

```c
#include <stdlib.h>            // 或malloc.h
void *a;
a = malloc(size);            // 返回一个指向申请到的内存开始处的指针，返回NULL表示堆空间申请失败
free(a);                    // 参数为指向使用的数据开始处的指针（假销毁）
```

## 2021.10.31

### **C语言 #4**

malloc的字符串末尾i手动加0

malloc()返回值是void*，~~辣鸡DevC++的MinGW~~ 部分编译器可能对此较严格

及时free()，防止内存泄露

#### 二维数组指针和数组指针

1. 二维数组指针：

> 指向二维数组的指针
> 
> ```C
> nt arr[2][5] = {1,2,3,4,5,6,7,8,9,10};
> int (*p)[5] = arr;
> printf("p[1][2] = %d", p[1][2]);
> ```

2. 数组指针

> 指向数组的指针
> 
> ```C
> int arr[2][5] = {1,2,3,4,5,6,7,8,9,10};
> ```

#### 结构体

```C
struct student {                // 定义结构体变量类型
    char name[20];                // 该结构体类型所含变量
    __bool sex;
    int code;
    struct {                    // 结构体里的结构体
        int 高数;
        int 大英;
        int C;
    }score;
} cjb, chs;                        // 顺手定义两个结构体变量
cjb.name = "陈佳博";
cjb.sex = 1;
chs.name = "陈泓盛";
chs.sex = 1;
struct student zsz;                // 补充张思雨学姐（bushi）
zsy.name = "张思雨";
zsy.sex = 2;
```

> 定义数据类型不消耗内存，定义此类型的变量才消耗内存。
> 
> 结构体的大小等于其所含变量大小之和~~（废话）~~，但可能被自愿调整。

结构体指针：

```C
struct student* master = &chs;
master->sex = 2;                        // （bushi）
```

 结构体传参时缩减信息

> 用指针

> 浅拷贝：
> 
> 深拷贝：

## 2021.11.5

### **算法 #2**

#### 基本数据结构

0. 数据结构：

> 可以进行增、删、改、查

1. 线性表

> 实现方式：
> 
> > 0. 一维数组：数据类型单一，增删时间复杂度大，改查方便快捷。
> > 
> > 1. 链表：含有指向下一个结构体的指针的结构体
> > 
> > > 灵活，增删方便，改查要遍历
> > > 
> > > 逻辑连续，物理空间不连续
> > > 
> > > ```C
> > > struct myLink{
> > >     int data;
> > >     int next = NULL;
> > > };
> > > typedef MyLk struct myLink;
> > > MyLk *now = NULL;
> > > MyLk *next = &d1;
> > > MyLk *d1 = malloc(sizeof(MyLk));
> > > MyLk *d2 = malloc(sizeof(MyLk));
> > > MyLk *d3 = malloc(sizeof(MyLk));
> > > MyLk *d4 = malloc(sizeof(MyLk));
> > > MyLk *d5 = malloc(sizeof(MyLk));
> > > d1.next = &d2;
> > > d2.next = &d3;
> > > d3.next = &d4;
> > > d4.next = &d5;
> > > MyLk *d6 = malloc(sizeof(MyLk));
> > > //
> > > d2.next = &d6;
> > > d6.next = &d3;            // 增
> > > //
> > > d3.next = &d5;
> > > d5.next = NULL;
> > > free(d5);                // 删
> > > ```
> > > 
> > > 双向链表：含有两个h指针，n分别指向它的上一个和下一个结构体
> > > 
> > > 注意：链表中的指针不能++或--

2. 栈

> 类似内存中的栈区，先进后出

3. 队列

> 先进先出，只允许尾部添加、头部删除。

4. 树

> 图的特殊情况，各节点具有从属关系
> 
> 没有父节点的节点叫根节点
> 
> 二叉树：
> 
> > 每个节点最多有两个子节点
> > 
> > 满二叉树：最下一层无子节点，其余各节点都有两个子节点
> 
> 完全二叉树：从下往上缺我们少部分节点的满二叉树
> 
> > 将n叉树转换为二叉树：左子节点，右兄弟节点

## 2021.11.21

### **前端通识**

C：编译型语言，JavaScript：解释型语言

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Login</title>
  </head>
  <style>
    html {
      height: 100%;
    }
    body {
      margin: 0;
      padding: 0;
      color: #fff;
      background: linear-gradient(#141e30, #243b55);
    }
    .login-box {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      width: 400px;
      padding: 40px;
      background-color: rgb(0, 0, 0, 0.5);
      box-shadow: 0 15px 25px rgb(0, 0, 0, 0.6);
      box-sizing: border-box;
    }
    h2 {
      text-align: center;
      margin-bottom: 30px;
    }
    .user-box {
      position: relative;
    }
    input {
      width: 100%;
      padding: 10px;
      font-size: 16px;
      color: #fff;
      outline: none;
      border: none;
      border-bottom: 1px solid #fff;
      background: transparent;
      margin-bottom: 30px;
    }
    label {
      position: absolute;
      top: 0;
      left: 0;
      font-size: 16px;
      color: #fff;
      pointer-events: none;
      transition: 0.5s;
    }
    .login-box .user-box input:focus ~ label,
    .login-box .user-box input:valid ~ label {
      top: -20px;
      left: 0;
      color: #03e9f4;
      font-size: 12px;
    }
    a {
      position: absolute;
      bottom: 5px;
      right: 10px;
      font-size: 16px;
      text-decoration: none;
      color: aqua;
      border: 1px solid aqua;
      padding: 5px 10px;
    }
  </style>

  <body>
    <div class="login-box">
      <form action="">
        <h2>Login</h2>
        <div class="user-box">
          <input type="text" name="" required />
          <label>Username</label>
        </div>
        <div class="user-box">
          <input type="password" name="" required />
          <label>Password</label>
        </div>
        <a href="#">SUBMIT </a>
      </form>
    </div>
  </body>
</html>
```

## 2021.11.23

### **机器人C语言小讲堂 #2**

```C
// 辗转相除法求最大公约数
int gcd(int m, int n) {
    return m == 0 ? n : gcd(n % m, m);
}
// 小学体育（bushi）老师说过，m * n == gcd(m,n)*lcd(m,n)
```

危险的gets：

> gets不检查字符串长度
> 
> 需要getchar()吃字符'\n'，同理，scan("%[\^\n]")也需要getchar()

## 2021.11.25

### **安卓通识**

08谷歌一顿炸，安卓干翻诺基亚

root

冰箱（冻结后台），需要root

消息推送

安卓：Java（虚拟机，效率低）、lua，iOS：Swift、ObjectC

###### 触摸事件监听：for(;;){}

//////////////////////////////////////////////////////////////////////////////////////

虚拟定位：截取GetLocation()、模拟器、虚拟容器

自动抢红包：~~守护进程~~这里用安卓的服务

10.13：拼手速

## 2021.11.28

### **iOS通识**

~~我也不知道讲了些啥~~

## 2021.11.30

### **机器人C语言小讲堂 #3**

~~和机器人对线真好玩~~

## 2021.12.1

### **后端通识**

除了前端都是后端，除了后端都是前端

#### 访问网络资源

> URL：协议://host/location
>
> DNS解析：域名与IP的相互转换（i浏览器缓存->hosts->DNS->上一层DNS->……->根域名服务器（13个））
>
> 连接：
>
> > TCP：
> >
> > UDP：
>
> （负载均衡，Nginx，导致IP不一定固定）

#### 前后端交流

> 接口

#### 请求&响应

> 请求方法：GET（只读）、POST（修改服务器数据）
>
> http版本：1.0（一次i请求一个资源）、1.1（一次请求多个资源）
>
> 请求体：POST用json
>
> 响应状态码：
>
> > 200：一切正常
> >
> > 3xx：跳转
> >
> > 4xx：前端的锅
> >
> > 5xx：后端的锅

#### 后端学什么

- 一门适合开发的语言：Java、Go
- 一种数据库：MySQL
- 连接语言与数据库（中间件）
- 服务器：Linux、Docker、K8s
- 没有框架、换一种语言，你还剩啥？？？

/////////////////////////////////////////////////////////////////////////////////////

预约洗澡：无时效、无签名

## 2021.12.8

### **安全通识**

TMRC （最早的hacker）「Positive！！」

> cool hack
>
> ugly hack

hacker & cracker

CTF分类：

> web、reverse、pwn、crypto、misc

机器码：CPU执行的代码

Java代码编译得到的东东叫“字节码”，由JVMi执行；

#### （二进制）安全学什么

> 汇编语言
>
> 调试器
>
> 常见加密算法
>
> 操作系统
>
> 编译原理
>
> 逆向

## 2021.12.15

### **游戏通识**

#### 游戏分类：

> ACT：动作类游戏
>
> RPG：角色扮演游戏
>
> FTG：格斗类
>
> AVG/ADV：冒险类游戏
>
> RTS：即时战略游戏
>
> STG：射击类游戏
>
> RAC/RCG：赛跑类
>
> FZG：益智类游戏
>
> SLG：模拟经营类游戏
>
> 视觉游戏
>
> 沙盒类游戏
>
> MMO
>
> MOBA
>
> AR/VR
>
> ……

#### 游戏设置：

> 垂直同步：FPS>刷新率时减慢GPU帧交换。
>
> 三缓：一定程度上缓解撕裂。

#### 线代

> 矩阵：一种变换
>
> 秩：维度
>
> 特征向量：变换过程中方向不变
>
> 特征值：大小的变化比例
>
> 行列式：面积、体积

## 2022.1.4

### **安全组培训 #0**

