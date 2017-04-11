# Oracle 11g 笔记

## 1. Oracle 11g 安装

- 默认端口: 1521

- 用浏览器链接oracle: [http://localhost:1158/em](http://localhost:1158/em) (需要启动OracleDBConsoleorcl服务)

- 如果安装时未更改SID则SID为orcl

- 使用sysdba身份登录: sqlplus / as sysdba

- 修改密码: alter user username identified by password;

  密码一般设置为: 超级管理员:sys  密码:sys

  ​			     普通管理员:system  密码:manager

  ​			     普通用户:scott  密码:tiger

- 解锁scott用户: alter user scott account unlock;




## 2. Oracle的逻辑结构

- Oracle的逻辑结构包括表空间(tablespace)，段(segment)，区(extent)，数据块(data block)
- oracle数据库在逻辑上是由多个表间组成的，表空间中存储的对象叫段，比如数据段，索引段，和回退段。段由区组成，区是磁盘分配的最小单位。段的增大是通过增加区的个数来实现的。每个区的大小是数据块大小的整数倍，区的大小可以不相同；数据块是数据库中最小的I/O单位，同时也是内存数据缓冲区的单位，及数据文件存储空间单位。块的大小由参数DB_BLOCK_SIZE设置，其值应设置为操作系统块大小的整数倍。



### 2.01 表空间

- 表空间是Oracle数据库最大的逻辑结构，一个Oracle数据库在逻辑上由多个表空间组成，一个表空间只隶属于一个数据库。
- Oracle中有一个称为SYSTEM的表空间，这个表空间是在创建或安装数据库时自动创建的。主要用于存储系统的数据字典，过程，函数，触发器等；也可以存储用户的表，索引等。
- 一个表空间可以有多数据文件，但是一个数据文件只能属于一个表空间。
- 一个表空间就是一片磁盘区域,他由一个或者多个磁盘文件组成,一个表空间可以容纳许多表、索引或者簇等。每个表空间有一个预制的磁盘区域称为初始区间（initial   extent）用完这个区间后再用下一个，直到用完表空间，这时候需要对表空间进行扩展，增加数据文件或者扩大已经存在的数据文件



### 2.02 段

- Oracle中的段可以分成4种类型：数据段、索引段、回滚段、临时段。
  - 数据段用来存储用户的数据，每个表都有一个对应的回滚段，其名称和数据表的名字相同。
  - 索引段用来存储系统、用户的索引信息。
  - 回滚段用来存储用户数据修改前的值，回退段与事务是一对多的关系，一个事务只能使用一个回退段，而一个回退段可存放一个或多个事务的回退数据。
  - 临时段用于order by语句的排序以及一些汇总。



### 2.03 区

- 区是磁盘空间分配的最小单位。磁盘按区划分，每次至少分配一个区。

- 区存储于段中，它由连续的数据块组成。

- 区的分配过程中，每次至分配5个区。如果所剩的空闲空间不够5个区，就会出现错误：ORA-01653。

- 可以通过字典dba_tablespaces查询表空间中区的信息。可以通过字典user_tables查询段中区的信息。可以通过字典user_extents查询区的分配状况。

- 我们可以通过以下SQL语句分别查询表空间、段、区中区的分配信息:

  ```sql
  select * from dba_tablespaces;  /*查询表空间中区的分配信息*/
  select table_name, tablespace_name, min_extents, max_extents from user_tables;  /*查询段空间中区的分配信息*/
  select * from user_extents;  /*查询区中区的分配信息*/
  ```



### 2.04 数据块

- 数据块是数据中中最小的数据组织单位与管理单位，是数据文件磁盘存储空间单位，也是数据库I/O 的最小单位，数据块大小由DB_BLOCK_SIZE参数决定，不同的oracle版本DB_BLOCK_SIZE的默认值是不同的。
- Oracle 11g 的DB_BLOCK_SIZE默认值为8192(8K)



## 3. SQL语句

### 3.01 SQL语句分为三类:DML、DDL、DCL

- DML(Data Manipulation Language): 数据操作语言，SQL中处理数据等操作统称为数据操纵语言

  - SELECT - retrieve data from the a database 查询

  - INSERT - insert data into a table 添加

  - UPDATE - updates existing data within a table 更新

  - DELETE - deletes all records from a table, the space for the records remain 删除

  - CALL - call a PL/SQL or Java subprogram

  - EXPLAIN PLAIN - explain access path to data

    Oracle RDBMS执行每一条SQL语句，都必须经过Oracle优化器的评估。所以，了解优化器是如何选择(搜索)路径以及索引是如何被使用的，对优化SQL语句有很大的帮助。Explain可以用来迅速方便地查出对于给定SQL语句中的查询数据是如何得到的即搜索路径(我们通常称为Access Path)。从而使我们选择最优的查询方式达到最大的优化效果。

  - LOCK TABLE - control concurrency 锁，用于控制并发

- DDL(Data Definition Language): 数据定义语言，用于定义和管理 SQL数据库中的所有对象的语言

  - CREATE - to create objects in the database 创建

  - ALTER - alters the structure of the database 修改

  - DROP - delete objects from the database 删除

  - TRUNCATE - remove all records from a table, including all spaces allocated for the records are removed - 删除整张表

    使用方法: truncate table 表名

    truncate速度快,而且效率高,因为:

    TRUNCATE TABLE 在功能上与不带 WHERE 子句的 DELETE 语句相同：二者均删除表中的全部行。但 TRUNCATE TABLE 比 DELETE 速度快，且使用的系统和事务日志资源少。

    DELETE 语句每次删除一行，并在事务日志中为所删除的每行记录一项。TRUNCATE TABLE 通过释放存储表数据所用的数据页来删除数据，并且只在事务日志中记录页的释放。

    TRUNCATE TABLE 删除表中的所有行，但表结构及其列、约束、索引等保持不变。新行标识所用的计数值重置为该列的种子。如果想保留标识计数值，请改用 DELETE。如果要删除表定义及其数据，请使用 DROP TABLE 语句。

    对于由 FOREIGN KEY 约束引用的表，不能使用 TRUNCATE TABLE，而应使用不带 WHERE 子句的 DELETE 语句。由于 TRUNCATE TABLE 不记录在日志中，所以它不能激活触发器。

    TRUNCATE TABLE 不能用于参与了索引视图的表。

  - COMMENT - add comments to the data dictionary 注释

  - GRANT - gives user's access privileges to database 授权

  - REVOKE - withdraw access privileges given with the GRANT command 收回已经授予的权限

- DCL(Data Control Language): 数据控制语言，用来授予或回收访问数据库的某种特权，并控制数据库操纵事务发生的时间及效果，对数据库实行监视等

  - COMMIT - save work done 提交
  - SAVEPOINT - identify a point in a transaction to which you can later roll back 保存点
  - ROLLBACK - restore database to original since the last COMMIT 回滚
  - SET TRANSACTION - Change transaction options like what rollback segment to use 设置当前事务的特性，它对后面的事务没有影响



### 3.02 组函数

- 把多条记录作为输入最后产生一个输出

#### max

```sql
select max(sal) from emp;  /*sal的最大值*/
```

#### min

```sql
select min(sal) from emp; /*sal的最小值*/
```

#### avg

```sql
select avg(sal) from emp;  /*sal的平均值*/
```

#### sum

```sql
select sum(sal) from emp;  /*sal的总和*/
```

#### count

```sql
select count(*) from emp;  /*这张表中非空行数*/
select count(*) from emp where deptno = 10;  /*deptno=10的行数*/
select count(comm) from emp;  /*这张表中comm非空的行数*/
```



### 3.03 group by 语句

- 当语句中有where语句和group by语句时，会先执行where语句，在where选取出的结果中进行分组

```sql
select avg(sal) from emp group by deptno;  /*把deptno值相同的分为一组，求每一组的sal的平均数*/
select max(sal) from emp group by deptno, job;  /*把deptno和job都相同的分为一组，求每一组的sal的最大值*/
```



### 3.04 having 语句

```sql
select avg(sal) from emp group by deptno having avg(sal) > 2000;  /*按照deptno分组后，求出sal的平均数大于2000的组的sal的平均数*/
```



### 3.05 order by 语句

- asc: 升序(默认)
- desc: 降序

```sql
select ename,sal from emp order by sal desc;  /*按照sal的值降序输出
```



### 3.06 SQL 1999

- SQL 1999的思想: where语句中只写数据过滤条件，不写表的连接条件

```sql
/*交叉连接*/
select ename,dname from emp,dept;
select ename,dname from emp cross join dept;  /*cross join意思就是交叉连接*/

/*等价连接*/
select ename,dname from emp,dept where emp.deptno=dept.deptno;
select ename,dname from emp join dept on (emp.deptno = dept.dept);
select ename,dname from emp join dept using(deptno);  /*不推荐使用*/

/*非等值连接*/
select ename,grade from emp e join salgrade s on (e.sal between s.losal and s.hisal);
select ename,dname,grade 
from emp e 
	join dept d on(e.deptno = d.deptno) 
	join salgrade s on (e.sal between s.losal and s.hisal) 
where ename not like '_A%';

/*自连接*/
select e1.ename,e2.ename from emp e1 join emp e2 on (e1.mgr = e2.empno);

/*外连接*/
select e1.ename,e2.ename from emp e1 left join emp e2 on(e1.mgr = e2.empno);  /*left join意思是左外连接，左外连接会把左边这张表(e1)中多余的数据(不能和另外一张表产生连接的数据)取出*/
select ename,dname from emp e right join dept d on (e.deptno = d.deptno);  /*right join意思是右外连接*/

/*全外连接，sql 1999 才得以实现，可把左右两表中多余的数据都取出来*/
select ename,dname from emp e full join dept d on (e.deptno = d.deptno);  /*full join意思为全外连接*/
```


### 3.07 导入与导出命令 

- exp命令用于把数据从远程数据库服务器导出至本地,生成dmp文件
- imp命令用于把本地的数据库dmp文件从本地导入到远程的Oracle数据库中

```sql
exp /*导出xx用户(或表)，会导出.dmp文件到当前文件夹*/
imp /*从.dmp文件选择内容导入到xx用户*/
```



### 3.08 创建用户，给用户权限

```sql
create user ryoukai identified by ryoukai default tablespace users quota 10M on users;  /*创建用户名为ryoukai，密码为ryoukai的用户，在默认的表空间users中分配10M空间给ryoukai用户*/
grant create session, create teble, create view to ryoukai;  /*为新用户ryoukai赋予权限，create session为登录权限*/
```



### 3.09 插入数据

```sql
insert into dept values(50, 'game', 'beijing');
insert into dept(deptno, dname, loc) values(50, 'game', 'beijing');	/*推荐写法*/
```



###  3.10 rownum

- 例:

  ```sql
  select empno, ename from emp where rownum <= 5;  /*从emp表中选取empno和ename，再从选取的结果中选取前5条*/
  ```


- rownum机制:
  1. Oracle executes your query.
  2. Oracle fetches the first row and calls it row number 1.
  3. Have we gotten past row number meets the criteria? If no, then Oracle discards the row, If yes, then Oracle return the row.
  4. Oracle fetches the next row and advances the row number (to 2, and then to 3, and then to 4, and so forth).
  5. Go to step 3.


- rownum是oracle系统按顺序分配的从查询返回的行的编号，返回的第一行分配的是1，第二行是2，依此类推，这个伪字段可以用于限制查询返回的总行数，而且rownum不能以任何表的名称作为前缀

- rownum只能和 < 或 <= 或 != 一起用，!= 的效果和 < 一样

  - 如果希望找到表中第一信息，可以使用rownum=1作为条件。但是想找到表中第二条信息，使用rownum=2结果查不到数据。因为rownum都是从1开始，但是1以上的自然数在rownum做等于判断是时认为都是false条件，所以无法查到rownum = n（n>1的自然数）

  - 如果想找到从第二行记录以后的记录，当使用rownum>2是查不出记录的，原因是由于rownum是一个总是从1开始的伪列，Oracle 认为rownum> n(n>1的自然数)这种条件依旧不成立，所以查不到记录

  - 那如何才能找到第二行以后的记录呀。可以使用以下的子查询方法来解决。注意子查询中的rownum必须要有别名，否则还是不会查出记录来，这是因为rownum不是某个表的列，如果不起别名的话，无法知道rownum是子查询的列还是主查询的列

    ```sql
    select ename from (select rownum r, ename from emp) where r > 10;
    ```

- rownum与排序

  - Oracle中的rownum是在select取数据的时候就产生了序号，需要注意

    ```sql
    select ename, sal from emp where rownum <= 5 order by sal desc;  /*首先选取出ename和sal，同时rownum已产生，所以后面的order by排序是将已选取的前五行数据排序，而不是所有数据排序再选出前五行*/
    select ename, sal from (select ename, sal from emp order by sal desc) where rownum <= 5;  /*使用子查询以达到先排序，再取出前五行的目的*/
    ```

    ​