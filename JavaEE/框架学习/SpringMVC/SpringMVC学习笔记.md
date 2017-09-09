# SpringMVC学习笔记

## 1. SpringMVC简介

### 1.01 什么是SpringMVC

- SpringMVC是Spring的一个模块，SpringMVC和Spring无需通过中间整合层进行整合
- SpringMVC是一个基于MVC的web框架





### 1.02 MVC在b/s系统下的应用

- c/s和b/s架构:

  - c/s架构: Client/Server，即客户端/服务端架构，典型的两层架构
  - b/s架构: Browser/Server，即浏览器/服务器架构。
    - Browser指的是Web浏览器，极少数事务逻辑在前端实现，但主要事务逻辑在服务器端实现
    - Browser客户端，WebApp服务器端和DB端构成所谓的三层架构
    - b/s架构的系统无需特别安装，只要有Web浏览器即可

- MVC:

  - M: model
    - pojo
    - service
    - dao
  - V: view
  - C: controller

  ```sequence
  用户->controller: request请求
  controller->model: 请求模型进行处理
  model->controller: 返回处理结果
  controller->view: 视图渲染，将模型数据填充到request域
  controller->用户: response响应
  ```

- SpringMVC

  ```sequence
  用户->前端控制器DispatcherServlet: request请求
  前端控制器DispatcherServlet->处理器映射器HandlerMapping: 请求根据request中的url查找handler
  处理器映射器HandlerMapping->Handler处理器(也叫controller): 根据url查找Handler
  处理器映射器HandlerMapping->前端控制器DispatcherServlet: 返回一个执行链HandlerExecutionChain
  前端控制器DispatcherServlet->处理器适配器HandlerAdapter: 请求执行Handler
  处理器适配器HandlerAdapter->Handler处理器(也叫controller): 执行
  Handler处理器(也叫controller)->处理器适配器HandlerAdapter: 返回ModelAndView
  处理器适配器HandlerAdapter->前端控制器DispatcherServlet: 返回ModelAndView
  前端控制器DispatcherServlet->视图解析器ViewResolver: 请求视图解析
  视图解析器ViewResolver->前端控制器DispatcherServlet: 返回view
  前端控制器DispatcherServlet->view: 视图渲染，将模型数据填充到request域
  前端控制器DispatcherServlet->用户: response响应
  ```

  步骤解析:

  1. 发起请求到前端控制器(DispatcherServlet)
  2. 前端控制器请求HandlerMapping查找Handler，可以根据xml配置、注解进行查找
  3. 处理器映射器HandlerMapping向前端控制器返回Handler
  4. 前端控制器调用处理器适配器(HandlerAdapter)去执行Handler
  5. 处理器适配器执行Handler
  6. Handler执行完成后向处理器适配器返回ModelAndView(SpringMVC的一个底层对象，包括了model和view)
  7. 处理器适配器向前端控制器返回ModelAndView
  8. 前端控制器请求视图解析器(ViewResolver)进行视图解析
  9. 视图解析器根据逻辑视图名解析成真正的视图(jsp)
  10. 视图解析器向前端控制器返回View
  11. 前端控制器进行视图渲染，将模型数据(模型数据在ModelAndView中)填充到request域
  12. 前端控制器向用户响应结果

  组件:

  - 前端控制器DispatcherServlet
    - 作用: 接收请求，响应结果，相当于转发器、中央处理器，减少其他组件之间的耦合度
    - 不需要程序员开发
  - 处理器映射器HandlerMapping
    - 作用: 根据请求的url查找Handler
    - 不需要程序员开发
  - 处理器适配器HandlerAdapter
    - 作用: 按照特定规则(HandlerAdapter要求的规则)执行Handler
    - 不需要程序员开发
  - 处理器Handler
    - 注意: 编写Handler时要按照HandlerAdapter的要求去写，这样适配器才能正确执行Handler
    - 需要程序员开发
  - 视图解析器ViewResolver
    - 作用: 进行视图解析，根据逻辑视图名解析成真正视图(SpringMVC中的View对象)
    - 不需要程序员开发
  - 视图View
    - View是一个接口，实现类支持不同的View类型(jsp、freemarker、pdf、excel...)
    - 需要程序员开发jsp




## 2. SpringMVC的简单使用

