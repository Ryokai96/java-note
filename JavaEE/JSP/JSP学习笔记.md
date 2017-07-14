# JSP 学习笔记

[TOC]

## 1. JSP简介

- JSP: Java Server Pages

- 拥有servlet的特性与优点(本身就是一个servlet)

- 直接在HTML中内嵌JSP代码

- JSP程序由JSP Engine先将它转换成Servlet代码，接着将它编译成类文件载入执行

- 只有当客户端第一次请求JSP时，才需要将其转换、编译

- 优点:

  - 优良的性能
  - 平台无关性: 操作系统无关，Web服务器无关
  - 可扩展性: tag的扩展机制，简化页面开发

- 例: HelloWorld.jsp

  ```jsp
  <html>
    	<head></head>
    	<body>
        	<%
    			out.println("HelloWorld!");
  			out.println(new java.util.Date());
    		%>
    	</body>
  </html>
  ```



## 2. 基本语法

- JSP传统语法:
  - Declaration  声明
  - Scriptlet  程序段
  - Expression  表达式
  - Comment  注释
  - Directives  编译指令
  - Action 动作指令
  - 内置对象



### 2.01 Declaration 声明

- 基本语法:

  > <%!  %>

- 说明: 在此声明的变量、方法都会被保留成唯一的一份(成员变量,成员方法)，直到jsp程序停止运行，而在<% %>中声明的变量为局部变量

- 例:

  ```jsp
  <%!
    	int i;
  	public void setName() {......}
  %>
  ```

- 例: AccessCounts.jsp

  ```jsp
  <%@ page language="java" contentType="text/html; charset=UTF-8"
      pageEncoding="UTF-8"%>
  <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
  <html>
  <head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
  <title>AccessCounts</title>
  </head>
  <body>
  <h1>JSP Declaration</h1>
  <%!
  	int accessCount = 0;
  %>
  <h2>Accesses to page since server reboot:
  <%= ++accessCount %> 
  <%-- <%=代表输出后面的内容 --%>
  </h2>
  </body>
  </html>
  ```

  ​

### 2.02 Scriptlet 程序段

- 基本语法: 

  > <% 程序代码区 %>

- 可以放入任何的Java程序代码

- 例:

  ```jsp
  <%
    	for(int i = 0; i < 10; i++) {
      	......
    	}
  %>
  ```

- 例: BGColor.jsp

  ```jsp
  <%@ page language="java" contentType="text/html; charset=UTF-8"
      pageEncoding="UTF-8"%>
  <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
  <html>
  <head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
  <title>BGColor</title>
  </head>

  <!-- html注释(客户端能看见) -->
  <%-- jsp注释(客户端看不见) --%>

  <%
  	String bgColor = request.getParameter("bgColor");
  	boolean hasExplicitColor;
  	if(bgColor != null) {
  		hasExplicitColor = true;
  	} else {
  		hasExplicitColor = false;
  		bgColor = "RED";
  	}
  %>

  <body bgcolor="<%= bgColor %>">
  <h2 align="center">Color Testing</h2>

  <%
  	if(hasExplicitColor) {
  		out.println("You supplied an explicit background color of" + bgColor + ".");
  	} else {
  		out.println("Using default background color of RED");
  	}
  %>
  </body>
  </html>
  ```

  ​

### 2.03 Expression 表达式 

- 基本语法:

  > <%=  %>

- =后面必须是字符串变量或者可以被转换成字符串的表达式

- 不需要以;结束

- 只有一行

- 例:

  ```jsp
  <%= "hello world" %>
  <%= i+1 %>
  <%= request.getParameter("name") %>
  ```

- 例: Expressions.jsp

  ```jsp
  <%@ page language="java" contentType="text/html; charset=UTF-8"
      pageEncoding="UTF-8"%>
  <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
  <html>
  <head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
  <title>Expressions</title>
  </head>
  <body>
  <h2>JSP Expressions</h2>
  <ul>
  	<li>Current time: <%= new java.util.Date() %>
  	<li>Your hostname: <%= request.getRemoteHost() %>
  	<li>Your session ID: <%= session.getId() %>
  	<li>the <code>testParam</code> form parameter: 
  		<%= request.getParameter("testParam") %>
  </ul>
  </body>
  </html>
  ```

  ​

