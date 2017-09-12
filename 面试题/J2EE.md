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


### 11. JDBC的脏读是什么？哪种数据库隔离级别能防止脏读？ 

- 当我们使用事务时，有可能会出现这样的情况，有一行数据刚更新，与此同时另一个查询读到了这个刚更新的值。这样就导致了脏读，因为更新的数据还没有进行持久化，更新这行数据的业务可能会进行回滚，这样这个数据就是无效的。
- 数据库的TRANSACTIONREADCOMMITTED，TRANSACTIONREPEATABLEREAD，和TRANSACTION_SERIALIZABLE隔离级别可以防止脏读。

### 12. JDBC的DriverManager是用来做什么的？

- JDBC的DriverManager是一个工厂类**，**我们通过它来创建数据库连接。当JDBC的Driver类被加载进来时，它会自己注册到DriverManager类里面。然后我们会把数据库配置信息传成DriverManager.getConnection()方法**，**DriverManager会使用注册到它里面的驱动来获取数据库连接**，**并返回给调用的程序。

### 13. execute，executeQuery，executeUpdate的区别是什么？

- execute方法的返回值是boolean类型。返回true表示有结果集，返回false表示无结果集
- executeQuery只能运行select语句
- executeUpdate能运行insert、delect、update语句,返回结果是处理的记录的条数

### 14. 什么是Spring beans?

- Spring bean 表示受到Spring管理的对象。**具体说来，它是被Spring框架容器初始化、配置和管理的对象。**Spring bean是在Spring的配置文件中定义（现在也可以通过annotation注解来定义），在Spring容器中初始化，然后注入到应用程序中的。
- **Spring bean可以是POJO吗？**当然可以，并且它通常就是。（即使它并不一定得是POJO，例如Spring可以用来处理重量级Java对象，比如EJB对象）。

### 15. 解释Spring支持的几种bean的作用域。

- Spring支持如下5种作用域：
  - singleton: 单例模式，在整个Spring IoC容器中，使用singleton定义的Bean将只有一个实例
  - prototype: 原型模式，每次通过容器的getBean方法获取prototype定义的Bean时，都将产生一个新的Bean实例
  - request: 对于每次HTTP请求，使用request定义的Bean都将产生一个新实例，即每次HTTP请求将会产生不同的Bean实例。只有在Web应用中使用Spring时，该作用域才有效
  - session: 对于每次HTTP Session，使用session定义的Bean豆浆产生一个新实例。同样只有在Web应用中使用Spring时，该作用域才有效
  - globalsession: 每个全局的HTTP Session，使用session定义的Bean都将产生一个新实例。典型情况下，仅在使用portlet context的时候有效。同样只有在Web应用中使用Spring时，该作用域才有效

### 16. 解释Spring框架中bean的生命周期

1. 实例化一个Bean－－也就是我们常说的new

2. 按照Spring上下文对实例化的Bean进行配置－－也就是IOC注入

3. 如果这个Bean已经实现了BeanNameAware接口，会调用它实现的setBeanName(String)方法，此处传递的就是Spring配置文件中Bean的id值

