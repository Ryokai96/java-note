# Servlet 学习笔记

[TOC]

### 1. Servlet 简介

- Java Servlet 是运行在 Web 服务器或应用服务器上的程序，它是作为来自 Web 浏览器或其他 HTTP 客户端的请求和 HTTP 服务器上的数据库或应用程序之间的中间层

- Servlet架构:

  ![servlet-arch](https://raw.githubusercontent.com/Ryokai96/java-note/master/JavaEE/Servlet/servlet-arch.jpg)

- Servlet的任务:

  - 读取客户端（浏览器）发送的显式的数据。这包括网页上的 HTML 表单，或者也可以是来自 applet 或自定义的 HTTP 客户端程序的表单。
  - 读取客户端（浏览器）发送的隐式的 HTTP 请求数据。这包括 cookies、媒体类型和浏览器能理解的压缩格式等等。
  - 处理数据并生成结果。这个过程可能需要访问数据库，执行 RMI 或 CORBA 调用，调用 Web 服务，或者直接计算得出对应的响应。
  - 发送显式的数据（即文档）到客户端（浏览器）。该文档的格式可以是多种多样的，包括文本文件（HTML 或 XML）、二进制文件（GIF 图像）、Excel 等。
  - 发送隐式的 HTTP 响应到客户端（浏览器）。这包括告诉浏览器或其他客户端被返回的文档类型（例如 HTML），设置 cookies 和缓存参数，以及其他类似的任务。

- Java Servlet 是运行在带有支持 Java Servlet 规范的解释器的 web 服务器上的 Java 类。

  Servlet 可以使用 **javax.servlet** 和 **javax.servlet.http** 包创建，它是 JavaEE的标准组成部分



### 2. Servlet 生命周期

1. 加载 ClassLoader
2. 实例化 new
3. 初始化 init(ServletConfig)
4. 处理请求 service doGet doPost
5. 退出服务 destroy()
6. 只有一个对象



### 3. Cookie

- 关闭web页面时，这个页面和服务器的socket连接就断开了，为了让服务器获取web页面关闭前的内容，要求出现一种保存c(客户端)/s(服务端)间状态的机制，这种机制需要满足：

  1. 服务器可以向客户端写内容，但只能是文本内容


    2. 客户端可以阻止服务器写入内容
    3. 服务器只能从客户端拿自己的webapp写入的内容

- Cookie: 保存到客户端的一个文本文件，与特定客户相关

- Cookie以"名-值"对的形式保存数据

- Cookie分为两种: 

  - 属于窗口/子窗口
  - 属于文本

- 一个servlet/jsp设置的cookie能够被同一个路径下面或者子路径下面的servlet/jsp读到(这里的路径指的是URL，并不是指真实的路径)

- 创建Cookie: new Cookie(name, value)



### 4. Session

- Session记录在服务端
- Session的实现:
  1. 通过Cookie来实现: 如果浏览器支持Cookie，创建Session的时候会把SessionID保存在Cookie里
  2. 通过URL来实现: 如果浏览器不支持Cookie，必须自己编程使用URL重写的方式实现Session
     - response.encodeURL()
       - 转码
       - URL后面加入SessionID
- 可以在web.xml中配置Session的过期时间
- Session不像Cookie拥有路径访问的问题
  - 同一个application下的servlet/jsp可以共享同一个session，前提是同一个客户端窗口



### 5. Application

- Application中的内容所有客户端都可以访问




### 6. javaBean

- 广义javaBean: 普通java类
- 狭义javaBean: 符合Sun JavaBean标准的类
- 在Servlet中使用Bean和在通常程序中使用Bean类似
  - 属性名称第一个字母必须小写，比如：private productId
  - 一般具有 getters and setters
  - Bean不应具有GUI表现
  - 一般是用来实现某一业务逻辑或取得特定结果




### 7. 过滤器Filter

- 过滤器是一个实现了 javax.servlet.Filter 接口的 Java 类。javax.servlet.Filter 接口定义了三个方法：

  |  序号  | 方法 & 描述                                  |
  | :--: | :--------------------------------------- |
  |  1   | **public void doFilter (ServletRequest, ServletResponse, FilterChain)**该方法完成实际的过滤操作，当客户端请求方法与过滤器设置匹配的URL时，Servlet容器将先调用过滤器的doFilter方法。FilterChain用户访问后续过滤器。 |
  |  2   | **public void init(FilterConfig filterConfig)**web 应用程序启动时，web 服务器将创建Filter 的实例对象，并调用其init方法，读取web.xml配置，完成对象的初始化功能，从而为后续的用户请求作好拦截的准备工作（filter对象只会创建一次，init方法也只会执行一次）。开发人员通过init方法的参数，可获得代表当前filter配置信息的FilterConfig对象。 |
  |  3   | **public void destroy()**Servlet容器在销毁过滤器实例前调用该方法，在该方法中释放Servlet过滤器占用的资源。 |

- FilterConfig: Filter 的 init 方法中提供了一个 FilterConfig 对象。

  如 web.xml 文件配置如下：

  ```xml
  <filter>
  	<filter-name>LoginFilter</filter-name>
  	<filter-class>com.runoob.test.LogFilter</filter-class>
  	<init-param>
  		<param-name>Site</param-name>
  		<param-value>Ryoukai.xyz</param-value>
  	</init-param>
  </filter>
  ```

  在 init 方法使用 FilterConfig 对象获取参数：

  ```xml
  public void  init(FilterConfig config) throws ServletException {
  	// 获取初始化参数
  	String site = config.getInitParameter("Site"); 
  	// 输出初始化参数
  	System.out.println("网站名称: " + site); 
  }
  ```

- web.xml配置各节点说明

  - `<filter>`指定一个过滤器。
    - `<filter-name>`用于为过滤器指定一个名字，该元素的内容不能为空。
    - `<filter-class>`元素用于指定过滤器的完整的限定类名。
    - `<init-param>`元素用于为过滤器指定初始化参数，它的子元素`<param-name>`指定参数的名字，`<param-value>`指定参数的值。
    - 在过滤器中，可以使用`FilterConfig`接口对象来访问初始化参数。
  - `<filter-mapping>`元素用于设置一个 Filter 所负责拦截的资源。一个Filter拦截的资源可通过两种方式来指定：Servlet 名称和资源访问的请求路径
    - `<filter-name>`子元素用于设置filter的注册名称。该值必须是在`<filter>`元素中声明过的过滤器的名字
    - `<url-pattern>`设置 filter 所拦截的请求路径(过滤器关联的URL样式)
  - `<servlet-name>`指定过滤器所拦截的Servlet名称。
  - `<dispatcher>`指定过滤器所拦截的资源被 Servlet 容器调用的方式，可以是`REQUEST`,`INCLUDE`,`FORWARD`和`ERROR`之一，默认`REQUEST`。用户可以设置多个`<dispatcher>`子元素用来指定 Filter 对资源的多种调用方式进行拦截。
  - `<dispatcher>`子元素可以设置的值及其意义
    - `REQUEST`：当用户直接访问页面时，Web容器将会调用过滤器。如果目标资源是通过RequestDispatcher的include()或forward()方法访问时，那么该过滤器就不会被调用。
    - `INCLUDE`：如果目标资源是通过RequestDispatcher的include()方法访问时，那么该过滤器将被调用。除此之外，该过滤器不会被调用。
    - `FORWARD`：如果目标资源是通过RequestDispatcher的forward()方法访问时，那么该过滤器将被调用，除此之外，该过滤器不会被调用。
    - `ERROR`：如果目标资源是通过声明式异常处理机制调用时，那么该过滤器将被调用。除此之外，过滤器不会被调用。



### 8. Servlet 异常处理

- 当一个 Servlet 抛出一个异常时，Web 容器在使用了 exception-type 元素的 **web.xml** 中搜索与抛出异常类型相匹配的配置
- 必须在 web.xml 中使用 **error-page** 元素来指定对特定**异常** 或 HTTP **状态码** 作出相应的 Servlet 调用

