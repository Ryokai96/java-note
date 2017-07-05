# MySQL 笔记

[TOC]

## 1. 开始使用

- MySQL默认端口号为 3306



### 1.01 开启MySQL服务，登录数据库

```mysql
# 以管理员身份执行，开启和关闭mysql服务
net start mysql	-- 开启mysql服务
net stop mysql	-- 关闭mysql服务

# 登录mysql
mysql -u root -p	-- 以root用户登录数据库，加上-p表示需输入密码
```



### 1.02 创建数据库，选择数据库

```mysql
create database mydata;	-- 创建数据库 mydata
use mydata;	-- 选择mydata为当前操作的数据库
```



### 1.03 创建表

```mysql
create table dept
(
	deptno int primary key,
  	dname varchar(14),
  	loc varchar(13)
);
```

```mysql
create table emp
(
	empno int primary key,
	ename varchar(10),
    job varchar(10),
    mgr int,
    hiredate datetime,
    sal double,
    comm double,
    deptno int,
    foreign key(deptno) references dept(deptno)	-- 创建外键关联
);
```



### 1.04 insert插入

```mysql
insert into dept values(10, 'A', 'A');
insert into dept values(20, 'B', 'B');
insert into dept values(30, 'C', 'C');
insert into dept values(40, 'D', 'D');
insert into dept values(50, 'E', 'E');
commit;
```



## 2. 与Oracle的不同之处

### 2.01 分页

```mysql
select * from dept order by deptno desc limit 2, 2;	-- 按照deptno倒序排列，取出排列结果中第三行开始的后两行
```



### 2.02 自动递增

```mysql
create table article
(
	id int primary key auto_increment,	-- auto_increment 即代表此字段自动递增
  	title varchar(255)
);

insert into article values (null, 'a');
insert into article values (null, 'b');
insert into article values (6, 'c');
insert into article values (null, 'd');
insert into article(title) values ('e');
```

插入结果:

```mysql
select * from article;
+----+-------+
| id | title |
+----+-------+
|  1 | a     |
|  2 | b     |
|  6 | c     |
|  7 | d     |
|  8 | e     |
+----+-------+
```

 

### 2.03 日期datetime

```mysql
select date_format(now(), '%y-%m-%d %h:%i:%s');
+-----------------------------------------+
| date_format(now(), '%y-%m-%d %h:%i:%s') |
+-----------------------------------------+
| 17-06-02 07:49:21                       |
+-----------------------------------------+

select date_format(now(), '%Y-%M-%D %H:%i:%S');
+-----------------------------------------+
| date_format(now(), '%Y-%M-%D %H:%i:%S') |
+-----------------------------------------+
| 2017-June-2nd 19:50:22                  |
+-----------------------------------------+
```

插入

```mysql
insert into emp values(9999, 'test', 'clerk', 7369, '1981-12-23 12:23:23', 8000, 20, 10);
```



## 3. JDBC

#### 示例: TestMySQLConnection.java

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class TestMySQLConnection {

	public static void main(String[] args) {
		Connection conn = null;
		Statement stmt = null;
		ResultSet rs = null;
		
		try {
			Class.forName("com.mysql.jdbc.Driver").newInstance();
			
			//IP为localhost，数据库名为mydata，用户为root，密码为root
		    conn = DriverManager.getConnection("jdbc:mysql://localhost/mydata?" + "user=root&password=root");
		    stmt = conn.createStatement();
		    rs = stmt.executeQuery("select * from dept");
		    
		    while(rs.next()) {
		    	System.out.println(rs.getString("deptno"));
		    }
		    
		} catch (InstantiationException e) {
			e.printStackTrace();
		} catch (IllegalAccessException e) {
			e.printStackTrace();
		} catch (ClassNotFoundException e) {
			e.printStackTrace();
		} catch (SQLException ex) {
		    System.out.println("SQLException: " + ex.getMessage());
		    System.out.println("SQLState: " + ex.getSQLState());
		    System.out.println("VendorError: " + ex.getErrorCode());
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
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}

	}

}

