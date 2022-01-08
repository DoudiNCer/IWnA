# 后端组培训笔记

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
