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

