# JDBC 学习笔记

[TOC]

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

      ```java
      stmt.executeUpdate("insert into jdbctest values('10001', '张锋')");
      ```

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
import java.sql.SQLException;
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
					stmt = null;
				}
				if(conn != null) {
					conn.close();
					conn = null;
				}
			} catch(SQLException e) {
				e.printStackTrace();
			}
		}
	}

}
```



#### 示例: TestDML.java

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Statement;

public class TestDML {

	public static void main(String[] args) {
		Connection conn = null;
		Statement stmt = null;
		try {
			Class.forName("oracle.jdbc.driver.OracleDriver");	//加载OracleDriver
			conn = DriverManager.getConnection("jdbc:oracle:thin:@127.0.0.1:1521:orcl", "ryoukai", "ryoukai");	//使用用户scott连接Oracle数据库
			stmt = conn.createStatement();
			String sql = "insert into jdbctest values('10001', '张锋')";
			stmt.executeUpdate(sql);	//执行DML语句
		} catch(ClassNotFoundException e) {
			e.printStackTrace();
		} catch(SQLException e) {
			e.printStackTrace();
		} finally {
			try {
				if(stmt != null) {
					stmt.close();
					stmt = null;
				}
				if(conn != null) {
					conn.close();
					conn = null;
				}
			} catch(SQLException e) {
				e.printStackTrace();
			}
		}
	}

}

```



## 2. JDBC进阶

### 2.01 PreparedStatement

- PreParedStatement可灵活地指定SQL语句中的变量

#### 示例: TestPreparedStatement.java

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class TestPreparedStatement {

	public static void main(String[] args) {
		Connection conn = null;
		PreparedStatement pstmt = null;
		try {
			Class.forName("oracle.jdbc.driver.OracleDriver");	//加载OracleDriver
			conn = DriverManager.getConnection("jdbc:oracle:thin:@127.0.0.1:1521:orcl", "ryoukai", "ryoukai");	//使用用户scott连接Oracle数据库
			pstmt = conn.prepareStatement("insert into jdbctest values(?, ?)");
			pstmt.setString(1, "10002");	//1代表第一个 ?
			pstmt.setString(2, "李鑫");	//2代表第二个 ?
			pstmt.executeUpdate();	//执行DML语句
		} catch(ClassNotFoundException e) {
			e.printStackTrace();
		} catch(SQLException e) {
			e.printStackTrace();
		} finally {
			try {
				if(pstmt != null) {
					pstmt.close();
					pstmt = null;
				}
				if(conn != null) {
					conn.close();
					conn = null;
				}
			} catch(SQLException e) {
				e.printStackTrace();
			}
		}
	}

}

```



### 2.02 CallableStatement

- CallableStatement用于调用存储过程

#### 示例: TestCallableStatement.java

​	存储过程 p :

```plsql
create or replace procedure p
    (v_a in number, v_b number, v_ret out number, v_temp in out number) --v_a为传入参数，v_b为传入参数，v_ret为传出参数，v_temp为即传入又传出参数
is
begin
    if(v_a > v_b) then
        v_ret := v_a;
    else
        v_ret := v_b;
    end if;
    v_temp := v_temp + 1;
end;
/
```

​	TestCallableStatement.java :

```java
import java.sql.CallableStatement;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Types;

public class TestCallableStatement {

	public static void main(String[] args) {
		Connection conn = null;
		CallableStatement cstmt = null;
		try {
			Class.forName("oracle.jdbc.driver.OracleDriver");
			conn = DriverManager.getConnection("jdbc:oracle:thin:@localhost:1521:orcl", "ryoukai", "ryoukai");
			cstmt = conn.prepareCall("{call p(?, ?, ?, ?)}");
			cstmt.registerOutParameter(3, Types.INTEGER);	//注册第三个参数为输出参数，类型为INTEGER
			cstmt.registerOutParameter(4, Types.INTEGER);	//注册第四个参数为输出参数，类型为INTEGER
			cstmt.setInt(1, 3);
			cstmt.setInt(2, 4);
			cstmt.setInt(4, 5);
			cstmt.execute();
			System.out.println(cstmt.getInt(3));
			System.out.println(cstmt.getInt(4));
		} catch (ClassNotFoundException e) {
			e.printStackTrace();
		} catch (SQLException e) {
			e.printStackTrace();
		} finally {
			try {
				if(cstmt != null) {
					cstmt.close();
					cstmt = null;
				}
				if(conn != null) {
					conn.close();
					conn = null;
				}
			} catch(SQLException e) {
				e.printStackTrace();
			}
		}
	}

}

