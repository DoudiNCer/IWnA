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
> public void show(String ...str){}	// 新写法
> public void show(String[] str){}	// 旧写法
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

> 基本数据类型如int,double,char等不是对象，这不符合Java“一切皆对象”的哲学思想。
>
> 然后就有了Integer,Double,Character等包装类。
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
>  	private static Singleton instance = new Singleton();  
>  	private Singleton (){}  
>  	public static Singleton getInstance() {  
>  		return instance;  
>  	}
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
> > >    	A a = (A)b;
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
>  	@Override
> 	public void run(){
>      	...
>  	}
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
Func func1 = System.out::println;	// 没有括号诶！
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
clazz.newInstance()；	// 调用空参构造器
```

```java
Constructor<T> constructor = clazz.getDeclaredConstructor();	// 获取含参构造器
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
Method declaredMethod = clazz.getDeclaredMethod(methodName, parameterTypes[]);	// 没有参数可以不写
```

~~然后慢慢get吧~~

#### 调用运行时类的指定结构

##### 调用属性

```java
field.setAccessible(true);	// 启用访问非public属性
field.get(obj);	
field.set(obj,value);
```

##### 调用方法

```java
method.setAccessible(true);	// 启用访问非public方法
method.invoke(obj, parameters[]);	// 没有参数可以不写
```

## 以下为Java Web内容

## File&IO流

&emsp;&emsp;Java通过File对象来操作文件或文件夹

### 初始化File对象

```java
String path = new String();			// 可使用java.io.File.separator代替分隔符
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
file.renameTo(new File(newName));				// 重命名
file.createNewFile();							// 新建
dir.makedir();									// 创建文件夹
dir.makedirs();									// 递归创建文件夹
file.delete();									// 删除
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
File file = new File(path);							// 初始化文件
FileReader fr = new FileReader(file);				// 初始化文件输入字符流
fr.read();											// 迭代读取文件单个字符（以EOL表示文件结尾）
chrr[] cbuf = new char[len];
fr.read(cbuf);										// 将读取内容写入字符数组
fr.close();											// 关闭文件流（物理连接无法被GC）
FileWriter fw = new FileWriter(file, true/false);	// 初始化文件写入字符流（指定是否为追加模式）
fw.write(str);										// 写入（文件不存在会自动创建）
fw.close();
```

> 注意：读写二进制文件需使用文件字节流

### 缓冲流

&emsp;&emsp;可以使用缓冲流对其他流进行“包装”以实现加速读写等功能。

```java
FileReader fr = new FileReader(file);
BufferedReader bfr = new BufferedReader(fr);
bfr.read();
bfr.readLine();											// 不含换行符，以null表示文件结尾
bfr.close();

