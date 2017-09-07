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


### 2.02 插入

1. 在映射文件中添加插入语句

   User.xml

   ```xml
   <!-- 添加用户 -->
   <!-- parameterType中填写pojo类型 -->
   <insert id="insertUser" parameterType="com.ryoukai.mybatis.po.User">
     <!-- #{}中只当属性名，MyBatis通过OGNL获取对象的属性值 -->
     insert into user(id,username,birthday,sex,address) values(#{id},#{username},#{birthday},#{sex},#{address})
   </insert>
   ```

2. 写代码测试

   MyBatisFirst.java

   ```java
   //添加用户信息
   @Test
   public void insertUser() {

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

     //插入用户对象
     User user = new User();
     user.setUsername("王小军");
     user.setBirthday(new Date());
     user.setSex("m");
     user.setAddress("江苏南京");

     sqlSession.insert("test.insertUser", user);

     //提交事务
     sqlSession.commit();

     //关闭会话
     sqlSession.close();
   }
   ```

3. 获取自增主键

   修改User.xml

   ```xml
   <!-- 添加用户 -->
   <!-- parameterType中填写pojo类型 -->
   <insert id="insertUser" parameterType="com.ryoukai.mybatis.po.User">
     <!-- 将插入数据的自增主键返回到user对象中 -->
     <!-- keyProperty: 将返回的结果设置到parameterType指定的对象对应名称的属性
      order: 指定select语句相对于insert语句的执行顺序
      resultType: 指定select返回结果的类型
      -->
     <selectKey keyProperty="id" order="AFTER" resultType="java.lang.Integer">
       <!-- last_insert_id()时mysql的函数，返回刚刚insert进去记录的主键值，只适用于自增主键 -->
       select last_insert_id()
     </selectKey>
     <!-- #{}中只当属性名，MyBatis通过OGNL获取对象的属性值 -->
     insert into user(id,username,birthday,sex,address) values(#{id},#{username},#{birthday},#{sex},#{address})
   </insert>
   ```

   修改MyBatisFirst.java

   ```java
   //添加用户信息
   @Test
   public void insertUser() {

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

     //插入用户对象
     User user = new User();
     user.setUsername("王小军");
     user.setBirthday(new Date());
     user.setSex("m");
     user.setAddress("江苏南京");

     sqlSession.insert("test.insertUser", user);

     //检查是否返回id
     System.out.println(user.getId());

     //提交事务
     sqlSession.commit();

     //关闭会话
     sqlSession.close();
   }
   ```

4. 使用uuid生成id

   User.xml

   ```xml
   <!-- 添加用户 -->
   <!-- parameterType中填写pojo类型 -->
   <insert id="insertUser" parameterType="com.ryoukai.mybatis.po.User">
     <!-- 将插入数据的自增主键返回到user对象中 -->
     <!-- keyProperty: 将返回的结果设置到parameterType指定的对象对应名称的属性
      order: 指定select语句相对于insert语句的执行顺序
      resultType: 指定select返回结果的类型
      -->
     <!-- <selectKey keyProperty="id" order="AFTER" resultType="java.lang.Integer">
      last_insert_id()时mysql的函数，返回刚刚insert进去记录的主键值，只适用于自增主键
      select last_insert_id()
     </selectKey> -->

     <!-- #{}中只当属性名，MyBatis通过OGNL获取对象的属性值 -->
     insert into user(id,username,birthday,sex,address) values(#{id},#{username},#{birthday},#{sex},#{address})

     <!-- 使用mysql的uuid()函数生成主键，将主键设置到user对象的id属性中 -->
     <selectKey keyProperty="id" order="BEFORE" resultType="java.lang.String">
       select uuid()
     </selectKey>
   </insert>
   ```

   ​

### 2.03 删除和更新

1. 在映射文件中写sql语句

   User.xml

   ```xml
   <!-- 根据id删除用户 -->
   <delete id="deleteUser" parameterType="java.lang.Integer">
     delete from user where id=#{id}
   </delete>

   <!-- 根据id更新用户，注意: user对象中的id为查询所用id -->
   <update id="updateUser" parameterType="com.ryoukai.mybatis.po.User">
     <!-- #{id}为从输入user对象中获取id的属性值 -->
     update user set username=#{username},birthday=#{birthday},sex=#{sex},address=#{address} where id=#{id}
   </update>
   ```

2. 写测试代码

   MyBatisFirst.java

   ```java
   //删除用户信息
   @Test
   public void deleteUser() {

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

     sqlSession.delete("test.deleteUser", 26);

     //提交事务
     sqlSession.commit();

     //关闭会话
     sqlSession.close();
   }

   //更新用户信息
   @Test
   public void updateUser() {

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

     //更新用户对象
     User user = new User();
     user.setId(25);
     user.setUsername("王小往");
     user.setBirthday(new Date());
     user.setSex("m");
     user.setAddress("江苏南京");

     sqlSession.update("test.updateUser", user);

     //检查是否返回id
     System.out.println(user.getId());

     //提交事务
     sqlSession.commit();

     //关闭会话
     sqlSession.close();
   }
   ```




## 3. MyBatis和Hibernate的本质区别和应用场景

- Hibernate: 是一个标准ORM框架(对象关系映射)，不需要在程序中写sql语句，sql语句自动生成，对sql语句进行优化、修改比较困难
  - 应用场景: 适用于需求变化较少的中小型项目，比如: 后台管理系统、erp、orm、oa
- MyBatis: 专注sql本身，需要在程序中自己编写sql语句，sql语句的修改、优化比较方便
  - 应用场景: 适用于需求变化较多的项目，比如: 互联网项目



## 4. MyBatis开发Dao的方法

### 4.01 SqlSession使用范围

1. 通过SqlSessionFactoryBuilder创建SqlSessionFactory，把SqlSessionFactoryBuilder当作一个工具类来使用，不需要使用单例模式，在需要创建SqlSessionFactory时，只需要new一个SqlSessionFactoryBuilder即可
2. 通过SqlSessionFactory创建SqlSession，使用单例模式管理SqlSessionFactory
3. SqlSession是面向用户(程序员)的接口，SqlSession中提供了很多操作数据库的方法，SqlSession是线程不安全的，在SqlSession实现类中除了接口中的方法，还有数据域的属性。
4. SqlSession最佳应用场合在方法体内，定义成局部变量使用



### 4.02 原始Dao开发方法

- 程序员需要写Dao接口和实现类
- 需要向dao实现类中注入SqlSessionFactory，在方法体内通过SqlSessionFactory创建SqlSession



#### 示例

1. Dao接口编写

   UserDao.java

   ```java
   package com.ryoukai.mybatis.dao;

   import com.ryoukai.mybatis.po.User;

   public interface UserDao {
   	
   	//根据id查询用户信息
   	public User findUserById(int id);
   	
   	//添加用户信息
   	public void insertUser(User user);
   	
   	//根据id删除用户信息
   	public void deleteUser(int id);
   	
   	//更新用户信息
   	public void updateUser(User user);
   }
   ```

2. 编写Dao接口的实现类

   UserDaoImpl.java

   ```java
   package com.ryoukai.mybatis.dao.impl;

   import org.apache.ibatis.session.SqlSession;
   import org.apache.ibatis.session.SqlSessionFactory;

   import com.ryoukai.mybatis.dao.UserDao;
   import com.ryoukai.mybatis.po.User;

   /**
    * UserDao接口实现类
    * @author Ryoukai
    *
    */
   public class UserDaoImpl implements UserDao{
   	
   	private SqlSessionFactory sqlSessionFactory;
   	
   	//通过构造函数注入SqlSessionFactory
   	public UserDaoImpl(SqlSessionFactory sqlSessionFactory) {
   		this.sqlSessionFactory = sqlSessionFactory;
   	}

   	@Override
   	public User findUserById(int id) {
   		SqlSession sqlSession = sqlSessionFactory.openSession();
   		User user = sqlSession.selectOne("test.findUserById", id);
   		sqlSession.close();
   		return user;
   	}

   	@Override
   	public void insertUser(User user) {
   		SqlSession sqlSession = sqlSessionFactory.openSession();
   		sqlSession.insert("test.insertUser", user);
   		sqlSession.commit();
   		sqlSession.close();
   	}

   	@Override
   	public void deleteUser(int id) {
   		SqlSession sqlSession = sqlSessionFactory.openSession();
   		sqlSession.insert("test.deleteUser", id);
   		sqlSession.commit();
   		sqlSession.close();
   	}

   	@Override
   	public void updateUser(User user) {
   		SqlSession sqlSession = sqlSessionFactory.openSession();
   		sqlSession.insert("test.updateUser", user);
   		sqlSession.commit();
   		sqlSession.close();
   	}
   }
   ```

3. 编写测试类

   UserDaoImplTest.java

   ```java
   package com.ryoukai.mybatis.dao.impl;

   import java.io.IOException;
   import java.io.InputStream;

   import org.apache.ibatis.io.Resources;
   import org.apache.ibatis.session.SqlSessionFactory;
   import org.apache.ibatis.session.SqlSessionFactoryBuilder;
   import org.junit.Before;
   import org.junit.Test;

   import com.ryoukai.mybatis.dao.UserDao;
   import com.ryoukai.mybatis.po.User;

   public class UserDaoImplTest {

   	private SqlSessionFactory sqlSessionFactory;
   	
   	//此方法在testFindUserById()之前执行
   	@Before
   	public void setUp() {
   		String resource = "SqlMapConfig.xml";
   		InputStream inputStream = null;
   		try {
   			inputStream = Resources.getResourceAsStream(resource);
   		} catch (IOException e) {
   			e.printStackTrace();
   		}
   		sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
   		
   	}
   	
   	@Test
   	public void testFindUserById() {
   		UserDao userDao = new UserDaoImpl(sqlSessionFactory);
   		User user = userDao.findUserById(1);
   		System.out.println(user.getUsername());
   	}

   }
   ```



