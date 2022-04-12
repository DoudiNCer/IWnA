# 后端组Java培训&自学笔记

## Week 0x00

## 2022.1.5

### 第0x00次培训

面向过程：

> 过程：没有返回值的函数
>
> 函数：有返回值的函数

面性对象：

> 面向函数，去除“过程”

#### OOP专有名词

> **封装**：将类的字段私有化（private），只有调用类的方法才能操作这些字段
>
> > ~~因此类至少要有一个public的方法~~
>
> **继承**：子类继承（extends）父类的字段和方法
>
> > Java不允许多继承，但允许多重继承
> >
> > 所有除java.lang.Object的类都直接或间接继承于java.lang.Object
>
> **接口**：具有继承关系的类可以实现（implements）某个接口（interface）的方法
>
> > 接口是完全抽象（abstruct）的类（class）
>
> **多态**：不同子类*重写*了父类的某个方法
>
> > 多态存在的三个条件：继承、重写和父类引用指向子类对象

## 2022.1.8

### 随手一记

#### *HashMap的线程不安全*

> &emsp;&emsp; 先看下面的代码：
>
> ```java
> import java.util.HashMap;
> 
> public class Main {
>        public static void main(String[] args) {
>            HashMap<Integer, String> hashMap = new HashMap<>();
>            hashMap.put(0, "One");
>            hashMap.put(1, "Two");
>            hashMap.put(2, "Three");
>            for (Integer i : hashMap.keySet()) {
>                if ((i & 2) == 0)       // Java不支持int和boolean的直接相互转化
>                    hashMap.remove(i);
>            }
>            System.out.println(hashMap.values());
>        }
> }
> ```
>
> &emsp;&emsp;会报错“java.util.ConcurrentModificationException”，因为HashMap的实现**不是同步的**，当循环依赖HashMap进行时，无法增/删HashMap的内容。
>
> &emsp; &emsp;为了实现删除指定键值对，我们可以开一个ArrayList保存要删除键值对的key，遍历完成后再进行删除操作/
>
> &emsp;&emsp;这让我想起来了Linux Shell中命令历史记录的保存，bash会在开启时加载history，关闭时写入history；zsh会在开启时加载history，命令执行时立即写入history。

### 第0x01次培训

#### 修饰符

