## J2EE问题

### 1. JSP内置对象，JSP中JSTL以及EL表达式如何使用

- JSP九大内置对象:

  - pageContext: 只对当前jsp页面有效，里面封装了基本的request和session的对象
  - Request: 对当前请求进行封装
  - Session: 浏览器会话对象，浏览器范围内有效
  - Application: 应用程序对象，对整个web工程都有效
  - Out: 面打印对象，在jsp页面打印字符串
  - Response: 返回服务器端信息给用户
  - Config: 单个servlet的配置对象，相当于servletConfig对象
  - Page: 当前页面对象，也就是this
  - Exception: 错误页面的exception对象，如果指定的是错误页面，这个就是异常对象

- JSP三大指令:

  - Page: 指令是针对当前页面的指令
  - Include: 用于指定如何包含另一个页面
  - Taglib: 用于定义和指定自定义标签

- JSP七大动作:

  - Forward: 执行页面跳转，将请求的处理转发到另一个页面
  - Param: 用于传递参数
  - Include: 用于动态引入一个jsp页面
  - Plugin: 用于下载javaBean或applet到客户端执行
  - useBean: 使用javaBean
  - setProperty: 修改javaBean实例的属性值
  - getProperty: 获取javaBean实例的属性值

- JSTL(JSP Standard Tag Library):

  - jstl用于使用标签代替简化 jsp中的<%  %>，含有if循、环判断语句、表达式赋值、基本输入输出等

  - 引用 <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>

  - 常用标签:

    - \<c:out>: 用于在 JSP 中显示数据

      ```jsp
      <c:out value="${anyValue}" default="value"  escapeXml="false"/>
      ```

    - \<c:set>: 将值存储到作用域或为JavaBean中的变量属性赋值

      ```jsp
      <c:set var=“name“  value=“chen“  scope=“page”/>
      ```

    - \<c:remove>: 删除存在于scope中的变量

      ```jsp
      <c:remove var="sampleValue" scope="session"/>
      ```

    - 条件控制标签:

      ```jsp
      <c:if>、<c:choose>、<c:when>和<c:otherwise> 用法同if()else{} 
      ```

    - 循环控制标签:

      ```jsp
      <c:forEach>
      ```

    - URL相关标签:

      - \<c:import>: 包含另一个 JSP 页面到本页面，作用类似\<jsp:include>，但作用范围广，不仅可以包含本项目下资源，也可以包含外部的资源

      - \<c:redirect>: 用于页面重定向

        ```jsp
        <c:redirect url=“fortoken.jsp“ />
        ```

      - \<c:param>: 用于包含传递参数

        ```jsp
        <c:param name="a" value="33"></c:param>
        ```

- EL(Expression Language)表达式

  - EL表达式一般操作的是作用域(application,session,request,pageContext)中的属性，EL变量指某一个作用域中的属性

  - el表达式操作不了局部变量

  - 使用EL表达式获取数据语法: “${标识符}”

  - EL表达式语句在执行时，会调用pageContext.findAttribute方法，用标识符为关键字，分别从page、request、session、application四个域中查找相应的对象，找到则返回相应对象，找不到则返回""(注意，不是null，而是空字符串)。 

  - EL表达式也可以很轻松获取JavaBean的属性，或获取数组、Collection、Map类型集合的数据

    ```jsp
    ${user.address.city}
    ${user.list[0]}	<!-- 访问有序集合某个位置的元素 -->
    ${map.key} <!-- 获得map集合中指定key的值 -->
    ```

  - 常用对象

    - pageContext: 代表pageContext对象

      ```jsp
      ${pageScope.name1}
      ```

    - pageScope: 代表page域

    - requestScope: 代表request域

    - sessionScope

    - applicationScoope

    - param: Map对象，保存了所有的请求参数

    - paramValues: Map对象，value对应一个数组

    - header: Map对象，获取一个请求头的值。注意: 如果头里面有"-"，例Accept-Encoding，则要headerValues["Accept-Encoding"]

    - headerValues: Map对象，value对应一个数组

    - cookie: Map对象，value对应一个cookie对象

    - initParam: Map对象。key=参数名，value=参数值，此参数是在web.xml中配置的，例如 ${initParam.encode }