FileWriter fw = new FileWriter(file,true);
BufferedWriter bfw = new BufferedWriter(fw);
bfw.write(str);
bfw.newLine();											// 插入换行符
bfw.flush();											// 刷新缓冲区（强制写入）
bfw.close();											// 关闭由外向内（自动关闭呢）
```

### 转换流

&emsp;&emsp;转换流用于将输入/输出字节流转换成字符流，包括InputStreamReader和OutputStreamWriter。

> 注意，转换流属于字符流

```java
FileInputStream frs = new FileInputStream(file);
InputStreamReader fir = new InputStreamReader(frs, StandardCharsets.UTF_8);	// 指定彼岸
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
ois.readObject();													// 需要强制类型转换
ois.close();
// 序列化
ObjectOutputStream oos = new ObjectOutputStream(outputStream);
oos.writeObject(obj);
oos.flush();														// 需要手动flush
oos.close();
```

#### 序列化自定义类

```java
class myClass implements Serializable{						// 实现Serializable接口
    private static final long serialVersionUID = 42L;		// 提供序列化版本号
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
RandomAccessFile raf = new RandomAccessFile(file,mode);		// mode表示访问模式
// r：只读
// rw：读写
// rwd：读写，同步文件内容
// rws：读写，同步文件内容与元数据
raf.seek(position);											// 修改访问指针位置
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
HttpURLConnection urlConnection = (HttpURLConnection) url.openConnection();	// 获取URL连接
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
@author			// 作者
@version		// 版本
@see			// 参考
@since			// 首次出现的版本
@param			// 参数说明
@return			// 返回值说明
@exception		// 异常说明
```

### 编译检查注解

```java
@Override			// 重写超类方法
@Deprecated			// 已过时
@SuppressWarning	// 抑制编译期警告
@SafeVarargs		// 忽略任何使用参数为泛型变量的方法或构造函数调用产生的警告。（J7）
```

### 自定义注解

```java
public @interface MyAnnotation {
}
```

### 元注解

&emsp;&emsp;元注解是用来修饰注解的注解，Java提供的元注解包括以下四个：

```java
@Retention		// 修饰注解生命期
RetentionPolicy.SOURCE	// 源代码保留
RetentionPolicy.CLASS	// class保留（默认）
RetentionPolicy.RUNTIME	// 运行时保留

@Target			// 限定修饰对象
CONTRACTOR				// 构造器
FIELD					// 属性
LOCAL_VARIABLE			// 变量
METHOD					// 方法
PACKAGE					// 包
PARAMETER				// 参数
TYPE					// 类、接口、enum
TYPE_PARAMETER			// 泛型类型的定义（J8）
TYPE_USE				// 任何使用泛型类型的地方（J8）

@Documented		// 该注解将被javadoc等工具读取（还要求RetentionPolicy.RUNTIME）
@Inherited		// 该注解将被继承
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

&emsp;&emsp;JDBC~~Java Database Connectivity）~~是一个独立于特定数据库管理系统，通用的数据库存取操作的应用编程接口。

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
DriverManager.registerDriver(driver);						// 可省略，会随Driver加载自动完成
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
> - CallableStatement用于执行SQL存储过程。

#### PreparedStatement的使用

&emsp;&emsp;与Statement相比，PreparedStatement使用预编译的SQL语句并通过占位符代替变量，具有以下优点：
> - 解决了SQL注入的安全问题
> - 可操作Blob数组
> - 实现更高效的批量操作

&emsp;&emsp;PreparedStatement对数据库的操作（增删改）基本符合以下的步骤：

```java
String sql = "insert into Beauty(Name) values (?);";	// 以“?”作为占位符
PreparedStatement sm = conn.prepareStatement(sql);
sm.setString(1, value);									// 填充占位符，从1开始
sm.execute();											// 执行SQL语句
sm.close();
```

&emsp;&emsp;如果需要对数据进行查询，可以使用executeQuery()执行SQL语句并获得结果集：

```java
ResultSet rs = sm.executeQuery();
ResultSetMetaData rsMetaData = rs.getMetaData();	// 回去结果集的元数据
rs.close();											// 需要手动关闭
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
       <!--开启属性注解支持-->
       <context:component-scan base-package="com.springtest" use-default-filters="false">
       <!--默认扫描所有类，除非设置了use-default-filters="false"-->
              <context:exclude-filter type="" expression=""/>
              <context:include-filter type="" expression=""/>
       </context:component-scan>
</beans>
```

创建对象

```java
@Component
@Service
@Controller
@Repository
// 四选一，默认以类名首字母小写作为id
```

自动装配

```java
@Autowired					// 自动装配对象，默认byType
@Qualifier(value = "")		// 使@Autowired以byName自动装配对象
@Resource					// 默认byType自动装配对象，给定name后byName自动装配
@Value						// 注入普通类型
```

> 0. 此处不需要set()方法
> 1. @Autowired、@Qualifier和@Value由org.springframework提供，@Resource由javax提供

### AOP

> **AOP**（Aspect Oriented Programming，面向切面编程）：将任务分为多个切面，在各个切面独立实现。