```



### 2.03 Batch

- Batch用于执行批处理
- Statement和PreparedStatement都可执行批处理

#### 示例: TestBatch.java

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;
import java.sql.Statement;

public class TestBatch {

	public static void main(String[] args) {
		Connection conn = null;
//		Statement stmt = null;
		PreparedStatement pstmt = null;
		try {
			Class.forName("oracle.jdbc.driver.OracleDriver");	//加载OracleDriver
			conn = DriverManager.getConnection("jdbc:oracle:thin:@127.0.0.1:1521:orcl", "ryoukai", "ryoukai");	//使用用户scott连接Oracle数据库
			
//			stmt = conn.createStatement();
//			stmt.addBatch("insert into jdbctest values('10003', '刘伦')");
//			stmt.addBatch("insert into jdbctest values('10004', '张明举')");
//			stmt.addBatch("insert into jdbctest values('10005', '郭少俊')");
//			stmt.executeBatch();	//批量执行DML语句
			
			pstmt = conn.prepareStatement("insert into jdbctest values(?, ?)");
			
			pstmt.setString(1, "10006");
			pstmt.setString(2, "Jone");
			pstmt.addBatch();
			
			pstmt.setString(1, "10007");
			pstmt.setString(2, "櫻木花道");
			pstmt.addBatch();
			
			pstmt.setString(1, "10008");
			pstmt.setString(2, "百事");
			pstmt.addBatch();
			
			pstmt.executeBatch();
		} catch(ClassNotFoundException e) {
			e.printStackTrace();
		} catch(SQLException e) {
			e.printStackTrace();
		} finally {
			try {
//				if(stmt != null) {
//					stmt.close();
//					stmt = null;
//				}
				if(pstmt != null) {
					pstmt.close();
					pstmt = null;
				}
				if(conn != null) {
					conn.close();
					conn = null;
				}
			} catch(SQLException e) {
				e.printStackTrace();
			}
		}
	}

}
```



### 2.04 JDBC处理Transaction

#### 示例: TestTransaction.java

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class TestTransaction {

	public static void main(String[] args) {
		Connection conn = null;
		PreparedStatement pstmt = null;
		try {
			Class.forName("oracle.jdbc.driver.OracleDriver");	//加载OracleDriver
			conn = DriverManager.getConnection("jdbc:oracle:thin:@127.0.0.1:1521:orcl", "ryoukai", "ryoukai");	//使用用户scott连接Oracle数据库
			
			conn.setAutoCommit(false);	//不自动提交
			
			pstmt = conn.prepareStatement("insert into jdbctest values(?, ?)");
			
			pstmt.setString(1, "10009");
			pstmt.setString(2, "Jone.P.Banks");
			pstmt.addBatch();
			
			pstmt.setString(1, "10010");
			pstmt.setString(2, "黄即福");
			pstmt.addBatch();
			
			pstmt.setString(1, "10011");
			pstmt.setString(2, "埽俊");
			pstmt.addBatch();
			
			pstmt.executeBatch();
		} catch(ClassNotFoundException e) {
			e.printStackTrace();
		} catch(SQLException e) {
			e.printStackTrace();
			try {
                if(conn != null) {
                    conn.rollback();	//回滚
                    conn.setAutoCommit(true);	//恢复自动提交
                }
			} catch (SQLException e1) {
				e1.printStackTrace();
			}
		} finally {
			try {
				if(pstmt != null) {
					pstmt.close();
					pstmt = null;
				}
				if(conn != null) {
					conn.close();
					conn = null;
				}
			} catch(SQLException e) {
				e.printStackTrace();
			}
		}
	}

}