### 2.04 Directive 编译指令

- Directive(编译指令)相当于在编译期间的命令

- 格式:

  > <%@Directive 属性="属性值" %>

- 常见的Directive:

  - page
  - include
  - taglib



#### Directive-page

- 指明JSP Container 的沟通方式

- 基本格式:

  ```jsp
  <%@ page language="java" contentType="text/html; charset=UTF-8"
      pageEncoding="UTF-8"%>
  ```

- 例: TestImport.jsp

  ```jsp
  <%@ page language="java" contentType="text/html; charset=UTF-8"
      pageEncoding="UTF-8" %>
  <%@ page import="java.util.*" %>
  <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
  <html>
  <head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
  <title>Test Import</title>
  </head>
  <body>
  <%= new Date() %>
  <%
  	out.println("你好");
  %>
  </body>
  </html>
  ```

- 例: TestErr.jsp

  ```jsp
  <%@ page language="java" contentType="text/html; charset=UTF-8"
      pageEncoding="UTF-8"%>
  <%@ page errorPage="ErrPage.jsp" %>
  <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
  <html>
  <head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
  <title>Test Err</title>
  </head>
  <body>
  <%
  String s = "123Plus";
  int i = Integer.parseInt(s);
  out.println("s=" + s + " i=" + i);
  %>
  </body>
  </html>
  ```

  ErrPage.jsp

  ```jsp
  <%@ page language="java" contentType="text/html; charset=UTF-8"
      pageEncoding="UTF-8"%>
  <%@ page isErrorPage="true" %>
  <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
  <html>
  <head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
  <title>ErrPage</title>
  </head>
  <body text="red">
  错误信息: <%= exception.getMessage() %>
  </body>
  </html>
  ```



#### Directive-include

- 在编译时将指定的JSP程序或者HTML文件包含进来

- 格式:

  > <%@include file="fileURL" %>

- JSP Engine 会在JSP程序的转换时期先把file属性设定的文件包含进来，然后开始执行转换及编译的工作

- 限制: 不能像fileURL中传递参数

- 例: TestBar.jsp

  ```jsp
  <%@ page language="java" contentType="text/html; charset=UTF-8"
      pageEncoding="UTF-8"%>
  <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
  <html>
  <head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
  <title>Insert title here</title>
  </head>
  <body>
  <table width="100%">
  <tr><td><%@ include file="TitleBar.jsp" %></td></tr>
  <tr><td><% out.println("<p>这是用户显示区<p>"); %>
  </table>
  </body>
  </html>
  ```

  TitleBar.jsp

  ```jsp
  <%@ page language="java" contentType="text/html; charset=UTF-8"
      pageEncoding="UTF-8"%>
  <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
  <html>
  <head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
  <title>TitleBar</title>
  </head>
  <body>
  <% out.println("Hi:" + request.getParameter("user")); %>
  </body>
  </html>
  ```

  ​

### 2.05 Action 动作指令

- Action是在运行期间的命令
- 常见的Action:
  - jsp:useBean
    - jsp:setProperty
    - jsp:getProperty
  - jsp:include
  - jsp:forward
    - jsp:param
  - jsp:plugin



#### jsp:include/jsp:param

- 用于动态包含jsp程序或html文件等

- 除非这个指令被执行到，否则它是不会被tomcat等jsp engine编译

- jsp:param用来设定include文件时的参数和对应的值

- 格式:

  ```jsp
  <jsp:include page="URLSpec" flush="true"/>
  <jsp:include page="URLSpec" flush="true">
    	<jsp:param name="ParamName" value="ParamValue"/>
  </jsp:include>
  ```

