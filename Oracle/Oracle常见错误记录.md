[TOC]

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



