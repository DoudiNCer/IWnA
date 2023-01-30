# Maven 学习笔记

&emsp;&emsp;Apache Maven 是一个基于项目对象模型（Project Object Model，POM）概念的 Java 项目管理工具。Maven 可以对项目进行依赖管理、构建、发布等操作。

## 项目与POM

&emsp;&emsp;Maven 项目的结构如下：

```shell
.
├── pom.xml                 // 项目 POM 配置文件
├── src            
│   ├── main
│   │   ├── java            // 项目源代码
│   │   ├── resources       // 项目资源文件
│   │   └── webapp
│   │       └── WEB-INF     // Web 应用目录
│   └── test
│       ├── java            // 测试代码
│       └── resources       // 测试资源文件
└── target                  // 构建输出
    ├── classes             // 编译输出
    └── test-classes        // 测试编译输出-

```

&emsp;&emsp;POM 文件包含了项目的各种信息，如依赖、构建方式等，它是一个 XML文档,，其[详细用法](MavenLearningEssay.assets/pom.xml)可供参考。

> 0. `SNAPSHOT`表示尚处于开发中的版本，通常在项目开发过程中使用`SNAPSHOT`的依赖，投入生产环境时使用正式的`Release`依赖-

## 项目构建

&emsp;&emsp;Maven 项目的生命周期如下：

| 阶段                  | 处理     | 描述                                                     |
| :-------------------- | :------- | :------------------------------------------------------- |
| 清理 clean            | 清理构建 | 移除所有上一次构建生成的文件                             |
| 验证 validate         | 验证项目 | 验证项目是否正确且所有必须信息是可用的                   |
| 编译 compile          | 执行编译 | 源代码编译在此阶段完成                                   |
| 测试 test             | 测试     | 使用适当的单元测试框架（例如JUnit）运行测试。            |
| 包装 package          | 打包     | 创建JAR/WAR包如在 pom.xml 中定义提及的包                 |
| 检查 verify           | 检查     | 对集成测试的结果进行检查，以保证质量达标                 |
| 安装 install          | 安装     | 安装打包的项目到本地仓库，以供其他项目使用               |
| 构建项目文档站点 site | 构建文档 | 生成项目的站点文档                                       |
| 部署 deploy           | 部署     | 拷贝最终的工程包到远程仓库中，以共享给其他开发人员和工程 |

&emsp;&emsp;当执行某个步骤时，其先前的步骤也会依次执行，比如执行以下命令将依次执行`package`及以前的步骤：

```bash
mvn package
```

&emsp;&emsp;Maven 使用原型 archetype 插件创建项目，构建一个简单项目的命令如下：

```bash
mvn archetype:generate "-DgroupId=com.companyname.bank" "-DartifactId=consumerBanking" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"
```

> 0. 一般构建项目靠 IDEA 等完成，谁学这玩意啊

## 仓库

&emsp;&emsp;仓库用于存储依赖和发布构建好的产品，Maven 的仓库分为本地仓库、中央仓库和远程仓库，搜索依赖时按照以上顺序搜索。

&emsp;&emsp;本地（local）仓库默认位于`~/.m2/repository`，在第一次运行 mvm 命令时创建。从中央仓库或远程仓库下载的依赖会储存在本地仓库。

&emsp;&emsp;中央（center）仓库由 Maven 社区提供，包含了绝大多数开源 Java 构件、源码等。可通过 http://search.maven.org/#browse 搜索中央仓库中的依赖。

&emsp;&emsp;远程（remote）仓库是团队或企业自己搭建的仓库。在 `pom.xml`中`<project>`标签下进行如下配置：

```xml
<repositories>
    <repository>
        <id>companyname.lib1</id>
        <url>http://download.companyname.org/maven2/lib1</url>
    </repository>
    <repository>
        <id>companyname.lib2</id>
        <url>http://download.companyname.org/maven2/lib2</url>
    </repository>
</repositories>
```

