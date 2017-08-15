# log4j 学习笔记

## 1. log4j 介绍

- 通过log4j可以看到程序运行过程中更详细的信息
- 经常使用log4j查看日志



## 2. log4j 使用

1. 导入log4j的jar包

   - log4j-1.2.17.jar
   - commons-logging-1.2.jar

2. 创建log4j的配置文件，到src下面

   log4j.properties

   ```properties
   ### direct log message to stdout ###
   log4j.appender.stdout=org.apache.log4j.ConsoleAppender
   log4j.appender.stdout.Target=System.err
   log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
   log4j.appender.stdout.ConversionPattern=%d{ABSOLUTE} %5p %c{1}:%L - %m%n

   ### direct log message to file mylog.log ###
   log4j.appender.file=org.apache.log4j.FileAppender
   log4j.appender.file.File=c:\mylog.log
   log4j.appender.file.layout=org.apache.log4j.PatternLayout
   log4j.appender.file.ConversionPattern=%d{ABSOLUTE} %5p %c{1}:%L - %m%n

   ### set log levels - for more verbose logging change 'info' to 'debug' ###

   log4j.rootLogger=info, stdout
   ```

3. 设置日志级别

   > log4j.rootLogger=info, stdout

   - info: 看到基本信息

   >  log4j.rootLogger=debug, stdout

   - debug: 看到更详细信息