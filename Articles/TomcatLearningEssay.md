# Tomcat

## Servlet

&emsp;&emsp;Servlet（Server Applet 服务器端小程序）

### C/S与B/S

- C/S：客户端/服务器模式
- B/S：浏览器/服务器模式

### 实现Servlet接口编写Servlet

&emsp;&emsp;Servlet定义了五个必须实现的接口：

- init
- getServletConfig
- service
- getServletInfo
- destroy

### 其他方法编写Servlet

#### 继承GenericServlet

&emsp;&emsp;只需实现service方法

##### 继承HttpServlet

&emsp;&emsp;HttpServlet提供了根据请求方式调用的方法：

- doGet
- doPost
- doPut
- doDelete

### 请求与响应

&emsp;&emsp;对于用户发送的请求（Request）可以通过以下方式处理：

```java
req.setCharacterEncoding("UTF-8");            // 设置编码（默认为ISO-8859-1）
req.getParameter("key");                      // 根据名称获取请求头的值
```

&emsp;&emsp;发送给用户的响应（Response）可以通过以下方式处理：

```java
resp.setHeader("key", "value");                    // 设置响应头
resp.setContentType("text/html;charset=utf-8");    // 设置响应体格式
resp.setCharacterEncoding("UTF-8");    
PrintWriter respWriter = resp.getWriter();         // 获取输出流
```

### 配置Servlet服务

&emsp;&emsp;可以通过web.xml配置Servlet：

```xml
<web-app>      
    <servlet>
        <servlet-name>my</servlet-name>
        <servlet-class>MyServlet</servlet-class>
        <!--Servlet的类名-->
        <load-on-startup>num</load-on-startup>
        <!--配置自动加载，数越大优先级越高，负数表示请求时才加载-->
    </servlet>

    <servlet-mapping>
        <servlet-name>my</servlet-name>
        <!--与要被映射的Servlet-name相同-->
        <url-pattern>/requestURL</url-pattern>
        <!--可以使用通配符*或/，后者不会匹配到jsp-->
    </servlet-mapping>
</web-app> 
```

&emsp;&emsp;也可以通过注解配置Servlet：

```java
@WebServlet(name = "className", value = {}, loadOnStartup = "-1", urlPatterns = {})
// value与urlPatterns功能相同，不可同时存在
```

### 转发与重定向

&emsp;&emsp;有时需要多个Servlet共同完成一次请求，此时可以通过**服务器**的**请求转发**完成：

```java
req.setAttribute("key", value);              // 重写请求头（添油加醋）
req.getRequestDispatcher("/path")            // 获取转发器
    .requestDispatcher.forward(req,resp);    // 转发请求
```

&emsp;&emsp;除了转发，还可通过**客户端**的**重定向**来完成：

```java
resp.sendRedirect("uri");
```

&emsp;&emsp;转发与重定向的区别：

|          |      转发      |     重定向      |
| :------: | :------------: | :-------------: |
|  作用于  |     服务器     |     客户端      |
| 请求次数 |       1        |        2        |
| 请求地址 |      不变      |      改变       |
| 数据共享 | 共享请求与响应 | 通过URI拼接实现 |
|   目标   |   同一webapp   |    任何位置     |

### Servlet生命周期

1. 实例化

   服务器启动时调用构造函数实例化Servlet

2. 初始化

   根据配置，在服务器启动后或收到请求时调用init()方法初始化servlet

3. 提供服务

   每次收到请求后调用service方法

4. 销毁

   服务器关闭前调用destroy方法，并由JVM负责完成垃圾回收

### 状态管理

&emsp;&emsp;由于HTTP连接是无状态的，因此需要通过Cookie和Session解决状态存储问题

#### Cookie

```java
// 创建Cookie
Cookie cookie = new Cookie("key", "value");    // 创建Cookie
cookie.setPath("/path");                       // 设置Cookie作用范围
cookie.setMaxAge(second);                      // 有效期，单位秒，0表示浏览器关闭，-1表示储存在内存中
resp.addCookie(cookie);                        // 将Cookie返回给客户端
// 相同名称和路径的Cookie会相互覆盖
// 获取Cookies
Cookie[] cookies = req.getCookies();
// Cookie编解码
URLEncoder.encode("name", str);                // 编码
URLDEcoder.decode("name", str);                // 解码
```

#### Session

```java
HttpSession session = req.getSession();        // 获取Session
session.setAttribute("name", value);           // 设置属性
session.getAttribute("name");                  // 读取属性
session.removeAttribute("name");               // 移除属性
session.setMaxInactiveInterval(second);        // 设置存活时间
session.invalidate();                          // 立刻注销
resp.encodeRedirectURL("/url");                // 追加Session的URL
```

### ServletContext

&emsp;&emsp;ServletContext随Servlet启动而产生，随Servlet销毁而消失，永远存储全局数据

```java
// 获取ServletContext
ServletContext servletContext = this.getServletContext();    // 推荐
ServletContext servletContext = req.getServletContext();
ServletContext servletContext = req.getSession().getServletContext();
// 获取信息
servletContext.getRealPath("contextPath");        // 获取某上下文的全局路径
servletContext.getContextPath();                  // 获取当前应用的上下文路径
// 操作属性
servletContext.setAttribute("name", value);       // 添加属性
servletContext.getAttribute("name");              // 获取属性
servletContext.removeAttribute("name");           // 删除属性
```

### 过滤器

&emsp;&emsp;过滤器（Filter）是一种处于客户端与服务器之间的过滤技术。Filter对象随Servlet产生而产生，随Servlet销毁而销毁。

#### 编写过滤器

```java
public class myfilter implements Filter{

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        chain.doFilter(request,response);        // 将请求传递到下一个过滤器或Servlet
    }
}
```

#### 配置过滤器

&emsp;&emsp;可通过XML文档配置Filter：

```xml
<filter>
    <filter-name>filter</filter-name>
    <filter-class>filterOPath</filter-class>
</filter>
<filter-mapping>
    <filter-name>filter</filter-name>
    <url-pattern>/*</url-pattern>
    <!--要被拦截的请求，不能用/-->
</filter-mapping>
```

&emsp;&emsp;也可通过注解配置Filter

```java
@WebFilter
```

#### Filter优先级

- 注解根据过滤器名称排序
- XML配置文件中会根据注册顺序
- XML优先于注解
- XML与注解重复的过滤器会执行多次