> **public**：对所有类可见
>
> **private**：同一类内可见
>
> **protected**：对包内同类和所有子类可见
>
> **default**(缺省)：包内可见
>
> |   修饰符    | 当前类 | 同一包内 | 子孙类(同一包) |                        子孙类(不同包)                        | 其他包 |
> | :---------: | :----: | :------: | :------------: | :----------------------------------------------------------: | :----: |
> |  `public`   |   Y    |    Y     |       Y        |                              Y                               |   Y    |
> | `protected` |   Y    |    Y     |       Y        | Y/N（[说明](https://www.runoob.com/java/java-modifier-types.html#protected-desc)） |   N    |
> |  `default`  |   Y    |    Y     |       Y        |                              N                               |   N    |
> |  `private`  |   Y    |    N     |       N        |                              N                               |   N    |

> **static**：属于类，同类所有对象共用
>
> > 可以在没对象的时候使用static方法
>
> **final**：类似const（const是个以后可能会被用到的保留字）
>
> > 修饰属性时禁止修改，修饰方法时禁止重写，修饰类时禁止继承

#### 方法重载

> 同类中名称相同、参数列表不同的方法会根据所给参数重载合适的方法
>
> 形参不同不算，**返回值类型不同不算**
>
> Java5开始的不定数量方法组成的重载：
>
> ```java
> public void show(String ...str){}   // 新写法
> public void show(String[] str){}    // 旧写法
> ```
>

#### 对象的生成与销毁

> 在C++中，生成对象时调用构造函数，销毁对象时调用析构函数
>
> Java生成对象使用构造函数，未编写构造函数时使用系统自动生成的无参构造函数。
>
> 调用构造函数时会先执行构造函数上方的初始化代码块
>
> Java不需要单独编写析构函数，即使写了void finalize()也**不一定**会被执行

#### 堆栈内存分析

> 先整一个对象出来：
>
> ```java
> Girl myGirlfriend = new Girl();
> ```
>
> 我们在栈区储存了myGirlfriend，这是一个~~指针~~引用，~~这不就是个指针吗~~这是引用，指向堆区的一块内存，这里储存了一个Girl类型的对象。
>
> 判断对象是否想等的方法equals()比较的是引用对象的地址是否想等，因此初始化两个一模一样的对象也是不相等的。

#### Java垃圾回收

> Java不需要程序猿手动销毁对象，而是使用JVM的垃圾回收（GC）机制。
>
> **引用计数器**：创建对象时为对象绑定一个计数器并初始化为1，每增加一个对该对象的引用计数器自增，减少引用时计数器自减，当计数器值为0时该i对象**可以**被回收，不是立刻被回收。
>
> **可达性分析**：当对象之间存在相互引用时计数器始终不能变为0，此时就需要可达性分析法：
>
> 从栈区寻找指向对象的引用，当某对象不可达时其可以被回收。
>
> 参考：https://www.cnblogs.com/xiaoxi/p/6486852.html

#### 包装类

> 基本数据类型如 int, double, char 等不是对象，这不符合Java“一切皆对象”的哲学思想。
>
> 然后就有了 Integer, Double, Character 等包装类。
>
> 虽然包装类可以像基本数据类型一样直接赋值，但某些时候就不灵了，比如判断两个Integer是否想等在数比较大的时候就不灵了。

#### 容器

> **ArrayList**：升级版数组，可以一直往里面放东西
>
> **HashMap**：哈希表、散列表，由key-value对组成
>
> **Set**：集合，其所含元素具有无序性和唯一性。

#### 单例模式

> 单例模式就是只允许存在一个该类的实例
>
> 实现单例模式的一般思路为私有化构造函数并只允许通过类的static方法获得唯一的实例。
>
> 饿汉式单例模式的实现：
>
> ```java
> public class Singleton {  
>      private static Singleton instance = new Singleton();  
>      private Singleton (){}  
>      public static Singleton getInstance() {  
>          return instance;  
>      }
> }
> // 优点：代码简单，线程安全
> // 缺点：可能产生垃圾类
> ```
>

## Week 0x01

## 2022.1.11

md，居家隔离14天

### 随手一记

#### *关于继承和多态的补充*

> （狭义）多态是运行时行为，重写的方法被调用时“编译看左边，运行看右边”。
>
> 广义上的多态还包括静态多态，如方法重载，属于编译时行为
>
> 父类private的属性和方法不会被继承，可被继承的方法使用父类private属性不受影响。
>
> **虚拟方法调用**：子类对象可通过super指代其父类的一个“虚拟对象”，调用父类方法
>
> > 父类的构造函数为super();，服从父类构造函数的重载。
>
> **向上造型与向下转型**：
>
> > **向上造型**：对父类的引用指向子类
> >
> > > 子类特有属性和方法不可见（但在堆中存在）
> > >
> > > 子类重写的父类方法仍然存在
> >
> > **向下转型**：对子类的引用指向父类变量
> >
> > > 需要强制类型转换，因此存在风险（报错java.lang.classCastException），需要instanceof判断：
> > >
> > > ```java
> > > if (b instanceof A){
> > >        A a = (A)b;
> > > }
> > > ```
> > >

## 2022.1.16

### 随手一记

#### 线程

> **匿名内部类**：这玩意儿在创建进程的时候很有用
>
> ```java
> new Thread(){
>      @Override
>     public void run(){
>          ...
>      }
> }.start();
> ```
>
> **创建线程的方法**：
>
> > 0. extend Thread
> > 1. implements Runnable 并用Thread的含参构造器构造（一个对象初始化多个线程时天然共享对象属性）
> > 1. implements Callable 并用Thread的含参构造器构造以启动线程、用FutureTask的含参构造器构造（用get()获得返回值、捕获异常）
> > 1. 线程池
>
> **线程同步的方法**：
>
> > 0. 同步代码块synchronized(){}（手动指定同步监视器）
> > 1. 同步方法synchronized（使用this或当前类本身（静态方法）作为同步监视器）
> > 1. 使用ReentrantLock的lock()和unlock()手动上锁、解锁（更加灵活、JVM资源消耗少）
>
> **线程间通信**：
>
> > &emsp;&emsp;由java.lang.Object提供（因此任何对象都可以充当同步监视器），但必须在synchronized代码块或synchronized方法中使用，且调用者为同步监视器
> >
> > wait()：使当前线程阻塞并释放同步锁
> >
> > notify()：释放一个wait()的线程（不释放锁）
> >
> > notifyAll()：释放所有wait()的线程

### 第0x02次培训

#### 接口

> 一种“完全”的抽象类，字段默认强制public static final，可以有public或private方法（java9开始）Java8开始可以有默认方法（default）并可被实现

接口与抽象类的区别与联系：

|          |       抽象类       |                             接口                             |
| :------: | :----------------: | :----------------------------------------------------------: |
| 构造函数 |         有         |                             没有                             |
|   字段   |       无限制       |                默认且强制public static final                 |
|   方法   |   可以没抽象方法   | 必须为公共抽象方法（8开始可实现默认方法、静态方法，9开始可以有私有方法）（暂时没有protected方法） |
|   子类   | 符合Java单继承规则 |    一个类可实现多个方法（默认方法可能出现“菱形继承”问题）    |

#### 关于多继承的讨论

> &emsp;&emsp;Java的继承符合“is a”的理念，因此不允许多继承。且多继承存在“菱形继承”问题：B和C继承A，D继承B和C,B和C分别重写了A的某方法。
>
> &emsp;&emsp; Java允许一个类实现多个接口，由于接口的方法都是抽象方法，不存在两个接口“同名方法实现不同”的问题。

## Week 0x02

## 2022.1.20

### 随手一记

#### Java中的各种“集合”

|               |   底层实现    |  无序性&唯一性  | 随机访问 | Synchronized |  implement   |
| :-----------: | :-----------: | :-------------: | :------: | :----------: | :----------: |
|   ArrayList   |     Array     |      false      |   true   |    false     |     List     |
|  LinkedList   |  LinkedList   |      false      |  false   |    false     |     List     |
|    Vector     |     Array     |      false      |   true   |     true     |     List     |
|    HashSet    |  "hash"Array  |      true       |   true   |     true     |     Set      |
| LinkedHashSet |    HashSet    |      true       |   true   |     true     |     Set      |
|    TreeSet    |    TreeMap    |      true?      |   true   |    false     |     Set      |
|   Hashtable   |               |      true       |   true   |     true     |     Map      |
|    HashMap    |               |      true       |   true   |    false     |     Map      |
| LinkedHashMap |    HashMap    |      true       |   true   |    false     |     Map      |
|    TreeMap    | Red-BlackTree | true?（可重复） |   true   |    false     | NavigableMap |

## 2022.1.22

### 随手一记

#### Lambda表达式

Lambda表达式，或者叫闭包，是Java*开始的一种更简洁地定义方法的形式。

Lambda表达式的基本形式如下：

```java
(参数列表) -> {代码块}
```

为了做到更加简单，Lambda可以省略某些内容，包括：

> - 参数列表和代码块只有一项时的括号（空参需要保留小括号）
> - 可以推导出的参数数据类型
> - 代码块仅有一个表达式时的返回语句和分号

当 Lambda 表达式中的代码块**仅调用一个方法并且参数全部传递到该方法中**时可用方法引用代替，如

```java
Func func1 = System.out::println;    // 没有括号诶！
```

引用构造器：

```java
Func func1 = MyClass::new;
```

有且仅有一个抽象方法的接口就叫**函数式接口**

## Week 0x03

## 2022.1.25

### 泛型

> &emsp;&emsp;从Java 5开始，程序猿可以使用泛型更灵活地实现不同类型之间的兼容问题，尽量避免了直接使用Object带来的不安全问题。

#### 泛型的使用

Java中的泛型写在<>里，多个泛型变量用“,”隔开，用extends限制泛型且限定条件用&连接，采取“唯一类在前，接口在后”的顺序，如：

```java
<T extends Thread & Runnable>
```

Java 中泛型标记符：

- **E** - Element (在集合中使用，因为集合中存放的是元素)
- **T** - Type（Java 类）
- **K** - Key（键）
- **V** - Value（值）
- **N** - Number（数值类型）
- **?** - 表示不确定的 java 类型（通配符类型）

#### 通配符类型

> &emsp;&emsp;由于泛型在某些时候过于严格，Java开发人员设计了一种灵活且安全的方式——通配符来解决这些问题。

&emsp;&emsp;在Java中，使用“?”指代任何类型，通配符也可以用extends来加以限定或使用super加以限定。

&emsp;&emsp;由于无法使用类型变量，通配符类型的使用会受到部分限制，比如extends修饰的通配符类型才能被赋值，使用super修饰的通配符类型才能被作为参数传入.

#### JVM对泛型的实现原理——类型擦除

> 虚拟机没有泛型类型对象——所有对象都属于普通类。

&emsp;&emsp;编译器会对泛型类型进行类型擦除并在必要时添加强制类型转换（这不又回去了吗？）。有限定类型的会被擦除，仅保留限定类型；没有限定类型的会被擦除，保留Object。

&emsp;&emsp;由于发生了类型擦除，编译器会为泛型类自动添加桥方法。

#### 泛型的限制与局限性

&emsp;&emsp;受制于泛型的实现原理，Java使用泛型具有以下的限制：

> - 不能用基本数据类型实例化泛型参数（自动装箱"解决"了这一问题）
> - 程序运行时的类型查询只适用于原始类型
> - 不能创建参数化类型的数组
> - Varargs 警告
> - 不能实例化类型变量
> - 不能构造泛型数组
> - 泛型类的静态上下文中类型变量无效
> - 不能抛出或捕获泛型类的实例
> - 可以取消对检查型异常的检查
> - 泛型类型不存在继承关系

## 2022.2.6

### 反射

> &emsp;&emsp;反射（Reflection）机制允许程序在执行期间取得任何类的内部信息，并能直接操作任何类的内部属性和方法。Java通过反射机制产生了一定的动态性，因此属于“准动态语言”。

#### Class类

> 万物皆对象，类也如此。Class<>便是一个用来表示类型的泛型类，

**具有Class的“类”**

> - class
> - interface
> - array（忽略长度）
> - enum
> - annotation
> - 基本数据类型
> - void

**获取Class<>的实例**

> 0. 直接实例化
>
> ```java
> Class<MyClass> clazz = MyClass.class;
> ```
>
> 1. 使用对象的getClass()方法
> 2. 使用Class的静态方法forname()
> 3. 使用类加载器Classloader

#### 类的加载过程

> - **加载**：将class字节码加载到内存，将静态变量和常量等转换为方法去中的运行时数据，初始化一个Class的实例。此过程有类加载器参与。
>
> - **链接**：
>
>   将Java类的二进制代码合并到JVM的运行状态之中。
>
>   > 0. **验证**：确保加载的类信息符合JVM规范，没有安全方面的问题。
>   > 1. **准备**：正式为类变量(static变量)分配内存并设置类变量初始值的阶段，这些内存都将在方法区中进行分配。注意此时的设置初始值为默认值，具体赋值在初始化阶段完成。
>   > 2. **解析**：虚拟机常量池内的符号引用替换为直接引用（地址引用）。
>
> - **初始化**：
>
>   初始化阶段是执行类构造器<clinit>()方法的过程。类构造器<clinit>()方法是由编译器自动收集类中的所有类变量的**赋值**动作和**静态语句块(static块)**中的语句合并产生的。
>   - 当初始化一个类的时候，如果发现其父类还没有进行过初始化、则需要先初始化其父类。
>   - 虚拟机会保证一个类的<clinit>()方法在多线程环境中被正确加锁和同步。

#### 创建运行时类对象

```java
clazz.newInstance()；    // 调用空参构造器
```

```java
Constructor<T> constructor = clazz.getDeclaredConstructor();    // 获取含参构造器
constructor.setAccessible(true);
T t = constructor.newInstance();
```

#### 获取运行时类的属性、方法

##### 获取运行时类的属性

```java
// 获取当前运行的类及其超类的public属性
Fieldd[] fields = clazz.getFields();
// 获取当前运行的类的所有属性
Fieldd[] declaredFields = clazz.getDeclaredFields();
// 获取指定的属性
Field field = clazz.getDeclared(fieldName);
```

**获取属性的修饰符**

```java
int modififier = field.getModifiers();
// 参考java.lang.reflect.Modifier
```

**获取属性的类型**

```java
Class type = field.getType();
```

**获取属性的变量名**

```java
String name = field.getName();
```

##### 获取运行时类的方法

```java
Method[] methods = clazz.getMethods();
Method[] declaredMethods = clazz.getDeclaredMethods();
Method declaredMethod = clazz.getDeclaredMethod(methodName, parameterTypes[]);    // 没有参数可以不写
```

~~然后慢慢get吧~~

#### 调用运行时类的指定结构

##### 调用属性

```java
field.setAccessible(true);    // 启用访问非public属性
field.get(obj);    
field.set(obj,value);
```

##### 调用方法

```java
method.setAccessible(true);          // 启用访问非public方法
method.invoke(obj, parameters[]);    // 没有参数可以不写
```

## 以下为Java Web内容

## File&IO流

&emsp;&emsp;Java通过File对象来操作文件或文件夹

### 初始化File对象

```java
String path = new String();            // 可使用java.io.File.separator代替分隔符
File file = new File(path);
File dir = new File(paraDir, dirName);
File file = new File(dir,filename);
```

### File的常用方法

```java
String path = file.getPath();
String absPath = file.getAbsolutePath();
Long length = file.length();
Long sec = file.getLastModified();
String[] name = dir.list();
File[] files = dir.listFiles();    
file.renameTo(new File(newName));                // 重命名
file.createNewFile();                            // 新建
dir.makedir();                                   // 创建文件夹
dir.makedirs();                                  // 递归创建文件夹
file.delete();                                   // 删除
```

### IO流基类

&emsp;&emsp;Java中的所有流都继承自四个基本的抽象类，即：

|      |    输入     |     输出     |
| :--: | :---------: | :----------: |
| 字节 | InputStream | OutputStream |
| 字符 |   Reader    |    Writer    |

### 文本文件内容的读写

&emsp;&emsp;通过文件字符流可直接对文本文件进行操作：

```java
File file = new File(path);                          // 初始化文件
FileReader fr = new FileReader(file);                // 初始化文件输入字符流
fr.read();                                           // 迭代读取文件单个字符（以EOL表示文件结尾）
chrr[] cbuf = new char[len];
fr.read(cbuf);                                       // 将读取内容写入字符数组
fr.close();                                          // 关闭文件流（物理连接无法被GC）
FileWriter fw = new FileWriter(file, true/false);    // 初始化文件写入字符流（指定是否为追加模式）
fw.write(str);                                       // 写入（文件不存在会自动创建）
fw.close();
```

> 注意：读写二进制文件需使用文件字节流

### 缓冲流

&emsp;&emsp;可以使用缓冲流对其他流进行“包装”以实现加速读写等功能。

```java
FileReader fr = new FileReader(file);
BufferedReader bfr = new BufferedReader(fr);
bfr.read();
bfr.readLine();                                         // 不含换行符，以null表示文件结尾
bfr.close();

FileWriter fw = new FileWriter(file,true);
BufferedWriter bfw = new BufferedWriter(fw);
bfw.write(str);
bfw.newLine();                                          // 插入换行符
bfw.flush();                                            // 刷新缓冲区（强制写入）
bfw.close();                                            // 关闭由外向内（自动关闭呢）
```

### 转换流

&emsp;&emsp;转换流用于将输入/输出字节流转换成字符流，包括InputStreamReader和OutputStreamWriter。

> 注意，转换流属于字符流

```java
FileInputStream frs = new FileInputStream(file);
InputStreamReader fir = new InputStreamReader(frs, StandardCharsets.UTF_8);    // 指定彼岸
fir.read(cbuf);
fir.close();
FileOutputStream fws = new FileOutputStream(file,false);
OutputStreamWriter fow = new OutputStreamWriter(fws, "GBK");
fow.write(cbuf);
fow.close();
```

### 标准输入输出

&emsp;&emsp;Java中的标准输入输出流有三个：

- System.in：标准输入
- Syatem.out：标准输出
- System.err：标准错误

> - 默认的标准输入输出为字节流
> - 如有需要，可通过System.setIn()，System.setOut()，System.setErr()修改标注输入输出。

### 对象流

&emsp;&emsp;对象流是用于处理基本数据类型数据和对象的处理流，OnjectOutputStream和ObjectInputstream通过**序列化**和**反序列化**将Java对象转换为与平台无关的二进制代码进行传输或将这些二进制代码转换为内存中的Jaa对象。

```java
// 反序列化
ObjectInputStream ois = new ObjectInputStream(inputStream);
ois.readObject();                                                   // 需要强制类型转换
ois.close();
// 序列化
ObjectOutputStream oos = new ObjectOutputStream(outputStream);
oos.writeObject(obj);
oos.flush();                                                        // 需要手动flush
oos.close();
```

#### 序列化自定义类

```java
class myClass implements Serializable{                       // 实现Serializable接口
    private static final long serialVersionUID = 42L;        // 提供序列化版本号
}
```

> 对象可序列化的条件：
>
> - 实现Serializable接口
> - 具有静态serialVersionUID字段
> - 不能为static或transient的对象（对象中存在的此类字段会被忽略）

### 其他的流

- 打印流：PrintStream和PrintWriter，提供高级的print功能
- 数据流：DataInputStream和DataOutputstream，传输基本数据类型

### 随机访问文件

&emsp;&emsp;可通过RandomAccessFile对文件进行随机访问。

```java
RandomAccessFile raf = new RandomAccessFile(file,mode);        // mode表示访问模式
// r：只读
// rw：读写
// rwd：读写，同步文件内容
// rws：读写，同步文件内容与元数据
raf.seek(position);                                            // 修改访问指针位置
raf.read();
raf.write(bytes);
raf.close();
```

> 注意：RandomAccessFile并未继承四种IO流基类

## Java网络通信

> 关于网络的基础知识，请参阅[Web学习笔记](WebLearningEssay.md)。

### 地址

&emsp;&emsp;可以通过InetAddress操作IP地址域名

```java
InetAddress addr = InetAddress.getByName(hostname);
InetAddress.getLocalHost();
addr.getHostAddress();
addr.getHostName();
```

### Socket

&emsp;&emsp;Socket（套接字）是程序间通信的通道，Java提供了Socket类用于网络通信。

&emsp;&emsp;TCP Socket的一个例子如下：

```java
// Client
Socket s2server = new Socket(serverAddr, port);
OutputStream os = s2server.getOutputStream();
os.write(obj.getBytes());
os.close();
s2server.close();

// Server
ServerSocket monitor = new ServerSocket(port);
Socket s2client = monitor.accept();
InputStream is = s2client.getInputStream();
ByteArrayOutputStream baos = new ByteArrayOutputStream();
int len;
byte[] buffer = new byte[1024];
while ((len = is.read(buffer)) != -1 ){
    baos.write(buffer, 0, len);
}
    // Close
baos.close();
is.close();
s2client.close();
monitor.close();
```

> UDP Socket使用DatagramSocket

### URL

&emsp;&emsp;Java提供的URL类可以简单的实现对URL的访问。

```java
URL url = new URL(urlstr);
HttpURLConnection urlConnection = (HttpURLConnection) url.openConnection();    // 获取URL连接
urlConnection.connect();
InputStream is = urlConnection.getInputStream();
is.read();
is.close();
urlConnection.disconnect();
```

## 注解

> &emsp;&emsp;注解（Annotation）是代码里的特殊标记，这些标记可以在编译、类加载、运行时被读取，并进行相应的处理。
>
> &emsp;&emsp;注解可以像修饰符一样用来修饰**包**、**类**、**构造器**、**方法**、**成员变量**、**参数**、**局部变量的声明**，这些信息被保存在annotation的键值对中。
>
> &emsp;&emsp;框架 == 注解 + 反射 + 设计模式; // true
>
> &emsp;&emsp;注解的本质就是一个 继承 自 Annotation 接口的接口。

### 生成文档注解

```java
@author         // 作者
@version        // 版本
@see            // 参考
@since          // 首次出现的版本
@param          // 参数说明
@return         // 返回值说明
@exception      // 异常说明
```

### 编译检查注解

```java
@Override           // 重写超类方法
@Deprecated         // 已过时
@SuppressWarning    // 抑制编译期警告
@SafeVarargs        // 忽略任何使用参数为泛型变量的方法或构造函数调用产生的警告。（J7）
```

### 自定义注解

```java
public @interface MyAnnotation {
}
```

### 元注解

&emsp;&emsp;元注解是用来修饰注解的注解，Java提供的元注解包括以下四个：

```java
@Retention               // 修饰注解生命期
RetentionPolicy.SOURCE   // 源代码保留
RetentionPolicy.CLASS    // class保留（默认）
RetentionPolicy.RUNTIME  // 运行时保留

@Target                  // 限定修饰对象
CONTRACTOR              // 构造器
FIELD                   // 属性
LOCAL_VARIABLE          // 变量
METHOD                  // 方法
PACKAGE                 // 包
PARAMETER               // 参数
TYPE                    // 类、接口、enum
TYPE_PARAMETER          // 泛型类型的定义（J8）
TYPE_USE                // 任何使用泛型类型的地方（J8）

@Documented              // 该注解将被javadoc等工具读取（还要求RetentionPolicy.RUNTIME）
@Inherited               // 该注解将被继承
```

### 	可重复注解

&emsp;&emsp;被@Repeatable修饰的注解可重复使用，定义方式如下：

```java
@Repeatable(MyAnnotations.class)
public @interface MyAnnotation {
    String[] value();
}

public @interface MyAnnotations {
    NoMonroe[] value();
}
```

## JDBC

> 关于MySQL和SQQL语法，请参阅[MySQL学习笔记](MySQLLearningEssay.md)。

&emsp;&emsp;JDBC~~（Java Database Connectivity）~~是一个独立于特定数据库管理系统，通用的数据库存取操作的应用编程接口。

### 建立连接

&emsp;&emsp;最简单的建立与MySQL数据库连接的方法如下：

```java
Driver driver = new com.mysql.cj.jdbc.Driver();
Properties info = new Properties();
info.setProperty("user", userName);
info.setProperty("password", password);
Connection conn = driver.connect(url, info);
```

&emsp;&emsp;为了提高程序可移植性，可使用反射创建驱动对象。

&emsp;&emsp;使用驱动管理器可以简化部分操作：

```java
DriverManager.registerDriver(driver);                        // 可省略，会随Driver加载自动完成
Connection conn = DriverManager.getConnection(url, info);
```

&emsp;&emsp;为实现解耦，通过配置文件获得连接信息，最终代码为：

```java
// 读取配置文件
InputStream is = Main.class.getResourceAsStream("jdbc.cfg");
Properties prop =  new Properties();
prop.load(is);
String url = prop.getProperty("url");
String userName = prop.getProperty("userName");
String password = prop.getProperty("password");
String device = prop.getProperty("device");
// 加载驱动
Class.forName(device);
// 连接
Connection conn = DriverManager.getConnection(url, userName, password);
//断开连接
conn.close();
```

### 操作和访问数据库

&emsp;&emsp;操作和访问数据库的方式有以下三种：

> - Statement：用于执行静态SQL语句并返回执行结果对象。
>
>   由于存在SQL注入等安全问题和操作繁琐等缺点，已不再使用。
>
> - PreparedStatement：用于存储预编译的SQL语句，可多次高效地执行这些SQL语句
>
> - CallableStatement：用于执行SQL存储过程。

#### PreparedStatement的使用

&emsp;&emsp;与Statement相比，PreparedStatement使用预编译的SQL语句并通过占位符代替变量，具有以下优点：
> - 解决了SQL注入的安全问题
> - 可操作Blob数组
> - 实现更高效的批量操作

&emsp;&emsp;PreparedStatement对数据库的操作（增删改）基本符合以下的步骤：

```java
String sql = "insert into Beauty(Name) values (?);";    // 以“?”作为占位符
PreparedStatement sm = conn.prepareStatement(sql);
sm.setString(1, value);                                 // 填充占位符，从1开始
sm.addBatch();                                          // 暂存待执行的SQL语句
sm.execute();                                           // 执行SQL语句
sm.close();
```

&emsp;&emsp;如果需要对数据进行查询，可以使用executeQuery()执行SQL语句并获得结果集：

```java
ResultSet rs = sm.executeQuery();
ResultSetMetaData rsMetaData = rs.getMetaData();       // 获取结果集的元数据
rs.close();                                            // 需要手动关闭
```

&emsp;&emsp;对结果集的访问可以使用迭代器，对结果集数据的存储一般使用ORM（Object Relational Mopping）：

> - 数据表<-->Java类
> - 记录<-->对象
> - 字段<-->对象属性

&emsp;&emsp;MySQL数据类型与Java数据类型对应关系

|    Java数据类型    |      MySQL数据类型       |
| :----------------: | :----------------------: |
|      boolean       |           BIT            |
|        byte        |         TINYINT          |
|       short        |         SMALLINT         |
|        int         |         INTEGER          |
|        long        |          BIGINT          |
|       String       | CHAR,VARCHAR,LONGVARCHAR |
|     byte array     |    BINARY,VAR BINARY     |
|   java.sql.Date    |           DATE           |
|   java.sql.Time    |           TIME           |
| java.sql.Timestamp |        TIMESTAMP         |

### 事务操作

**事务**：使数据从一种状态转换到另一种状态的一组逻辑操作单元
**事务操作**：保证所有操作都作为一个工作单元处理。在执行事务的时候，要么所有事务都被提交（commit），要么数据库管理系统放弃所有操作，整个事务回滚（rollback）。

#### 事务的ACID属性

- Atomicity：原子性，事务为一个不可分割的基本单元
- Consistency：一致性，事务必须使数据库从一个一致的转台变换到另一个一致的状态
- Isolation：隔离性，事务的执行相互隔离，并发的各个事务不会相互干扰
- Durability：持久性，事务被提交后其对数据库的更改为永久性的，后续的操作或数据库故障不会对其产生影响

#### 数据库并发问题

&emsp;&emsp;对于并发的事务，当其操作同一数据且未作相应措施时，会出现一些问题：

> - 脏读：B读取了被A修改但未提交的数据，若A回滚，则B读取的数据有误
> - 不可重复读：A两次读取同一数据之间该数据已被B修改
> - 幻读：A读取某张表的过程中B增/删了部分数据

#### 数据库的隔离级别

&emsp;&emsp;为了在保证隔离性的同时保证一定的并发性，需要在数据库的隔离级别中做出取舍：

> - READ UNCOMMITED：读未提交数据，允许读取未被其他事务提交的数据
> - READ COMMITED：读已提交数据，仅允许读取已被其他事务提交的数据
> - REPEATABLE READ：可重复读，确保事务可多次对同一字段读取到相同的值
> - SERIALIZABLE：串行化，确保一个事务执行期间可在表中读取到相同的数据

&emsp;&emsp;其中 Oracle 仅支持 READ COMMITED 和 SERIALIZABLE，默认为 READ COMMITED；MySQL 支持四种隔离级别，默认为 REPEATABLE READ。

&emsp;&emsp;在JDBC中通过以下方法管理数据库隔离级别：

```java
conn.getTranactionIsolation();  // 获取当前隔离级别
conn.setAutoCommit(false);      // 关闭自动提交
conn.setTranactionIsolation(Connection.);  // 设置隔离级别
```

### 数据库连接池

&emsp;&emsp;在以前的开发中，需要访问数据库时就创建连接，操作完成后就断开连接，具有连接利用率低下、资源消耗量大、安全性差（可能造成内存泄露）、资源使用不可控等缺点，因此我们引入数据库连接池统一管理数据库连接。

&emsp;&emsp;Java的数据库连接池用javax.sql.DataSource表示，该接口由不同的厂商进行实现，常见的有DBCP、C3P0、Druid、Proxool、BoneCP等。

#### C3P0

0. 导入依赖

1. 配置c3p0-config.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!--参考https://www.cnblogs.com/SUN99bk/p/12181493.html#_label4_1-->
   <c3p0-config>   
       <default-config>   
       <!--当连接池中的连接耗尽的时候c3p0一次同时获取的连接数。Default: 3 -->   
       <property name="acquireIncrement">3</property>   
    
       <!--定义在从数据库获取新连接失败后重复尝试的次数。Default: 30 -->   
       <property name="acquireRetryAttempts">30</property>   
          
       <!--两次连接中间隔时间，单位毫秒。Default: 1000 -->   
       <property name="acquireRetryDelay">1000</property>   
          
       <!--连接关闭时默认将所有未提交的操作回滚。Default: false -->   
       <property name="autoCommitOnClose">false</property>   
          
       <!--c3p0将建一张名为Test的空表，并使用其自带的查询语句进行测试。如果定义了这个参数那么   
       属性preferredTestQuery将被忽略。你不能在这张Test表上进行任何操作，它将只供c3p0测试   
       使用。Default: null-->   
       <property name="automaticTestTable">Test</property>   
          
       <!--获取连接失败将会引起所有等待连接池来获取连接的线程抛出异常。但是数据源仍有效   
       保留，并在下次调用getConnection()的时候继续尝试获取连接。如果设为true，那么在尝试   
       获取连接失败后该数据源将申明已断开并永久关闭。Default: false-->   
       <property name="breakAfterAcquireFailure">false</property>   
          
       <!--当连接池用完时客户端调用getConnection()后等待获取新连接的时间，超时后将抛出   
       SQLException,如设为0则无限期等待。单位毫秒。Default: 0 -->   
       <property name="checkoutTimeout">100</property>   
          
       <!--通过实现ConnectionTester或QueryConnectionTester的类来测试连接。类名需制定全路径。   
       Default: com.mchange.v2.c3p0.impl.DefaultConnectionTester-->   
       <property name="connectionTesterClassName"></property>   
          
       <!--指定c3p0 libraries的路径，如果（通常都是这样）在本地即可获得那么无需设置，默认null即可   
       Default: null-->   
       <property name="factoryClassLocation">null</property>   
          
       <!--强烈不建议使用该方法，将这个设置为true可能会导致一些微妙而奇怪的bug-->   
       <property name="forceIgnoreUnresolvedTransactions">false</property>   
          
       <!--每60秒检查所有连接池中的空闲连接。Default: 0 -->   
       <property name="idleConnectionTestPeriod">60</property>   
          
       <!--初始化时获取三个连接，取值应在minPoolSize与maxPoolSize之间。Default: 3 -->   
       <property name="initialPoolSize">3</property>   
          
       <!--最大空闲时间,60秒内未使用则连接被丢弃。若为0则永不丢弃。Default: 0 -->   
       <property name="maxIdleTime">60</property>   
          
       <!--连接池中保留的最大连接数。Default: 15 -->   
       <property name="maxPoolSize">15</property>   
          
       <!--JDBC的标准参数，用以控制数据源内加载的PreparedStatements数量。但由于预缓存的statements   
       属于单个connection而不是整个连接池。所以设置这个参数需要考虑到多方面的因素。   
       如果maxStatements与maxStatementsPerConnection均为0，则缓存被关闭。Default: 0-->   
       <property name="maxStatements">100</property>   
          
       <!--maxStatementsPerConnection定义了连接池内单个连接所拥有的最大缓存statements数。Default: 0 -->   
       <property name="maxStatementsPerConnection"></property>   
          
       <!--c3p0是异步操作的，缓慢的JDBC操作通过帮助进程完成。扩展这些操作可以有效的提升性能   
       通过多线程实现多个操作同时被执行。Default: 3-->   
       <property name="numHelperThreads">3</property>   
          
       <!--当用户调用getConnection()时使root用户成为去获取连接的用户。主要用于连接池连接非c3p0   
       的数据源时。Default: null-->   
       <property name="overrideDefaultUser">root</property>   
          
       <!--与overrideDefaultUser参数对应使用的一个参数。Default: null-->   
       <property name="overrideDefaultPassword">password</property>   
          
       <!--密码。Default: null-->   
       <property name="password"></property>   
          
       <!--定义所有连接测试都执行的测试语句。在使用连接测试的情况下这个一显著提高测试速度。注意：   
       测试的表必须在初始数据源的时候就存在。Default: null-->   
       <property name="preferredTestQuery">select id from test where id=1</property>   
          
       <!--用户修改系统配置参数执行前最多等待300秒。Default: 300 -->   
       <property name="propertyCycle">300</property>   
          
       <!--因性能消耗大请只在需要的时候使用它。如果设为true那么在每个connection提交的   
       时候都将校验其有效性。建议使用idleConnectionTestPeriod或automaticTestTable   
       等方法来提升连接测试的性能。Default: false -->   
       <property name="testConnectionOnCheckout">false</property>   
          
       <!--如果设为true那么在取得连接的同时将校验连接的有效性。Default: false -->   
       <property name="testConnectionOnCheckin">true</property>   
          
       <!--用户名。Default: null-->   
       <property name="user">root</property>   
          
       <!--早期的c3p0版本对JDBC接口采用动态反射代理。在早期版本用途广泛的情况下这个参数   
       允许用户恢复到动态反射代理以解决不稳定的故障。最新的非反射代理更快并且已经开始   
       广泛的被使用，所以这个参数未必有用。现在原先的动态反射与新的非反射代理同时受到   
       支持，但今后可能的版本可能不支持动态反射代理。Default: false-->   
       <property name="usesTraditionalReflectiveProxies">false</property> 
       </default-config>      
   </c3p0-config>
   ```

2. 使用

   ```java
   DataSource ds = new ComboPooledDataSource();    // 获取数据库连接池
   Connection conn = ds.getConnection();           // 获取连接，可指定配置名称
   conn.close();                                   // 关闭连接
   ds.destroy();                                   // 关闭连接池
   ```

#### DBCP

&emsp;&emsp;DBCP的使用与C3P0类似，导入jakarta.commons.dbcp，使用BasicDataSource获取数据库连接池或使用BasicDataSourceFactory.createDataSouece()根据配置文件创建数据库连接池。

#### Druid

&emsp;&emsp;国内目前广泛使用的是阿里巴巴的Druid（德鲁伊）数据库连接池，其使用方法与DBCP类似，使用DruidDataSoueceFactory工厂根据配置文件获取数据库连接池。

### Apache-DBUtils

&emsp;&emsp;~~欢迎来到最令人兴奋的“前面白学”部分:joy:~~Common-DBUtils是Apahe提供的一个开源的对JDBC简单封装的工具类库。

```java
QueryRunner runner = new QueryRunner();
runner.update(connection, sql, params);
runner.query(connection, sql, ResultSetHandler<>, params);
/* 常用的Handler
 * BeanHandler<>：返回具体的单个对象
 * BeanListHandler<>：返回具体的多个对象
 * Array/Map(List)Handler：以Array/Map储存记录的各个字段
 * ScalerHandler：获取具体的字段
*/
DBUtils.closeQuietly(conn);
```

## Servlet

&emsp;&emsp;Servlet（Server Applet 服务器端小程序）

### C/S与B/S

- C/S：客户端/服务器模式
- B/S：浏览器/服务器模式

### 实现Servlet接口编写Servlet

&emsp;&emsp;Servlet定义了五个必须实现的接口：

- init
- getServletConfig
- service
- getServletInfo
- destroy

### 其他方法编写Servlet

#### 继承GenericServlet

&emsp;&emsp;只需实现service方法

##### 继承HttpServlet

&emsp;&emsp;HttpServlet提供了根据请求方式调用的方法：

- doGet
- doPost
- doPut
- doDelete

### 请求与响应

&emsp;&emsp;对于用户发送的请求（Request）可以通过以下方式处理：

```java
req.setCharacterEncoding("UTF-8");            // 设置编码（默认为ISO-8859-1）
req.getParameter("key");                      // 根据名称获取请求头的值
```

&emsp;&emsp;发送给用户的响应（Response）可以通过以下方式处理：

```java
resp.setHeader("key", "value");                    // 设置响应头
resp.setContentType("text/html;charset=utf-8");    // 设置响应体格式
resp.setCharacterEncoding("UTF-8");    
PrintWriter respWriter = resp.getWriter();         // 获取输出流
```

### 配置Servlet服务

&emsp;&emsp;可以通过web.xml配置Servlet：

```xml
<web-app>      
    <servlet>
        <servlet-name>my</servlet-name>
        <servlet-class>MyServlet</servlet-class>
        <!--Servlet的类名-->
        <load-on-startup>num</load-on-startup>
        <!--配置自动加载，数越大优先级越高，负数表示请求时才加载-->
    </servlet>

    <servlet-mapping>
        <servlet-name>my</servlet-name>
        <!--与要被映射的Servlet-name相同-->
        <url-pattern>/requestURL</url-pattern>
        <!--可以使用通配符*或/，后者不会匹配到jsp-->
    </servlet-mapping>
</web-app> 
```

&emsp;&emsp;也可以通过注解配置Servlet：

```java
@WebServlet(name = "className", value = {}, loadOnStartup = "-1", urlPatterns = {})
// value与urlPatterns功能相同，不可同时存在
```

### 转发与重定向

&emsp;&emsp;有时需要多个Servlet共同完成一次请求，此时可以通过**服务器**的**请求转发**完成：

```java
req.setAttribute("key", value);              // 重写请求头（添油加醋）
req.getRequestDispatcher("/path")            // 获取转发器
    .requestDispatcher.forward(req,resp);    // 转发请求
```

&emsp;&emsp;除了转发，还可通过**客户端**的**重定向**来完成：

```java
resp.sendRedirect("uri");
```

&emsp;&emsp;转发与重定向的区别：

|          |      转发      |     重定向      |
| :------: | :------------: | :-------------: |
|  作用于  |     服务器     |     客户端      |
| 请求次数 |       1        |        2        |
| 请求地址 |      不变      |      改变       |
| 数据共享 | 共享请求与响应 | 通过URI拼接实现 |
|   目标   |   同一webapp   |    任何位置     |

### Servlet生命周期

1. 实例化

   服务器启动时调用构造函数实例化Servlet

2. 初始化

   根据配置，在服务器启动后或收到请求时调用init()方法初始化servlet

3. 提供服务

   每次收到请求后调用service方法

4. 销毁

   服务器关闭前调用destroy方法，并由JVM负责完成垃圾回收

### 状态管理

&emsp;&emsp;由于HTTP连接是无状态的，因此需要通过Cookie和Session解决状态存储问题

#### Cookie

```java
// 创建Cookie
Cookie cookie = new Cookie("key", "value");    // 创建Cookie
cookie.setPath("/path");                       // 设置Cookie作用范围
cookie.setMaxAge(second);                      // 有效期，单位秒，0表示浏览器关闭，-1表示储存在内存中
resp.addCookie(cookie);                        // 将Cookie返回给客户端
// 相同名称和路径的Cookie会相互覆盖
// 获取Cookies
Cookie[] cookies = req.getCookies();
// Cookie编解码
URLEncoder.encode("name", str);                // 编码
URLDEcoder.decode("name", str);                // 解码
```

#### Session

```java
HttpSession session = req.getSession();        // 获取Session
session.setAttribute("name", value);           // 设置属性
session.getAttribute("name");                  // 读取属性
session.removeAttribute("name");               // 移除属性
session.setMaxInactiveInterval(second);        // 设置存活时间
session.invalidate();                          // 立刻注销
resp.encodeRedirectURL("/url");                // 追加Session的URL
```

### ServletContext

&emsp;&emsp;ServletContext随Servlet启动而产生，随Servlet销毁而消失，永远存储全局数据

```java
// 获取ServletContext
ServletContext servletContext = this.getServletContext();    // 推荐
ServletContext servletContext = req.getServletContext();
ServletContext servletContext = req.getSession().getServletContext();
// 获取信息
servletContext.getRealPath("contextPath");        // 获取某上下文的全局路径
servletContext.getContextPath();                  // 获取当前应用的上下文路径
// 操作属性
servletContext.setAttribute("name", value);       // 添加属性
servletContext.getAttribute("name");              // 获取属性
servletContext.removeAttribute("name");           // 删除属性
```

### 过滤器

&emsp;&emsp;过滤器（Filter）是一种处于客户端与服务器之间的过滤技术。Filter对象随Servlet产生而产生，随Servlet销毁而销毁。

#### 编写过滤器

```java
public class myfilter implements Filter{

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        chain.doFilter(request,response);        // 将请求传递到下一个过滤器或Servlet
    }
}
```

#### 配置过滤器

&emsp;&emsp;可通过XML文档配置Filter：

```xml
<filter>
    <filter-name>filter</filter-name>
    <filter-class>filterOPath</filter-class>
