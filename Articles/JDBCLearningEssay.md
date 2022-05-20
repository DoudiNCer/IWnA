# JDBC

> 关于MySQL和SQL语法，请参阅[MySQL学习笔记](MySQLLearningEssay.md)。

&emsp;&emsp;JDBC~~（Java Database Connectivity）~~是一个独立于特定数据库管理系统，通用的数据库存取操作的应用编程接口。

## 建立连接

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

## 操作和访问数据库

&emsp;&emsp;操作和访问数据库的方式有以下三种：

> - Statement：用于执行静态SQL语句并返回执行结果对象。
>
>   由于存在SQL注入等安全问题和操作繁琐等缺点，~~已不再使用~~。
>
> - PreparedStatement：用于存储预编译的SQL语句，可多次高效地执行这些SQL语句
>
> - CallableStatement：用于执行SQL存储过程。

### PreparedStatement的使用

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

## 事务操作

**事务**：使数据从一种状态转换到另一种状态的一组逻辑操作单元
**事务操作**：保证所有操作都作为一个工作单元处理。在执行事务的时候，要么所有事务都被提交（commit），要么数据库管理系统放弃所有操作，整个事务回滚（rollback）。

### 事务的ACID属性

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

## 数据库连接池

&emsp;&emsp;在以前的开发中，需要访问数据库时就创建连接，操作完成后就断开连接，具有连接利用率低下、资源消耗量大、安全性差（可能造成内存泄露）、资源使用不可控等缺点，因此我们引入数据库连接池统一管理数据库连接。

&emsp;&emsp;Java的数据库连接池用javax.sql.DataSource表示，该接口由不同的厂商进行实现，常见的有DBCP、C3P0、Druid、Proxool、BoneCP等。

### C3P0

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

### DBCP

&emsp;&emsp;DBCP的使用与C3P0类似，导入jakarta.commons.dbcp，使用BasicDataSource获取数据库连接池或使用BasicDataSourceFactory.createDataSouece()根据配置文件创建数据库连接池。

### Druid

&emsp;&emsp;国内目前广泛使用的是阿里巴巴的Druid（德鲁伊）数据库连接池，其使用方法与DBCP类似，使用DruidDataSoueceFactory工厂根据配置文件获取数据库连接池。

## Apache-DBUtils

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

## 