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



## 2. SQL语句

### 2.01 组函数

- 把多条记录作为输入最后产生一个输出

#### max

```sql
select max(sal) from emp;  sal的最大值
```

#### min

```sql
select min(sal) from emp; sal的最小值
```

#### avg

```sql
select avg(sal) from emp;  sal的平均值
```

#### sum

```sql
select sum(sal) from emp;  sal的总和
```

#### count

```sql
select count(*) from emp;  这张表中非空行数
select count(*) from emp where deptno = 10;  deptno=10的行数
select count(comm) from emp;  这张表中comm非空的行数
```



### 2.02 group by 语句

- 当语句中有where语句和group by语句时，会先执行where语句，在where选取出的结果中进行分组

```sql
select avg(sal) from emp group by deptno;  把deptno值相同的分为一组，求每一组的sal的平均数
select max(sal) from emp group by deptno, job;  把deptno和job都相同的分为一组，求每一组的sal的最大值
```



### 2.03 having 语句

```sql
select avg(sal) from emp group by deptno having avg(sal) > 2000;  按照deptno分组后，求出sal的平均数大于2000的组的sal的平均数
```



### 2.04 order by 语句

- asc: 升序(默认)
- desc: 降序

```sql
select ename,sal from emp order by sal desc;  按照sal的值降序输出
```