```



## 4. tree

```mysql
# 创建数据库bbs
create database bbs;

# 选择数据库bbs为当前操作的数据库
use bbs;

# 创建表article
create table article
(
	id int primary key auto_increment,
  	pid int,	-- 父节点id
  	rootid int,	-- 根节点id
  	title varchar(255),	-- 标题
  	cont text,	--内容
  	pdate datetime,	-- 发表时间
  	isleaf int	-- 是否是叶子节点，1代表是叶子结点，0代表不是叶子节点
);

# 插入数据
insert into article values (null, 0, 1, '蚂蚁大战大象', '蚂蚁大战大象', now(), 1);
insert into article values (null, 1, 1, '大象被打趴下了', '大象被打趴下了', now(), 1);
insert into article values (null, 2, 1, '蚂蚁也不好过', '蚂蚁也不好过', now(), 0);
insert into article values (null, 2, 1, '瞎说', '瞎说', now(), 1);
insert into article values (null, 4, 1, '没有瞎说', '没有瞎说', now(), 0);
insert into article values (null, 1, 1, '怎么可能', '怎么可能', now(), 1);
insert into article values (null, 6, 1, '怎么没有可能', '怎么没有可能', now(), 0);
insert into article values (null, 6, 1, '可能性是很大的', '可能性是很大的', now(), 0);
insert into article values (null, 2, 1, '大象进医院了', '大象进医院了', now(), 1);
insert into article values (null, 9, 1, '蚂蚁是护士', '蚂蚁是护士', now(), 0);
```



- 打印tree

  ArticleTree.java

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class ArticleTree {

	public static void main(String[] args) {
		new ArticleTree().show();
	}
	
	/**
	 * 打印树
	 */
	private void show() {
		Connection conn = null;
		Statement stmt = null;
		ResultSet rs = null;
		
		try {
			Class.forName("com.mysql.jdbc.Driver");
			conn = DriverManager.getConnection("jdbc:mysql://localhost/bbs?useSSL=true&user=root&password=root");
			stmt = conn.createStatement();
			rs = stmt.executeQuery("select * from article where pid = 0");	//找出所有父节点id为0的节点
			
			while(rs.next()){
				System.out.println(rs.getString("cont"));	//打印节点
				tree(conn, rs.getInt("id"), 1);	//打印子节点
			}
		} catch (ClassNotFoundException e) {
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
	
	/**
	 * 打印所有子节点
	 * @param conn 连接实例
	 * @param id 父节点id
	 * @param level 树的层级
	 */
	private void tree(Connection conn, int id, int level) {
		Statement stmt = null;
		ResultSet rs = null;
		StringBuffer strPre = new StringBuffer("");	//用于凸显层次
		for(int i = 0; i < level; i++) {
			strPre.append("            ");
		}
		
		try {
			stmt = conn.createStatement();
			String sql = "select * from article where pid = " + id;	//找出所有父节点id为id的节点
			rs = stmt.executeQuery(sql);
			
			while(rs.next()) {
				System.out.println(strPre + rs.getString("cont"));	//打印节点
				
				if(rs.getInt("isleaf") != 0) {	//如果不是叶子结点
					tree(conn, rs.getInt("id"), level + 1);	//打印其所有子节点
				}
			}
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
			} catch(SQLException e) {
				e.printStackTrace();
			}
		}
	}

}
```

输出结果:

> 蚂蚁大战大象
>
> ​		大象被打趴下了
>
> ​		            	蚂蚁也不好过           
>
> ​				瞎说                       
>
> ​						 	没有瞎说            
>
> ​				大象进医院了                        
>
> ​							蚂蚁是护士怎么可能            
>
> ​		怎么可能
>
> ​				怎么没有可能
>
> ​            			可能性是很大的