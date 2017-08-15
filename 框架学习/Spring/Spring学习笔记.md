# Spring学习笔记

## 1. Spring 概念

- Spring核心主要两部分:
  - AOP: 面向切面编程(扩展功能不是修改源代码实现)
  - IOC: 控制反转(由Spring来负责创建对象)
- Spring是一站式框架
  - 一站式: Spring在javaee三层结构中，每一层都提供不同的解决技术
    - web层: SpringMVC
    - service层: Spring的IOC
    - dao层: Spring的jdbcTemplate
- Spring版本: Spring4.x





## 2. Spring的IOC操作

- 把对象的创建交给Spring进行管理
- IOC操作分为两部分:
  - IOC的配置文件方式
  - IOC的注解方式





### 2.01 IOC 底层原理

- IOC底层原理使用技术
  - xml配置文件
  - dom4j解析xml
  - 工厂设计模式
  - 反射





#### 问题提出

- 编码思想: 高内聚，低耦合

- 问题:

  ```java
  public class User {
    public void add() {
      ......
    }
  }

  //在servlet调用User类里的方法
  User user = new User();
  user.add();
  ```

  如上代码有明显缺陷：耦合度太高

- 使用工厂模式解耦合操作

  ```java
  public class UserService {
    public void add() {
      ......
    }
  }

  //创建工厂类
  public class Factory {
    public static UserService getService() {
      return new UserService();
    }
  }

  //Servlet通过工厂类调用UserService中的add方法
  public class UserServlet {
    UserService s = Factory.getService();
    s.add();
  }
  ```

  这样能解Service和Servlet的耦合，但是Servlet和工厂类Factory又出现了耦合




#### 使用IOC来降低类之间的耦合度

- 假设有一个类UserService

  ```java
  public class UserService {
    
  }
  ```


1. 创建xml配置文件，配置要创建对象的类

   ```xml
   <bean id="userService" class="UserService"/>
   ```

2. 创建一个工厂类，使用dom4j解析配置文件，再使用反射创建类对象

   ```java
   public class UserFactory {
     
     //返回UserService对象的方法
     public static UserService getService() {
       //使用dom4j解析xml文件
       //根据xml中的id值，得到id值对应的class属性值
       String classValue = "class属性值";
       
       //使用反射创建类对象
       Class clazz = Class.forName(classValue);
       UserService service = clazz.newInstance();
       return service;
     }
   }
   ```

3. Servlet中取得UserService对象

   ```java
   public class UserServlet {
     UserService s = UserFactory.getService();
   }
   ```


   ​

### 2.02 IOC 的简单使用

1. 导入jar包

   - 官网下载zip压缩包，解压后jar包都在libs文件夹中
   - 每一种都有三个jar包，分别是jar包、javadoc文档和源代码
   - 做Spring最基本功能时，只需要导入四个核心的jar包:
     - beans
     - core
     - context
     - expression
   - 导入支持日志输出的jar包
     - commons-logging-1.2.jar
     - log4j-1.2.17.jar

2. 创建类，在类里面创建方法

   User.java

   ```java
   package com.ryoukai.ioc;

   public class User {
   	public void add() {
   		System.out.println("add...");
   	}
   }
   ```

3. 创建Spring配置文件，配置创建类

   - Spring核心配置文件名称和位置不是固定的，建议放到src下面，官方建议名字为applicationContext.xml

   - 引入schema约束

     ```xml
     <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="
             http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

     </beans>
     ```

   - 配置对象创建

     bean1.xml

     ```xml
     <?xml version="1.0" encoding="UTF-8"?>
     <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="
             http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
     	
     	<bean id="user" class="com.ryoukai.ioc.User"></bean>
     </beans>
     ```

4. 写代码测试对象创建

   TestIOC.java

   ```java
   package com.ryoukai.ioc;

   import org.junit.Test;
   import org.springframework.context.ApplicationContext;
   import org.springframework.context.support.ClassPathXmlApplicationContext;

   public class TestIOC {
   	
   	@Test
   	public void testUser() {
   		//加载Spring配置文件，根据配置文件创建对象
   		ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");
   		
   		//得到配置创建的对象
   		User user = (User)context.getBean("user");
   		
   		System.out.println(user);
   		user.add();
   	}

   }
   ```




### 2.03 bean实例化的三种方式

#### 使用类的无参数构造创建

- 如上例中，使用User类的无参构造函数实例化bean，若User类没有无参数的构造函数，则会报异常:org.springframework.beans.factory.BeanCreationException



#### 使用静态工厂创建

- 静态工厂: 创建静态的方法，返回类对象


