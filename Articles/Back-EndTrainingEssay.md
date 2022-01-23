# 后端组培训笔记

## Week 0x00

## 2022.1.5

### 第0x00次培训

面向过程：

> 过程：没有返回值的函数
>
> 函数：有返回值的函数

面性对象：

> 面向函数，去除“过程”

#### OOP专有名词：

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
>      public static void main(String[] args) {
>            HashMap<Integer, String> hashMap = new HashMap<>();
>            hashMap.put(0, "One");
>            hashMap.put(1, "Two");
>            hashMap.put(2, "Three");
>            for (Integer i : hashMap.keySet()) {
>                if ((i & 2) == 0)       // Java不支持int和boolean的直接相互转化
>                    hashMap.remove(i);
>            }
>            System.out.println(hashMap.values());
>      }
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
> (缺省)：包内可见

> **static**：属于类，同类所有对象共用
>
> > 可以在没对象的时候使用static的方法
>
> **final**：类似const（const是个以后可能会被用到的保留字）
>
> > 修饰属性时禁止修改，修饰方法时禁止重写，修饰类时禁止继承

#### 方法重载

> 同类中名称相同、参数列表不同的方法会根据所给参数重载合适的方法
>
> 形参不同不算
>
> Java5开始的不定数量方法组成的重载：
>
> ```java
> public void show(String ...str){}	// 新写法
> public void show(String[] str){}	// 旧写法
> ```
>
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
> 我们在栈区储存了myGirlfriend，这是一个~~指针~~引用，~~z这不就是个指针吗~~这是引用，指向堆区的一块内存，这里储存了一个Girl类型的对象。
>
> 判断对象是否想等的方法equals()比较的是引用对象的地址是否想等，因此初始化两个一模一样的对象也是不想等的

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
>  @Override
> 	public void run(){
>      ...
>  }
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

#### 接口：

> 一种“完全”的抽象类，字段默认强制public static final，可以有public或private方法（java9开始）Java8开始可以有默认方法（default）并可被实现

接口与抽象类的区别与联系：

|          |       抽象类       |                             接口                             |
| :------: | :----------------: | :----------------------------------------------------------: |
| 构造函数 |         有         |                             没有                             |
|   字段   |       无限制       |                默认且强制public static final                 |
|   方法   |   可以没抽象方法   | 必须为公共抽象方法（8开始可实现默认方法、静态方法，9开始可以有私有方法）（暂时没有protected方法） |
|   子类   | 符合Java单继承规则 |    一个类可实现多个方法（默认方法可能出现“菱形继承”问题）    |

#### 关于多继承的讨论：

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
