# Qt学习笔记

&emsp;&emsp;Qt 是一个纯 C++ 开发的跨平台的 C++  GUI 程序开发框架，当然 Qt 也支持其他语言，如 Python 等。

> - 此笔记为使用 C++ 的 Qt 学习笔记，阅读前请先学习 C++ 的基础知识。
> - 由于 Qt 涉及的类与函数过多，本笔记不可能将其完全罗列，学习的过程中还需多翻翻帮助手册（Qt Assistant）。

## Qt项目

&emsp;&emsp;一个简单的 Qt 应用程序的示例如下：

```c++
// hello.cpp
#include <QApplication>
#include <QLabel>

int main(int argc, char* argv[]) {              // 复习一下这俩参数是干啥的
	QApplication myApp(argc, argv);             // 创建QT应用程序
	QLabel label("Hello World!");               // 创建一个QLabel对象
	label.show();                               // 显示这个QLabel对象
	return QApplication::exec();                // 运行QT应用程序，进入Qt事件循环
}
```

&emsp;&emsp;然后进行编译三连击：

```shell
qmake -project        # 创建项目文件
qmake                 # 生成Makefile
make                  # make编译
```

## 窗口

&emsp;&emsp;为了同一管理控件，可将控件停靠到指定的窗口上，此时这些控件将作为子窗口束缚到该父窗口内，与父窗口一起移动、隐蔽或关闭。

&emsp;&emsp;要想让一个控件停靠到某窗口上，需将该窗口的引用传入控件的构造函数。

&emsp;&emsp;QWidge及其子类可作为窗口使用，常用的窗口类有：

- QWidget
- QMainWindow：常用作主窗口
- QDialog：常用作对话框

&emsp;&emsp;父窗口的show函数和析构函数会自动调用子窗口的show函数和析构函数。

### QWidget

&emsp;&emsp;QWidget是所有窗口的超类，继承自 QObject 和 QPaintDevice 。使用QWidget需要引入QWidget头文件并在项目文件中添加 QT += widgets 。

### QMainWindow

### QDialog

## 信号与槽

&emsp;&emsp;信号（Signal）和槽（Slot）是Qt的一种通信机制，实现对象间的数据交互。

### 信号和槽的连接

&emsp;&emsp;可通过QObject的connect函数连接信号和槽：

```c++
QObject::connect(myButton, SIGNAL(clicked()), &myApp, SLOT(quit())); 
// 分别对应发送者、信号、接收者3、槽函数，其中信号和槽函数需通过SIGNAL()和SLOT()宏函数转换后才能使用
```

&emsp;&emsp;一般来说，信号函数与槽函数的参数保持一致。信号函数参数可多于槽函数，多余参数会被忽略。

&emsp;&emsp;信号与槽可进行一对一、一对多、多对一连接且触发顺序不确定。也可以信号与信号进行连接，实现消息传递。

### 自定义信号函数和槽函数

&emsp;&emsp;有些时候需要自定义信号函数和槽函数，自定义的信号函数需要使用signals修饰，槽函数需要slots修饰，并且在声明前添加Q_OBJECT宏，如：

```c++
// CalcMainWindow.h
//...
Q_OBJECT 
public slots:
	void equClicked(void);
signals:    // 信号函数只能声明，不能定义
	void mySignal(const QString&);
//...
```

&emsp;&emsp;信号函数通过 emit 宏触发（也可以直接调用），因此也需要 Q_OBJECT 宏如：

```c++
emit mySignal(str);
```

&emsp;&emsp;槽函数类似普通函数，在其内部完成所需操作即可。

## Qt Designer

&emsp;&emsp;为了简化设计UI的步骤，我们可以使用Qt Designer来完成UI设计。生成的ui文件可通过uic转换成对应的头文件，且该头文件的setupUI()方法可根据名称自动装配槽函数。

&emsp;&emsp;Qt Designer生成的ui文件本质上是一个XML文件，可通过uic命令转换为对应的头文件或由make进行手动转换。