- 例: 

  bean类: Bean2.java

  ```java
  package com.ryoukai.bean;

  public class Bean2 {

  	public void add() {
  		System.out.println("bean2....");
  	}
  }
  ```

  静态工厂类: Bean2Factory.java

  ```java
  package com.ryoukai.bean;

  public class Bean2Factory {
  	
  	public static Bean2 getBean2() {
  		return new Bean2();
  	}
  }
  ```

  配置文件: bean2.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="
          http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
  	
  	<bean id="bean2" class="com.ryoukai.bean.Bean2Factory" factory-method="getBean2"></bean>
  </beans>
  ```

  测试类: TestIOC.java

  ```java
  package com.ryoukai.bean;

  import org.junit.Test;
  import org.springframework.context.ApplicationContext;
  import org.springframework.context.support.ClassPathXmlApplicationContext;

  public class TestIOC {
  	
  	@Test
  	public void testUser() {
  		//加载Spring配置文件，根据配置文件创建对象
  		ApplicationContext context = new ClassPathXmlApplicationContext("bean2.xml");
  		
  		//得到配置创建的对象
  		Bean2 bean2 = (Bean2)context.getBean("bean2");
  		
  		System.out.println(bean2);
  		bean2.add();
  	}

  }
  ```




#### 使用实例工厂创建

- 实例工厂: 创建一个非静态的方法，返回类对象

- 例:

  实例工厂类: Bean3Factory.java

  ```java
  public class Bean3Factory {
      public Bean3 getBean3() {
          return new Bean3();
      }
  }
  ```

  配置文件: bean3.xml

  ```java
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="
          http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
  	
  	<bean id="bean3Factory" class="com.ryoukai.bean.Bean3Factory"></bean>
  	<bean id="bean3" factory-bean="bean3Factory" factory-method="getBean3"></bean>
  </beans>
  ```

  ​


### 2.04 bean标签常用属性

#### id属性

- 起名称，id属性值可以任意命名
- id属性值不能包含特殊符号
- 根据id值可得到配置对象



#### class属性

- 创建对象所在类的全路径



#### name属性

- 功能和使用方法和id属性一样
- name属性值可以包含特殊符号
- 是针对整合struts1遗留的属性，现在一般使用id属性而不使用name属性



#### scope属性

- 表示bean的作用范围
- singleton: 默认值，表示对象是一个单例对象
- prototype: 表示对象是一个多例对象
- request: 创建的对象放到request域里面
- session: 创建的对象放到session域里面
- globalSession: 创建的对象放到globalSession域里面




### 2.05 属性注入

- 创建对象时，给类的属性设置值称为属性注入

- 属性注入的方式:

  - 有参构造注入

  - set方法注入

  - 使用接口注入

    ```java
    public interface Dao {
        public void add(String name);
    }

    public class DaoImpl implements Dao {
      	private String name;
        public void add(String name) {
            this.name = name;
        }
    }
    ```

  - **在Spring框架里，只支持有参构造注入和set方法注入**




#### 有参构造注入属性

要注入属性值的类: PropertyDemo1.java

```java
package com.ryoukai.property;

public class PropertyDemo1 {
	
	private String username;

	public PropertyDemo1(String username) {
		this.username = username;
	}
	
	public void test1() {
		System.out.println("demo1..." + username);
	}
}

```

配置文件: demo1.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
	
	<bean id="demo1" class="com.ryoukai.property.PropertyDemo1">
     	<!-- 有参构造注入属性 -->
		<constructor-arg name="username" value="Jack"></constructor-arg>
	</bean>
</beans>
```

测试类: TestIOC.java

```java
package com.ryoukai.property;

import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class TestIOC {
	
	@Test
	public void testUser() {
		//加载Spring配置文件，根据配置文件创建对象
		ApplicationContext context = new ClassPathXmlApplicationContext("demo1.xml");
		
		//得到配置创建的对象
		PropertyDemo1 propertyDemo1 = (PropertyDemo1)context.getBean("demo1");
		
		System.out.println(propertyDemo1);
		propertyDemo1.test1();
	}

}
```



#### set方法注入属性

要注入属性值的类: Book.java

```java
package com.ryoukai.property;

public class Book {
	
	private String bookName;

	public void setBookName(String bookName) {
		this.bookName = bookName;
	}
	
	public void demoBook() {
		System.out.println("book..." + bookName);
	}
}
```

配置文件: book.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id="book" class="com.ryoukai.property.Book">
		<!-- set方法注入属性值 -->
		<property name="bookName" value="Tom"></property>
	</bean>
</beans>
```

测试类: TestIOC.java

```java
package com.ryoukai.property;

import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class TestIOC {
	
	@Test
	public void testUser() {
		//加载Spring配置文件，根据配置文件创建对象
		ApplicationContext context = new ClassPathXmlApplicationContext("book.xml");
		
		//得到配置创建的对象
		Book book = (Book)context.getBean("book");
		
		System.out.println(book);
		book.demoBook();
	}
}
```



#### 注入对象类型的属性s

1. 创建service类和dao类
2. 在service里面声明一个dao类型的属性
3. 在service中生成dao类型的属性的set方法
4. 配置文件中完成注入关系




#### p名称空间注入

配置文件: book.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
	
	<!-- p名称空间注入 -->
	<bean id="book" class="com.ryoukai.property.Book" p:bookName="goodbook"></bean>
	
</beans>
```



#### 注入复杂类型属性

要注入属性值的类: Book.java

```java
package com.ryoukai.property;

import java.util.List;
import java.util.Map;
import java.util.Properties;


public class Book {
	
	private String bookName;
	private String[] arrs;
	private List<String> list;
	private Map<String, String> map;
	private Properties properties;

	public void setArrs(String[] arrs) {
		this.arrs = arrs;
	}

	public void setList(List<String> list) {
		this.list = list;
	}

	public void setMap(Map<String, String> map) {
		this.map = map;
	}

	public void setProperties(Properties properties) {
		this.properties = properties;
	}

	public void setBookName(String bookName) {
		this.bookName = bookName;
	}
	
	public void test1() {
		System.out.println("arrs:" + arrs);
		System.out.println("list:" + list);
		System.out.println("map:" + map);
		System.out.println("properties:" + properties);
	}
}
```

配置文件: bean1.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
	
	<bean id="book" class="com.ryoukai.property.Book">
		<!-- 数组 -->
		<property name="arrs">
			<list>
				<value>1</value>
				<value>2</value>
				<value>3</value>
			</list>
		</property>
		
		<!-- List -->
		<property name="list">
			<list>
				<value>4</value>
				<value>5</value>
				<value>6</value>
			</list>
		</property>
		
		<!-- Map -->
		<property name="map">
			<map>
				<entry key="aa" value="7"></entry>
				<entry key="bb" value="8"></entry>
				<entry key="cc" value="9"></entry>
			</map>
		</property>
		
		<!-- Properties -->
		<property name="properties">
			<props>
				<prop key="driverclass">com.mysql.jdbc.driver</prop>
				<prop key="username">root</prop>
			</props>
		</property>
	</bean>
	
</beans>
```

测试类: TestIOC.java

```java
package com.ryoukai.property;

