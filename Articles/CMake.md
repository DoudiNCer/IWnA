# CMake教程

>CMake is a tool to manage building of source code. Originally, CMake was designed as a generator for various dialects of `Makefile`, today CMake generates modern buildsystems such as `Ninja` as well as project files for IDEs such as Visual Studio and Xcode.
>
>CMake is widely used for the C and C++ languages, but it may be used to build source code of other languages too.

&emsp;&emsp;简单来说，CMake是一个开源的跨平台构建脚本生成工具，它可以通过CMakeList.txt生成Makefile或其他编译脚本，再通过相应的自动化编译工具编译源代码、链接库文件、生成程序并提供安装脚本。

## cmake命令

> 具体可参阅[CMake(1)manual](https://cmake.org/cmake/help/latest/manual/cmake.1.html)

## CMakeLists

&emsp;&emsp;cmake 通过读取 CMakeLists.txt 文件来确定如何处理代码

### CMakeLists 指令

```cmake
cmake_minimum_required(VERSION 3.21)            # 所需最低cmake版本
project($projName)                              # 项目名称
#[[
    默认支持所有语言，否则可在括号内添加所支持的语言，如CXX、JAVA
    project()隐式声明了<projectname>_BINARY_DIR和<projectname>_SOURCE_DIR
    以及PROJECT_BINARY_DIR和PROJECT_SOURCE_DIR
]]

set(CMAKE_CXX_STANDARD 14)                      # 用于设置变量
#[[
    常用的变量：
    
]]

message($type $str)                             # 向控制台发送的信息
#[[
    信息的等级有三种：
        SEND_ERROR：错误信息，会跳过生成过程
        SATUS：一般提示信息
        FATAL_ERROR：严重错误信息，会立刻终止cmake运行
]]

include_directories($headerPath)                # 头文件搜索路径

link_directories($libPath)                      # 非标准共享库搜索路径

target_linked_libraries($execName $libName)     # 添加标准共享库

add_executable($execName $(SRC_LIST))           # 生成可执行文件，SRC_LIST可不加扩展名

find_package($pkgName REQUIRED)                 # 所需要的库，REQUIRED表示此库是必需的

```

### CMakeLists语法

- 语法类似Shell（你这Win和Linux反复横跳呢），如\$()和\${}
- 参数之间可用逗号或空格，因此含有空格的参数需要使用引号包围
- 指令大小写不敏感

## CMake项目

&emsp;&emsp;为了让我们的代码看起来更像一个项目，我们可以整理一下：

```shell
.
├── build
├── CMakeLists.txt
├── doc
│   └── index.md
├── lib
│   └── CMakeLists.txt
├── README.md
└── src
    ├── CMakeLists.txt
    └── main.cpp
```

&emsp;&emsp;./CMakeLists.txt

```cmake
project(myProj)
add_subdirectory(src bin)                       # 指定子cmake工程和编译目标目录

INSTALL($type $files DESTINATION $dir)          # 配置程序安装
#[[
    $type 包括以下几类：
        FILE：与程序运行无关的文档等，
        PROGRAMS：非目标程序的可执行文件（如脚本）
        DIRECTORY：目录
        TARGETS：编译目标产物，安装动态库时使用 LIBRARY DESTINATION，静态库使用ARCHIVE DESTINATION。
    $dir 使用相对路径时在 $CMAKE_INSTALL_PREFIX 下（默认为 /usr/local ）寻找
]]
```

## 库文件

&emsp;&emsp;静态库在编译链接时会整合进程序，文件名一般以a或lib结尾；动态库独立于程序存在，文件名一般以so或dll结尾。

```cmake
add_library($libName $type $src)                # 编译库
#[[
    生成的库是文件名会添加 lib 前缀和扩展名
    $type 分为 SHARED（动态库）和 STATIC（静态库）
    不可同时构建 $libName 相同的静态库和动态库，以第一个出现的为准
]]

set_target_properties($libName PROPERTIES {$key $value})  # 设置目标文件属性
#[[
    常见的$key：
        OUTPUT_NAME：输出文件名
        CLEAN_DIRECT_OUTPUT：为1时清理已存在的文件
        VERSION：动态库版本
        SOVERSION：API版本
]]
```