4. 如果这个Bean已经实现了BeanFactoryAware接口，会调用它实现的setBeanFactory(setBeanFactory(BeanFactory)传递的是Spring工厂自身(可以用这个方式来获取其它Bean，只需在Spring配置文件中配置一个普通的Bean就可以)

5. 如果这个Bean已经实现了ApplicationContextAware接口，会调用setApplicationContext(ApplicationContext)方法，传入Spring上下文(同样这个方式也可以实现步骤4的内容，但比4更好，因为ApplicationContext是BeanFactory的子接口，有更多的实现方法)

6. 如果这个Bean关联了BeanPostProcessor接口，将会调用postProcessBeforeInitialization(Object obj, String s)方法，BeanPostProcessor经常被用作是Bean内容的更改，并且由于这个是在Bean初始化结束时调用那个的方法，也可以被应用于内存或缓存技术

7. 如果Bean在Spring配置文件中配置了init-method属性会自动调用其配置的初始化方法。

8. 如果这个Bean关联了BeanPostProcessor接口，将会调用postProcessAfterInitialization(Object obj, String s)方法

注: 以上工作完成以后就可以应用这个Bean了，那这个Bean是一个Singleton的，所以一般情况下我们调用同一个id的Bean会是在内容地址相同的实例，当然在Spring配置文件中也可以配置非Singleton，这里我们不做赘述

9. 当Bean不再需要时，会经过清理阶段，如果Bean实现了DisposableBean这个接口，会调用那个其实现的destroy()方法

10. 最后，如果这个Bean的Spring配置中配置了destroy-method属性，会自动调用其配置的销毁方法

### 17. 什么是ORM？

- 关系型数据库和实体间做映射，操作对象的属性和方法，跳过SQL语句
- 优点: 专用、庞大的数据库访问层可能不再需要、提高效率，像操作对象一样提取数据
- 缺点: 固定思维模式、牺牲执行效率，很有可能将全部数据提取到内存对象中，持久化所有属性——不希望

### 18. MyBatis中使用#和$书写占位符有什么区别？

- \${}是Properties文件中的变量占位符，它可以用于标签属性值和sql内部，属于静态文本替换，比如${driver}会被静态替换为com.mysql.jdbc.Driver。#{}是sql的参数占位符，Mybatis会将sql中的#{}替换为?号，在sql执行前会使用PreparedStatement的参数设置方法，按序给sql的?号占位符设置参数值，比如ps.setInt(0, parameterValue)，#{item.name}的取值方式为使用反射从参数对象中获取item对象的name属性值，相当于param.getItem().getName()

### 19. 解释一下MyBatis中命名空间(namespace)的作用

- 对sql进行分类化管理，进行sql隔离
- 区分二级缓存区域

### 20. Mybatis是如何进行分页的？分页插件的原理是什么？

- Mybatis使用RowBounds对象进行分页，它是针对ResultSet结果集执行的内存分页，而非物理分页，可以在sql内直接书写带有物理分页的参数来完成物理分页功能，也可以使用分页插件来完成物理分页
- 分页插件的基本原理是使用Mybatis提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的sql，然后重写sql，根据dialect方言，添加对应的物理分页语句和物理分页参数。
- 举例：select * from student，拦截sql后重写为：select t.* from （select * from student）t limit 0，10

### 21. Mybatis动态sql是做什么的？都有哪些动态sql？能简述一下动态sql的执行原理不？

- Mybatis动态sql可以让我们在Xml映射文件内，以标签的形式编写动态sql，完成逻辑判断和动态拼接sql的功能，Mybatis提供了9种动态sql标签trim|where|set|foreach|if|choose|when|otherwise|bind。
- 其执行原理为，使用OGNL从sql参数对象中计算表达式的值，根据表达式的值动态拼接sql，以此来完成动态sql的功能

### 22. JDBC编程有哪些不足之处，MyBatis是如何解决这些问题的？

- 数据库连接，使用时就创建，不使用时就释放，对数据库进行频繁连接开启和关闭，造成数据库资源浪费，影响了数据库性能
  - 解决方式: 使用数据库连接池
- 将sql语句硬编码到java代码中，如果sql语句要修改，需要重新编译java代码，不利于系统维护
  - 解决方式: 将sql语句配置在xml配置文件中，即使sql语句发生变化，也不需要重新编译java代码
- 向preparedStatement中设置参数，对占位符号位置和设置参数值，硬编码在java代码中，不利于系统维护
  - 解决方式: 将sql语句及占位符及参数全部配置在xml配置文件中
- 从resultSet中遍历结果集数据时，将获取表的字段进行了硬编码，不利于系统维护
  - 解决方式: 将查询的结果集自动映射成java对象

### 23. 什么是AJAX？

- 传统的Web应用交互由用户触发一个HTTP请求到服务器,服务器对其进行处理后再返回一个新的HTHL页到客户端, 每当服务器处理客户端提交的请求时,客户都只能空闲等待,并且哪怕只是一次很小的交互、只需从服务器端得到很简单的一个数据,都要返回一个完整的HTML页,而用户每次都要浪费时间和带宽去重新读取整个页面。这个做法浪费了许多带宽，由于每次应用的交互都需要向服务器发送请求，应用的响应时间就依赖于服务器的响应时间。这导致了用户界面的响应比本地应用慢得多
- 与此不同，AJAX应用可以仅向服务器发送并取回必需的数据，它使用SOAP或其它一些基于XML的Web Service接口，并在客户端采用JavaScript处理来自服务器的响应。因为在服务器和浏览器之间交换的数据大量减少，结果我们就能看到响应更快的应用。同时很多的处理工作可以在发出请求的客户端机器上完成，所以Web服务器的处理时间也减少了
- Ajax的工作原理相当于在用户和服务器之间加了—个中间层(AJAX引擎),使用户操作与服务器响应异步化。并不是所有的用户请求都提交给服务器,像—些数据验证和数据处理等都交给Ajax引擎自己来做, 只有确定需要从服务器读取新数据时再由Ajax引擎代为向服务器提交请求

### 24. AJAX有哪些优点和缺点？

- 优点: 
  - 最大的一点是页面无刷新，用户的体验非常好
  - 使用异步方式与服务器通信，具有更加迅速的响应能力
  - 可以把以前一些服务器负担的工作转嫁到客户端，利用客户端闲置的能力来处理，减轻服务器和带宽的负担，节约空间和宽带租用成本。并且减轻服务器的负担，ajax的原则是“按需取数据”，可以最大程度的减少冗余请求，和响应对服务器造成的负担
  - 基于标准化的并被广泛支持的技术，不需要下载插件或者小程序
  - ajax可使因特网应用程序更小、更快，更友好
- 缺点: 
  - ajax不支持浏览器back按钮
  - 安全问题 AJAX暴露了与服务器交互的细节
  - 对搜索引擎的支持比较弱
  - 破坏了程序的异常机制
  - 不容易调试

### 25. Ajax的实现流程是怎样的？

```java
//当页面加载完毕后，执行以下代码  
window.onload = function(){  
    document.getElementById("ok").onclick = function(){  
        //1 创建XMLHttpRequest对象  
        var xhr = ajaxFunction();  
          
        /* 
         * 2    客户端与服务器端，建立连接 
         *  
         * open(method, url, asynch) 
         *  * method:请求类型，类似 “GET”或”POST”的字符串 
         *  * url:请求路径 
         *  * asynch:表示请求是否要异步传输，默认值为true(异步)。 
         */   
        xhr.open("get","../testServlet?timeStamp="+new Date().getTime()+"&c=9",true);  
          
          
        /* 
         * 3    客户端向服务器端发送请求 
         *  
         * send()方法 
         *  * 如果请求类型是GET方式的话，send()方法发送的请求数据，服务器端接收不到.这个步骤是不能省略的！ 
         */   
        xhr.send("a=7&b=8");        //send(null);  
          
        /*  
         * 4    服务器端响应数据 
         *  
         * readyState 属性表示Ajax请求的当前状态。它的值用数字代表。 
            0 代表未初始化。 还没有调用 open 方法 
            1 代表正在加载。 open 方法已被调用，但 send 方法还没有被调用 
            2 代表已加载完毕。send 已被调用。请求已经开始 
            3 代表交互中。服务器正在发送响应 
            4 代表完成。响应发送完毕 
             
            常用状态码及其含义： 
            404 没找到页面(not found) 
            403 禁止访问(forbidden) 
            500 内部服务器出错(internal service error) 
            200 一切正常(ok) 
            304 没有被修改(not modified)(服务器返回304状态，表示源文件没有被修改 ) 
         */  
        xhr.onreadystatechange = function(){  
//          alert(xhr.readyState);  
//          alert(xhr.status);  
            if(xhr.readyState==4){  
                if(xhr.status==200||xhr.status==304){  
                    var data = xhr.responseText;  
                      
                    alert(data);  
                }  
            }  
        }  
          
          
    }  
}  
  
function ajaxFunction(){  
   var xmlHttp;  
   try{ // Firefox, Opera 8.0+, Safari  
        xmlHttp=new XMLHttpRequest();  
    }  
    catch (e){  
       try{// Internet Explorer  
             xmlHttp=new ActiveXObject("Msxml2.XMLHTTP");  
          }  
        catch (e){  
          try{  
             xmlHttp=new ActiveXObject("Microsoft.XMLHTTP");  
          }  
          catch (e){}  
          }  
    }  
  
    return xmlHttp;  
 }
```

### 26. 简单说一下数据库的三范式？

- 第一范式: 
  - 所谓第一范式（1NF）是指数据库表的每一列都是不可分割的基本数据项，同一列中不能有多个值，即实体中的某个属性不能有多个值或者不能有重复的属性。如果出现重复的属性，就可能需要定义一个新的实体，新的实体由重复的属性构成，新实体与原实体之间为一对多关系。在第一范式（1NF）中表的每一行只包含一个实例的信息。即列不可分
  - 在任何一个关系数据库中，第一范式（1NF）是对关系模式的基本要求，不满足第一范式（1NF）的数据库就不是关系数据库。
  - **确保每列保持原子性**
- 第二范式: 
  - 第二范式（2NF）是在第一范式（1NF）的基础上建立起来的，即满足第二范式（2NF）必须先满足第一范式（1NF）。第二范式（2NF）要求数据库表中的每个实例或行必须可以被惟一的区分。为实现区分通常需要为表加上一个列，以存储各个实例的惟一标识。要求实体的属性完全依赖于主关键字。
  - 简单来说就是不能部分依赖。即一张表存在组合主键时，其他非主键字段不能部分依赖
  - **确保表中的每列都和主键相关**
- 第三范式:
  - 满足第三范式（3NF）必须先满足第二范式（2NF）。简而言之，**第三范式（3NF）要求一个数据库表中不包含已在其它表中已包含的非主关键字信息**
  - 在第二范式的基础上，数据表中如果不存在非关键字段对任一候选关键字段的传递函数依赖则符合第三范式
  - 简单来说就是不能存在传递依赖。即除主键外，其他字段必须依赖主键
  - **确保每列都和主键列直接相关,而不是间接相关**

### 27. Java集合框架是什么？说出一些集合框架的优点？

- 每种编程语言中都有集合，最初的Java版本包含几种集合类：Vector、Stack、HashTable和Array。随着集合的广泛使用，Java1.2提出了囊括所有集合接口、实现和算法的集合框架。在保证线程安全的情况下使用泛型和并发集合类，Java已经经历了很久。它还包括在Java并发包中，阻塞接口以及它们的实现。
- 集合框架的部分优点如下:
  - 使用核心集合类降低开发成本，而非实现我们自己的集合类
  - 随着使用经过严格测试的集合框架类，代码质量会得到提高
  - 通过使用JDK附带的集合类，可以降低代码维护成本
  - 复用性和可操作性

### 28. 集合框架中的泛型有什么优点？

- Java1.5引入了泛型，所有的集合接口和实现都大量地使用它。泛型允许我们为集合提供一个可以容纳的对象类型，因此，如果你添加其它类型的任何元素，它会在编译时报错。这避免了在运行时出现ClassCastException，因为你将会在编译时得到报错信息。泛型也使得代码整洁，我们不需要使用显式转换和instanceOf操作符。它也给运行时带来好处，因为不会产生类型检查的字节码指令

### 29. Iterater和ListIterator之间有什么区别？