```



### 2.05 JDBC处理可滚动的结果集(Movable ResultSet)

#### 示例: TestScroll.java

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class TestScroll {

	public static void main(String[] args) {
		Connection conn = null;
		Statement stmt = null;
		ResultSet rs = null;
		try {
			Class.forName("oracle.jdbc.driver.OracleDriver");
			conn = DriverManager.getConnection("jdbc:oracle:thin:@localhost:1521:orcl", "ryoukai", "ryoukai");
			stmt = conn.createStatement(ResultSet.TYPE_SCROLL_INSENSITIVE, //设置对滚动不敏感
										ResultSet.CONCUR_READ_ONLY);	//并发只读
			rs = stmt.executeQuery("select * from student order by S#");
			rs.next();	//定位到下一个
			System.out.println(rs.getInt(1));	//取得第一个字段作为int型变量并输出
			rs.last();	//定位到最后一行
			System.out.println(rs.getString(1));	//取得第一个字段作为String型变量并输出
			System.out.println(rs.isLast());	//打印是否在最后一行	true
			System.out.println(rs.isAfterLast());	//打印是否是最后一行的下一行  false
			System.out.println(rs.getRow());	//打印一共多少行
			rs.previous();	//定位到上一行
			System.out.println(rs.getString(1));
			rs.absolute(6);	//绝对定位到第六行
			System.out.println(rs.getString(1));
		} catch (ClassNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} finally {
			try {
				if(rs != null) {
					rs.close();
					rs = null;
				}
				if(stmt != null) {
					stmt.close();
					stmt = null;
				}
				if(conn != null) {
					conn.close();
					conn = null;
				}
			} catch(SQLException e) {
				e.printStackTrace();
			}
		}

	}
}
```



### 2.06 JDBC处理可更新的结果集(Updatable ResultSet)

#### 示例: TestUpdateRs.java

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class TestUpdateRs {

	public static void main(String[] args) {
		Connection conn = null;
		Statement stmt = null;
		ResultSet rs = null;
		try {
			Class.forName("oracle.jdbc.driver.OracleDriver");
			conn = DriverManager.getConnection("jdbc:oracle:thin:@localhost:1521:orcl", "ryoukai", "ryoukai");
			stmt = conn.createStatement(ResultSet.TYPE_SCROLL_INSENSITIVE, //设置对滚动不敏感
										ResultSet.CONCUR_UPDATABLE);	//并发访问时可以进行更新
			rs = stmt.executeQuery("select * from student order by S#");
			rs.next();	//定位到下一个
			rs.updateString("SNAME", "AAAA");	//把这一行的SNAME字段的数据更新为AAAA
			rs.updateRow();//更新这一行到数据库中
			
			rs.moveToInsertRow();	//在结果集的最后一行后有一行InsertRow，可以往这一行插入数据，现在移动到这一行
			rs.updateInt(1, 9999);	//把第一个字段设置为9999
			rs.updateString("SNAME", "BBBB");	//把SNAME字段设置为BBBB
			rs.updateInt("SAGE", 20);	//把SAGE字段设置为20
			rs.updateString("SSEX", "m");	//把SSEX字段设置为n
			rs.insertRow();//插入这一行岛数据库中
			
			rs.moveToCurrentRow();	//将光标移动到新建的行
			
			rs.absolute(5);	//绝对定位到第5行
			rs.deleteRow();//删除这一行
			
		} catch (ClassNotFoundException e) {
			e.printStackTrace();
		} catch (SQLException e) {
			e.printStackTrace();
		} finally {
			try {
				if(rs != null) {
					rs.close();
					rs = null;
				}
				if(stmt != null) {
					stmt.close();
					stmt = null;
				}
				if(conn != null) {
					conn.close();
					conn = null;
				}
			} catch(SQLException e) {
				e.printStackTrace();
			}
		}
	}

}

```



### 2.07 DateSource & RowSet

- DataSource
  - DriverManager的替代
  - 方便的实现连接池
  - 分布式的实现
    - DataSource的属性可以动态改变
- RowSet
  - 新的ResultSet
  - 从ResultSet继承
  - 支持断开的结果集
  - 支持JavaBean的标准

