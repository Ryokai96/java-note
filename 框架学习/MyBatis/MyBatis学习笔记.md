# MyBatis学习笔记

## 1. MyBatis原理

### 1.01 原生态jdbc程序中问题总结

1. 创建数据库和表，插入初始化数据

   ```sql
   create database testmybatis;
   use testmybatis;

   CREATE TABLE `testmybatis`.`user` (
     `id` INT NOT NULL AUTO_INCREMENT,
     `username` VARCHAR(32) NOT NULL COMMENT '用户名称',
     `birthday` DATE NULL COMMENT '生日',
     `sex` CHAR NULL COMMENT '性别',
     `address` VARCHAR(256) NULL COMMENT '地址',
     PRIMARY KEY (`id`))
   ENGINE = InnoDB;

   INSERT INTO `testmybatis`.`user` (`id`, `username`, `birthday`, `sex`, `address`) VALUES ('1', '张三', '2000-02-11', 'm', '江西省南昌市');
   ```

2. jdbc程序: 使用jdbc查询testmybatis数据库中user表的记录

   添加jar包

   - mysql-connector-java-5.1.42-bin.jar

   TestJdbc.java

   ```java
   package com.ryoukai.mybatis.jdbc;

   import java.sql.Connection;
   import java.sql.DriverManager;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.SQLException;


   public class TestJdbc {
   	
   	public static void main(String[] args) {
   		Connection connection = null;
   		PreparedStatement preparedStatement = null;
   		ResultSet resultSet = null;
   		
   		try {
   			//加载数据库驱动
   			Class.forName("com.mysql.jdbc.Driver");
   			
   			//获取数据库连接
   			connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/testmybatis?characterEncoding=utf-8", "root", "root");
   			
   			String sql = "select * from user where username=?";

   			//获取预处理statement
   			preparedStatement = connection.prepareStatement(sql);
   			//设置参数
   			preparedStatement.setString(1, "张三");
   			//执行sql语句，返回查询结果集
   			resultSet = preparedStatement.executeQuery();
   			
   			//遍历查询结果集
   			while(resultSet.next()) {
   				System.out.println(resultSet.getString("id") + " " + resultSet.getString("username"));
   			}
   		} catch (ClassNotFoundException e) {
   			e.printStackTrace();
   		} catch (SQLException e) {
   			e.printStackTrace();
   		} finally {
   			//释放资源
   			if(resultSet != null) {
   				try {
   					resultSet.close();
   				} catch (SQLException e) {
   					e.printStackTrace();
   				}
   			}
   			if(preparedStatement != null) {
   				try {
   					preparedStatement.close();
   				} catch (SQLException e) {
   					e.printStackTrace();
   				}
   			}
   			if(connection != null) {
   				try {
   					connection.close();
   				} catch (SQLException e) {
   					e.printStackTrace();
   				}
   			}
   		}
   	}
   }
   ```

3. 上述程序问题总结

   - 数据库连接，使用时就创建，不使用时就释放，对数据库进行频繁连接开启和关闭，造成数据库资源浪费，影响了数据库性能

     设想解决方案:

     - 使用数据库连接池

   - 将sql语句硬编码到java代码中，如果sql语句要修改，需要重新编译java代码，不利于系统维护

     设想解决方案:

     - 将sql语句配置在xml配置文件中，即使sql语句发生变化，也不需要重新编译java代码

   - 向preparedStatement中设置参数，对占位符号位置和设置参数值，硬编码在java代码中，不利于系统维护

     设想解决方案:

     - 将sql语句及占位符及参数全部配置在xml配置文件中

   - 从resultSet中遍历结果集数据时，将获取表的字段进行了硬编码，不利于系统维护

     设想解决方案:

     - 将查询的结果集自动映射成java对象



### 1.02 MyBatis框架概述

- MyBatis是一个持久层框架，是Apache下的顶级项目，后来托管到GoogleCode下，再后来托管到GitHub下
- MyBatis让程序员将主要精力放在sql上，通过MyBatis提供的映射方式，自由灵活生成(半自动化，大部分需要程序员编写sql)满足需求的sql语句
- MyBatis可以将向preparedStatement中输入的参数自动进行输入映射，将查询结果集灵活映射成java对象



### 1.03 MyBatis框架原理

- SqlMapConfig.xml(MyBatis的全局配置文件): 配置了数据源、事务等MyBatis运行环境
- mapper.xml: 配置映射关系(配置sql语句)
- SqlSessionFactory(会话工厂): 根据配置文件创建会话工厂，作用: 创建SqlSession(会话)
- SqlSession(会话): 是一个接口，作用: 操作数据库(发出sql)
- Executor(执行器): 也是一个接口(有两个实现: 基本执行器、缓存执行器)，SqlSession内部通过Executor操作数据库
- MappedStatement(底层封装对象): 对操作数据库进行封装，包括sql语句、输入参数、输出结果类型



### 1.04 MyBatis目录结构

- mybatis-3.4.4.jar: 核心包
- lib目录下: 依赖包



## 2. MyBatis使用入门

### 2.01 查询