import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class TestIOC {
	
	@Test
	public void testBook() {
		//加载Spring配置文件，根据配置文件创建对象
		ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");
		
		//得到配置创建的对象
		Book book = (Book)context.getBean("book");
		
		System.out.println(book);
		book.test1();
	}

}
```



#### IOC 和 DI 的关系

- IOC: 控制反转
- DI: 依赖注入(向类里面的属性中设置值)
- 关系: 依赖注入不能单独存在，需要在IOC的基础之上完成




### 2.06 Spring整合web项目原理

- 思想: 把加载配置文件和创建对象过程，在服务器启动的时候完成
- 原理: 
  - 在服务器启动时，为每个项目创建ServletContext对象
  - 在ServletContext对象创建时，使用监听器可以监听到ServletContext对象在什么时候创建
  - 使用监听器监听到ServletContext对象创建的时候，加载Spring配置文件，把配置文件中配置的对象进行创建
  - 使用setAttribute方法把创建出来的对象放到ServletContext域对象里
  - 获取对象时，使用getAttribute方法到ServletContext域里获取对象



### 2.07 Spring注解

  - 注解: 代码里面特殊的标记，使用注解可以完成一些相关的功能
  - 注解写法:
    > @注解名称(属性名称=属性值)
  - 注解可以使用在类上面、方法上面和属性上面



#### 注解创建对象

- 使用注解还需要导入spring-aop-4.3.8.RELEASE.jar
- 过程示例:
  1. 创建类: User.java
     ```java
     package com.ryoukai.anno;

     public class User {
         
       public void add() {
         System.out.println("add...");
       }
     }
     ```

  2. 创建Spring配置文件，引入约束
     ```xml
     <?xml version="1.0" encoding="UTF-8"?>
     <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns:context="http://www.springframework.org/schema/context" xsi:schemaLocation="
             http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
             http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd"> <!-- bean definitions here -->

     </beans>
     ```
  3. 在配置文件中开启注解扫描
     ```xml
     <?xml version="1.0" encoding="UTF-8"?>
     <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns:context="http://www.springframework.org/schema/context" xsi:schemaLocation="
             http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
             http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd"> <!-- bean definitions here -->
        
         <!-- 开启注解扫描 -->
         <!-- 到包里面扫描类、方法、属性上是否有注解 -->
         <context:component-scan base-package="com.ryoukai"></context:component-scan>

     </beans>
     ```
  4. ​在User类前加入注解
     ```java
     package com.ryoukai.anno;

     import org.springframework.stereotype.Component;

     @Component(value="user")	//功能相当于配置文件中<bean id="user" class="com.ryoukai.anno.User"></bean>
     public class User {
      
       public void add() {
         System.out.println("add...");
       }
     }
     ```
  5. 测试类: TestAnno.java
     ```java
     package com.ryoukai.anno;

     import org.junit.Test;
     import org.springframework.context.ApplicationContext;
     import org.springframework.context.support.ClassPathXmlApplicationContext;

     public class TestAnno {
      
       @Test
       public void testUser() {
         ApplicationContext context = new ClassPathXmlApplicationContext ("bean1.xml");
         User user = (User) context.getBean("user");
         System.out.println(user);
         user.add();
       }
     }
     ```

- 创建对象可使用四种注解
  - @Component   :以下三个都是Component的衍生注解，目前功能是一样的，目的是为了让标注类本身的用途更清晰，Spring在后续的版本会对其加强
  - @Controller  :Web层
  - @Service     :业务层
  - @Repository  :持久层

- 创建单实例或多实例对象
  ```java
  package com.ryoukai.anno;

  import org.springframework.context.annotation.Scope;
  import org.springframework.stereotype.Component;

  @Component(value="user")	//功能相当于配置文件中<bean id="user" class="com.ryoukai.anno.User"></bean>
  @Scope(value="prototype")	//prototype表示多实例对象，singleton表示单实例对象
  public class User {
    
    public void add() {
      System.out.println("add...");
    }
  }
  ```



#### 注解注入属性

- 示例:
  - UserService.java
    ```java
    package com.ryoukai.anno;

    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.stereotype.Service;

    @Service(value="userService")
    public class UserService {
      
      //使用注解方式时不需要set方法，在userDao属性上使用注解完成对象注入
      //注入属性可使用Autowired或Resource注解，使用Resource需要指定要注入属性的对象
      //name属性值写注解创建UserDao对象的value值
      @Resource(name="userDao")
      private UserDao userDao;	

      public void add() {
        System.out.println("service...");
        userDao.add();
      }
    }
    ```
  - UserDao.java
    ```java
    package com.ryoukai.anno;

    import org.springframework.stereotype.Component;

    @Component(value="userDao")
    public class UserDao {
      
      public void add() {
        System.out.println("dao...");
      }
    }
    ```
  - 测试类: TestAnno.java
    ```java
    package com.ryoukai.anno;

    import org.junit.Test;
    import org.springframework.context.ApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;

    public class TestAnno {
      
      @Test
      public void testService() {
        ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");
        UserService userService = (UserService) context.getBean("userService");
        System.out.println(userService);
        userService.add();
      }
    }
    ```


### 2.08 配置文件和注解的混合使用

- 创建对象操作使用配置文件方式实现
- 注入属性的操作使用注解方式实现
- 代码示例:
  - 配置文件: bean2.xml
    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:context="http://www.springframework.org/schema/context" xsi:schemaLocation="
            http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd"> <!-- bean definitions here -->

      <!-- 开启注解扫描 -->
      <context:component-scan base-package="com.ryoukai.xmlanno"></context:component-scan>
      
      <!-- 配置对象 -->
      <bean id="bookService" class="com.ryoukai.xmlanno.BookService"></bean>
      <bean id="bookDao" class="com.ryoukai.xmlanno.BookDao"></bean>
      <bean id="ordersDao" class="com.ryoukai.xmlanno.OrdersDao"></bean>
      
    </beans>
    ```
  - BookDao.java
    ```java
    package com.ryoukai.xmlanno;

    public class BookDao {
      
      public void book() {
        System.out.println("BookDao...");
      }
    }
    ```
  - OrdersDao.java
    ```java
    package com.ryoukai.xmlanno;

    public class OrdersDao {
      
      public void buy() {
        System.out.println("OrdersDao...");
      }
    }
    ```
  - BookService.java
    ```java
    package com.ryoukai.xmlanno;

    import javax.annotation.Resource;

    public class BookService {
      
      @Resource(name="bookDao")
      private BookDao bookDao;
      
      @Resource(name="ordersDao")
      private OrdersDao ordersDao;

      public void add() {
        System.out.println("BookService...");
        bookDao.book();
        ordersDao.buy();
      }
    }
    ```
  - 测试类: Test.java
    ```java
    package com.ryoukai.xmlanno;

    import org.junit.Test;
    import org.springframework.context.ApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;

    public class Test {
      
      @Test
      public void testService() {
        ApplicationContext context = new ClassPathXmlApplicationContext("bean2.xml");
        BookService bookService = (BookService) context.getBean("bookService");
        bookService.add();
      }
      
    }
    ```




