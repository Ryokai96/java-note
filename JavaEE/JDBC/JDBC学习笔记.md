# JDBC 学习笔记

## 1. JDBC初步

### 1.01 JDBC定义

- JDBC(Java Database Connectivity)
- JDBC是一种用于执行SQL语句的Java API，可以为多种关系型数据库提供统一访问，它由一组用Java语言编写的类和接口组成
- 缺点：由于SQL语句必须在JDBC中体现，但是不同数据库的SQL语句会有微小区别，所以使用JDBC从一种数据库移植到另一种数据库还是要花费一定精力。而Hibernate把这个缺点弥补了，这是Hibernate流行的原因之一



### 1.02 JDBC编程步骤

1. Load the Driver 加载用于连接某数据库的专用驱动

   - 实例化：Class.forName() | Class.forName().newInstance() | new DriverName()


   - 例：

     ```java
     Class.formName("oracle.jdbc.driver.OracleDriver");
     ```

     或

     ```java
     Class.formName("oracle.jdbc.driver.OracleDriver").newInstance();
     ```

     或

     ```java
     new oracle.jdbc.driver.OracleDriver();
     ```

   - 实例化时自动向DriverManager注册，不需要显示调用DriverManager.registerDriver方法

2. Connect to the Database 连接数据库

   - DriverManager.getConnection()

   - 例：连接Oracle

     ```java
     Connection conn = DriverManager.getConnection("jdbc:oracle:thin:@127.0.0.1:1521:orcl", "scott", "tiger");	//Oracle连接串固定写法: jdbc:oracle:thin:@IP:Oracle端口号:SID
     ```

3. Execute the SQL 执行SQL语句

   1. Connection.CreateStatement()

      ```java
      Statement stmt = conn.createStatement();
      ```

   2. Statement.executeQuery()

      ```java
      stmt.executeQuery("select * from dept");
      ```

   3. Statement.executeUpdate()

4. Retrieve the result data 取得SQL语句执行结果

   - ResultSet rs = stmt.executeQuery(); 取得结果集rs

     ```java
     ResultSet rs = stmt.executeQuery("select * from dept");
     ```


   - 循环取得结果 while(rs.next())

5. Show the result data 显示取得的结果

   - 将数据库中的各种类型转换为Java中的类型(getXXX 方法)

     ```java
     while(rs.next()) {
       	rs.getString("deptno");
       	rs.getInt("deptno");
     }
     ```

6. Close

   - close the resultset. / close the statement / close the connection

     ```java
     rs.close();
     stmt.close();
     conn.close();
     ```



#### 示例: Test_JDBC.java

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;

public class Test_JDBC {

	public static void main(String[] args) throws Exception{
		Connection conn = null;
		Statement stmt = null;
		ResultSet rs = null;
		try {
			Class.forName("oracle.jdbc.driver.OracleDriver");	//加载OracleDriver
			conn = DriverManager.getConnection("jdbc:oracle:thin:@127.0.0.1:1521:orcl", "scott", "tiger");	//使用用户scott连接Oracle数据库
			stmt = conn.createStatement();
			rs = stmt.executeQuery("select * from dept");	//执行SQL语句并取得结果集rs
			while(rs.next()) {	//遍历结果集，输出结果
			  	System.out.println(rs.getString("deptno"));
			  	System.out.println(rs.getInt("deptno"));
			}
		} catch(ClassNotFoundException e) {
			e.printStackTrace();
		} catch(SQLException e) {
			e.printStackTrace();
		} finally {
			try {
				if(rs != null) {
					rs.close();
					rs = null;
				}
				if(stmt != null) {
					stmt.close();
					rs = null;
				}
				if(conn != null) {
					conn.close();
					rs = null;
				}
			} catch(SQLException e) {
				e.printStackTrace();
			}
		}
	}

}
```

