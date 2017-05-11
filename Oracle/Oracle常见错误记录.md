---
title: Oracle常见错误记录
date: 2017-05-10 16:08:30
tags: [Oracle, errors]
categories: Oracle
keywords: Oracle常见错误, Oracle error, Oracle个人记录
description: 记录个人使用Oracle时出现的错误
---

在使用Oracle的时候，发现以前碰到的错误过了一段时间就会忘，忘了就继续出错 - - !

特开一贴，记录犯过的错误

另外吐槽一下Oracle的中文错误提示真的太变态了，看的人根本摸不着头脑，不知道英文的如何



#### ORA-00937: 不是单组分组函数

- select列表项中除了包含聚合函数外，还包含了表的某些列，那么你将必须使用group by语句，否则语法通不过

```plsql
SELECT student.s#, student.sname, COUNT(sc.c#), SUM(sc.score)
FROM student left join sc ON(student.s# = sc.s#);
--在select中使用了聚合函数COUNT()和SUM()，并且还有表的某些列，不使用group by语句就会报错
```

应改为

```plsql
SELECT student.s#, student.sname, COUNT(sc.c#), SUM(sc.score)
FROM student left join sc ON(student.s# = sc.s#)
group by student.s#, student.sname;
```





#### ORA-00979: 不是GROUP BY 表达式

- select 列表项中不存在的列可以出现在group by的列表项中，但**在select列表项中出现的列必须全部出现在group by后面(聚合函数除外)。**

```plsql
SELECT student.s#, student.sname, COUNT(sc.c#), SUM(sc.score)
FROM student left join sc ON(student.s# = sc.s#)
group by student.s#;
--没错，就是在解决ORA-00937错误的时候，随手加了个group by语句，结果出现了新的错误。。。
```

应改为

```plsql
SELECT student.s#, student.sname, COUNT(sc.c#), SUM(sc.score)
FROM student left join sc ON(student.s# = sc.s#)
group by student.s#, student.sname;
```