- 例: include.jsp

  ```jsp
  <%@ page language="java" contentType="text/html; charset=UTF-8"
      pageEncoding="UTF-8"%>
  <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
  <html>
  <head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
  <title>include</title>
  </head>
  <body>
  <%@ include file="date.jsp" %>
  <jsp:include page="date.jsp"></jsp:include>
  </body>
  </html>
  ```

  date.jsp

  ```jsp
  <%@ page language="java" contentType="text/html; charset=UTF-8"
      pageEncoding="UTF-8"%>
  <%@ page import="java.util.*" %>
  <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
  <html>
  <head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
  <title>date</title>
  </head>
  <body>
  <%= (new Date()).toLocaleString() %>
  </body>
  </html>
  ```

  ​

#### jsp:forward/jsp:param

- 用于将一个jsp的内容传送到page所指定的jsp程序或者Servlet中处理(URL)

- 格式:

  ```jsp
  <jsp:forward page="urlSpec" flush="true"/>
  <jsp:forward page="urlSpec">
    	<jsp:param name="paramName" value="paramValue"/>
  </jsp:forward>
  ```

- jsp:param用于指定参数和其对应的值

- forward的页面和forward到的页面用的是同一个request

- 与此相对应的是: response.sendRedirect

  - jsp:forward与response.sendRedirect的区别
    - jsp:forward使用同一个request
    - response.sendRedirect是不同的request



#### jsp:useBean

- 通过jsp:useBean，可以在JSP中使用定义好的Bean

- 基本用法:

  ```jsp
  <jsp:useBean id="beanName" scope="page|request|session|application"
               class="package BeanClass" type="typeName"/>
  <jsp:useBean...>
    	<jsp:setProperty...>
      <jsp:getProperty...>
  </jsp:useBean>
  ```

- jsp:useBean各项参数含义:

  - id: 对象实例名称
  - scope: Bean作用的范围，默认为page，对整个jsp页面有效
  - class: Bean类名称
  - beanName: Bean的名称
  - type: Bean实例类型，可以是本类，或其父类，或实现的接口，默认为本类

- Scope各项参数的意义:

  - page: 仅涵盖使用javaBean的页面
  - request: 有效范围仅限于使用javaBean的请求
  - session: 有效范围在用户整个连接过程中(整个会话阶段均有效)
  - application: 有效范围涵盖整个应用程序。也就是对整个网站均有效

- Bean应该放在包内，不要使用裸体类

- 例: CounterBean.java

  ```java
  package com.ryoukai.servlet;
  import java.io.Serializable;

  public class CounterBean implements Serializable {
  	int count = 0;
  	
  	public CounterBean() {}

  	public int getCount() {
  		count++;
  		return count;
  	}

  	public void setCount(int count) {
  		this.count = count;
  	}
  }
  ```

  StringBean.jsp

  ```jsp
  <%@ page language="java" contentType="text/html; charset=UTF-8"
      pageEncoding="UTF-8"%>

  <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
  <html>
  <head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
  <title>StringBean</title>
  </head>
  <body>

  <jsp:useBean id="cb" class="com.ryoukai.servlet.CounterBean"></jsp:useBean>
  <%-- 这句话相当于<% CounterBean cb = new CounterBean(); %> --%>

  <jsp:setProperty property="count" name="cb" value="23"/>
  <%-- 这句话相当于<% cb.setCount(23); %> --%>

  <jsp:getProperty property="count" name="cb"/>
  <%-- 这句话相当于<% out.write(cb.getCount()); %> --%>

  </body>
  </html>
  ```

  ​

### 2.06 JSP内置对象

- out
- request
- response
- pageContext
- session
- application
- config
- exception
- page



#### out

- 常用方法
  - println()  向客户端输出各种类型数据
  - newLine()  输出一个换行符



#### request

- 常用方法
  - getParameter("paramName")  获得Form提交过来的参数值 



### response

- 常用方法
  - addCookie(Cookie cookie)  添加一个Cookie对象
  - addHeader(String name, String value)  添加HTTP头信息，该头信息将发送到客户端
  - sendError(int)  向客户端发送错误的信息，参数为错误码
  - sendRedirect(String url)  重定向JSP文件
  - setContentType(String contentType)  设置MIME类型与编码方式