&emsp;&emsp;在使用生成的代码时，可直接使用一个子类继承生成的类（is a）或将生成的类作为属性加入到窗口类中（has a），现主要使用后者。

## 事件

&emsp;&emsp;事件（event）是由系统或者 Qt 本身在不同的场景下发出的。当用户按下 / 移动鼠标、敲下键盘，或者是窗口关闭 / 大小发生变化 / 隐藏或显示都会发出一个相应的事件。一些事件在对用户操作做出响应时发出，如鼠标 / 键盘事件等；另一些事件则是由系统自动发出，如计时器事件。

### 事件的处理流程

&emsp;&emsp;在 Qt 中，所有事件会被封装成对象，所有的事件继承自 QEvent 抽象类。当当事件发生时，会调用 QObject 的虚函数event()并将事件作为参数传入。

### 常用事件

#### 绘图事件

&emsp;&emsp;通过重写绘图事件（ QPaintEvent ）的 paintEvent() 方法可自定义图像绘制，绘图事件的触发条件有：

- 启动程序后窗口第一次显示
- 窗口由隐蔽变为看见
- 窗口最小化/正常/最大化之间的切换
- 窗口尺寸改变
- QWidget 的 update 或 repaint 方法被调用

#### 定时器事件

&emsp;&emsp;Qt 中实现定时执行某操作可通过 QObject 提供的定时器事件或 QTimer 提供的定时器信号实现。通过定时器事件实现定时器的方法如下：

```c++
virtual void mousePressEvent(QMouseEvent *e);virtual void mousePressEvent(QMouseEvent *e);int timerId = startTimer(interval, timerType);   // 开启定时器
/* 
    以interval毫秒为周期执行事件，返回值为定时器ID，0表示定时器开启失败
    可选参数timerType表示定时器精确度：
        PreciseTimer为精确到毫秒
        CoarseTimer为误差在5%内virtual void mousePressEvent(QMouseEvent *e);virtual void mousePressEvent(QMouseEvent *e);
        VeryCoarseTimer为四舍五入到秒
 */
void QObject::timerEvent(QTimerEvent*);        // 要重写的定时器处理函数
kullTimer(timerId);                            // 关闭定时器
```

#### 键盘鼠标事件

&emsp;&emsp;QWidget 定义了以下鼠标事件（ QMouswEvent ）处理函数：

```c++
virtual void mousePressEvent(QMouseEvent *e);
virtual void mouseReleaseEvent(QMouseEvent *e);
virtual void mouseDoubleClickEvent(QMouseEvent *e);
virtual void mouseNoveEvent(QMouseEvent *e);
```

&emsp;&emsp;和以下的键盘事件处理函数：

```c++
virtual void keyPressEvent(QKeyEvent e);
virtual void keyReleaseEvent(QKeyEvent e);
```

## Qt数据库操作

&emsp;&emsp;Qt 封装了常见数据库的操作，引入 QSqlDatabase 头文件并在 Qt 项目中添加`Qt += sql`可开启对数据库的支持。

### 数据库连接

&emsp;&emsp;QSqlDatabase 类用于建立数据库连接：

```c++
QSqlDatabase db = QSqlDatabase::addDatabase("QPSQL");
// 有JDBC内味了，还是四大参数：驱动、数据库地址、用户名、密码。
db.open();
db.close();
```

### 执行SQL语句

&emsp;&emsp;使用 QSqlQuery 编写执行 SQL 语句并获取查询结果，使用 QSqlQueryModel 执行SQL语句并获取只读的查询结果集。

```c++
QSqlQuery query("SELECT name FROM Student");
// 字符串拼接
QSqlQuery query;
query.prepare("select * from Student where id = :stuId");
// 占位符替换
query.bindValue(":stuId", 3);
query.exec();
while (query.next()) {
    QString stuName = query.value(1).toString();
    QDebug() << stuName;
}
```