1. MyBatis全局配置文件

   SqlMapConfig.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE configuration
   PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
   "http://mybatis.org/dtd/mybatis-3-config.dtd">

   <configuration>
   	<!-- 和Spring整合后environments配置将废除 -->
   	<environments default="development">
   		<environment id="development">
   			<!-- 使用jdbc事务管理，事务控制由MyBatis完成 -->
   			<transactionManager type="JDBC"/>
   			<!-- 数据库连接池，由MyBatis管理 -->
   			<dataSource type="POOLED">
   				<property name="driver" value="com.mysql.jdbc.Driver"/>
   				<property name="url" value="jdbc:mysql://localhost:3306/testmybatis?characterEncoding=utf-8"/>
   				<property name="username" value="root"/>
   				<property name="password" value="root"/>
   			</dataSource>
   		</environment>
   	</environments>
   </configuration>
   ```

2. 创建po类

   User.java

   ```java
   package com.ryoukai.mybatis.po;

   import java.util.Date;

   public class User {
   	
   	private Integer id;
   	private String username;
   	private String sex;
   	private Date birthday;
   	private String address;
   	
   	public Integer getId() {
   		return id;
   	}
   	public void setId(Integer id) {
   		this.id = id;
   	}
   	public String getUsername() {
   		return username;
   	}
   	public void setUsername(String username) {
   		this.username = username;
   	}
   	public String getSex() {
   		return sex;
   	}
   	public void setSex(String sex) {
   		this.sex = sex;
   	}
   	public Date getBirthday() {
   		return birthday;
   	}
   	public void setBirthday(Date birthday) {
   		this.birthday = birthday;
   	}
   	public String getAddress() {
   		return address;
   	}
   	public void setAddress(String address) {
   		this.address = address;
   	}
   	
   }
   ```

   ​

3. 映射文件

   User.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE mapper
   PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
   "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

   <!-- namespace命名空间，作用是对sql进行分类化管理，进行sql隔离
   注意: 使用mapper代理方法开发的话，namespace有特殊重要的作用
    -->
   <mapper namespace="test">
   	<!-- 在映射文件中配置sql语句 -->
   	<!-- id表示statement的id，parameterType表示参数类型，resultType表示输出结果所映射的java对象类型 -->
   	<select id="findUserById" parameterType="int" resultType="com.ryoukai.mybatis.po.User">
   		<!-- #{}表示占位符，其中的id表示接收输入的参数，参数名称为id，若输入的参数是简单类型，#{}中的参数名称可以任意
   		 -->
   		select * from user where id=#{id}
   	</select>
   </mapper>
   ```

4. 在SqlMapConfig.xml中加载映射文件

   ```xml
   <!-- 加载映射文件 -->
   <mappers>
     <mapper resource="sqlmap/User.xml"/>
   </mappers>
   ```

5. 编写代码

   MyBatisFirst.java

   ```java
   package com.ryoukai.mybatis.first;

   import java.io.IOException;
   import java.io.InputStream;

   import org.apache.ibatis.io.Resources;
   import org.apache.ibatis.session.SqlSession;
   import org.apache.ibatis.session.SqlSessionFactory;
   import org.apache.ibatis.session.SqlSessionFactoryBuilder;
   import org.junit.Test;

   import com.ryoukai.mybatis.po.User;

   public class MyBatisFirst {
   	
   	//根据id查询用户信息，得到一条信息的结果
   	@Test
   	public void findUserById() {
   		
   		//MyBatis配置文件
   		String resource = "SqlMapConfig.xml";
   		
   		InputStream inputStream = null;
   		
   		try {	
   			//得到配置文件流
   			inputStream = Resources.getResourceAsStream(resource);
   		} catch (IOException e) {
   			e.printStackTrace();
   		}
   		
   		//创建会话工厂
   		SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
   		
   		//通过工厂得到SqlSession
   		SqlSession sqlSession = sqlSessionFactory.openSession();
   		
   		//通过SqlSession操作数据，第一个参数为statement的id，第二个参数指定和映射文件中所匹配的parameterType类型的参数
   		User user = sqlSession.selectOne("test.findUserById", 1);
   		
   		System.out.println(user.getUsername() + " " + user.getAddress());
   		
   		//释放资源
   		sqlSession.close();
   	}
   }
   ```

6. 根据用户名模糊查询

   User.xml中添加

   ```xml
   <!-- 模糊查询，可能返回多条 -->
   <select id="findUserByName" parameterType="java.lang.String" resultType="com.ryoukai.mybatis.po.User">
     <!-- ${}表示拼接sql字符串，将接收到的参数不加任何修饰拼接在sql中 -->
     <!-- 使用${}容易引起sql注入，不建议使用，如果传入的是简单类型，${}中只能写value -->
     select * from user where username like '%${value}%'
   </select>
   ```

   MyBatisFirst.java中添加新的方法

   ```java
   //根据用户名称模糊查询用户列表
   @Test
   public void findUserByName() {

     //MyBatis配置文件
     String resource = "SqlMapConfig.xml";

     InputStream inputStream = null;

     try {	
       //得到配置文件流
       inputStream = Resources.getResourceAsStream(resource);
     } catch (IOException e) {
       e.printStackTrace();
     }

     //创建会话工厂
     SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

     //通过工厂得到SqlSession
     SqlSession sqlSession = sqlSessionFactory.openSession();

     //List中的User和resultType中resultType所指定的一致
     List<User> list = sqlSession.selectList("test.findUserByName", "小明");

     for(int i = 0; i < list.size(); i++) {
       System.out.println(list.get(i).getUsername());
     }
   }
   ```

   ​

