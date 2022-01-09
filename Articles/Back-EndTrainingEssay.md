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
> > 因此类至少要有一个public的方法
>
> **继承**：子类继承（extends）父类的字段和方法
>
> > Java不允许多继承，但允许多重继承
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

### *HashMap的线程不安全*

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
> **private**：仅自己的方法可见
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

#### 对象的h生成与销毁

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
>     private static Singleton instance = new Singleton();  
>     private Singleton (){}  
>     public static Singleton getInstance() {  
>     	return instance;  
>     }  
> }
> // 优点：代码简单，线程安全
> // 缺点：可能产生垃圾类
> ```
>
> 
