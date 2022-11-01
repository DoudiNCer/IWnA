## Spring

&emsp;&emsp;Spring是一个开源（[Apache-2.0 License](https://github.com/spring-projects/spring-framework/blob/main/LICENSE.txt)）的**轻量级**的**控制反转**(IoC)和**面向切面**(AOP)的JavaEE框架。

### IoC

&emsp;&emsp;**IoC**（Inversion of Control，控制反转）：将对象的创建和调用交由用户负责，降低代码耦合度。DI（Dependency Injection，依赖注入）是一种常用的IoC实现方法。

&emsp;&emsp;IoC使用的主要技术为XML解析、工厂模式和反射。

#### 创建对象、注入属性

```xml
<bean id="myObj" class="com.example.MyObj" scope="singleton">
<!--“name”可代替"id"，前者值可以含有特殊符号-->
<!--scope用于指定bean的作用域。默认为单例（“static”）“singleton”，此外还有多实例“prototype”、请求“request”、会话“sessiom”和全局会话“global-session”-->
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
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
                           http://www.springframework.org/schema/aop  http://www.springframework.org/schema/aop/spring-aop.xsd">
       <context:component-scan base-package="com.springtest"></context:component-scan>
       <!--开启注解扫描-->
       <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
       <!--开启AspectJ自动生成代理对象-->
   </beans>
   ```

4. 注解增强方法

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

### JDBCTemplate

&emsp;&emsp;JDBCTemplate是Spring对JDBC的封装

#### 依赖

> - druid
> - mysql-connector-java
> - spring-jdbc

#### 配置数据库连接池与JDBCTemplate类

```xml
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource"
      destroy-method="close">
    <property name="url" value="" />
    <property name="username" value="" />
    <property name="password" value="" />
    <property name="driverClassName" value="" />
</bean>
<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
    <property name="dataSource" ref="dataSource" />
</bean>
```

#### 编写DAO

&emsp;&emsp;DAO的基本结构与增删改查的实现方法如下：

```java
public class userDaoImpl implements userDao{
    @Autowired
    private JdbcTemplate jdbcTemplate;
    public int xxxUsers(){
        String sql;
        int update = jdbcTemplate.update(sql, objs);    // 增删改
        String name = jdbcTemplate.queryForObject(sql, String.class, args); // 查询单个数据
       User user = jdbcTemplate.queryForObject(sql, new BeanPropertyRowMapper<User>(User.class), args); // 查询对象或对象列表
        return update;
    }
}
```

&emsp;&emsp;JdbcTemplate提供了一个批量处理的函数：

```java
batchUpdate(String sql, List<Object[]> batchArgs);
```

### 事务操作

&emsp;&emsp;Spring对JDBC事务操作也进行了封装，一般将事务管理添加到Service层中。

&emsp;&emsp;Spring事务管理方式包括编程式事务管理和式事务管理，现普遍使用的为后者。

#### 注解声明式事务管理

0. 在Spring配置文件中配置事务管理器、开启事务注解

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:context="http://www.springframework.org/schema/context"
          xmlns:aop="http://www.springframework.org/schema/aop"
          xmlns:tx="http://www.springframework.org/schema/tx"
          xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/beans/spring-comtext.xsd
                           http://www.springframework.org/schema/aop  http://www.springframework.org/schema/aop/spring-aop.xsd
                           http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">
   
       <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource"
             destroy-method="close">
       <!-- 线程池 -->
           <property name="url" value="" />
           <property name="username" value="" />
           <property name="password" value="" />
           <property name="driverClassName" value="" />
       </bean>
       <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
           <property name="dataSource" ref="dataSource" />
       </bean>
       <bean id="transationManager" class="org.springframework.jdbc.datasouece.DataSourceTransationManager">
       <!-- 事务管理器 -->
           <property name="dataSource" ref="dataSource" />
       </bean>
   
       <tx:annotation-driven transaction-manager="transationManager" />
   </beans>
   ```

   或者使用以下配置类：

   ```java
   @Configuration
   @ComponentScan(basePackage="")
   @EnableTransactionManagement    // 开启事务
   public class TxConfig{
       @Bean
       public DruidDataSource getDruidDataSource(){
           DruidDataSource dataSource = new DruidDataSource();
           dataSource.setDriverClassName("");
           dataSource.setUrl("");
           dataSource.setUsername("");
           dataSource.setPassword("");
           return dataSource;
       }
       @Bean
       public JdbcTemplate getJdbcTemplate(DataSource dataSource){
           JdbcTemplate jdbcTemplate = new JdbcTemplate();
           jdbcTemplate.setDataSource(dataSource);
           return jdbcTemplate;
       }
       @Bean
       public DataSourceTransationManager getDataSourceTransationManager(DataSource dataSource){
           DataSourceTransationManager transationManager = new DataSourceTransationManager();
           transationManager.setDataSource(dataSource);
           return transationManager;
       }
   }
   ```

1. 在Service类或方法上添加@Transactional注解

   ```java
   @Transactional
   /** 常用属性（可选）
     * propagation：事务传播行为
     * isolation：事务隔离级别
     * timeout：超时时间，单位秒，默认-1，不提交就回滚
     * readOnly：是否只读，默认false
     * rollBackFor：出现哪些异常就回滚
     * noRollBackFor：出现哪些异常不回滚
   **/
   ```

#### 配置文件声明式事务管理

0. 在Spring配置文件中配置事务管理器

1. 配置通知、切入点和切面

   ```xml
   <tx:advice id=“txAdvice”>
   <!-- 配置通知 -->
       <tx:attributes>
       <!-- 配置事务参数 -->
           <tx:method name="account*" />
           <!-- 添加事务的规则，Transactional的参数写在这里 -->
       </tx:attributes>
   </tx:advice>
   <aop:config>
       <aop:pointcut id="pt" expression="" />
       <!-- 配置切入点 -->
       <aop:advisor advice-ref="txAdvice" pointcut-ref="pt" />
       <!-- 配置切面 -->
   </aop:config>
   ```


#### 事务传播行为

&emsp;&emsp;事务传播行为（propagation behavior）指的就是当一个事务方法被另一个事务方法调用时，这个事务方法应该如何进行。

|           名称           |                             解释                             |
| :----------------------: | :----------------------------------------------------------: |
|   PROPAGATION_REQUIRED   |  当前方法必须运行在一个事务的中，若不存在事务则新建一个事务  |
| PROPAGATION_REQUIRED_NEW | 当前方法必须运行在自己的新事务中。若当前正在运行事务，则挂起该事务；若使用JTATransactionManager，则需要访问TransactionManager |
|   PROPAGATION_SUPPORTS   |         当前方法可以运行在当前事务中或不运行在事务中         |
| PROPAGATION_NOT_SUPPORTS | 当前方法不能运行在事务中。若当前正在运行事务，则将其挂起；若使用JTATransactionManager，则需要访问TransactionManager |
|  PROPAGATION_MANDATORY   | 当前方法必须运行在事务内部，否则抛出异常IllegalTransactionStateException |
|    PROPAGATION_NEVER     | 当前方法不应该运行在事务中，若当前正在运行事务，则会抛出异常 |
|  PROPAGATION_MANDATORY   | 若当前存在事务，则在嵌套事务中运行该方法，否则新建一个事务。 |

### Spring5新特性

&emsp;&emsp;Spring5支持的最低Java版本为Java8，在此基础上Spring5引入许多新功能并做了一些优化：

#### 整合日志框架

&emsp;&emssp;Spring5框架自带了通用的日志封装，移除了Log4JConfigListener，不再默认支持Log4J,建议使用Log4J2。

&emsp;&emsp;Spring5使用Log4J2的步骤：

0. 引入loh4j2和slf4j

1. 创建log4j2.xml配置文件

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!--日志级别以及优先级排序: OFF > FATAL > ERROR > WARN > INFO > DEBUG > TRACE > ALL -->
   <!-- Configuration 后面的 status 用于设置 log4j2 自身内部的信息输出，可以不设置，当设置成 trace 时，可以看到 log4j2 内部各种详细输出-->
   <configuration status="INFO">
       <!--先定义所有的 appender-->
       <appenders>
           <!--输出日志信息到控制台-->
           <console name="Console" target="SYSTEM_OUT">
               <!--控制日志输出的格式-->
               <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
           </console>
       </appenders>
       <!--然后定义 logger，只有定义了 logger 并引入的 appender，appender 才会生效-->
       <!--root：用于指定项目的根日志，如果没有单独指定 Logger，则会使用 root 作为默认的日志输出-->
       <loggers>
           <root level="info">
               <appender-ref ref="Console"/>
           </root>
       </loggers>
   </configuration>
   
   ```

2. 手动输出日志

   ```java
   public class UserLog{
       private static final Loger logger = LoggerFactory.getLogger(UserLog.class);
       public static void main(String[] args){
           logger.info("This is an info log");
           logger.warn("This is a warning log");
       }
   }
   ```

#### Nullable注解

&emsp;&emsp;@Nullable注解可用于方法、属性和参数，表示方法返回值、属性、参数可为空。

#### 函数式风格Bean注入

```java
// 创建 GenericApplicationContext 对象
GenericApplicationContext context = new GenericApplicationContext();
// 调用 context 的方法对象注册
context.refresh();
//context.registerBean("user1", User.class, () -> new User());
context.registerBean("user1", User.class, User::new);
// 获取在 spring 注册的对象
User user = (User) context.getBean("user1");	// 若未提供名称，需要全路径
```

#### 支持整合JUnit5测试框架

&emsp;&emsp;Spring5开始支持JUnit5并兼容JUnit4。Spring5使用JUnit4的步骤如下：

0. 引入spring-test，hamcrest-core，junit4

1. 以注解的形式创建测试类

   ```java
   @EunWith(SpringJUnit4ClassRunner.class)         // 单元测试框架版本
   @ContextConfiguration("classpath:bean1.xml")    // 配置文件
   public class JTest{
       @Tesr
       public void test(){}
   }
   ```

&emsp;&emsp;Spring5使用JUnit5的步骤如下：

0. 引入JUnit5相关依赖

1. 以注解的形式创建测试类

   ```java
   @ExtendWith(SpringExtension.class)
   @ContextConfiguration("classpath:bean1.xml")
   // 或者使用@SpringJUnitConfig(location = "classpath:bean1.xml")
   public class JTest{
       @Tesr
       public void test(){}
   }
   ```

## SpringWebFlux

&emsp;&emsp;SpringWebFlux是 Spring Framework 5.0中引入的新的响应式web框架。与Spring MVC不同，它不需要Servlet API，是完全异步且非阻塞的，并且通过Reactor项目实现了Reactive Streams规范。

&emsp;&emsp;Spring WebFlux 用于创建基于事件循环执行模型的完全异步且非阻塞的应用程序。

&emsp;&emsp;建议参考[Spring WebFlux官方文档](https://docs.spring.io/spring-framework/docs/current/reference/html/web-reactive.html)。

<img src="https://docs.spring.io/spring-framework/docs/current/reference/html/images/spring-mvc-and-webflux-venn.png"  />

### 相关术语

- 同步：当一个同步调用发出后，调用者要一直等待返回消息（或者调用结果）通知后，才能进行后续的执行；
- 异步：当一个异步过程调用发出后，调用者不能立刻得到返回消息（结果）。在调用结束之后，通过消息回调来通知调用者是否调用成功。
- 阻塞：阻塞调用是指调用结果返回之前，当前线程会被挂起，一直处于等待消息通知，不能够执行其他业务,函数只有在得到结果之后才会返回。
- 非阻塞：非阻塞和阻塞的概念相对应，指在不能立刻得到结果之前，该函数不会阻塞当前线程，而会立刻返回。

### 响应式编程

&emsp;&emsp;响应式编程（Reacting Programming）是一种面向数据流和变化传播的编程范式。~~Qt的信号和槽？~~（乱入）

&emsp;&emsp;在响应式编程中，Reactor是满足Reactive规范的框架。

### 所需依赖



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

3. RESTFul风格的请求参数

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

## Spring Security

>&emsp;&emsp;Spring Security is a powerful and highly customizable authentication and access-control framework. It is the de-facto standard for securing Spring-based applications.
>
>&emsp;&emsp;Spring Security is a framework that focuses on providing both **authentication** and **authorization** to Java applications. Like all Spring projects, the real power of Spring Security is found in  how easily it can be extended to meet custom requirements

&emsp;&emsp;Spring Security 的前身为 Acegi，经不断迭代最终成为一个功能强大的重型身份认证和用户授权框架。当然，由于 Spring Security 过于庞大，有时会选用轻量级的 Shiro。即“SSM + Shiro”或“Spring Boot/Cloud + Spring Security”。

### 依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

### 过滤器链

&emsp;&emsp;Spring Security本质为一个过滤器链

### Web权限
