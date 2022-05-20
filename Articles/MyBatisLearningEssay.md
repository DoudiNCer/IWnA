# MyBatis

&emsp;&emsp;MyBatis是一款优秀的持久层框架，它支持自定义 SQL、存储过程以及高级映射。MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。

&emsp;&emsp;MyBatis需要以下依赖：

> - mybatis
> - jdbc
> - junit
> - log4j（日志记录，可选）

## MyBatis配置文件

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

## Mapper

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

## 获取参数值

&emsp;&emsp;可以使用#{}（本质为占位符替换）或${}（本质为字符串拼接）获取方法的参数。当仅有一个参数时，{}内可使用任意名称；当有多个参数时可通过arg0，arg1或param1，param2访问对应参数（或在调用时通过@Param注解指定参数名，此时arg0，arg1等失效）。

&emsp;&emsp;如果参数是一个Map,可通过键名来访问；如果参数是一个对象，可通过属性名访问（要求有get方法）。

## 查询结果的处理

&emsp;&emsp;查询结果为单个实体对象时可用实体对象接收，查询结果为多个时需要使用List接收。

&emsp;&emsp;也可用@MapKey指定数数据表主键作为key,以Map保存查询结果。

## 自定义映射

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

## 动态SQL

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

## MyBatis缓存

### 一级缓存

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

### 二级缓存

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

### 缓存查询顺序

- 先二后一
- 一无查库
- 一死入二

## MyBatis逆向工程

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

## MyBatis分页

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


## 