### 2. 对web.xml的理解，J2EE规范，J2EE入口Servlet、Listener的理解

- Servlet与JSP有什么区别:

  - Servlet和JSP完成的功能是相同的，都可以接收用户的请求，可以对用户进行响应，可以调用业务方法。

    不同点在于JSP是在html或者xml中嵌入了Java代码或者JSP的标记，在制作页面方面具有优势，可以使用网页编辑工具来制作网页，然后嵌入Java或者JSP的标记。Servlet是纯Java代码，通常用于控制，不用于输出。在MVC模式中，JSP通常充当视图，Servlet通常充当控制器。另外，JSP在运行的时候还要转换成类似于Servlet的Java代码。

- Servelt通常有哪些方法

  - init方法，完成初始化

    service方法，包括doGet和doPost，用于接收用户的请求，调用后台的JavaBean或者EJB，选择界面对用户响应

    destroy方法，用于释放资源

- Servlet的生命周期

  - 当接收到请求的时候，容器察看对应的Servlet对象是否存在，如果不存在，需要加载Servetl，实例化Servlet，调用init方法进行初始化。如果已经存在，根据用户的请求创建request和response对象，把这两个对象作为参数调用Servlet对象的service方法，Servlet通过这个方法与用户进行交互，方法执行完之后，对请求的处理结束。Servelt对象继续等待下一个请求。当应用卸载的时候，调用destroy方法释放资源。多个请求共享Servelt对象

- doGet方法和doPost方法中的两个参数是什么

  - HttpServletRequest: 封装了与请求相关的信息，用于获取用户请求
  - HttpServletResponse: 封装了与响应相关的信息，用于获取用户响应

- Filter的作用

  - Filter是特殊的Servlet，能够对特定的请求路径进行过滤，在访问这个路径之前，先执行过滤器，过滤器进行预处理，过滤器决定是否继续执行后续的文件。典型的应用，可以把用户验证的代码写在过滤器中，然后把过滤的路径配制成需要验证的文件的路径

- Listener的作用

  - Servlet监听器对特定的事件进行监听，当产生这些事件的时候，会执行监听器的代码。可以对应用的加载、卸载，对session的初始化、销毁，对session中值变化等事件进行监听



### 3. Tomcat的部署方式有哪些

- 方法一: 在tomcat中的conf目录中，在server.xml中的，\<host/>节点中添加

  ```xml
  <Context path="/hello" docBase="D:/eclipse/forwebtools/workspace/hello/WebRoot" debug="0" privileged="true"> 
  </Context>
  ```

- 方法二: 将web项目文件件拷贝到webapps 目录中

- 方法三: 在conf目录中，新建Catalina/localhost目录，在该目录中新建一个xml文件，名字可以随意取，只要和当前文件中的文件名不重复就行了，该xml文件的内容为

  ```xml
  <Context path="/hello" docBase="D:/eclipse/forwebtools/workspace/hello/WebRoot" debug="0" privileged="true"> 
  </Context>
  ```

  这种方法有个优点: 可以定义别名。服务器端运行的项目名称为path，外部访问的URL则使用XML的文件名。这个方法很方便的隐藏了项目的名称，对一些项目名称被固定不能更换，但外部访问时又想换个路径，非常有效

- 方法四: 可以用tomcat在线后台管理器,一般tomcat都打开了,直接上传war就可以



### 4. 说一说Servlet的生命周期

- servlet 有良好的生存期的定义， 包括加载和实例化、 初始化、 处理请求以及服务结束。这个生存期由 javax.servlet.Servlet 接口的 init,service 和 destroy 方法表达。Servlet 被服务器实例化后， 容器运行其 init 方法， 请求到达时运行其 service 方法， service方法自动派遣运行与请求对应的 doXXX 方法（ doGet， doPost） 等， 当服务器决定将实例销毁的时候调用其 destroy 方法。
- web 容器加载 servlet， 生命周期开始。 通过调用 servlet 的 init()方法进行 servlet 的初始化。通过调用 service()方法实现， 根据请求的不同调用不同的 do***()方法。 结束服务， web 容器调用 servlet 的 destroy()方法。 