## 3. AOP

- AOP: 面向切面编程，扩展功能不通过修改源代码来实现
- AOP采取横向抽取机制，取代了传统纵向继承体系重复性代码



### 3.01 AOP原理

#### 问题提出

```java
public class User {
  //添加用户的方法
  public void add() {
    //方法逻辑
    
    //若要实现一个扩展功能: 在添加用户后，记录在什么时间添加哪个用户，即日志功能
    //则需要修改源代码，添加日志逻辑
  }
}
```



#### 纵向继承机制

```java
public class BaseUser {
  //日志添加的方法
  public void writeLog() {
    //添加日志逻辑
  }
}

public class User extends BaseUser {
  //添加用户的方法
  public void add() {
    //添加用户逻辑

    //功能扩展，添加日志的功能，则让User类继承BaseUser类
    //调用父类的方法实现日志功能
    super.writeLog();
  }
}
```
- 纵向继承机制并不能根本解决问题，比如父类中方法名称发生变化，在子类中调用的方法名称也需要改变




#### AOP: 横向抽取机制

- 底层实现: 动态代理方式实现


#### 有接口的情况

```java
public interface Dao {
  public void add();
}

public class DaoImpl implements Dao {
  public void add() {
    //添加逻辑
  }
}
```
- 使用jdk动态代理，创建和DaoImpl类平级的代理对象，实现和DaoImpl相同的功能



#### 没有接口的情况

```java
public class User {
  public void add() {

  }
}

//动态代理实现
//创建User类的子类代理对象
//在子类里调用父类的方法完成增强
```
- 使用cglib动态代理



### 3.02 AOP操作术语

- Joinpoint(连接点): 类里面可以被增强的方法，称为连接点。
- **Pointcut(切入点)**: 在类里面有很多的方法可以被增强，实际操作中，只是增强了其中的某个方法，则实际增强的方法称为切入点。
- **Advice(通知/增强)**: 方法扩展的功能，这个功能的逻辑称为通知或增强；通知分为前置通知、后置通知、异常通知、最终通知、环绕通知。
  - 前置通知: 在方法之前执行
  - 后置通知: 在方法之后执行
  - 异常通知: 方法出现异常
  - 最终通知: 在后置之后执行
  - 环绕通知: 在方法之前和方法之后执行
- **Aspect(切面)**: 把增强应用到具体的方法上面，这个过程称为切面(把增强用到切入点的过程)
- Introduction(引介): 在不修改代码的前提下，为类动态添加一些方法或属性
- Target(目标对象): 要增强的类
- Weaving(织入): 把增强应用到Target的过程
- Proxy(代理): 一个类被AOP织入增强后，就产生一个代理类



### 3.03 Spring的AOP操作

- 在Spring里进行AOP操作，使用AspectJ实现
  - AspectJ是一个面向切面的框架，它不是Spring的一部分，和Spring一起使用来进行AOP操作
  - Spring2.0以后新增了对AspectJ的支持
- 使用AspectJ实现AOP的两种方式
  - 基于AspectJ的xml配置
  - 基于AspectJ的注解方式



#### xml配置方式的操作流程

1. 除了导入基本的jar包之外，还需要导入AOP相关的jar包

   - spring-aop-4.3.8.RELEASE.jar
   - spring-aspects-4.3.8.RELEASE.jar
   - aopalliance-1.0.jar
   - aspectjweaver-1.8.10.jar

2. 创建配置文件，导入AOP的约束

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop" xsi:schemaLocation="
           http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd"> <!-- bean definitions here -->

   </beans>
   ```

3. 使用表达式配置切入点

   - 常用的表达式: 格式: execution(<访问修饰符>?<返回类型><方法名>(<参数>)<异常>)
   - execution(* com.ryoukai.aop.Book.add(..))
     - 其中*表示访问修饰符public、private、protect、default都包含
     - com.ryoukai.aop.Book.add(..)为切入点，括号中的..表示可能含有参数
     - 注意*和后面的内容之间有一个空格
   - execution(* com.ryoukai.aop.Book.*(..))
     - 表示对Book类中所有方法进行增强
   - execution(* \*.\*(..))
     - 表示对所有包里所有类的所有方法进行增强
   - execution(* \*.save*(..))
     - 表示对所有包里所有类中以save开头的方法进行增强



#### 过程示例

1. 创建类Book和MyBook

   Book.java

   ```java
   package com.ryoukai.aop;

   public class Book {
   	
   	public void add() {
   		System.out.println("add...");
   	}
   }
   ```

   MyBook.java

   ```java
   package com.ryoukai.aop;

   public class MyBook {
   	
   	public void before1() {
   		System.out.println("前置增强...");
   	}
   }
   ```

2. 创建配置文件

   bean1.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop" xsi:schemaLocation="
           http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd"> <!-- bean definitions here -->
   	
   	<!-- 配置对象 -->
   	<bean id="book" class="com.ryoukai.aop.Book"></bean>
   	<bean id="myBook" class="com.ryoukai.aop.MyBook"></bean>
   	
   	<!-- 配置AOP操作 -->
   	<aop:config>
   		<!-- 配置切入点 -->
   		<aop:pointcut expression="execution(* com.ryoukai.aop.Book.add(..))" id="pointcut1"/>
   		
   		<!-- 配置切面 -->
   		<aop:aspect ref="myBook">
   			<!-- 配置增强类型 -->
   			<!-- before为前置增强 -->
   			<aop:before method="before1" pointcut-ref="pointcut1"/>
   		</aop:aspect>
   	</aop:config>
   	
   </beans>
   ```