</filter>
<filter-mapping>
    <filter-name>filter</filter-name>
    <url-pattern>/*</url-pattern>
    <!--要被拦截的请求，不能用/-->
</filter-mapping>
```

&emsp;&emsp;也可通过注解配置Filter

```java
@WebFilter
```

#### Filter优先级

- 注解根据过滤器名称排序
- XML配置文件中会根据注册顺序
- XML优先于注解
- XML与注解重复的过滤器会执行多次

## MyBatis

&emsp;&emsp;MyBatis是一款优秀的持久层框架，它支持自定义 SQL、存储过程以及高级映射。MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。

&emsp;&emsp;MyBatis需要以下依赖：

> - mybatis
> - jdbc
> - junit
> - log4j（日志记录，可选）

### MyBatis配置文件

```xml
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration><!-- 配置 -->
    <properties /><!-- 配置文件全局变量 -->
    <settings /><!-- 设置 -->
    <typeAliases><!-- 类型别名（大小写不敏感） -->
        <typealias type="" alias=""></typealias><!-- 不指定alias默认为类名-->
        <package name="" /><!-- 导入一个包的所有类-->
    </typeAliases>
    <typeHandlers /><!-- 类型处理器 -->
    <objectFactory /><!-- 对象工厂 -->
    <plugins /><!-- 插件 -->
    <environments><!-- 数据库环境 -->
        <environment id = "development"><!-- 数据库环境，id为唯一标识 -->
            <transactionManager type="jdbc"/><!-- 事务管理器 -->
            <!-- JDBC：原生事务管理
                 MANAGED：被其他框架管理，如Spring -->
            <dataSource type="POOLED"><!-- 数据源 -->
            <!-- POOLED：使用数据库连接池
                 UNPOOLED：不使用数据库连接池
                 JNDI：使用上下文中的数据源 -->
                <property name="driver" value=""/>
                <property name="url" value=""/>
                <property name="username" value=""/>
                <property name="passowrd" value=""/>
            </dataSource>
        </environment>
    </environments>
    <databaseIdProvider /><!-- 数据库厂商标识 -->
    <mappers><!-- 映射器 -->
        <mapper resources="mapper/UserMapper.xml"/>
        <package name="" /><!-- 导入一个包的所有配置文件-->
    </mappers>
</configuration>
```

&emsp;&emsp;Settings的选项如下：


|              配置项               | 作用                                                         |                           配置选项                           | 默认值                                                       |
| :-------------------------------: | ------------------------------------------------------------ | :----------------------------------------------------------: | ------------------------------------------------------------ |
|           cacheEnabled            | 该配置影响所有映射器中配置缓存的全局开关                     |                         true\|false                          | true                                                         |
|        lazyLoadingEnabled         | 延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。在特定关联关系中可通过设置 fetchType 属性来覆盖该项的开关状态 |                         true\|false                          | false                                                        |
|       aggressiveLazyLoading       | 当启用时，对任意延迟属性的调用会使带有延迟加载属性的对象完整加载；反之，每种属性将会按需加载 |                         true\|false                          | 版本3.4.1 （不包含） 之前默认值为 true，之后为 false         |
|     multipleResultSetsEnabled     | 是否允许单一语句返回多结果集（需要兼容驱动）                 |                         true\|false                          | true                                                         |
|          useColumnLabel           | 使用列标签代替列名。不同的驱动会有不同的表现，具体可参考相关驱动文档或通过测试这两种不同的模式来观察所用驱动的结果 |                         true\|false                          | true                                                         |
|         useGeneratedKeys          | 允许JDBC 支持自动生成主键，需要驱动兼容。如果设置为 true，则这个设置强制使用自动生成主键，尽管一些驱动不能兼容但仍可正常工作（比如 Derby） |                         true\|false                          | false                                                        |
|        autoMappingBehavior        | 指定 MyBatis 应如何自动映射列到字段或属性。 NONE 表示取消自动映射。 PARTIAL 表示只会自动映射，没有定义嵌套结果集和映射结果集。 FULL 会自动映射任意复杂的结果集（无论是否嵌套） |                     NONE、PARTIAL、FULL                      | PARTIAL                                                      |
| autoMappingUnkno wnColumnBehavior | 指定自动映射当中未知列（或未知属性类型）时的行为。 默认是不处理，只有当日志级别达到 WARN 级别或者以下，才会显示相关日志，如果处理失败会抛出 SqlSessionException 异常 |                    NONE、WARNING、FAILING                    | NONE                                                         |
|        defaultExecutorType        | 配置默认的执行器。SIMPLE 是普通的执行器；REUSE 会重用预处理语句（prepared statements）；BATCH 执行器将重用语句并执行批量更新 |                     SIMPLE、REUSE、BATCH                     | SIMPLE                                                       |
|      defaultStatementTimeout      | 设置超时时间，它决定驱动等待数据库响应的秒数                 |                          任何正整数                          | Not Set (null)                                               |
|         defaultFetchSize          | 设置数据库驱动程序默认返回的条数限制，此参数可以重新设置     |                          任何正整数                          | Not Set (null)                                               |
|       safeRowBoundsEnabled        | 允许在嵌套语句中使用分页（RowBounds）。如果允许，设置 false  |                         true\|false                          | false                                                        |
|     safeResultHandlerEnabled      | 允许在嵌套语句中使用分页（ResultHandler）。如果允许，设置false |                         true\|false                          | true                                                         |
|     mapUnderscoreToCamelCase      | 是否开启自动驼峰命名规则映射，即从经典数据库列名 A_COLUMN 到经典 Java 属性名 aColumn 的类似映射 |                         true\|false                          | false                                                        |
|          localCacheScope          | MyBatis 利用本地缓存机制（Local Cache）防止循环引用（circular references）和加速联复嵌套査询。 默认值为 SESSION，这种情况下会缓存一个会话中执行的所有查询。若设置值为 STATEMENT，本地会话仅用在语句执行上，对相同 SqlScssion 的不同调用将不会共享数据 |                      SESSION\|STATEMENT                      | SESSION                                                      |
|          jdbcTypeForNull          | 当没有为参数提供特定的 JDBC 类型时，为空值指定 JDBC 类型。某些驱动需要指定列的 JDBC 类型，多数情况直接用一般类型即可，比如 NULL、VARCHAR 或 OTHER |                     NULL、VARCHAR、OTHER                     | OTHER                                                        |
|      lazyLoadTriggerMethods       | 指定哪个对象的方法触发一次延迟加载                           |                              —                               | equals、clone、hashCode、toString                            |
|     defaultScriptingLanguage      | 指定动态 SQL 生成的默认语言                                  |                              —                               | org.apache.ibatis .script.ing.xmltags .XMLDynamicLanguageDriver |
|        callSettersOnNulls         | 指定当结果集中值为 null 时，是否调用映射对象的 setter（map 对象时为 put）方法，这对于 Map.kcySet() 依赖或 null 值初始化时是有用的。注意，基本类型（int、boolean 等）不能设置成 null |                         true\|false                          | false                                                        |
|             logPrefix             | 指定 MyBatis 增加到日志名称的前缀                            |                          任何字符串                          | Not set                                                      |
|              loglmpl              | 指定 MyBatis 所用日志的具体实现，未指定时将自动査找          | SLF4J\|LOG4J\|LOG4J2\|JDK_LOGGING \|COMMONS_LOGGING \|ST DOUT_LOGGING\|NO_LOGGING | Not set                                                      |
|           proxyFactory            | 指定 MyBatis 创建具有延迟加栽能力的对象所用到的代理工具      |                       CGLIB\|JAVASSIST                       | JAVASSIST （MyBatis 版本为 3.3 及以上的）                    |
|              vfsImpl              | 指定 VFS 的实现类                                            |    提供 VFS 类的全限定名，如果存在多个，可以使用逗号分隔     | Not set                                                      |
|        useActualParamName         | 允许用方法参数中声明的实际名称引用参数。要使用此功能，项目必须被编译为 Java 8 参数的选择。（从版本 3.4.1 开始可以使用） |                         true\|false                          | true                                                         |

### Mapper

&emsp;&emsp;在MyBatis中，创建Mapper需要一个Mapper的接口以及一个XML映射文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.doudi.sugw.mapper.UserMapper">
<!--与对应接口匹配-->
    <insert id="" resultType="com.doudi.sugw.pojo.User">
    <!--id与抽象方法匹配
    resultType根据字段名匹配所给类的属性名，并返回所给类的对象作为结果-->
        <!--SQLs-->
    </insert>
</mapper>
```

&emsp;&emsp;通过以下步骤使用Mapper

```java
// 加载配置文件
InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
// 创建SqlSessionFactoryBuilder（工厂的构造器）
SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
// 创建SqlSessionFactory（工厂）
SqlSessionFactory build = builder.build(is);
// 创建会话
SqlSession sqlSession = build.openSession(false);    // 是否自动提交
UserMapper mapper = sqlSession.getMapper(UserMapper.class);
// 调用mapper的方法
int lineNum = mapper.insert();
// 手动提交
sqlSession.commit();
sqlSession.close();
```

### 获取参数值

&emsp;&emsp;可以使用#{}（本质为占位符替换）或${}（本质为字符串拼接）获取方法的参数。当仅有一个参数时，{}内可使用任意名称；当有多个参数时可通过arg0，arg1或param1，param2访问对应参数（或在调用时通过@Param注解指定参数名）。

&emsp;&emsp;如果参数是一个Map,可通过键名来访问；如果参数是一个对象，可通过属性名访问（要求有get方法）。

### 查询结果的处理

&emsp;&emsp;查询结果为单个实体对象时可用实体对象接收，查询结果为多个时需要使用List接收。

&emsp;&emsp;也可用@MapKey指定数数据表主键作为key,以Map保存查询结果。

### 自定义映射

&emsp;&emsp;当查询结果的字段名与实体类的属性名不匹配时，可通过SQL语句的字段别名解决，也可通过MyBatis的下划线命名——驼峰命名转换（mapUnderrsocreToCamelCase）来解决，或者通过自定义映射（resultMap）来解决：

```xml
<resultMap id="myresmap" type=""></resultMap>
    <id property="proName" column="pro_name"></id><!-- 主键 -->
    <result property="proName" column="pro_name"></id><!-- 其他字段 -->
    <association></association><!-- 多对一 -->
    <collection></collection><!-- 一对多 -->
<select id="selectsql" resultMap="myresmap">
</select>
```

&emsp;&emsp;当查询结果为多对一（多个字段对应某类的对个属性）时，可通过在propriety中指定属性的集联属性复制或使用association单步查询：

```xml
<association property="proName" javaType="Class">
    <id property="" column=""></id>
    <result property="" column=""></id>
</association>
```

&emsp;&emsp;或多步查询：

```xml
<association property="proName"    
             select="" 
             column=""
             fatchType="lazy/eager"/>
<!-- 分别对应属性名、查询语句的唯一标识（类名+id）、连表条件和是否延迟加载 -->
```

> &emsp;&emsp;当开启lazyLoadingEnabled（延迟加载）并关闭aggressiveLazyLoading后多步查询可减少不必要的加载，加快运行速度。

&emsp;&emsp;有时会遇到查询结果为“一对多”（结果中包含一个集合）的情况，可通过collection来解决：

```xml
<collection property="" ofType=""><!-- ofType为“集合”的类型 -->
    <id property="" column=""></id>
    <result property="" column=""></id>
</collection>
```

&emsp;&emsp;类似association，collection也可以分布查询。

### 动态SQL

&emsp;&emsp;动态SQL是MyBatis的一种根据条件判断对SQL语句进行拼接的技术：

```xml
<select id="selectUser" resultType="User">
    select * from t_user 
    <where><!-- 可动态生成where并自动清理if前面的and/or-->
        <if test="uName != null and uName != ">
            and user_name = #{}
        </if>
    </where>
</select>
```

&emsp;&emsp;除此之外，还可通过trim批量处理：

```xml
<trim prefix="" suffix="" prefixOverrides="" suffixOverrides="">
<!-- prefix/suffix用于在整个代码块前后添加指定内容
     prefixOverrides/suffixOverrides用于在每个子项目前后删去指定内容 -->
</trim>
```

&emsp;&emsp;对于集联判断，可用choose when otherwise,类似if、els if、else：

```xml
<choose>
    <when test="">
        SQL
    </when>
    <when test="">
        SQL
    </when>
    <otherwise>
        SQL
    </otherwise>
</choose>
```

&emsp;&emsp;循环操作可通过foreach实现：

```xml
<delete id="delBaseRunWay">
    delete from t_user where id in 
    <foreach collection="eids" item="eid" separator="," open="(" close=")">
        #{eid}
    </foreach>
</delete>
```

&emsp;&emsp;有时需要对SQL语句片段多次使用，可以用sql标签保存这些片段：

```xml
<sql id="sqlName">
    SQLs
</sql>
<!-- 使用include引用 -->
<include refid="sqlName"></include>
```

### MyBatis缓存

#### 一级缓存

&emsp;&emsp;一级缓存为SqlSession级别，默认开启。一级缓存失效的情况：

> - 查询条件不同
>
> - 进行了增删改操作
>
> - 手动清空缓存
>
>   ```java
>   sqlsession.clearcache();
>   ```

#### 二级缓存

&emsp;&emsp;二级缓存为SqlSessionFactory级别，需手动开启：

> - 在核心配置文件开启cacheEnabled（默认开启）
>
> - 映射文件中设置cache标签
>
>   ```xml
>   <cache eviction="" flushInterval="" size="" readOnly=""/>
>   ```
>
> - 当SqlSession关闭或提交后才会生效
>
> - 被查询的实体类实现序列化（Serializable）接口
>
> - 未进行增删改以及清空一级缓存操作

&emsp;&emsp;二级缓存的配置：

> - eviction：缓存回收策略
>   - LRU：Lease Recently Used移除最长时间不被使用的（默认）
>   - FIFO：先进先出
>   - SOFT：软引用
>   - WEAK：弱引用
> - flushInterval：刷新间隔，单位为毫秒，默认不刷新
> - size：缓存实例对象的数量
> - readOnly：是否只读，默认false,效率低但安全

#### 缓存查询顺序

- 先二后一
- 一无查库
- 一死入二

### MyBatis逆向工程

&emsp;&emsp;通过数据库逆向生成MyBatis的Mapper就叫做MyBatis逆向工程

0. 添加依赖

   ```xml
   
   <!--mybatis自动生成代码插件-->
   <plugin>
       <groupId>org.mybatis.generator</groupId>
       <artifactId>mybatis-generator-maven-plugin</artifactId>
       <version>1.3.7</version>
       <configuration>
           <!-- 配置文件路徑 -->
           <configurationFile>src/main/resources/generator/mybatis-generator.xml</configurationFile>
           <verbose>true</verbose>
           <!-- 是否覆盖，true表示会替换生成的JAVA文件，false则不覆盖 -->
           <overwrite>true</overwrite>
       </configuration>
       <dependencies>
           <!--mysql驱动包-->
           <dependency>
               <groupId>mysql</groupId>
               <artifactId>mysql-connector-java</artifactId>
               <version>5.1.46</version>
           </dependency>
           <dependency>
               <groupId>org.mybatis.generator</groupId>
               <artifactId>mybatis-generator-core</artifactId>
               <version>1.3.7</version>
           </dependency>
           <!-- lombok插件，用于减少生成的代码 -->
           <dependency>
               <groupId>com.softwareloop</groupId>
               <artifactId>mybatis-generator-lombok-plugin</artifactId>
               <version>1.0</version>
           </dependency>
       </dependencies>
   </plugin>
   ```

1. MyBatis核心配置文件

2. 创建generator核心配置文件

   ```xml
   <!-- 文件名必须为generatorConfig.xml -->
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE generatorConfiguration
           PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
           "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
   <generatorConfiguration>
       <!--
         targetRuntime: 执行生成的逆向工程的版本
         MyBatis3Simple: 生成基本的CRUD（清新简洁版）
         MyBatis3: 生成带条件的CRUD（奢华尊享版）
       -->
       <context id="DB2Tables" targetRuntime="MyBatis3Simple">
           <!-- 数据库的连接信息 -->
           <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
                           connectionURL="jdbc:mysql://localhost:3306/mybatis"
                           userId="root"
                           password="123456">
           </jdbcConnection>
           <!-- javaBean的生成策略-->
           <javaModelGenerator targetPackage="com.atguigu.mybatis.pojo" targetProject=".\src\main\java">
               <property name="enableSubPackages" value="true" />
               <property name="trimStrings" value="true" />
           </javaModelGenerator>
           <!-- SQL映射文件的生成策略 -->
           <sqlMapGenerator targetPackage="com.atguigu.mybatis.mapper"
                            targetProject=".\src\main\resources">
               <property name="enableSubPackages" value="true" />
           </sqlMapGenerator>
           <!-- Mapper接口的生成策略 -->
           <javaClientGenerator type="XMLMAPPER"
                                targetPackage="com.atguigu.mybatis.mapper" targetProject=".\src\main\java">
               <property name="enableSubPackages" value="true" />
           </javaClientGenerator>
           <!-- 逆向分析的表 -->
           <!-- tableName设置为*号，可以对应所有表，此时不写domainObjectName -->
           <!-- domainObjectName属性指定生成出来的实体类的类名 -->
           <table tableName="t_emp" domainObjectName="Emp"/>
           <table tableName="t_dept" domainObjectName="Dept"/>
       </context>
   </generatorConfiguration>
   
   ```

3. 执行maven脚本

### MyBatis分页

0. 添加依赖：com.github.pagehalper

1. 配置MyBatis核心配置文件

   ```xml
   <plugin interceptor="com.github.pagehalper.PageInterceptor"></plugin>
   ```

2. 使用PageHelper

   ```java
   PageHelper.startPage(int pageNum, int PageSize);
   // 查询前开启
   ```


## Spring

&emsp;&emsp;Spring是一个开源（[Apache-2.0 License](https://github.com/spring-projects/spring-framework/blob/main/LICENSE.txt)）的**轻量级**的**控制反转**(IoC)和**面向切面**(AOP)的JavaEE框架。

### IoC

&emsp;&emsp;**IoC**（Inversion of Control，控制反转）：将对象的创建和调用交由用户负责，降低代码耦合度。DI（Dependency Injection，依赖注入）是一种常用的IoC实现方法。

&emsp;&emsp;IoC使用的主要技术为XML解析、工厂模式和反射。

#### 创建对象、注入属性

```xml
<bean id="myObj" class="com.example.MyObj" scope="singleton">
<!--“name”可代替"id"，前者值可以含有特殊符号-->
<!--scope用于指定bean的作用域。默认为单例（“static”）“singleton”，此外还有多实例“prototype”、请求“request”和会话“sessiom”-->
    <constructor-arg name="field" value="value"></constructor-arg>
    <constructor-arg index="0" value="value"></constructor-arg>
    <!--调用含参构造器创建对象并指定参数值-->
    <property name="fieldName" value="value"></property>
    <!--通过调用set方法注入所依赖的对象-->
    <property name="field">
        <null/>
    </property>
     <!--注入空值-->
    <property name="name">
        <value><![CDATA[value]]></value>
    </property>
    <!--使用CDATE注入特殊值-->
    <property name="fieldObj" ref="beanName"></property>
    <!--注入Bean-->
</bean>
```

除此之外，还有一种称为“P名称空间”的注入方式

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
       <bean id="myObj" class="com.example.MyObj" p:fieldName="value"></bean>
</beans>
```

#### FactoryBean

&emsp;&emsp;工厂Bean（FactoryBean）是一种特殊的Bean，用来创造创建多个同一类的对象。该对象实现FactoryBean接口，相当于一个工厂类。

#### Bean的生命周期

0. Spring 启动，查找并加载需要被 Spring 管理的 Bean，对 Bean 进行实例化。

1. 对 Bean 进行属性注入。

2. 如果 Bean 实现了 BeanNameAware 接口，则 Spring 调用 Bean 的 setBeanName() 方法传入当前 Bean 的 id 值。

3. 如果 Bean 实现了 BeanFactoryAware 接口，则 Spring 调用 setBeanFactory() 方法传入当前工厂实例的引用。

4. 如果 Bean 实现了 ApplicationContextAware 接口，则 Spring 调用 setApplicationContext() 方法传入当前 ApplicationContext 实例的引用。

5. 如果 Bean 实现了 BeanPostProcessor 接口，则 Spring 调用该接口的预初始化方法 postProcessBeforeInitialzation() 对 Bean 进行加工操作，此处非常重要，Spring 的 AOP 就是利用它实现的。

6. 如果 Bean 实现了 InitializingBean 接口，则 Spring 将调用 afterPropertiesSet() 方法。

7. 如果在配置文件中通过 init-method 属性指定了初始化方法，则调用该初始化方法。

8. 如果 BeanPostProcessor 和 Bean 关联，则 Spring 将调用该接口的初始化方法 postProcessAfterInitialization()。此时，Bean 已经可以被应用系统使用了。

9. 如果在 <bean> 中指定了该 Bean 的作用域为 singleton，则将该 Bean 放入 Spring IoC 的缓存池中，触发 Spring 对该 Bean 的生命周期管理；如果在 <bean> 中指定了该 Bean 的作用域为 prototype，则将该 Bean 交给调用者，调用者管理该 Bean 的生命周期，Spring 不再管理该 Bean。

10. 如果 Bean 实现了 DisposableBean 接口，则 Spring 会调用 destory() 方法销毁 Bean；如果在配置文件中通过 destory-method 属性指定了 Bean 的销毁方法，则 Spring 将调用该方法对 Bean 进行销毁。

#### 自动装配

> 根基指定的规则（根据名称装配/或根据类型装配），Spring将特定属性注入的操作叫做自动装配。

```xml
<bean id="myObj" class="com.example.MyObj" autowire="no">
<!--通过autowire属性指定自动装配行为：
    no：不自动装配（默认行为）
    byName：根据属性名装配符合条件的bean
    byType：根据属性类型装配符合条件的bean
    constructor：自动装配构造器参数
-->
</bean>
```

#### 获取Bean

&emsp;可以通过BeanFactory或ApplicationContext加载配置文件，前者是Spring内部的创建方法，实际开发中主要使用后者。

```java
ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
MyClass myClass = (MyClass) context.getBean("myClass");
((ClassPathXmlApplicationContext) context).close();
```

#### 基于注解实现IoC

&emsp;&emsp;除了通过XML文件来配置IoC，还可以通过注解实现IoC：

开启注解支持

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                            http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
       <context:annotation-config/>
       <!--开启属性注解支持（该注解已被context:component-scan替代）-->
       <context:component-scan base-package="com.springtest" use-default-filters="false">
       <!--默认扫描所有类，除非设置了use-default-filters="false"-->
              <context:exclude-filter type="" expression=""/>
              <context:include-filter type="" expression=""/>
       </context:component-scan>
</beans>
```

创建对象

```java
@Component    // 普通组件
@Service      // 业务层组件
@Controller   // 控制器
@Repository   // 持久层
// 四选一，默认以类名首字母小写作为id
```

自动装配

```java
@Autowired                    // 自动装配对象，默认byType
@Qualifier(value = "")        // 使@Autowired以byName自动装配对象
@Resource                     // 默认byType自动装配对象，给定name后byName自动装配
@Value                        // 注入普通类型
```

> 0. 此处不需要set()方法
> 1. @Autowired、@Qualifier和@Value由org.springframework提供，@Resource由javax提供

### AOP

&emsp;&emsp;**AOP**（Aspect Oriented Programming，面向切面编程）：~~将任务分为多个切面，在各个切面独立实现。~~可以在不修改已有代码的情况下添加新功能。

&emsp;&emsp;AOP的底层原理为动态代理。

#### 动态代理

```java
public class ProxyVegetableFarm {
    public static Object getProxyInstance(Object object){
        MyInvocationHandler handler = new MyInvocationHandler();
        handler.bind(object);
        return Proxy.newProxyInstance(object.getClass().getClassLoader(), object.getClass().getInterfaces(), handler);
    }
}

class MyInvocationHandler implements InvocationHandler {
    private Object object;

    public void bind(Object object){
        this.object = object;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        // some func();
        method.invoke(object, args);
        // some func();
    }
}
```

#### AOP专业术语

 - Joint point（连接点）：表示在程序中明确定义的点，典型的包括方法调用、对类成员的访问以及异常处理程序块的执行等等，它自身还可以嵌套其它 joint point。

 - Pointcut（切入点）：表示一组 joint point，这些 joint point 或是通过逻辑关系组合起来，或是通过通配、正则表达式等方式集中起来，它定义了相应的 Advice 将要发生的地方。

 - Advice（增强/通知）：Advice 定义了在 Pointcut 里面定义的程序点具体要做的操作，它通过 before、after 和 around 来区别是在每个 joint point 之前、之后还是代替执行的代码。

   |   通知类型   |       连接点       |                   实现接口                   | 注解            |
   | :----------: | :----------------: | :------------------------------------------: | --------------- |
   |   前置通知   |       方法前       |  org.springframework.aop.MethodBeforeAdvice  | @Before         |
   |   最终通知   |       方法后       |     org.springframework.aop.AfterAdvice      | @After          |
   | 后置正常通知 | 方法后且不抛出异常 | org.springframework.aop.AfterReturningAdvice | @AfterReturning |
   |   环绕通知   |      方法前后      | org.aopalliance.intercept.MethodInterceptor  | @Around         |
   |   异常通知   |  方法后且抛出异常  |     org.springframework.aop.ThrowsAdvice     | @AfterThrowing  |
   |   引介通知   |  类的新方法/属性   | org.springframework.aop.IntroductionAdvisor  |                 |

 - Aspect（切面）： Aspect 声明类似于 Java 中的类声明，在 Aspect 中会包含着一些 Pointcut 以及相应的 Advice。
 - Target（目标对象）：织入 Advice 的目标对象.。
 - Weaving（织入）：将 Aspect 和其他对象连接起来, 并创建 Adviced object 的过程

#### 切入点表达式

```java
execution(Permission ReturnType Class.Method(args...))
// 可以使用通配符*
```

#### 基于AspectJ注解实现AOP

0. 开启注解扫描

1. 注解被增强类

   ```java
   @Component
   // 增强类和被增强类都需要
   ```

2. 注解增强类

   ```java
   @Component
   @Aspect
   ```

3. 在配置文件中开启代理生成

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:context="http://www.springframework.org/schema/context"
          xmlns:aop="http://www.springframework.org/schema/aop"
          xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/beans/spring-comtext.xsd
                           http://www.springframework.org/schema/aop  http://www.springframework.org/schema/beans/spring-aop.xsd">
       <context:component-scan base-package="com.springtest"></context:component-scan>
       <!--开启注解扫描-->
       <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
       <!--开启AspectJ自动生成代理对象-->
   </beans>
   ```

5. 注解增强方法

   ```java
   // 根据需要选择其一
   @Before("execution()")
   @After("execution()")
   @AfterReturning("execution()")
   @AfterThrowing("execution()")
   @Around("execution()")
   ```

   Around有些特殊，可用过如下方法调用被增强方法：

   ```java
   @Around("execution()")
   public void show(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
       System.out.println("邓紫棋真美！");
       proceedingJoinPoint.proceed();
       System.out.println("我爱邓紫棋！");
   }
   ```

#### 相同切入点抽取

&emsp;&emsp;有时会有多个通知使用同一切入点，可以通过以下技巧提取公共切入点：

```java
@Pointcut("")
public void publicPintcut(){}
@Before("publicPintcut()")
```

#### 通知优先级

&emsp;&emsp;可以通过以下注解控制同一切入点的通知优先级：

```java
@Order(int) // 数字越小优先级越高，默认为2147483647（2^31-1）
```

#### 基于配置文件实现AOP

```xml
<bean id="advising" class="com.springtest.MyClass"></bean>
<bean id="advised" class="com.springtest.MyBean"></bean>
<aop:config>
    <aop:pointcut id="pointoutName" expression="execution()"/>
    <!--配置切入点-->
    <aop:aspect ref="advising">
    <!--配置切面-->
        <aop:around method="show" pointcut-ref="pointoutName"/>
    </aop:aspect>
</aop:config>
```

## SpringMVC

### MVC

&emsp;&emsp;MVC是一种软件架构的思想，将软件按照模型、视图、控制器来划分

- M：**Model**，模型层，指工程中的JavaBean，作用是处理数据
- V：**View**，视图层，指工程中的html或jsp等页面，作用是与用户进行交互，展示数据
- C：**Controller**，控制层，指工程中的servlet，作用是接收请求和响应浏览器

MVC的工作流程：

&emsp;&emsp;用户通过视图层发送请求到服务器，在服务器中请求被Controller接收，Controller调用相应的Model层处理请求，处理完毕将结果返回到Controller，Controller再根据请求处理的结果找到相应的View视图，渲染数据后最终响应给浏览器

### 配置DispatcherServlet

&emsp;&emsp;可以在web.xml中注册DispatcherServlet，并在每个Servlet对应的xml中配置Servlet。也可以通过以下方式配置：

1. 在web.xml注册DispatcherServlet

```xml
<web-app>      
    <servlet>
        <servlet-name>DispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring-mvc.xml</param-value>
            <!--配置文件路径-->
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>DispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
        <!--不要匹配到jsp-->
    </servlet-mapping>
</web-app> 
```
2. 在spring-mvc.xml中配置其他Servlet

### 编写Controller

&emsp;&emsp;一个简单的Controller的样例如下：

```java
@Controller
public class myController {
    @RequestMapping(value = "/hello", method = RequestMethod.POST)
    public String hello(){
        System.out.println("There is a hello request");
        return "Hello World --doudi";
    }
}
```

Controller的相关注解：

```java
// 绑定请求与方法
@RequestMapping()
// value/path：请求地址
//     支持ant风格路径："?"（任意单个字符）、"*"（任意个任意字符）和"**"（任意层目录）
//     路径占位符：使用{variable}作为占位符，访问时通过路径传参（restful风格），只能通过@PathVariable获取参数值
// method：请求方法，默认匹配所有方法
//     还可使用四个派生注解：@GetMapping @PostMapping @PutMapping和@DeleteMapping
// params：请求参数
//     支持"param"（含有某参数）、"!param"（不含有某参数）、"param=value"和"param!=value"
// headers：请求头，使用方法类似params
// consumes：ContentType（请求体数据格式）
// produces：Accept（客户端支持的响应体数据格式）
```

### 获取请求参数

0. 通过ServletAPI获取请求参数

     ```java
     @RequestMapping(value = "/hello", method = RequestMethod.POST)
     public String hello(HttpServletRequest req, HttpServletResponse resp, HttpSession session){
     // 然后都会了吧
         return "Hello World --doudi";
     }
     ```

1. 通过控制器方法获取参数：直接在形参列表添加请求参数（如有需要，可通过@RequestParam修改参数名）

   ```java
   @RequestParam(value="", required=false, defaultValue=0)
   ```

2. 五大注解

   ```java
   @RequestParam   // 请求参数
   @RequiredHeader // 请求头
   @RequestBody    // 请求体
   @CookieValue    // Cookie
   @RequestPart    // 用于文件上传的复合型请求体      
   ```

3.   RESTFul风格的请求参数

    ```java
    @PathVariable      // RESTFul风格的请求参数
    @MatrixVariable    // 矩阵变量，获取RESTFul风格请求参数中的附加请求字段
    ```

4. 通过POJO实体对象：形参直接写对象（仅限请求体）

### 域对象共享数据

0. 使用ModelAndView向request共享数据

   ```java
   // 这是个Contrller的方法
   public ModelAndView modelAndView(){
       ModelAndView mav = new ModelAndView();
       // 添加属性
       mav.addObject("name", "value");
       // 设置视图
       mav.setViewName("viewName");
       return mav;
   }
   ```

1. 使用Model/Map/ModelMap向request共享数据（将Model作为形参）

2. 使用ServletAPI向Request/Response/Session共享数据

### SpringMVC的视图

> 这里不想学了，以后用到了再补吧

### 静态资源

开启mvc对静态资源的访问：

```xml
<mvc:defaultservvlet-handler />
<!-- 开放静态资源 -->
<mvc:annotation-driven />
<!-- 开启注解驱动 -->
```

### HttpMessageConvert

&emsp;&emsp;HttpMessageConvert，报文信息转换器，用于将请求报文/响应报文与Java对象相互转换。

#### 请求报文

&emsp;&emsp;@RequestBody可用于获得请求体，注解一个String形参可获得请求体：

```java
PostMapping("/helllo")
    public String post( @RequestBody String requestBody){
        return "Hello World" + requestBody;
    }
```

&emsp;&emsp;RequestEntity封装了请求报文，添加此形参后可用于获取请求报文。

#### 响应报文

&emsp;&emsp;@ResponseBody注解一个方法可将该方法的返回值作为响应体。如果需要返回一个对象，需要使用jakson-databind将其转换成json格式的数据：

> - 导入依赖
> - 开启注解驱动
> - 添加@ResponseBody注解

&emsp;&emsp;ResponseEntity封装了响应报文，可用于将数据响应给用户。

### 拦截器

&emsp;&emsp;SpringMVC的拦截器用于拦截请求，位于DispatcherServlet与Controller之间。需要实现HandlerInterceptor接口或继承HandlerInterceptorAdapter类。

#### 配置拦截器

&emsp;&emsp;可以在配置文件中配置拦截器：

```xml
<mvc:interceptors>
    <bean class=""></bean>
</mvc:interceptors>
```

&emsp;&emsp;也可在配置类中配置拦截器

```java
@Configuration
class configure implements WebMvcConfigurer{
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new myInterceptor)      // 所添加的拦截器
                .addPathPatterns()                      // 拦截的请求
                .excludePathPatterns();                 // 不拦截的请求
    }
}
```



#### 编写拦截器

&emsp;&emsp;根据需要重写以下方法：

> - preHandle()：执行Controller前
> - postHandle()：执行Controller后生成视图前
> - afterCompletion()：生成视图后

## SpringBoot

### 服务启动

&emsp;&emsp;被@SugwApplication注解的类会被当作起动器并在SpringBoot项目起动时调用其main方法，如：

```java
@SpringBootApplication
public class SugwApplication {
    public static void main(String[] args) {
        SpringApplication.run(SugwApplication.class, args);
    }
}
```

### SpringBoot配置文件

&emsp;&emsp;SpringBoot 的配置文件为 resources/application.properties ，可使用其他格式，建议为YAML

&emsp;&emsp;要想获得完整的支持，需导入 spring-boot-configuration-processor 。

> 建议参考[官方文档](https://docs.spring.io/spring-boot/docs/2.5.10/reference/html/application-properties.html#appendix.application-properties)，很短的

&emsp;&emsp;YAML 除了用来编写 SpringBoot 的主配置文件外，还可将属性注入到其他配置类中，如：

```java
@ConfigurationProperties(prefix = "zml")
// 与 application.yml 中的类 zml 匹配
@PropertySources(value = "classpath:zml.yml")
// 指定配置文件，且需要 @value($(variable)) 取出变量值手动赋值
public class zml{
    private String name;
    private Date birthday;
    private List hobby;
    private map score;
    private boolean beautiful;
}
```

```yaml
zml:
    name: Monroe
    birthday: 1993/1/11
    # hobby: [sing, dance]
    hobby:
        - sing
        - dance
    # score: {Chinese: 128, Math: 72, English: 130}
    score: 
        Chinese: 128
        Math: 72
        English: 130
    beautiful: true
```

&emsp;&emsp;@ConfigurationProperties 还支持其他功能：

> - 松散绑定：first_name == firstName
> - JSR303 数据校验：开启 @Validated 后可对某些字段开启数据类型/格式校验

### 配置类

&emsp;&emsp;Spring 支持将 xml 配置文件中所有配置转移到配置类中，实现完全注解开发。

```java
@Configuration(proxyBeanMethods = true)   // 是否以单例模式获取组件
@ComponentScan                            // 包扫描范围
@Import                                   // 导入组件
@Conditional*                             // 根据条件装配组件
@ImportResource                           // 导入 XML 配置文件中的组件
@EnableConfiguration                      // 从配置文件自动注入并注册组件
public class MyConfig{
    @Bean    // 以方法名为 id 创建 Bean
    public User user0(){
        return new User();
    }
}
```

> 除了编写自定义的配置类，还可将配置写在主程序类

### Web开发

#### 静态资源

&emsp;&emsp;SpringMVC 会扫描 /static，/public，/resources，/META-INF/rsources 下的静态资源。

> 注意请求优先级为动态资源>静态资源

&emsp;&emsp;默认情况下静态资源“位于根目录下，若需要修改访问路径和扫描目录可以通过以下设置修改：

```yaml
spring: 
    mvc: 
        static-path-pattern: /static/**
        # 修改访问路径
    resources: 
        static-locations: classpath:/res/
        # 修改静态资源路径
```

&emsp;&emsp;还可访问 /webjar 下的 jar 包

## JWT

### JWT结构

&emsp;&emsp;JWT令牌由三部分组成，结构为Header.Pay咯啊的。Signature，具体为：

- 标头（Header）：由令牌类型（JWT）和加密方式（HS256/RS256）经Base64编码得到

  ```json
  {
      "alg": "HS256",
      "typ": "JWT"
  }
  ```

- 荷载（Payload）：由有关实体（通常为用户）和其他数据经Base54编码得到

- 签名（Signature）：由前两部分与随机盐（密钥）经标头中的加密方式加密得到

### JWT原生使用

0. 引入依赖com.auth0.java-jwt

1. 生成token

   ```java
   String token = JWT.create()
       .withHeader()        // 标头
       .withClaim()         // 荷载
       .sign()              // 签名
       .withExpiresAt();    // 过期时间
   ```

2. 验证

   ```java
   JWTVerifier verifier = JWT.require().build();    // 创建验证对象
   ```

## Spring Security

>&emsp;&emsp;Spring Security is a powerful and highly customizable authentication and access-control framework. It is the de-facto standard for securing Spring-based applications.
>
>&emsp;&emsp;Spring Security is a framework that focuses on providing both **authentication** and **authorization** to Java applications. Like all Spring projects, the real power of Spring Security is found in  how easily it can be extended to meet custom requirements

&emsp;&emsp;Spring Security 的前身为 Acegi，经不断迭代最终成为一个功能强大的重型身份认证和用户授权框架。当然，由于 Spring Security 过于庞大，有时会选用轻量级的 Shiro。即“SSM + Shiro”或“Spring Boot/Cloud + Spring Security”。

### 过滤器链

&emsp;&emsp;Spring Security本质为一个过滤器链