### 4.03 原始Dao开发问题总结

1. dao接口实现类方法中存在大量模板方法，若能将这些代码提取出来，将大大减少程序员的工作量
2. 调用sqlSession方法时，将statement的id硬编码了
3. 调用sqlSession方法时传入的变量，由于sqlSession的方法使用泛型，即使变量类型传入错误，在编译阶段也不报错，不利于程序开发



### 4.04 Mapper代理开发

- 程序员只需要编写mapper接口(相当于dao接口)
- 程序员还需要编写mapper.xml映射文件
- 只要程序员编写mapper接口遵循一些开发规范，MyBatis就可以自动生成Mapper接口实现类的代理对象



#### 开发规范

- 在mapper.xml中namespace等于mapper接口地址
- mapper接口中的方法名和mapper.xml中statement的id一致
- mapper接口中方法输入参数类型和mapper.xml中statement的parameterType指定的类型一致
- mapper接口中方法的返回值类型和mapper.xml中statement的resultType指定的类型一致
- 如果mapper接口中方法返回单个pojo对象(非集合对象)，代理对象的内部通过selectOne查询数据库
- 如果mapper接口中方法返回集合对象，代理对象的内部通过selectList查询数据库
- mapper接口中方法的参数只能有一个



#### 示例过程

1. 创建mapper接口

   UserMapper.java

   ```java
   package com.ryoukai.mybatis.mapper;

   import com.ryoukai.mybatis.po.User;

   public interface UserMapper {
   	
   }
   ```

2. 创建mapper映射文件

   UserMapper.xml，在namespace中填写UserMapper的地址

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE mapper
   PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
   "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

   <mapper namespace="com.ryoukai.mybatis.mapper.UserMapper">
   	<select id="findUserById" parameterType="int"
   		resultType="com.ryoukai.mybatis.po.User">
   		select * from user where id=#{id}
   	</select>

   	<select id="findUserByName" parameterType="java.lang.String" resultType="com.ryoukai.mybatis.po.User">
   		select * from user where username like '%${value}%'
   	</select>
   	
   	<insert id="insertUser" parameterType="com.ryoukai.mybatis.po.User">
   		<selectKey keyProperty="id" order="AFTER" resultType="java.lang.Integer">
   			select last_insert_id()
   		</selectKey>
         
   		insert into user(id,username,birthday,sex,address) values(#{id},#{username},#{birthday},#{sex},#{address})
   	</insert>

   	<delete id="deleteUser" parameterType="java.lang.Integer">
   		delete from user where id=#{id}
   	</delete>
   	
   	<update id="updateUser" parameterType="com.ryoukai.mybatis.po.User">
   		update user set username=#{username},birthday=#{birthday},sex=#{sex},address=#{address} where id=#{id}
   	</update>
   	
   </mapper>
   ```

3. 将映射文件加载到MyBatis全局配置文件SqlMapConfig.xml

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
   			<transactionManager type="JDBC" />
   			<!-- 数据库连接池，由MyBatis管理 -->
   			<dataSource type="POOLED">
   				<property name="driver" value="com.mysql.jdbc.Driver" />
   				<property name="url"
   					value="jdbc:mysql://localhost:3306/testmybatis?characterEncoding=utf-8" />
   				<property name="username" value="root" />
   				<property name="password" value="root" />
   			</dataSource>
   		</environment>
   	</environments>

   	<!-- 加载映射文件 -->
   	<mappers>
   		<mapper resource="mapper/UserMapper.xml"/>
   	</mappers>
   </configuration>
   ```

4. 在UserMapper.java中添加抽象方法

   注意方法名要和UserMapper.xml中statement的id一致，方法输入参数类型和UserMapper.xml中statement的parameterType指定的类型一致，方法的返回值类型和UserMapper.xml中statement的resultType指定的类型一致

   UserMapper.java

   ```java
   package com.ryoukai.mybatis.mapper;

   import com.ryoukai.mybatis.po.User;

   public interface UserMapper {
   	
   	//根据id查询用户信息
   	public User findUserById(int id);	
     
   	//根据用户名模糊查询
   	public List<User> findUserByName(String name);
   	
   	//添加用户信息
   	public void insertUser(User user);
   	
   	//根据id删除用户信息
   	public void deleteUser(int id);
   	
   	//更新用户信息
   	public void updateUser(User user);
   }
   ```

5. 编写测试类

   UserMapperTest.java

   ```java
   package com.ryoukai.mybatis.mapper;

   import java.io.IOException;
   import java.io.InputStream;

   import org.apache.ibatis.io.Resources;
   import org.apache.ibatis.session.SqlSession;
   import org.apache.ibatis.session.SqlSessionFactory;
   import org.apache.ibatis.session.SqlSessionFactoryBuilder;
   import org.junit.Before;
   import org.junit.Test;

   import com.ryoukai.mybatis.po.User;

   public class UserMapperTest {

   	private SqlSessionFactory sqlSessionFactory;
   	
   	@Before
   	public void setUp() {
   		String resource = "SqlMapConfig.xml";
   		InputStream inputStream = null;
   		try {
   			inputStream = Resources.getResourceAsStream(resource);
   		} catch (IOException e) {
   			e.printStackTrace();
   		}
   		sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
   	}

   	@Test
   	public void testFindUserById() {
   		SqlSession sqlSession = sqlSessionFactory.openSession();
   		
   		//创建UserMapper实例对象，MyBatis自动生成UserMapper的代理对象
   		UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
   		
         	//调用userMapper的方法
   		User user = userMapper.findUserById(1);	
   		System.out.println(user.getUsername());
   		
   		List<User> list = userMapper.findUserByName("小明");
   		for(int i = 0; i < list.size(); i++) {
   			System.out.println(list.get(i).getUsername());
   		}
   	}
   }
   ```

   ​

#### mapper接口方法参数只能有一个对开发是否有影响

- 系统框架中，dao层的代码是被业务层公用的
- 即使mapper接口只有一个参数，仍然可以使用包装类型的pojo满足不同业务方法的需求
  - 注意: 持久层方法的参数可以使用包装类型，service方法中建议不要使用包装类型(因为不利于业务层的扩展维护)



## 5. 高级映射示例

### 5.01 建表，分析表关系

- user表(用户表)

  ```mysql
  CREATE TABLE `user` (
    `id` int(11) NOT NULL,
    `username` varchar(32) COLLATE utf8mb4_unicode_ci NOT NULL COMMENT '用户名称',
    `birthday` date DEFAULT NULL COMMENT '生日',
    `sex` char(1) COLLATE utf8mb4_unicode_ci DEFAULT NULL COMMENT '性别',
    `address` varchar(256) COLLATE utf8mb4_unicode_ci DEFAULT NULL COMMENT '地址',
    PRIMARY KEY (`id`)
  ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci
  ```

- items表(商品表)

  ```mysql
  CREATE TABLE `items` (
    `id` int(11) NOT NULL AUTO_INCREMENT,
    `name` varchar(32) COLLATE utf8mb4_unicode_ci NOT NULL COMMENT '商品名称',
    `price` float(10,1) NOT NULL COMMENT '商品定价',
    `detail` text COLLATE utf8mb4_unicode_ci COMMENT '商品描述',
    `pic` varchar(512) COLLATE utf8mb4_unicode_ci DEFAULT NULL COMMENT '商品图片',
    `createtime` datetime DEFAULT NULL COMMENT '生产日期',
    PRIMARY KEY (`id`)
  ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci
  ```

- orders表(订单表)

  ```mysql
  CREATE TABLE `orders` (
    `id` int(11) NOT NULL AUTO_INCREMENT,
    `user_id` int(11) NOT NULL COMMENT '下单用户id',
    `number` varchar(32) COLLATE utf8mb4_unicode_ci NOT NULL COMMENT '订单号',
    `createtime` datetime NOT NULL COMMENT '创建订单时间',
    `note` varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL COMMENT '备注',
    PRIMARY KEY (`id`),
    KEY `FK_orders_id` (`user_id`),
    CONSTRAINT `FK_orders_id` FOREIGN KEY (`user_id`) REFERENCES `user` (`id`) ON DELETE NO ACTION ON UPDATE NO ACTION
  ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci
  ```

- orderdetail表

  ```mysql
  CREATE TABLE `orderdetail` (
    `id` int(11) NOT NULL AUTO_INCREMENT,
    `orders_id` int(11) NOT NULL COMMENT '订单id',
    `items_id` int(11) NOT NULL COMMENT '商品id',
    `items_num` int(11) DEFAULT NULL COMMENT '商品的购买数量',
    PRIMARY KEY (`id`),
    KEY `FK_orderdetail_1` (`orders_id`),
    KEY `FK_orderdetail_2` (`items_id`),
    CONSTRAINT `FK_orderdetail_1` FOREIGN KEY (`orders_id`) REFERENCES `orders` (`id`) ON DELETE NO ACTION ON UPDATE NO ACTION,
    CONSTRAINT `FK_orderdetail_2` FOREIGN KEY (`items_id`) REFERENCES `items` (`id`) ON DELETE NO ACTION ON UPDATE NO ACTION
  ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci
  ```

- 分析表关系:

  - user和orders:
    - user->orders: 一个用户可以创建多个订单，一对多
    - orders->user: 一个订单只由一个用户创建，一对一
  - orders和orderdetail:
    - orders->orderdetail: 一个订单可以包含多个订单明细，一对多
    - orderdetail->orders: 一个订单明细只包括在一个订单中，一对一
  - orderdetail和items:
    - orderdetail->items: 一个订单明细包含一个商品信息，一对一
    - items->orderdetail: 一个商品可以包括在多个订单明细中，多对一
  - orders和items: 通过orderdetail建立关系
    - orders->items: 一对多
    - items->orders: 一对多
    - 总之orders和items是多对多关系
  - user和items: 多对多关系




### 5.02 一对一查询

- 需求: 查询订单信息，关联查询创建订单的用户信息

- 确定查询主表和关联表: 

  - 主表: 订单表
  - 关联表: 用户表

- sql语句

  ```sql
  select orders.*,user.username,user.sex,user.address
  from orders, user --外连接
  where orders.user_id = user.id;
  ```



#### resultType

- 创建pojo: 将上边sql查询的结果映射到pojo中，pojo中必须包含所有查询列名

  Orders.java

  ```java
  package com.ryoukai.mybatis.po;

  import java.util.Date;
  import java.util.List;

  public class Orders {
  	private Integer id;
  	private Integer userId;
  	private String number;
  	private Date createtime;
  	private String note;
  	
  	public Integer getId() {
  		return id;
  	}
  	public void setId(Integer id) {
  		this.id = id;
  	}
  	public Integer getUserId() {
  		return userId;
  	}
  	public void setUserId(Integer userId) {
  		this.userId = userId;
  	}
  	public String getNumber() {
  		return number;
  	}
  	public void setNumber(String number) {
  		this.number = number;
  	}
  	public Date getCreatetime() {
  		return createtime;
  	}
  	public void setCreatetime(Date createtime) {
  		this.createtime = createtime;
  	}
  	public String getNote() {
  		return note;
  	}
  	public void setNote(String note) {
  		this.note = note;
  	}
  	
  }
  ```

  原始的Orders.java不能映射全部字段，需要创建新的pojo

  OrdersCustom.java

  ```java
  package com.ryoukai.mybatis.po;

  /**
   * 通过此类映射订单和用户查询的结果，让此类继承字段较多的pojo类
   * @author Ryoukai
   *
   */
  public class OrdersCustom extends Orders {
  	private String username;
  	private String sex;
  	private String address;
  	
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
  	public String getAddress() {
  		return address;
  	}
  	public void setAddress(String address) {
  		this.address = address;
  	}
  }
  ```


- OrdersMapperCustom.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

  <!-- namespace命名空间，作用是对sql进行分类化管理，进行sql隔离 注意: 使用mapper代理方法开发的话，namespace有特殊重要的作用 -->
  <mapper namespace="com.ryoukai.mybatis.mapper.OrdersMapperCustom">
  	<!-- 查询订单关联查询用户信息 -->
  	<select id="findOrdersUser" resultType="com.ryoukai.mybatis.po.OrdersCustom">
  		select orders.*,user.username,user.sex,user.address
  			from orders, user
  			where orders.user_id = user.id
  	</select>
  	
  </mapper>
  ```

- OrdersMapperCustom.java

  ```java
  package com.ryoukai.mybatis.mapper;

  import java.util.List;

  import com.ryoukai.mybatis.po.OrdersCustom;

  public interface OrdersMapperCustom {
  	public List<OrdersCustom> findOrdersUser() throws Exception;
  }
  ```

- 测试类

  ```java
  package com.ryoukai.mybatis.mapper;

  import java.io.IOException;
  import java.io.InputStream;
  import java.util.List;

  import org.apache.ibatis.io.Resources;
  import org.apache.ibatis.session.SqlSession;
  import org.apache.ibatis.session.SqlSessionFactory;
  import org.apache.ibatis.session.SqlSessionFactoryBuilder;
  import org.junit.Before;
  import org.junit.Test;

  import com.ryoukai.mybatis.po.OrdersCustom;

  public class OrdersMapperCustomTest {
  	
  	private SqlSessionFactory sqlSessionFactory;
  	
  	//此方法在testFindUserById()之前执行
  	@Before
  	public void setUp() {
  		String resource = "SqlMapConfig.xml";
  		InputStream inputStream = null;
  		try {
  			inputStream = Resources.getResourceAsStream(resource);
  		} catch (IOException e) {
  			e.printStackTrace();
  		}
  		sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
  		
  	}

  	@Test
  	public void testFindOrdersUser() throws Exception {
  		SqlSession sqlSession = sqlSessionFactory.openSession();
  		OrdersMapperCustom ordersMapperCustom = sqlSession.getMapper(OrdersMapperCustom.class);
  		List<OrdersCustom> list = ordersMapperCustom.findOrdersUser();
  		for(OrdersCustom ordersCustom : list) {
  			System.out.println(ordersCustom.getUsername());
  		}
  	}

  }
  ```



#### resultMap

- 使用resultMap将查询结果中的订单信息映射到Orders对象，在Orders类中添加User属性，将关联查询出来的用户信息映射到Oders对象中的User属性中

  Orders.java

  ```java
  package com.ryoukai.mybatis.po;

  import java.util.Date;

  public class Orders {
  	private Integer id;
  	private Integer userId;
  	private String number;
  	private Date createtime;
  	private String note;
  	//用户信息
  	private User user;
  	
  	public Integer getId() {
  		return id;
  	}
  	public void setId(Integer id) {
  		this.id = id;
  	}
  	public Integer getUserId() {
  		return userId;
  	}
  	public void setUserId(Integer userId) {
  		this.userId = userId;
  	}
  	public String getNumber() {
  		return number;
  	}
  	public void setNumber(String number) {
  		this.number = number;
  	}
  	public Date getCreatetime() {
  		return createtime;
  	}
  	public void setCreatetime(Date createtime) {
  		this.createtime = createtime;
  	}
  	public String getNote() {
  		return note;
  	}
  	public void setNote(String note) {
  		this.note = note;
  	}
  	public User getUser() {
  		return user;
  	}
  	public void setUser(User user) {
  		this.user = user;
  	}
  	
  }
  ```

- OrdersMapperCustom.xml

  ```java
  <?xml version="1.0" encoding="UTF-8"?>
  <!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

  <!-- namespace命名空间，作用是对sql进行分类化管理，进行sql隔离 注意: 使用mapper代理方法开发的话，namespace有特殊重要的作用 -->
  <mapper namespace="com.ryoukai.mybatis.mapper.OrdersMapperCustom">

  	<!-- 订单查询关联用户的resultMap -->
  	<!-- 将整个查询的结果映射到com.ryoukai.mybatis.po.Orders中 -->
  	<resultMap type="com.ryoukai.mybatis.po.Orders" id="OrdersUserResultMap">
  		<!-- 配置映射的订单信息 -->
  		<!-- id:指定查询订单信息中的唯一标识，如果有多个列组成唯一标识，则需要配置多个id
  			column: 表中代表唯一标识的列
  			property: po中和表中列对应的属性
  		-->
  		<id column="id" property="id"/>
  		<result column="user_id" property="userId"/>
  		<result column="number" property="number"/>
  		<result column="createtime" property="createtime"/>
  		<result column="note" property="note"/>
  		
  		<!-- 配置映射的关联的用户信息 -->
  		<!-- association: 用于映射关联查询单个对象的信息
  			property: 要将关联查询的用户信息映射到Orders中的属性
  		 -->
  		<association property="user" javaType="com.ryoukai.mybatis.po.User">
  			<!-- id: 关联查询用户的唯一标识
  				column: Orders表中唯一标识用户信息的列
  				property: User的po类中与之对应的属性
  			 -->
  			<id column="user_id" property="id"/>
  			<result column="username" property="username"/>
  			<result column="sex" property="sex"/>
  			<result column="address" property="address"/>
  		</association>
  	</resultMap>
  	
  	<!-- 使用resultMap查询订单关联查询用户信息 -->
  	<select id="findOrdersUserResultMap" resultMap="OrdersUserResultMap">
  		select orders.*,user.username,user.sex,user.address
  			from orders, user
  			where orders.user_id = user.id
  	</select>
  	
  </mapper>
  ```

- OrdersMapperCustom.java

  ```java
  package com.ryoukai.mybatis.mapper;

  import java.util.List;

  import com.ryoukai.mybatis.po.Orders;
  import com.ryoukai.mybatis.po.OrdersCustom;

  public interface OrdersMapperCustom {
    
  	//使用resultMap查询订单关联查询用户信息
  	public List<Orders> findOrdersUserResultMap() throws Exception;
  }
  ```

  ​


#### ResultType和ResultMap的对比

- 实现一对一查询，使用resultType实现较为简单，如果pojo中没有包括查询出来的列名，需要增加对应的属性，即可完成映射，如果没有查询结果的特殊要求，建议使用resultType
- resultMap需要单独定义resultMap，实现有点麻烦，如果对查询结果有特殊要求，使用resultMap可以完成将关联查询映射到pojo的属性中
- resultMap可以实现延迟加载，而resultType无法实现延迟加载




### 5.03 一对多查询

- 需求: 查询订单及订单明细的信息

- 确定主查询表和关联查询表

  - 主查询表: 订单表
  - 关联查询表: 订单明细表

- sql语句

  ```mysql
  select 
  	orders.*,
  	user.username,
  	user.sex,
  	user.address,
  	orderdetail.id orderdetail_id,	-- 使用别名，防止和order.id重复
  	orderdetail.items_id,
  	orderdetail.items_num,
  	orderdetail.orders_id
  from 
  	orders, 
  	user, 
  	orderdetail -- 外连接
  where 
  	orders.user_id=user.id and orderdetail.orders_id=orders.id;
  ```

- 要求对orders的映射不能出现重复记录

  在Orders.java中添加List\<Orderdetail> orderdetails属性

  最终会将订单信息映射到orders中，订单所对应的订单明细映射到orders中的orderdetails属性中

  每个orders中的orderdetails属性存储了该订单所对应的订单明细

- 在Orders.java中添加订单明细属性

  ```java
  package com.ryoukai.mybatis.po;

  import java.util.Date;
  import java.util.List;

  public class Orders {
  	private Integer id;
  	private Integer userId;
  	private String number;
  	private Date createtime;
  	private String note;
  	//用户信息
  	private User user;
  	//订单明细
  	private  List<Orderdetail> orderdetails;
  	
  	public Integer getId() {
  		return id;
  	}
  	public void setId(Integer id) {
  		this.id = id;
  	}
  	public Integer getUserId() {
  		return userId;
  	}
  	public void setUserId(Integer userId) {
  		this.userId = userId;
  	}
  	public String getNumber() {
  		return number;
  	}
  	public void setNumber(String number) {
  		this.number = number;
  	}
  	public Date getCreatetime() {
  		return createtime;
  	}
  	public void setCreatetime(Date createtime) {
  		this.createtime = createtime;
  	}
  	public String getNote() {
  		return note;
  	}
  	public void setNote(String note) {
  		this.note = note;
  	}
  	public User getUser() {
  		return user;
  	}
  	public void setUser(User user) {
  		this.user = user;
  	}
  	public List<Orderdetail> getOrderdetails() {
  		return orderdetails;
  	}
  	public void setOrderdetails(List<Orderdetail> orderdetails) {
  		this.orderdetails = orderdetails;
  	}
  	
  }
  ```

- OrdersMapperCustom.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

  <!-- namespace命名空间，作用是对sql进行分类化管理，进行sql隔离 注意: 使用mapper代理方法开发的话，namespace有特殊重要的作用 -->
  <mapper namespace="com.ryoukai.mybatis.mapper.OrdersMapperCustom">
  	<!-- 查询订单关联查询用户信息 -->
  	<select id="findOrdersUser" resultType="com.ryoukai.mybatis.po.OrdersCustom">
  		select orders.*,user.username,user.sex,user.address
  			from orders, user
  			where orders.user_id = user.id
  	</select>
  	
  	<!-- 订单查询关联用户的resultMap -->
  	<!-- 将整个查询的结果映射到com.ryoukai.mybatis.po.Orders中 -->
  	<resultMap type="com.ryoukai.mybatis.po.Orders" id="OrdersUserResultMap">
  		<!-- 配置映射的订单信息 -->
  		<!-- id:指定查询订单信息中的唯一标识，如果有多个列组成唯一标识，则需要配置多个id
  			column: 表中代表唯一标识的列
  			property: po中和表中列对应的属性
  		-->
  		<id column="id" property="id"/>
  		<result column="user_id" property="userId"/>
  		<result column="number" property="number"/>
  		<result column="createtime" property="createtime"/>
  		<result column="note" property="note"/>
  		
  		<!-- 配置映射的关联的用户信息 -->
  		<!-- association: 用于映射关联查询单个对象的信息
  			property: 要将关联查询的用户信息映射到Orders中的属性
  		 -->
  		<association property="user" javaType="com.ryoukai.mybatis.po.User">
  			<!-- id: 关联查询用户的唯一标识
  				column: Orders表中唯一标识用户信息的列
  				property: User的po类中与之对应的属性
  			 -->
  			<id column="user_id" property="id"/>
  			<result column="username" property="username"/>
  			<result column="sex" property="sex"/>
  			<result column="address" property="address"/>
  		</association>
  	</resultMap>
  	
  	<!-- 查询订单及订单明细的resultmap
  	使用extends继承，就不用在这里配置订单信息和用户信息的映射
  	 -->
  	<resultMap type="com.ryoukai.mybatis.po.Orders" id="OrdersAndOrderdetailResultMap" extends="OrdersUserResultMap">
  		
  		<!-- 订单明细信息 -->
  		<!-- 一个订单可关联查询出多条订单明细，要使用collection进行映射
  		collection: 对关联查询到多条记录映射到集合对象中
  		property: 将关联查询到的多条记录映射到Orders中的哪个属性中
  		ofType: 指定要映射到集合属性中pojo的类型
  		-->
  		<collection property="orderdetails" ofType="com.ryoukai.mybatis.po.Orderdetail">
  			<!-- id: 订单明细的唯一标识
  				property: 要将订单明细的唯一标识映射到Orderdetail类中的哪个属性
  			-->
  			<id column="orderdetail_id" property="id"/>
  			<result column="items_id" property="itemsId"/>
  			<result column="items_num" property="itemsNum"/>
  			<result column="orders_id" property="ordersId"/>
  		</collection>
  	</resultMap>
  	
  	
  	<!-- 使用resultMap查询订单关联查询用户信息 -->
  	<select id="findOrdersUserResultMap" resultMap="OrdersUserResultMap">
  		select orders.*,user.username,user.sex,user.address
  			from orders, user
  			where orders.user_id = user.id
  	</select>
  	
  	<!-- 使用resultMap查询订单关联查询用户信息及订单明细 -->
  	<select id="findOrdersAndOrderdetailResultMap" resultMap="OrdersAndOrderdetailResultMap">
  		select order.*,user.username,user.sex,user.address,orderdetail.id orderdetail_id,orderdetail.items_id,orderdetail.items_num,orderdetail.orders_id
  			from orders, user, orderdetail
  			where orders.user_id = user.id and orderdetail.orders_id=orders.id
  	</select>
  	
  </mapper>
  ```

- OrdersMapperCustom.java

  ```java
  package com.ryoukai.mybatis.mapper;

  import java.util.List;

  import com.ryoukai.mybatis.po.Orders;
  import com.ryoukai.mybatis.po.OrdersCustom;

  public interface OrdersMapperCustom {
  	public List<OrdersCustom> findOrdersUser() throws Exception;
  	
  	//使用resultMap查询订单关联查询用户信息
  	public List<Orders> findOrdersUserResultMap() throws Exception;
  	
  	//查询订单(关联用户)及订单明细
  	public List<Orders> findOrdersAndOrderdetailResultMap() throws Exception;
  }
  ```

- 测试类

  ```java
  package com.ryoukai.mybatis.mapper;

  import java.io.IOException;
  import java.io.InputStream;
  import java.util.List;

  import org.apache.ibatis.io.Resources;
  import org.apache.ibatis.session.SqlSession;
  import org.apache.ibatis.session.SqlSessionFactory;
  import org.apache.ibatis.session.SqlSessionFactoryBuilder;
  import org.junit.Before;
  import org.junit.Test;

  import com.ryoukai.mybatis.po.Orders;
  import com.ryoukai.mybatis.po.OrdersCustom;

  public class OrdersMapperCustomTest {
  	
  	private SqlSessionFactory sqlSessionFactory;
  	
  	//此方法在testFindUserById()之前执行
  	@Before
  	public void setUp() {
  		String resource = "SqlMapConfig.xml";
  		InputStream inputStream = null;
  		try {
  			inputStream = Resources.getResourceAsStream(resource);
  		} catch (IOException e) {
  			e.printStackTrace();
  		}
  		sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
  		
  	}

  	@Test
  	public void testFindOrdersUser() throws Exception {
  		SqlSession sqlSession = sqlSessionFactory.openSession();
  		OrdersMapperCustom ordersMapperCustom = sqlSession.getMapper(OrdersMapperCustom.class);
  		List<OrdersCustom> list = ordersMapperCustom.findOrdersUser();
  		for(OrdersCustom ordersCustom : list) {
  			System.out.println(ordersCustom.getUsername());
  		}
  	}
  	
  	@Test
  	public void testFindOrdersAndOrderdetailResultMap() throws Exception {
  		SqlSession sqlSession = sqlSessionFactory.openSession();
  		OrdersMapperCustom ordersMapperCustom = sqlSession.getMapper(OrdersMapperCustom.class);
  		List<Orders> list = ordersMapperCustom.findOrdersAndOrderdetailResultMap();
  		for(Orders order : list) {
  			System.out.println(order.getOrderdetails());
  		}
  	}

  }
  ```

- 小结

  - MyBatis使用resultMap中collection对关联查询的多条记录映射到一个list集合属性中
  - 若使用resultTyoe实现，将订单明细映射到Orders中的orderdetails属性中，则需要自己使用双重循环遍历，去掉重复记录



### 5.04 多对多查询

- 需求: 查询用户及购买商品信息

- 查询主表和关联表: 

  - 主表: 用户表
  - 关联表: 由于用户和商品没有直接关联，是通过订单和订单明细进行关联，所以关联表是orders、orderdetail、items

- sql语句

  ```mysql
  select 
  	orders.*,
  	user.username,
  	user.sex,
  	user.address,
  	orderdetail.id orderdetail_id,	-- 使用别名，防止和order.id重复
  	orderdetail.items_id,
  	orderdetail.items_num,
  	orderdetail.orders_id,
  	items.name items_name,
  	items.detail items_detail,
  	items.price items_price
  from 
  	orders, 
  	user, 
  	orderdetail,
  	items
  where 
  	orders.user_id=user.id and orderdetail.orders_id=orders.id and orderdetail.items_id=items.id;
  ```

- 映射思路:

  - 将用户信息映射到user中
  - 在user类中添加订单列表属性 List\<Orders> ordersList，将用户创建的订单映射到ordersList
  - 在Orders中添加订单明细列表属性 List\<Orderdetail> orderdetails，将订单明细映射到orderdetails
  - 在Orderdetail中添加items属性，将订单明细所对应的商品映射到items中

- OrdersMapperCustom.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

  <mapper namespace="com.ryoukai.mybatis.mapper.OrdersMapperCustom">
    
  	<!-- 查询用户及其购买的商品 -->
  	<resultMap type="com.ryoukai.mybatis.po.User" id="UserAndItemsResultMap">
  		<!-- 用户信息 -->
  		<id column="user_id" property="id"/>
  		<result column="username" property="username"/>
  		<result column="sex" property="sex"/>
  		<result column="address" property="address"/>
  		
  		<!-- 订单信息，一个用户对应多个订单，使用collection -->
  		<collection property="ordersList" ofType="com.ryoukai.mybatis.po.Orders">
  			<id column="id" property="id"/>
  			<result column="user_id" property="userId"/>
  			<result column="number" property="number"/>
  			<result column="createtime" property="createtime"/>
  			<result column="note" property="note"/>
  			
  			<!-- 订单明细，一个订单包含多个订单明细 -->
  			<collection property="orderdetails" ofType="com.ryoukai.mybatis.po.Orderdetail">
  				<id column="orderdetail_id" property="id"/>
  				<result column="items_id" property="itemsId"/>
  				<result column="items_num" property="itemsNum"/>
  				<result column="orders_id" property="ordersId"/>
  				
  				<!-- 商品信息，一个订单明细对应一个商品 -->
  				<association property="items" javaType="com.ryoukai.mybatis.po.Items">
  					<id column="items_id" property="id"/>
  					<result column="items_name" property="name"/>
  					<result column="items_detail" property="detail"/>
  					<result column="items_price" property="price"/>
  				</association>
  			</collection>
  		</collection>
  	
  	</resultMap>

  	
  	<!-- 使用resultMap查询用户及购买的商品信息 -->
  	<select id="findUserAndItemsResultMap" resultMap="UserAndItemsResultMap">
  		select 
  			orders.*,
  			user.username,
  			user.sex,
  			user.address,
  			orderdetail.id orderdetail_id,
  			orderdetail.items_id,
  			orderdetail.items_num,
  			orderdetail.orders_id,
  			items.name items_name,
  			items.detail items_detail,
  			items.price items_price
  		from 
  			orders, 
  			user, 
  			orderdetail,
  			items
  		where 
  			orders.user_id=user.id and orderdetail.orders_id=orders.id and orderdetail.items_id=items.id
  	</select>
  	
  </mapper>
  ```

- OrdersMapperCustom.java

  ```java
  package com.ryoukai.mybatis.mapper;

  import java.util.List;

  import com.ryoukai.mybatis.po.User;

  public interface OrdersMapperCustom {
  	
  	//查询用户购买商品信息
  	public List<User> findUserAndItemsResultMap() throws Exception;
  }
  ```

- 测试类

  ```java
  package com.ryoukai.mybatis.mapper;

  import java.io.IOException;
  import java.io.InputStream;
  import java.util.List;

  import org.apache.ibatis.io.Resources;
  import org.apache.ibatis.session.SqlSession;
  import org.apache.ibatis.session.SqlSessionFactory;
  import org.apache.ibatis.session.SqlSessionFactoryBuilder;
  import org.junit.Before;
  import org.junit.Test;

  import com.ryoukai.mybatis.po.User;

  public class OrdersMapperCustomTest {
  	
  	private SqlSessionFactory sqlSessionFactory;
  	
  	//此方法在testFindUserById()之前执行
  	@Before
  	public void setUp() {
  		String resource = "SqlMapConfig.xml";
  		InputStream inputStream = null;
  		try {
  			inputStream = Resources.getResourceAsStream(resource);
  		} catch (IOException e) {
  			e.printStackTrace();
  		}
  		sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
  		
  	}
  	
  	@Test
  	public void testFindUserAndItemsResultMap() throws Exception {
  		SqlSession sqlSession = sqlSessionFactory.openSession();
  		OrdersMapperCustom ordersMapperCustom = sqlSession.getMapper(OrdersMapperCustom.class);
  		List<User> list = ordersMapperCustom.findUserAndItemsResultMap();
  		System.out.println(list);
  	}

  }
  ```

- 多对多查询总结:

  - 查询用户购买的商品信息明细清单(用户名、用户地址、购买商品名称、购买商品时间、购买商品数量)，使用resultType将查询的记录映射到一个扩展的pojo中，很简单实现明细清单的功能
  - 使用resultMap是针对那些查询结果映射有特殊要求的功能，比如要求映射成的list中包含多个list



### 5.05 延迟加载(懒加载)

- 延迟加载: 先从单表查询、需要时再从关联表去关联查询，大大提高数据库性能，因为查询单表要比关联查询多张表快
  - 例: 查询订单并且关联查询用户信息，如果先查询订单信息即可满足要求，当我们需要查询用户信息时再查询用户信息。对用户信息的按需查询就叫做延迟加载
- resultMap拥有association和collection，具备延迟加载的功能



#### 使用association实现延迟加载

- 需求: 查询订单并且关联查询用户信息

- 需要定义两个mapper的方法对应的statement

  - 查询订单信息，在查询订单的statement中使用assocition去延迟加载关联查询用户信息
  - 关联查询用户信息(通过查询到的订单信息中user_id去关联查询用户信息)

- OrdersMapperCustom.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

  <mapper namespace="com.ryoukai.mybatis.mapper.OrdersMapperCustom">	
  	
  	<!-- 延迟加载的resultMap -->
  	<resultMap type="com.ryoukai.mybatis.po.Orders" id="OrdersUserLazyLoadingResultMap">
  		<!-- 对订单信息进行映射配置 -->
  		<id column="id" property="id"/>
  		<result column="user_id" property="userId"/>
  		<result column="number" property="number"/>
  		<result column="createtime" property="createtime"/>
  		<result column="note" property="note"/>
  		
  		<!-- 实现对用户信息进行延迟加载
  			select: 指定延迟加载需要执行的statement的id(根据user_id查询用户信息的statement)
  				          这里可使用userMapper.xml中的findUserById完成根据用户id查询用户信息
  			column: 订单信息中关联用户信息查询的列(user_id)
  			关联查询的sql等价为:
  			select orders.*
  				(select username from user where orders.userid = user.id)username,
  				(selectsex from user where orders.user_id = user.id)sex
  			from orders
  		-->
  		<association property="user" javaType="com.ryoukai.mybatis.po.User" select="com.ryoukai.mybatis.mapper.UserMapper.findUserById" column="user_id"></association>
  	</resultMap>
  	
  	<!-- 查询订单关联查询用户，用户信息需要延迟加载 -->
  	<select id="findOrdersUserLazyLoading" resultMap="OrdersUserLazyLoadingResultMap">
  		select * from orders
  	</select>
  	
  </mapper>
  ```

  UserMapper.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

  <mapper namespace="com.ryoukai.mybatis.mapper.UserMapper">
  	<select id="findUserById" parameterType="int"
  		resultType="com.ryoukai.mybatis.po.User">
  		<!-- #{}表示占位符，其中的id表示接收输入的参数，参数名称为id，若输入的参数是简单类型，#{}中的参数名称可以任意 -->
  		select * from user where id=#{id}
  	</select>
  </mapper>
  ```

- 通过resultMap将延迟加载的执行进行配置，先执行findOrderUserLazyLoading，当需要去查询用户的时候再去执行findUserById

- OdersMapperCustom.java

  ```java
  package com.ryoukai.mybatis.mapper;

  import java.util.List;

  import com.ryoukai.mybatis.po.Orders;

  public interface OrdersMapperCustom {
  	//查询订单关联查询用户，用户信息是延迟加载
  	public List<Orders> findOrdersUserLazyLoading() throws Exception;
  }
  ```

- MyBatis默认没有开启延迟加载，需要在SqlMapConfig.xml中配置

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
  			<transactionManager type="JDBC" />
  			<!-- 数据库连接池，由MyBatis管理 -->
  			<dataSource type="POOLED">
  				<property name="driver" value="com.mysql.jdbc.Driver" />
  				<property name="url"
  					value="jdbc:mysql://localhost:3306/testmybatis?characterEncoding=utf-8" />
  				<property name="username" value="root" />
  				<property name="password" value="root" />
  			</dataSource>
  		</environment>
  	</environments>
  	
  	<!-- 配置延迟加载 -->
  	<settings>
  		<!-- 打开延迟加载 -->
  		<setting name="lazyLoadingEnabled" value="true"/>
  		<!-- 将积极加载改为按需加载 -->
  		<setting name="aggressiveLazyLoading" value="false"/>
  	</settings>

  	<!-- 加载映射文件 -->
  	<mappers>
  		<mapper resource="sqlmap/User.xml" />
  		<mapper resource="mapper/UserMapper.xml"/>
  	</mappers>
  </configuration>
  ```

- 测试类: 

  1. 执行findOrdersUserLazyLoading()方法，该方法内部调用OdersMapperCustom.xml中的findOrdersUserLazyLoading查询Orders信息(此时是单表查询)
  2. 在程序中去遍历上一步查询出的List\<Orders>，当我们调用Oders中的getUser方法时，开始进行延迟加载
  3. 延迟加载，调用UserMapper.xml中的findUserById去获取用户信息

  OrdersMapperCustomTest.java

  ```java
  package com.ryoukai.mybatis.mapper;

  import java.io.IOException;
  import java.io.InputStream;
  import java.util.List;

  import org.apache.ibatis.io.Resources;
  import org.apache.ibatis.session.SqlSession;
  import org.apache.ibatis.session.SqlSessionFactory;
  import org.apache.ibatis.session.SqlSessionFactoryBuilder;
  import org.junit.Before;
  import org.junit.Test;

  import com.ryoukai.mybatis.po.Orders;
  import com.ryoukai.mybatis.po.User;

  public class OrdersMapperCustomTest {
  	
  	private SqlSessionFactory sqlSessionFactory;
  	
  	//此方法在testFindUserById()之前执行
  	@Before
  	public void setUp() {
  		String resource = "SqlMapConfig.xml";
  		InputStream inputStream = null;
  		try {
  			inputStream = Resources.getResourceAsStream(resource);
  		} catch (IOException e) {
  			e.printStackTrace();
  		}
  		sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
  		
  	}
    
  	//查询订单关联查询用户，用户信息使用延迟加载
  	@Test
  	public void testFindOrdersUserLazyLoading() throws Exception {
  		SqlSession sqlSession = sqlSessionFactory.openSession();
  		OrdersMapperCustom ordersMapperCustom = sqlSession.getMapper(OrdersMapperCustom.class);
  		//查询订单信息(单表)
  		List<Orders> list = ordersMapperCustom.findOrdersUserLazyLoading();
  		//遍历list
  		for(Orders order : list) {
  			//执行getUser(去查询用户信息，这里实现按需加载)
  			User user = order.getUser();
  			System.out.println(user);
  		}
  	}

  }
  ```

  ​

#### 手动实现延迟加载

- 定义两个mapper方法
  - 查询订单列表
  - 根据用户id查询用户信息
- 实现思路: 
  1. 执行第一个mapper方法，获取订单信息列表
  2. 在service中，按需去调用第二个mapper方法去查询用户信息



## 6. 查询缓存

- 查询缓存: 用于减轻数据库压力，提升数据库性能
- MyBatis提供一级缓存和二级缓存
  - 一级缓存是sqlSession级别的缓存，MyBatis在操作数据库时需要构造sqlSession对象，在对象中有一个数据结构(HashMap)用于存储缓存数据，不同的sqlSession之间的缓存数据区域(hashMap)是互不影响的
  - 二级缓存是mapper级别的缓存，多个sqlSession去操作同一个mapper的sql语句，多个sqlSession去操作数据库所得到的数据会保存在二级缓存区域(多个sqlSession可以共用一个二级缓存区域)
- 为什么要用缓存
  - 如果缓存中有数据，就不用从数据库中获取，大大提高系统性能



### 6.01 一级缓存

#### 一级缓存原理

- 先去找缓存中是否有需要的数据，如果没有，从数据库查询所需数据，得到的数据存储到一级缓存中。如果缓存中有所需数据，直接从缓存中获取所需数据
- 如果sqlSession去执行commit操作(执行插入、更新、删除)，会清空sqlSession的一级缓存，这样做的目的是为了让缓存中存储的是最新的信息，避免**脏读**




#### 一级缓存测试

- MyBatis默认开启一级缓存，不需要在配置文件中设置

- 测试代码

  ```java
  @Test
  	public void testCache1() {
  		SqlSession sqlSession = sqlSessionFactory.openSession();
  		UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
  		
  		//第一次发起请求，查询id为1的用户
  		User user1 = userMapper.findUserById(1);
  		System.out.println(user1);
  		
  		//第二次发起请求，查询id为1的用户
  		User user2 = userMapper.findUserById(1);
  		System.out.println(user2);
  		
        	//如果过sqlSession去执行commit操作，会清空SqlSession的一级缓存
  		userMapper.updateUser(user1);
  		sqlSession.commit();
  		
  		User user3= userMapper.findUserById(1);
  		System.out.println(user3);
  		
  		sqlSession.close();
  	}
  ```

  ​


#### 一级缓存的应用

- MyBatis和Spring整合开发，事务控制在service中，一个service方法中包括很多mapper方法调用，使用一级缓存可提高性能



### 6.02 二级缓存

- 如果执行两次service调用查询相同的数据，不走一级缓存，因为service方法执行一次后sqlSession就会关闭，一级缓存会清空



#### 二级缓存原理

- 二级缓存比一级缓存范围更大，多个sqlSession可以共享一个Mapper的二级缓存区域(不同二级缓存区域按namespace分)
- 每个namespace的mapper有一个二级缓存区域，两个mapper的namespace如果相同，这两个mapper执行sql查询到数据将存在相同的二级缓存区域中



#### 二级缓存测试

- 开启二级缓存: MyBatis的二级缓存是mapper范围级别，除了在SqlMapConfig.xml中开启外(默认已开启)，还要在具体的mapper.xml中开启二级缓存

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
  			<transactionManager type="JDBC" />
  			<!-- 数据库连接池，由MyBatis管理 -->
  			<dataSource type="POOLED">
  				<property name="driver" value="com.mysql.jdbc.Driver" />
  				<property name="url"
  					value="jdbc:mysql://localhost:3306/testmybatis?characterEncoding=utf-8" />
  				<property name="username" value="root" />
  				<property name="password" value="root" />
  			</dataSource>
  		</environment>
  	</environments>
  	
  	<!-- 配置延迟加载 -->
  	<settings>
  		<!-- 打开延迟加载 -->
  		<setting name="lazyLoadingEnabled" value="true"/>
  		<!-- 将积极加载改为按需加载 -->
  		<setting name="aggressiveLazyLoading" value="false"/>
  		
  		<!-- 开启二级缓存 -->
  		<setting name="cacheEnabled" value="true"/>
  	</settings>

  	<!-- 加载映射文件 -->
  	<mappers>
  		<mapper resource="sqlmap/User.xml" />
  		<mapper resource="mapper/UserMapper.xml"/>
  	</mappers>
  </configuration>
  ```

  UserMapper.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

  <!-- namespace命名空间，作用是对sql进行分类化管理，进行sql隔离 注意: 使用mapper代理方法开发的话，namespace有特殊重要的作用 -->
  <mapper namespace="com.ryoukai.mybatis.mapper.UserMapper">
  	
  	<!-- 开启本mapper的namespace下的二级缓存 -->
  	<cache/>
  	
  	<!-- 在映射文件中配置sql语句 -->
  	<!-- id表示statement的id，parameterType表示参数类型，resultType表示输出结果所映射的java对象类型 -->
  	<select id="findUserById" parameterType="int"
  		resultType="com.ryoukai.mybatis.po.User">
  		<!-- #{}表示占位符，其中的id表示接收输入的参数，参数名称为id，若输入的参数是简单类型，#{}中的参数名称可以任意 -->
  		select * from user where id=#{id}
  	</select>
  	
  	<!-- 模糊查询，可能返回多条 -->
  	<select id="findUserByName" parameterType="java.lang.String" resultType="com.ryoukai.mybatis.po.User">
  		<!-- ${}表示拼接sql字符串，将接收到的参数不加任何修饰拼接在sql中 -->
  		<!-- 使用${}容易引起sql注入，如果传入的是简单类型，${}中只能写value -->
  		select * from user where username like '%${value}%'
  	</select>
  	
  	<!-- 添加用户 -->
  	<!-- parameterType中填写pojo类型 -->
  	<insert id="insertUser" parameterType="com.ryoukai.mybatis.po.User">
  		<!-- 将插入数据的自增主键返回到user对象中 -->
  		<!-- keyProperty: 将返回的结果设置到parameterType指定的对象对应名称的属性
  			order: 指定select语句相对于insert语句的执行顺序
  			resultType: 指定select返回结果的类型
  		 -->
  		<selectKey keyProperty="id" order="AFTER" resultType="java.lang.Integer">
  			<!-- last_insert_id()时mysql的函数，返回刚刚insert进去记录的主键值，只适用于自增主键 -->
  			select last_insert_id()
  		</selectKey>
  		
  		<!-- #{}中只当属性名，MyBatis通过OGNL获取对象的属性值 -->
  		insert into user(id,username,birthday,sex,address) values(#{id},#{username},#{birthday},#{sex},#{address})
  		
  		<!-- 使用mysql的uuid()函数生成主键，将主键设置到user对象的id属性中 -->
  		<!-- <selectKey keyProperty="id" order="BEFORE" resultType="java.lang.String">
  			select uuid()
  		</selectKey> -->
  	</insert>
  	
  	<!-- 根据id删除用户 -->
  	<delete id="deleteUser" parameterType="java.lang.Integer">
  		delete from user where id=#{id}
  	</delete>
  	
  	<!-- 根据id更新用户，注意: user对象中的id为查询所用id -->
  	<update id="updateUser" parameterType="com.ryoukai.mybatis.po.User">
  		<!-- #{id}为从输入user对象中获取id的属性值 -->
  		update user set username=#{username},birthday=#{birthday},sex=#{sex},address=#{address} where id=#{id}
  	</update>
  	
  </mapper>
  ```

- 调用pojo类实现序列化接口: 因为二级缓存数据存储介质多种多样，不一定在内存，为了将缓存数据取出，执行反序列化操作

  User.java

  ```java
  public class User implements Serializable
  ```

- 测试方法

  ```java
  @Test
  public void testCache2() {
      SqlSession sqlSession1 = sqlSessionFactory.openSession();
      SqlSession sqlSession2 = sqlSessionFactory.openSession();
      SqlSession sqlSession3 = sqlSessionFactory.openSession();

      UserMapper userMapper1 = sqlSession1.getMapper(UserMapper.class);
      UserMapper userMapper2 = sqlSession2.getMapper(UserMapper.class);
      UserMapper userMapper3 = sqlSession3.getMapper(UserMapper.class);

      //第一次发起请求，查询id为1的用户
      User user1 = userMapper1.findUserById(1);
      System.out.println(user1);

      //使用sqlSession3执行commit()操作
      User user2 = userMapper2.findUserById(1);
      user2.setUsername("小明");
      sqlSession3.commit();

      User user3 = userMapper3.findUserById(1);
      System.out.println(user3);

      sqlSession1.close();
      sqlSession2.close();
      sqlSession3.close();
  }
  ```

- 禁用二级缓存

  - 在statement中设置useCache="false"可以禁用当前select语句的二级缓存，即每次查询都会发出sql去数据库查询，useCache默认是ture

  例: 在UserMapper.xml中禁用findUserById的二级缓存

  ```xml
  <select id="findUserById" parameterType="int" resultType="com.ryoukai.mybatis.po.User" useCache="false">
      <!-- #{}表示占位符，其中的id表示接收输入的参数，参数名称为id，若输入的参数是简单类型，#{}中的参数名称可以任意 -->
      select * from user where id=#{id}
  </select>
  ```

- 刷新缓存(清空缓存)

  - 在mapper的同一个namespace中，如果有其它insert、update、delete操作数据后需要刷新缓存，如果不执行刷新缓存，就会出现脏读
  - 设置statement配置中的flushCache="true"可以刷新缓存，默认情况下flushCache的值为true，如果改成false则不会刷新，容易出现脏读
  - 一般情况下，执行完commit操作都需要刷新缓存，避免脏读

  例如: 关闭deleteUser的缓存刷新

  ```xml
  <!-- 根据id删除用户 -->
  <delete id="deleteUser" parameterType="java.lang.Integer" flushCache="false">
    	delete from user where id=#{id}
  </delete>
  ```

- Cache参数

  - flushInterval(刷新间隔)可以被设置为任意的正整数，代表隔多少毫秒执行一次缓存刷新，默认情况不会自动刷新缓存
  - size(引用数目)可以被设置为任意的正整数，表示可缓存的对象的数目，默认值是1024
  - readOnly(只读)属性可以被设置为true或false。只读的缓存会给所有调用者返回缓存对象相同的实例，所以这些对象不能被修改。可读写的缓存会返回缓存对象的拷贝(通过序列化)，这会慢一些，但是安全，因此默认是readOnly的值默认是false

  例:

  ```xml
  <cache eviction="FIFO" flushInterval="60000" size="512" readOnly="true"/>
  ```

  ​



### 6.03 MyBatis整合Ehcache

- Ehcache是一个分布式缓存框架



#### 分布式缓存

- 为了提高系统并发、性能，一般对系统进行分布式部署(集群部署方式)
- 不使用分布式缓存，缓存的数据在各自服务器单独存储，不方便系统开发。所以使用分布式缓存对缓存数据进行集中式管理
- MyBatis无法实现分布式缓存，需要和其他的分布式缓存框架进行整合



#### 整合方法

- MyBatis提供了一个cache接口，如果要实现自己的缓存逻辑，就实现这个cache接口即可

- MyBatis提供了一个cache接口的默认实现类PerpetualCache

- MyBatis和Ehcache整合: MyBatis和Ehcache整合包中提供了一个cache接口的实现类

- 例:

  ```xml
  <!-- type: 指定cache接口的实现类类型，MyBatis默认使用PerpetualCache -->
  <cache type="PerpetualCache"/>
  ```

- MyBatis和Ehcache整合步骤:

  1. 加入整合包:

     - ehcache-core-2.6.8.jar
     - mybatis-ehcache-1.0.3.jar

  2. 配置mapper中的cache中的type为ehcache对cache接口的实现类型

     ```xml
     <cache type="org.mybatis.caches.ehcache.EhcacheCache"/>
     ```

  3. 加入Ehcache的配置文件

     ehcache.xml

     ```xml
     <ehcache xmlns="http://www.w3/org/2001/XMLSchema-instance"
     	xsi:xsi:noNamespaceSchemaLocation="../config/ehcache.xsd">
     	<diskStore path="C:\Users\Ryoukai\Desktop\test"/>
     	<defaultCache
     		maxElementsInmemory="1000"
     		maxElementsOnDisk="10000000"
     		eternal="false"
     		overflowToDisk="false"
     		timeToIdleSeconds="120"
     		timeToLiveSeconds="120"
     		diskExpiryThreadIntervalSeconds="120"
     		memoryStoreEvictionPolicy="LRU">
     	</defaultCache>	
     </ehcache>
     ```

     ​

### 6.04 二级缓存应用场景

- 对于访问多且用户对查询结果实时性要求不高的，可采用MyBatis二级缓存技术降低数据库访问量，提高访问速度，例如: 耗时较高的统计分析sql、电话账单查询sql等
- 实现方法: 通过设置刷新间隔时间，由MyBatis每隔一段时间自动清空缓存，根据数据变化频率设置缓存刷新间隔flushInterval，比如设置为30分钟、60分钟、24小时等，根据需求而定



#### 二级缓存局限性

- MyBatis二级缓存对细粒度的数据级别的缓存实现不好，比如如下需求: 对商品信息进行缓存，由于商品信息查询访问量大，但是要求用户每次都能查询到最新的信息，但是MyBatis无法实现当一个商品信息变化时只刷新该商品的信息，因为MyBatis的二级缓存区域以mapper为单位划分，当一个商品信息变化会将所有商品的缓存数据全部清空。解决此类问题需要在业务层根据需求对数据有针对性缓存



## 7. MyBatis和Spring整合

### 7.01 整合思路: 

- 需要Spring通过单例方式管理SqlSessionFactory
- Spring和MyBatis整合生成代理对象，使用SqlSessionFactory创建SqlSession。(此步骤由Spring和MyBatis整合自动完成)
- 持久层的mapper都需要由Spring进行管理



### 7.01 整合步骤

#### 原始Dao开发整合步骤

1. 导入jar包

   - MyBatis的jar包
   - Spring的jar包
   - MyBatis和Spring的整合包: 早期iBatis和Spring整合包由Spring官方提供，现在MyBatis和Spring整合包由MyBatis官方提供
     - mybatis-spring-1.3.1.jar

2. SqlSessionFactory的配置

   - 在applicationContext.xml配置SqlSessionFactory和数据源
   - SqlSessionFactory在MyBatis和Spring的整合包下

   applicationContext.xml

   ```xml
   <beans xmlns="http://www.springframework.org/schema/beans"
   	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   	xmlns:context="http://www.springframework.org/schema/context"
   	xmlns:aop="http://www.springframework.org/schema/aop"
   	xmlns:tx="http://www.springframework.org/schema/tx"
   	xsi:schemaLocation="http://www.springframework.org/schema/beans
   	http://www.springframework.org/schema/beans/spring-beans.xsd
   	http://www.springframework.org/schema/context
   	http://www.springframework.org/schema/context/spring-context.xsd
   	http://www.springframework.org/schema/aop
   	http://www.springframework.org/schema/aop/spring-aop.xsd
   	http://www.springframework.org/schema/tx
   	http://www.springframework.org/schema/tx/spring-tx.xsd">
     
     	<!-- 加载配置文件 -->
     	<context:property-placeholder location="classpath:db.properties"/>
     	
     	<!-- 配置数据源，使用dbcp连接池 -->
     	<bean id="dataSource" class="org.apache.commons.dbcp2.BasicDataSource" destroy-method="close">
     		<!-- 加载db.properties -->
     		<property name="driverClassName" value="${jdbc.driver}"/>
     		<property name="url" value="${jdbc.url}"/>
     		<property name="username" value="${jdbc.username}"/>
     		<property name="password" value="${jdbc.password}"/>

     	</bean>
     	
     	<!-- 配置sqlSessionFactory -->
     	<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
     		<!-- 加载MyBatis的配置文件 -->
     		<property name="configLocation" value="mybatis/SqlMapConfig.xml"/>
     		<!-- 数据源 -->
     		<property name="dataSource" ref="dataSource"/>
     	</bean>
     	
   </beans>
   ```

3. 配置mapper.xml

   拷贝之前使用的User.xml，稍做修改(修改包名)

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE mapper
   PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
   "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

   <!-- namespace命名空间，作用是对sql进行分类化管理，进行sql隔离 注意: 使用mapper代理方法开发的话，namespace有特殊重要的作用 -->
   <mapper namespace="test">
   	<!-- 在映射文件中配置sql语句 -->
   	<!-- id表示statement的id，parameterType表示参数类型，resultType表示输出结果所映射的java对象类型 -->
   	<select id="findUserById" parameterType="int"
   		resultType="com.ryoukai.ssm.po.User">
   		<!-- #{}表示占位符，其中的id表示接收输入的参数，参数名称为id，若输入的参数是简单类型，#{}中的参数名称可以任意 -->
   		select * from user where id=#{id}
   	</select>
   </mapper>
   ```

4. 在SqlMapConfig.xml中加载User.xml

   SqlMapConfig.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE configuration
   PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
   "http://mybatis.org/dtd/mybatis-3-config.dtd">

   <configuration>
   	
   	<!-- 别名定义 -->
   	<typeAliases>
   		<package name="com.ryoukai.ssm.po"/>
   	</typeAliases>

   	<!-- 加载映射文件 -->
   	<mappers>
   		<mapper resource="sqlmap/User.xml"/>
   		
   		<!-- 批量加载mapper，指定mapper接口的包名，MyBatis自动扫描该包名下所有mapper接口
   			需遵循的规范: 要将mapper接口类名和mapper.xml映射文件名称保持一致，且在一个目录中
   		-->
   		<package name="com.ryoukai.ssm.mapper"/>
   	</mappers>
   </configuration>
   ```

5. pojo类

   拷贝之前使用的pojo类: Items.java、Orderdetail.java、Orders.java、OrdersCustom.java、User.java

6. dao接口

   拷贝之前使用的dao及其实现类: UserDao.java、UserDaoImpl.java

7. 让UserDaoImpl继承SqlSessionDaoSupport

   UserDaoImpl.java

   ```java
   package com.ryoukai.ssm.dao.impl;

   import org.apache.ibatis.session.SqlSession;
   import org.mybatis.spring.support.SqlSessionDaoSupport;

   import com.ryoukai.ssm.dao.UserDao;
   import com.ryoukai.ssm.po.User;

   public class UserDaoImpl extends SqlSessionDaoSupport implements UserDao{
   	
   	@Override
   	public User findUserById(int id) {
   		//通过继承SqlSessionDaoSupport，使用其getSqlSession()方法得到SqlSession
   		SqlSession sqlSession = this.getSqlSession();
   		User user = sqlSession.selectOne("test.findUserById", id);
   		return user;
   	}
   }
   ```

8. 在applicationContext.xml中配置dao

   ```xml
   <!-- 原始dao接口 -->
   <bean id="userDao" class="com.ryoukai.ssm.dao.impl.UserDaoImpl">
    	 <property name="sqlSessionFactory" ref="sqlSessionFactory"/>
   </bean>
   ```

9. 测试类

   UserDaoImplTest.java

   ```java
   package com.ryoukai.ssm.dao.impl;

   import org.junit.Before;
   import org.junit.Test;
   import org.springframework.context.ApplicationContext;
   import org.springframework.context.support.ClassPathXmlApplicationContext;

   import com.ryoukai.ssm.dao.UserDao;
   import com.ryoukai.ssm.po.User;

   public class UserDaoImplTest {
   	
   	//Spring容器
   	private ApplicationContext applicationContext;
   	
   	@Before
   	public void setUp() throws Exception {
   		applicationContext = new ClassPathXmlApplicationContext("classpath:spring/applicationContext.xml");
   	}

   	@Test
   	public void testFindUserById() {
   		UserDao userDao = (UserDao) applicationContext.getBean("userDao");
   		//调用UserDao的方法
   		User user = userDao.findUserById(1);
   		System.out.println(user);
   	}

   }
   ```

   ​

#### mapper代理开发

1. mapper接口

   拷贝UserMapper.java

   ```java
   package com.ryoukai.ssm.mapper;

   import com.ryoukai.ssm.po.User;

   public interface UserMapper {
   	
   	//根据id查询用户信息
   	public User findUserById(int id);
   }
   ```

2. mapper.xml

   拷贝UserMapper.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE mapper
   PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
   "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

   <mapper namespace="com.ryoukai.ssm.mapper.UserMapper">

   	<select id="findUserById" parameterType="int"
   		resultType="com.ryoukai.ssm.po.User" useCache="true">
   		select * from user where id=#{id}
   	</select>

   </mapper>
   ```

3. 通过MapperFactoryBean生成代理对象

   applicationContext.xml

   ```xml
   <!-- mapper配置 -->
   <!-- MapperFactoryBean: 根据Mapper接口生成代理对象 -->
   <bean id="userMapper" class="org.mybatis.spring.mapper.MapperFactoryBean.class">
       <property name="mapperInterface" value="com.ryoukai.ssm.mapper.UserMapper"/>
       <property name="sqlSessionFactory" value="sqlSessionFactory"/>
   </bean>
   ```

4. 在applicationContext.xml中配置mapper扫描器

   ```xml
   <!-- mapper批量扫描 -->
   <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
       <!-- 如果扫描多个包，每个包中间使用逗号分隔，不能使用通配符 -->
       <property name="basePackage" value="com.ryoukai.ssm.mapper"/>
       <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
   </bean>
   ```

5. 测试类

   ```java
   package com.ryoukai.ssm.mapper;

   import org.junit.Before;
   import org.junit.Test;
   import org.springframework.context.ApplicationContext;
   import org.springframework.context.support.ClassPathXmlApplicationContext;

   import com.ryoukai.ssm.po.User;

   public class UserMapperTest {
   	
   	private ApplicationContext applicationContext;

   	@Before
   	public void setUp() throws Exception {
   		applicationContext = new ClassPathXmlApplicationContext("classpath:spring/applicationContext.xml");
   	}

   	@Test
   	public void testFindUserById() {
   		UserMapper userMapper = (UserMapper) applicationContext.getBean("userMapper");
   		User user = userMapper.findUserById(1);
   		System.out.println(user);
   	}

   }
   ```




## 8. 逆向工程

- MyBatis提供逆向工程，针对单表自动生成MyBatis执行所需要的代码(mapper接口、mapper.xml、pojo类)
- 企业的实际开发中，常用的逆向工程方式:
  - 由数据库的表来生成Java代码



### 8.01 逆向工程使用步骤

1. 下载逆向工程

   - mybatis-generator-1.3.5.zip

2. 运行逆向工程的方式:

   - 命令行工具和xml文件
   - Ant和xml文件
   - Maven插件
   - Java程序和xml文件
   - Java程序和Java的配置文件
   - Eclipse插件

   推荐使用Java程序和xml文件的运行方式，不依赖开发工具

3. 生成代码配置文件

   generatorConfig.xml(官方提供的示例)

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE generatorConfiguration
     PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
     "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

   <generatorConfiguration>
     <classPathEntry location="/Program Files/IBM/SQLLIB/java/db2java.zip" />

     <context id="testTables" targetRuntime="MyBatis3">
       
       <!-- 数据库连接的信息: 驱动类、连接地址、用户名、密码 -->
       <jdbcConnection driverClass="com.mysql.jdbc.Driver"
           connectionURL="jdbc:mysql://localhost:3306/test"
           userId="root"
           password="root">
       </jdbcConnection>
       
       <!-- <jdbcConnection driverClass="oracle.jdbc.OracleDriver"
           connectionURL="jdbc:oracle:thin:@127.0.0.1:1521:orcl"
           userId="scott"
           password="tiger">
       </jdbcConnection> -->
       
       <!-- <jdbcConnection driverClass="COM.ibm.db2.jdbc.app.DB2Driver"
           connectionURL="jdbc:db2:TEST"
           userId="db2admin"
           password="db2admin">
       </jdbcConnection> -->
   	
       <javaTypeResolver >
         <!-- value默认为false，表示把DECIMAL和NUMBERIC类型解析为Integer
    	  	若value为true，表示把DECIMAL和NUMBERIC类型解析为java.math.BigDecimal -->
         <property name="forceBigDecimals" value="false" />
       </javaTypeResolver>
   	
       <!-- targetProject: 生成PO类的位置 -->
       <javaModelGenerator targetPackage="test.model" targetProject="\MBGTestProject\src">
         <!-- enableSubPackages: 是否让schema作为包的后缀 -->
         <property name="enableSubPackages" value="true" />
         <!-- trimStrings: 是否清理从数据库返回的值前后的空格 -->
         <property name="trimStrings" value="true" />
       </javaModelGenerator>
   	
       <!-- targetProject: mapper映射文件生成的位置 -->
       <sqlMapGenerator targetPackage="test.xml"  targetProject="\MBGTestProject\src">
         <!-- enableSubPackages: 是否让schema作为包的后缀 -->
         <property name="enableSubPackages" value="true" />
       </sqlMapGenerator>
   	
       <!-- targetPackage: mapper接口生成的位置 -->
       <javaClientGenerator type="XMLMAPPER" targetPackage="test.dao"  targetProject="\MBGTestProject\src">
         <!-- enableSubPackages: 是否让schema作为包的后缀 -->
         <property name="enableSubPackages" value="true" />
       </javaClientGenerator>
   	
       <!-- 指定数据库表 -->
       <table tableName="user"></table>
       <table tableName="items"></table>
       <table tableName="orders"></table>
       <table tableName="orderdetails"></table>
       
       <!-- 有些表字段需要指定java类型 -->
       <!-- <table schema="" tableName="">
    			<columnOverride column="" javaType="" />
            </table> -->
       
       <!-- <table schema="DB2ADMIN" tableName="ALLTYPES" domainObjectName="Customer" >
         <property name="useActualColumnNames" value="true"/>
         <generatedKey column="ID" sqlStatement="DB2" identity="true" />
         <columnOverride column="DATE_FIELD" property="startDate" />
         <ignoreColumn column="FRED" />
         <columnOverride column="LONG_VARCHAR_FIELD" jdbcType="VARCHAR" />
       </table> -->

     </context>
   </generatorConfiguration>
   ```

4. 执行生成程序

   ```java
   List<String> warnings = new ArrayList<String>();
   boolean overWrite = true;
   //指定逆向工程配置文件
   File configFile = new File("generatorConfig.xml");
   ConfigurationParser cp = new ConfigurationParser(warnings);
   Configuration config = cp.parseConfiguration(configFile);
   DefaultShellCallback callback = new DefaultShellCallback(overWrite);
   MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config, callback, warnings);
   myBatisGenerator.generate(null);
   ```

   执行这段代码，会生成po类和mapper接口及mapper.xml

5. 使用生成的代码

   - 将生成工程中生成的代码拷贝到自己的工程中