3. 测试类

   TestAOP.java

   ```java
   package com.ryoukai.aop;

   import org.junit.Test;
   import org.springframework.context.ApplicationContext;
   import org.springframework.context.support.ClassPathXmlApplicationContext;

   public class TestAOP {
   	
   	@Test
   	public void testBook() {
   		ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");
   		Book book = (Book) context.getBean("book");
   		book.add();
   	}

   }
   ```



### 3.03 基于AspectJ的注解AOP操作

#### 示例过程

1. 创建类Book和MyBook

   Book.java

   ```java
   package com.ryoukai.aop;

   public class Book {
   	
   	public void add() {
   		System.out.println("add...");
   	}
   }
   ```

   MyBook.java

   ```java
   package com.ryoukai.aop;

   public class MyBook {

   	public void before1() {
   		System.out.println("before...");
   	}
   }
   ```

2. 创建配置文件，引入约束，配置对象

   bean1.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop" xsi:schemaLocation="
           http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd"> <!-- bean definitions here -->
   	
   	<!-- 配置对象 -->
   	<bean id="book" class="com.ryoukai.aop.Book"></bean>
   	<bean id="myBook" class="com.ryoukai.aop.MyBook"></bean>
   	
   </beans>
   ```

3. 在配置文件中开启AOP操作

   bean1.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop" xsi:schemaLocation="
           http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd"> <!-- bean definitions here -->
   	
   	<!-- 开启AOP操作 -->
   	<aop:aspectj-autoproxy></aop:aspectj-autoproxy>
   	
   	<!-- 配置对象 -->
   	<bean id="book" class="com.ryoukai.aop.Book"></bean>
   	<bean id="myBook" class="com.ryoukai.aop.MyBook"></bean>
   	
   </beans>
   ```

4. 使用注解进行AOP操作

   MyBook.java

   ```java
   package com.ryoukai.aop;

   import org.aspectj.lang.annotation.Aspect;
   import org.aspectj.lang.annotation.Before;

   //在增强类前面加上注解@Aspect
   @Aspect
   public class MyBook {

   	//在方法上面使用注解完成增强配置
   	@Before(value="execution(* com.ryoukai.aop.Book.add(..))")
   	public void before1() {
   		System.out.println("before...");
   	}
   }
   ```

5. 测试类

   TestAOP.java

   ```java
   package com.ryoukai.aop;

   import org.junit.Test;
   import org.springframework.context.ApplicationContext;
   import org.springframework.context.support.ClassPathXmlApplicationContext;

   public class TestAOP {
   	
   	@Test
   	public void testDemo() {
   		ApplicationContext context = new ClassPathXmlApplicationContext("bean3.xml");
   		Book book = (Book) context.getBean("book");
   		book.add();
   	}
   }
   ```

   ​

## 4. Spring的jdbcTemplate操作

- Spring对不同的持久层技术进行了封装
- jdbcTemplate对jdbc进行了封装
- jdbcTemplate使用和dbutils使用很相似，都对数据库进行crud操作



### 4.01 增加

1. 除基础的jar包外，导入jdbcTemplate相关jar包，以及数据库jdbc驱动

   - spring-jdbc-4.3.8.RELEASE.jar
   - spring-tx-4.3.8.RELEASE.jar
   - mysql-connector-java-5.1.42-bin.jar

2. 示例

   JdbcTemplateDemo1.java

   ```java
   package com.ryoukai.jdbc;

   import org.junit.Test;
   import org.springframework.jdbc.core.JdbcTemplate;
   import org.springframework.jdbc.datasource.DriverManagerDataSource;

   public class JdbcTemplateDemo1 {

   	//添加操作
   	@Test
   	public void add() {
   		//设置数据库信息
   		DriverManagerDataSource dataSource = new DriverManagerDataSource();
   		dataSource.setDriverClassName("com.mysql.jdbc.Driver");
   		dataSource.setUrl("jdbc:mysql:///testspring");
   		dataSource.setUsername("root");
   		dataSource.setPassword("root");
   		
   		//创建jdbcTemplate对象，设置数据源
   		JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
   		
   		//调用jdbcTemplate对象里面的方法实现
   		String sql = "insert into test values(?,?)";
   		int rows = jdbcTemplate.update(sql, "1", "ryoukai");
   		System.out.println(rows);
   	}
   }
   ```



### 4.02 修改

- 示例

  JdbcTemplateDemo1.java

  ```java
  package com.ryoukai.jdbc;

  import org.junit.Test;
  import org.springframework.jdbc.core.JdbcTemplate;
  import org.springframework.jdbc.datasource.DriverManagerDataSource;

  public class JdbcTemplateDemo1 {
  	
  	//修改操作
  	@Test
  	public void update() {
  		//设置数据库信息
  		DriverManagerDataSource dataSource = new DriverManagerDataSource();
  		dataSource.setDriverClassName("com.mysql.jdbc.Driver");
  		dataSource.setUrl("jdbc:mysql:///testspring");
  		dataSource.setUsername("root");
  		dataSource.setPassword("root");
  		
  		//创建jdbcTemplate对象，设置数据源
  		JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
  		
  		//调用jdbcTemplate对象里面的方法实现
  		String sql = "update test set name=? where id=?";
  		int rows = jdbcTemplate.update(sql, "libai", "1");
  		System.out.println(rows);
  	}
  }
  ```