### 2.01 SpringMVC配置文件

- 在web.xml中配置加载SpringMVC的配置文件

  ```xml
  <servlet>
    <servlet-name>springmvc</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
  </servlet>

  <!-- contextConfigLocation配置加载springmvc的配置文件 -->
  <init-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:springmvc.xml</param-value>
  </init-param>

  <servlet-mapping>
    <servlet-name>springmvc</servlet-name>
    <!-- url-pattern的几种配置方法
      1、*.action 所有以.action结尾都由的DispatcherServlet进行解析
      2、/  所有访问的地址都由DispatcherServlet进行解析，对于静态文件的解析需要配置不让DispatcherServlet进行解析，使用此种方式可以实现RESTFUL风格的url
      3、/* 这种配置是错误的。当最终转发到一个jsp页面时，仍然会由DispatcherServlet解析jsp，不能根据jsp页面找到Handler，会报错
      -->
    <url-pattern>/</url-pattern>
  </servlet-mapping>
  ```

- springmvc配置的头

  springmvc.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
  	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  	xmlns:xsi="http://www.springframework.org/schema/mvc"
  	xmlns:context="http://www.springframework.org/schema/context"
  	xmlns:aop="http://www.springframework.org/schema/aop"
  	xmlns:tx="http://www.springframework.org/schema/tx"
  	xsi:schemaLocation="http://www.springframework.org/schema/beans
  	http://www.springframework.org/schema/beans/spring-beans.xsd
  	http://www.springframework.org/schema/mvc
  	http://www.springframework.org/schema/mvc/spring-mvc.xsd
  	http://www.springframework.org/schema/context
  	http://www.springframework.org/schema/context/spring-context.xsd
  	http://www.springframework.org/schema/aop
  	http://www.springframework.org/schema/aop/spring-aop.xsd
  	http://www.springframework.org/schema/tx
  	http://www.springframework.org/schema/tx/spring-tx.xsd">
  	
  </beans>
  ```

- 开发Handler

  需要实现controller接口，才能由org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter适配器来执行

  ItemsController1.java

  ```java
  package com.ryoukai.ssm.controller;

  import java.util.ArrayList;
  import java.util.List;

  import javax.servlet.http.HttpServletRequest;
  import javax.servlet.http.HttpServletResponse;

  import org.springframework.web.servlet.ModelAndView;
  import org.springframework.web.servlet.mvc.Controller;

  import com.ryoukai.ssm.po.Items;

  public class ItemsController1 implements Controller{

  	@Override
  	public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
  		//调用service查找数据库，这里使用静态数据模拟
  		List<Items> itemsList = new ArrayList<Items>();
  		
  		//向list中填充静态数据
  		Items items = new Items();
  		items.setName("good");
  		items.setPrice(200f);
  		items.setDetail("very good good");
  		itemsList.add(items);
  		
  		//返回ModelAndView
  		ModelAndView modelAndView = new ModelAndView();
  		//指定数据，addObject相当于setAttribute
  		modelAndView.addObject("itemsList", itemsList);
  		//指定视图
  		modelAndView.setViewName("/WEB-INF/jsp/items/itemsList.jsp");
  		
  		return modelAndView;
  	}

  }
  ```

- 配置Handler

  ```xml
  <!-- 配置Handler
  将bean的name作为url进行查找，需要在配置Handler时指定beanname(就是url) 
  -->
  <bean name="/queryItems.action" class="com.ryoukai.ssm.controller.ItemsController1"/>
  ```

- 配置处理器映射器

  ```xml
  <!-- 配置处理器映射器 -->
  <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
  ```

- 配置处理器适配器

  ```xml
  <!-- 处理器适配器
    所有处理器适配器都实现HandlerAdapter接口，框架据此判断是否是处理器适配器
    -->
  <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
  ```

- 配置视图解析器

  配置解析jsp的视图解析器

  ```xml
  <!-- 视图解析器
  		解析jsp标签的话，默认使用jstl标签
  	-->
  <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"/>
  ```

- 访问页面时注意事项

  - 地址需输入配置文件中配置的Handler的url，处理器映射器根据这个url找到Handler



### 2.02 非注解的处理器映射器和适配器

- 非注解的处理器映射器:
  - org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping