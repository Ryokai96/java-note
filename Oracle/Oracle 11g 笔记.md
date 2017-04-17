# Oracle 11g 笔记

## 1. Oracle 11g 安装

- 默认端口: 1521

- 用浏览器链接oracle: [http://localhost:1158/em](http://localhost:1158/em) (需要启动OracleDBConsoleorcl服务)

- 如果安装时未更改SID则SID为orcl

- 使用OS身份连接: sqlplus / as sysdba

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




### 3.11 update 更新

```sql
update emp set sal = sal * 2 where deptno = 10;  /*更新表emp中内容，把deptno=10的每行中的sal改为原来的两倍*/
```



### 3.12 delete 删除

```sql
delete from emp;  /*删除emp中所有行*/
delete from dept where deptno = 10;  /*删除表dept中deptno=10的行*/
```



### 3.13 create table 创建表，约束

- 创建约束: constraint 约束名 约束条件

- default: 给该字段设置默认值

- 非空约束: not null

- 唯一约束: unique

  唯一约束要求该字段不能有重复的值，但多个null(空值)不认为是重复的值

- 主键约束: primary key

  主键非空且唯一

- 外键约束: foreign key

  外键约束是建立在两个字段上的，某个字段会参考另一个字段上的值，参考的字段的值不能设为被参考的字段没有的值

  **被参考字段必须是主键**

```sql
create table t (a varchar2(10));  /*创建表名为t的表，表中有一个字段，列名为a，数据类型为varchar2(10)(可变字符串，字符串长度最多为10)*/

create table class
(
  id number(4) primary key,
  name varchar2(20) not null
);

create table stu
(
  id number(6) primary key,  /*给该字段添加主键约束*/
  name varchar2(20) constraint stu_name_nn not null,  /*constraint用于创建约束，stu_name_nn 为约束名，约束条件是不能为空*/
  sex number(1),
  age number(3),
  sdate date,
  grade number(2) default 1,  /*default指定默认值为1*/
  class number(4) references class(id),  /*创建外键约束，参考字段为class，被参考字段为表class中的id字段*/
  email varchar2(50),
  constraint stu_name_email_uni unique(email, name)  /*创建一个表级的约束，约束条件为email和name的组合不能重复*/
  /*constraint stu_id_pk primary key(id)  创建表级约束，将id设为该表主键*/
  /*constraint stu_class_fk foreign key(class) references class(id)  建立外键，参考字段为class，被参考字段为表class中的id字段*/
);
```



### 3.14 drop 删除表

```sql
drop table t;  /*删除表t*/
```



### 3.15 alter table 修改表结构

```sql
alter table stu add (addr varchar2(100));  //给表stu增加一个字段addr
alter table stu drop (addr);  //删除表stu的addr字段
alter table stu modify (addr varchar2(50));  //修改表stu的addr字段的最大长度为50，若addr字段中有数据超过了50，则不可修改，会报错
alter table stu drop constraint stu_class_fk;  //删除表stu的约束条件stu_class_fk
alter table stu add constraint stu_class_fk foreign key (class) references class(id);  //为表stu增加外键stu_class_fk
```



### 3.16 索引

- 为某个字段建立索引可提高访问这个字段的数据的效率，但是会减慢修改这个字段的数据的效率，**不要轻易建立索引**


- 当为一个表的某个字段添加了主键约束或唯一约束，会自动为这个字段添加索引

```sql
create index idx_stu_email on stu (email);  /*为表stu的email字段建立名为idx_stu_email的索引*/
create index idx_stu_email_class on stu (email, class);  /*为表stu的email和class的组合建立索引*/
drop index idx_stu_email_class;  /*删除索引*/
```



### 3.17 视图

- 视图相当于一个子查询，可以简化查询，可对机密数据提供安全保护
- 表结构改变，需要手动修改视图，这会增加维护的支出
- 更新视图的数据，实际是更新原表的数据，但是有时会根据多张表建立视图，视图只有满足下列条件才可更新
  - select语句在选择列表中没有聚合函数，也不包含TOP,GROUP BY,UNION(除非视图是分区视图)或DISTINCT子句。聚合函数可以用在FROM子句的子查询中，只要不修改函数返回的值
  - select语句的选择列表中没有派生列。派生列是由任何非简单列表达式(使用函数、加法或减法运算符等)所构成的结果集列
  - select语句中的FROM子句至少引用一个表。select语句不能只包含非表格格式的表达式(即不是从表派生出的表达式)
  - INSERT,UPDATE和DELETE语句在引用可更新视图之前，也必须如上述条件指定的那样满足某些限制条件。只有当视图可更新，并且所编写的UPDATE或INSERT语句只修改视图的FROM子句引用的一个基表中的数据时，UPDATE和INSERT语句才能引用视图。只有当视图在其FROM子句中只引用一个表时，DELETE语句才能引用可更新的视图

```sql
create view v$_stu as select id, name, age from stu;  /*新建视图*/
```



### 3.18 序列 (Oracle独有)

- 序列用于产生一个有序的数，且这个数不会重复，即便两个线程同时访问，这个数也不会重复

```sql
create table article 
(
  id number,
  title varchar2(1024),
  cont long
);
create sequence seq;  /*创建名为seq的序列*/
insert into article(id, title, cont) values(seq.nextval, 'a', 'b');  /*nextval为sequence的一个属性，表示下一个sequence的值，此时seq.nextval值为1*/
insert into article(id, title, cont) values(seq.nextval, 'a', 'b');  /*此时seq.nextval值为2*/
insert into article(id, title, cont) values(seq.nextval, 'a', 'b');  /*此时seq.nextval值为3*/
select seq.nextval from dual;  /*此时seq.nextval为4*/
select seq.nextval from dual;  /*此时seq.nextval为5*/
drop sequence seq;  /*删除序列*/
```



## 4. transaction 事务

- 对Oracle来说，一个transaction起始于第一条dml语句，结束于ddl语句和dcl语句

- rollback可以回滚到整个transaction前

  ```sql
  update emp set sal = sal * 2;  /*transaction开启*/
  delete from dept;
  insert into salgrade(grade, losal, hisal) values(6, 10000, 20000);
  rollback;  /*transaction结束，回滚到整个transaction之前，即回退到上面三条语句执行前*/

  update emp set sal = sal*2;  /*transaction开启*/
  commit;  /*提交，此时transaction结束*/
  rollback;  /*transaction已结束，无法回滚*/

  update dept set deptno = deptno * 2;  /*transaction开启*/
  create table t (a varchar2(20));  /*transaction结束，并自动提交*/
  rollback;  /*无法回滚*/
  ```

- 当用户正常退出时，transaction自动提交；用户非正常断开连接时，transaction自动rollback




## 5. 数据字典表

- 每个用户都有数据字典表，用于记录用户拥有的表、视图等的信息

```sql
select table_name from user_tables;  /*查询此用户所有表的表名*/
select view_name from user_views;  /*查询此用户所有视图的视图名*/
select constraint_name, table_name from user_constraints;  /*查询此用户所有约束的约束名，以及约束所在表的表名*/
```

- dictionary表用于记录Oracle数据库各数据字典表的信息

  ```sql
  desc dictionary  /*dictionary表只有两个字段，TABLE_NAME(表名)和COMMENTS(这张表的描述)*/
  ```

  >  名称                                      是否为空? 类型
  >  ----------------------------------------- -------- ----------------------------
  >  TABLE_NAME                                         VARCHAR2(30)
  >  COMMENTS                                           VARCHAR2(4000)




## 6. 三范式

- 范式： 数据库设计的规则
- 三范式目标：不存在冗余数据
- 第一范式：要有主键，列不可分(能用字段表示就无需再分成两个字段)
- 第二范式：不存在部分依赖
  - 当一张表里有多个字段作为主键时，非主键的字段不依赖于非主键字段或者部分主键字段(只能依赖于所有主键的组合)，若非如此，需要将该表分割成多表
- 第三范式：不存在传递依赖，不是主键的任何其他字段必须直接依赖于主键