### 4.03 删除

- 示例:

  JdbcTemplateDemo1.java

  ```java
  package com.ryoukai.jdbc;

  import org.junit.Test;
  import org.springframework.jdbc.core.JdbcTemplate;
  import org.springframework.jdbc.datasource.DriverManagerDataSource;

  public class JdbcTemplateDemo1 {
  	
  	//删除操作
  	@Test
  	public void delete() {
  		//设置数据库信息
  		DriverManagerDataSource dataSource = new DriverManagerDataSource();
  		dataSource.setDriverClassName("com.mysql.jdbc.Driver");
  		dataSource.setUrl("jdbc:mysql:///testspring");
  		dataSource.setUsername("root");
  		dataSource.setPassword("root");
  		
  		//创建jdbcTemplate对象，设置数据源
  		JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
  		
  		//调用jdbcTemplate对象里面的方法实现
  		String sql = "delete from test where id=?";
  		int rows = jdbcTemplate.update(sql, "1");
  		System.out.println(rows);
  	}
  }
  ```

  ​

### 4.04 查询

#### 示例: 查询表中有多少条记录

JdbcTemplateDemo1.java

```java
package com.ryoukai.jdbc;

import org.junit.Test;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.datasource.DriverManagerDataSource;

public class JdbcTemplateDemo1 {
  
	//查询表中有多少条记录
	@Test
	public void testCount() {
		//设置数据库信息
		DriverManagerDataSource dataSource = new DriverManagerDataSource();
		dataSource.setDriverClassName("com.mysql.jdbc.Driver");
		dataSource.setUrl("jdbc:mysql:///testspring");
		dataSource.setUsername("root");
		dataSource.setPassword("root");
		
		//创建jdbcTemplate对象，设置数据源
		JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
		
		//调用jdbcTemplate对象里面的方法实现
		String sql = "select count(*) from test";
		//第一个参数是sql语句，第二个参数是要返回的类型
		int count = jdbcTemplate.queryForObject(sql, Integer.class);
		System.out.println(count);
	}
}
```



#### 示例: 查询返回结果为对象

User.java

```java
package com.ryoukai.jdbc;

public class User {
	
	private Integer id;
	private String name;
	
	public Integer getId() {
		return id;
	}
	public void setId(Integer id) {
		this.id = id;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
}
```

JdbcTemplateDemo1.java

```java
package com.ryoukai.jdbc;

import java.sql.ResultSet;
import java.sql.SQLException;

import org.junit.Test;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.core.RowMapper;
import org.springframework.jdbc.datasource.DriverManagerDataSource;

public class JdbcTemplateDemo1 {
	
	//查询返回对象
	@Test
	public void testObject() {
		//设置数据库信息
		DriverManagerDataSource dataSource = new DriverManagerDataSource();
		dataSource.setDriverClassName("com.mysql.jdbc.Driver");
		dataSource.setUrl("jdbc:mysql:///testspring");
		dataSource.setUsername("root");
		dataSource.setPassword("root");
		
		//创建jdbcTemplate对象，设置数据源
		JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
		
		//调用jdbcTemplate对象里面的方法实现
		String sql = "select * from test where id=?";
		//第一个参数是sql语句，第二个参数是RowMapper接口，第三个参数是可变参数(要传的值)
		User user = jdbcTemplate.queryForObject(sql, new MyRowMapper(), "1");
		System.out.println(user.getId() + " " + user.getName());
	}
}

//实现RowMapper接口
class MyRowMapper implements RowMapper<User> {

	@Override
	public User mapRow(ResultSet rs, int num) throws SQLException {
		//从结果集里面得到数据
		int id = rs.getInt("id");
		String name = rs.getString("name");
		
		//把得到数据封装到对象里面
		User user = new User();
		user.setId(id);
		user.setName(name);
		
		return user;
	}
	
}
```



#### 示例: 查询返回结果为List

User.java

```java
package com.ryoukai.jdbc;

public class User {
	
	private Integer id;
	private String name;
	
	public Integer getId() {
		return id;
	}
	public void setId(Integer id) {
		this.id = id;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
}
```

JdbcTemplateDemo1.java

```java
package com.ryoukai.jdbc;

import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.List;

import org.junit.Test;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.core.RowMapper;
import org.springframework.jdbc.datasource.DriverManagerDataSource;

public class JdbcTemplateDemo1 {
	
	//查询返回List
	@Test
	public void testList() {
		//设置数据库信息
		DriverManagerDataSource dataSource = new DriverManagerDataSource();
		dataSource.setDriverClassName("com.mysql.jdbc.Driver");
		dataSource.setUrl("jdbc:mysql:///testspring");
		dataSource.setUsername("root");
		dataSource.setPassword("root");
		
		//创建jdbcTemplate对象，设置数据源
		JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
		
		//调用jdbcTemplate对象里面的方法实现
		String sql = "select * from test";
		//第一个参数是sql语句，第二个参数是RowMapper接口，第三个参数是可变参数(要传的值)
		List<User> list = jdbcTemplate.query(sql, new MyRowMapper());
		
		for(int i = 0; i < list.size(); i++) {
			System.out.println(list.get(i).getId() + " " + list.get(i).getName());
		}
	}
}

//实现RowMapper接口
class MyRowMapper implements RowMapper<User> {

	@Override
	public User mapRow(ResultSet rs, int num) throws SQLException {
		//从结果集里面得到数据
		int id = rs.getInt("id");
		String name = rs.getString("name");
		
		//把得到数据封装到对象里面
		User user = new User();
		user.setId(id);
		user.setName(name);
		
		return user;
	}
	
}
```