### 5. Servlet API中forward()与redirect()的区别？

- Forward和Redirect代表了两种请求转发方式: 直接转发和间接转发
- 直接转发方式(Forward): 客户端和浏览器只发出一次请求，Servlet、HTML、JSP或其它信息资源，由第二个信息资源响应该请求，在请求对象request中，保存的对象对于每个信息资源是共享的
- 间接转发方式(Redirect): 实际是两次HTTP请求，服务器端在响应第一次请求的时候，让浏览器再向另外一个URL发出请求，从而达到转发的目的。



### 6. request.getAttribute()和 request.getParameter()有何区别?

- getParameter 得到的都是 String 类型的，getAttribute 则可以是对象
- getParameter()是获取 POST/GET 传递的参数值，getAttribute()是获取对象容器中的数据值
- getParameter用于客户端重定向时，点击了链接或提交按扭时传值用，即用于在用表单或 url 重定向传值时接收数据用。getAttribute用于服务器端重定向时，在 sevlet 中使用了 forward 函数，或 struts 中使用了mapping.findForward，getAttribute 只能收到程序用 setAttribute 传过来的值。



### 7. jsp静态包含和动态包含的区别

- 在jsp中有两种包含，静态包含<%@include file="xxx.jsp"%>和动态包含\<jsp:include page="xxx.jsp">
- <%@include file="xxx.jsp"%>为jsp中的编译指令，其文件的包含是发生在jsp向servlet转换的时期，而\<jsp:include page="xxx.jsp">是jsp中的动作指令，其文件的包含是发生在编译时期，也就是将java文件编译为class文件的时期
- 使用静态包含只会产生一个class文件，而使用动态包含会产生多个class文件
- 使用静态包含，包含页面和被包含页面的request对象为同一对象，因为静态包含只是将被包含的页面的内容复制到包含的页面中去；而动态包含包含页面和被包含页面不是同一个页面，被包含的页面的request对象可以取到的参数范围要相对大些，不仅可以取到传递到包含页面的参数，同样也能取得在包含页面向下传递的参数



### 8. Http中，get和post方法的区别

- get:
  - 服务器交互: 获取数据
  - 数据提交方式: 通过URL，以?分割URL和传输数据，参数之间以&相连
  - 服务器获取数据方式: Request、QueryString
  - 数据大小: 理论上1024字节，各浏览器限制不同，IE为2083k
  - 安全性: 安全性差
  - 性能: 高
- post:
  - 服务器交互: 传送数据
  - 数据提交方式: 数据放置在Header内
  - 服务器获取数据方式: Request.Form
  - 数据大小: 没有限制，起限制作用的是服务器设置和内存大小以及程序的处理能力
  - 安全性: 安全性好
  - 性能: 略低
- 建议:
  - get方式的安全性较Post方式要差些，包含机密信息的话，建议用Post数据提交方式
  - 在做数据查询时，建议用Get方式；而在做数据添加、修改或删除时，建议用Post方式



### 9. 什么是cookie？Session和cookie有什么区别？

- Cookie和Session都是会话技术，Cookie是运行在客户端，Session是运行在服务器端
- Cookie有大小限制以及浏览器在存cookie的个数也有限制，Session是没有大小限制，服务器能存多少Session和内存大小有关
- Cookie有安全隐患，通过拦截或本地文件找得到你的cookie后可以进行攻击
- Session是保存在服务器端上会存在一段时间才会消失，如果session过多会增加服务器的压力



### 10. jsp和servlet的区别、共同点、各自应用的范围

- JSP在本质上就是SERVLET，但是两者的创建方式不一样
- Servlet完全是JAVA程序代码构成，擅长于流程控制和事务处理，通过Servlet来生成动态网页很不直观。JSP由HTML代码和JSP标签构成，可以方便地编写动态网页。因此在实际应用中采用Servlet来控制业务流程，而采用JSP来生成动态网页
- JSP编译后是"类servlet"
- Servlet和JSP最主要的不同点在于，Servlet的应用逻辑是在Java文件中，并且完全从表示层中的HTML里分离开来。而JSP是Java和HTML组合成一个扩展名为.jsp的文件



### 11. 