### 4.05 Spring配置c3p0连接池

- 导入jar包

  - c3p0-0.9.5.2.jar
  - mchange-commons-java-0.2.11.jar

- 创建配置文件，配置连接池

  bean1.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xmlns:aop="http://www.springframework.org/schema/aop" xsi:schemaLocation="
          http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd"> <!-- bean definitions here -->
  	
  	<!-- 配置c3p0连接池 -->
  	<!-- 配置对象 -->
  	<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
  		<!-- 注入属性值 -->
  		<property name="dirverClass" value="com.mysql.jdbc.Driver"></property>
  		<property name="jdbcUrl" value="jdbc:mysql:///testspring"></property>
  		<property name="user" value="root"></property>
  		<property name="password" value="root"></property>
  	</bean>
  	
  </beans>
  ```

- 示例

  1. 创建UserService类和UserDao类

     UserDao.java

     ```java
     package com.ryoukai.c3p0;

     import org.springframework.jdbc.core.JdbcTemplate;

     public class UserDao {
     	
     	private JdbcTemplate jdbcTemplate;

     	public void setJdbcTemplate(JdbcTemplate jdbcTemplate) {
     		this.jdbcTemplate = jdbcTemplate;
     	}

     	public void add() {
     		
     	}

     }
     ```

     UserService.java

     ```java
     package com.ryoukai.c3p0;

     public class UserService {
     	
     	private UserDao userDao;
     	
     	public void setUserDao(UserDao userDao) {
     		this.userDao = userDao;
     	}
     	
     	public void add() {
     		userDao.add();
     	}
     }
     ```

  2. 配置userService和userDao对象，在userService中注入userDao对象

     ```xml
     <bean id="userService" class="com.ryoukai.c3p0.UserService">
     		<!-- 注入userDao对象 -->
     		<property name="userDao" ref="userDao"></property>
     	</bean>
     	
     <bean id="userDao" class="com.ryoukai.c3p0.UserDao"></bean>
     ```

  3. 配置jdbcTemplate对象，将其注入userDao中

     ```xml
     <bean id="userDao" class="com.ryoukai.c3p0.UserDao">
       <!-- 注入jdbcTemplate对象 -->
       <property name="jdbcTemplate" ref="jdbcTemplate"></property>
     </bean>

     <!-- 配置jdbcTemplate对象 -->
     <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate"></bean>
     ```

  4. 在jdbcTemplate对象中注入dataSource对象

     ```xml
     <!-- 配置jdbcTemplate对象 -->
     <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
       <!-- 把dataSource传递到jdbcTemplate中 -->
       <property name="dataSource" ref="dataSource"></property>
     </bean>
     ```

  5. 完善UserDao中的add方法

     UserDao.java

     ```java
     package com.ryoukai.c3p0;

     import org.springframework.jdbc.core.JdbcTemplate;

     public class UserDao {
     	
     	private JdbcTemplate jdbcTemplate;

     	public void setJdbcTemplate(JdbcTemplate jdbcTemplate) {
     		this.jdbcTemplate = jdbcTemplate;
     	}

     	//添加操作
     	public void add() {
     		String sql = "insert into test(name) value(?)";
     		jdbcTemplate.update(sql, "zhangql");
     	}

     }
     ```

  6. 测试类

     TestService.java

     ```java
     package com.ryoukai.c3p0;

     import org.junit.Test;
     import org.springframework.context.ApplicationContext;
     import org.springframework.context.support.ClassPathXmlApplicationContext;

     public class TestService {

     	@Test
     	public void testDemo() {
     		ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");
     		UserService service = (UserService) context.getBean("userService");
     		service.add();
     	}
     }
     ```

     ​

### 4.06 Spring的事务管理

- Spring事务管理的两种方式
  - 编程式事务管理
  - 声明式事务管理
    - 基于xml配置文件实现
    - 基于注解实现
- Spring事务管理的api
  - Spring事务管理高层抽象主要包括三个接口
    - PlatformTransactionManager: 事务管理器
    - TransactionDefinition: 事务定义信息(隔离、传播、超时、只读)
    - TransactionStatus: 事务具体运行状态
  - Spring为不同的持久层框架提供了不同的PlatformTransactionManager接口实现



#### 示例: 搭建转账环境

1. 需求: 小王转账1000给小马

2. 创建数据库表，添加数据

   ```mysql
   CREATE TABLE `account` (
     `id` int(11) NOT NULL AUTO_INCREMENT,
     `username` varchar(20) COLLATE utf8mb4_unicode_ci NOT NULL,
     `salary` double NOT NULL,
     PRIMARY KEY (`id`)
   ) ENGINE=InnoDB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci

   INSERT INTO `testspring`.`account` (`id`, `username`, `salary`) VALUES ('1', '小王', '10000');
   INSERT INTO `testspring`.`account` (`id`, `username`, `salary`) VALUES ('2', '小马', '10000');
   ```

3. 导入jar包

   - aopalliance-1.0.jar
   - aspectjweaver-1.8.10.jar
   - c3p0-0.9.5.2.jar
   - commons-logging-1.2.jar
   - log4j-1.2.17.jar
   - mchange-commons-java-0.2.11.jar
   - mysql-connector-java-5.1.42-bin.jar
   - spring-aop-4.3.8.RELEASE.jar
   - spring-aspects-4.3.8.RELEASE.jar
   - spring-beans-4.3.8.RELEASE.jar
   - spring-context-4.3.8.RELEASE.jar
   - spring-core-4.3.8.RELEASE.jar
   - spring-expression-4.3.8.RELEASE.jar
   - spring-jdbc-4.3.8.RELEASE.jar
   - spring-tx-4.3.8.RELEASE.jar

4. 创建service和dao类，完成注入关系

   OrdersDao.java

   ```java
   package com.ryoukai.dao;

   import org.springframework.jdbc.core.JdbcTemplate;

   public class OrdersDao {
   	
   	private JdbcTemplate jdbcTemplate;

   	public void setJdbcTemplate(JdbcTemplate jdbcTemplate) {
   		this.jdbcTemplate = jdbcTemplate;
   	}
   	
   }
   ```

   OrdersService.java

   ```java
   package com.ryoukai.service;

   import com.ryoukai.dao.OrdersDao;

   public class OrdersService {

   	private OrdersDao ordersDao;

   	public void setOrdersDao(OrdersDao ordersDao) {
   		this.ordersDao = ordersDao;
   	}
   	
   }
   ```

5. 创建配置文件，引入约束，配置c3p0连接池，配置service、dao和jdbcTemplate对象，完成属性注入

   bean1.xml

   ```xml
   <?xml version="1.0" encoding="utf-8"?>

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
     
     	<!-- 配置c3p0连接池 -->
   	<!-- 配置对象 -->
   	<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
   		<!-- 注入属性值 -->
   		<property name="driverClass" value="com.mysql.jdbc.Driver"></property>
   		<property name="jdbcUrl" value="jdbc:mysql:///testspring"></property>
   		<property name="user" value="root"></property>
   		<property name="password" value="root"></property>
   	</bean>
     	
     	<bean id="ordersService" class="com.ryoukai.service.OrdersService">
   		<property name="ordersDao" ref="ordersDao"></property>
   	</bean>
   	
   	<bean id="ordersDao" class="com.ryoukai.dao.OrdersDao">
   		<property name="jdbcTemplate" ref="jdbcTemplate"></property>
   	</bean>
   	
   	<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
   		<property name="dataSource" ref="dataSource"></property>
   	</bean>
     
   </beans>
   ```

6. 在dao中实现对数据库操作的方法

   OrdersDao.java

   ```java
   package com.ryoukai.dao;

   import org.springframework.jdbc.core.JdbcTemplate;

   public class OrdersDao {
   	
   	private JdbcTemplate jdbcTemplate;

   	public void setJdbcTemplate(JdbcTemplate jdbcTemplate) {
   		this.jdbcTemplate = jdbcTemplate;
   	}
   	
   	//扣钱的方法
   	public void decreassMoney(double money, String username) {
   		String sql = "update account set salary=salary-? where username=?";
   		jdbcTemplate.update(sql, money, username);
   	}
   	
   	//加钱的方法
   	public void addMoney(double money, String username) {
   		String sql = "update account set salary=salary+? where username=?";
   		jdbcTemplate.update(sql, money, username);
   	}
   	
   }
   ```

7. 在service中实现业务逻辑

   OrdersService.java

   ```java
   package com.ryoukai.service;

   import com.ryoukai.dao.OrdersDao;

   public class OrdersService {

   	private OrdersDao ordersDao;

   	public void setOrdersDao(OrdersDao ordersDao) {
   		this.ordersDao = ordersDao;
   	}
   	
   	//转账业务
   	public void transferMoney(double money, String username1, String username2) {
   		ordersDao.decreassMoney(money, username1);
   		ordersDao.addMoney(money, username2);
   	}
   }
   ```

8. 测试

   TestService.java

   ```java
   package com.ryoukai.service;

   import org.junit.Test;
   import org.springframework.context.ApplicationContext;
   import org.springframework.context.support.ClassPathXmlApplicationContext;

   public class TestService {
   	
   	@Test
   	public void testDemo() {
   		ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");
   		OrdersService service = (OrdersService) context.getBean("ordersService");
   		service.transferMoney(1000, "小王", "小马");
   	}
   }
   ```

9. 问题: 如果service中的transferMoney中ordersDao.decreassMoney(money, username1);执行后，ordersDao.addMoney(money, username2);未执行，则小王少了1000，而小马没有增加1000

10. 解决: 添加事务解决，出现异常进行回滚操作

11. 声明事务管理(xml配置文件方式)

    - 使用AOP思想

    - 配置事务管理器

      ```xml
      <!-- 配置事务管理器 -->
      <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <!-- 注入dataSource -->
        <property name="dataSource" ref="dataSource"></property>
      </bean>
      ```

    - 配置事务增强

      ```xml
      <!-- 配置事务增强 -->
      <tx:advice id="txadvice" transaction-manager="transactionManager">
        <!-- 事务操作 -->
        <tx:attributes>
          <!-- 设置进行事务操作的方法匹配规则 -->
          <tx:method name="transferMoney"/>
        </tx:attributes>
      </tx:advice>
      ```

    - 配置切面

      ```xml
      <!-- 配置切面 -->
      <aop:config>
        <!-- 配置切入点 -->
        <aop:pointcut expression="excution(* com.ryoukai.service.OrdersService.setOrdersDao(OrdersDao))" id="pointcut1"/>
        <!-- 配置切面 -->
        <aop:advisor advice-ref="txadvice" pointcut-ref="pointcut1"/>
      </aop:config>
      ```

12. 声明事务管理(注解方式)

    - 配置事务管理器

    - 开启事务注解

      ```xml
      <!-- 开启事务注解 -->
      <tx:annotation-driven transaction-manager="transactionManager"/>
      ```

    - 在要使用事务的方法所在类上面添加注解

      OrdersService.java

      ```java
      package com.ryoukai.service;

      import org.springframework.transaction.annotation.Transactional;

      import com.ryoukai.dao.OrdersDao;

      //事务注解
      @Transactional
      public class OrdersService {

      	private OrdersDao ordersDao;

      	public void setOrdersDao(OrdersDao ordersDao) {
      		this.ordersDao = ordersDao;
      	}
      	
      	//转账业务
      	public void transferMoney(double money, String username1, String username2) {
      		ordersDao.decreassMoney(money, username1);
      		ordersDao.addMoney(money, username2);
      	}
      }
      ```

      ​