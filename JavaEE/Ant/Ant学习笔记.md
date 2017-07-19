# Ant学习笔记

## 1. 环境搭建

- 从 https://mirrors.tuna.tsinghua.edu.cn/apache/ant/binaries/ 下载zip文件，解压到方便的地址。如 D:\Apache Software

- 新建环境变量，命名为ANT_HOME，该环境变量指向 Ant 的安装文件夹，在这个例子中，该文件夹为 D:\Apache Software\apache-ant-1.10.1

- 将 Apache Ant 的批处理文件的路径添加到 PATH 环境变量里。在这个例子中，该路径应为 D:\Apache Software\apache-ant-1.10.1\bin 文件夹

- 验证安装

  ```powershell
  C:\Users\Ryoukai>ant -version
  Apache Ant(TM) version 1.10.1 compiled on February 2 2017
  ```

  ​

## 2. 构建文件

- Ant构建文件是用XML编写的
- 一般，Ant的构建文件默认为build.xml，放在项目顶层目录



### 2.01 Ant属性

- Ant 使用**属性 (property)** 元素来让你能够具体说明属性。这就允许这些属性能够在不同的构建和不同的环境下发生改变
- 默认情况下，Ant 提供以下预定义的属性，这些属性都是可以在构建文件中使用的

|             属性              |                    解释                    |
| :-------------------------: | :--------------------------------------: |
|          ant.file           |                该构建文件的绝对路径                |
|         ant.version         |            安装的 Apache Ant 的版本            |
|           basedir           | 构建文件的基目录的绝对路径，作为 **project** 元素的 **basedir** 属性 |
|      ant.java.version       |        Ant 使用的 JAVA 语言的软件开发工具包的版本        |
|      ant.project.name       | 项目的名字，具体声明为 **project** 元素的 **name** 属性  |
| ant.project.default-target  |                当前项目的默认目标                 |
| ant.project.invoked-targets |           在当前项目中被调用的目标的逗号分隔列表            |
|        ant.core.lib         |            Ant 的 jar 文件的绝对路径             |
|          ant.home           |                Ant 安装的根目录                |
|       ant.library.dir       |     Ant 库文件的主目录，一般为 ANT_HOME/lib 文件夹     |

- 用户也可以使用 property 元素定义一些额外的属性，例:

  ```xml
  <?xml version="1.0"?>
  	<project name="Hello World Project" default="info">

  	<property name="sitename" value="ryoukai.xyz"/>
  	<target name="info">
  		<echo>Apache Ant Version is ${ant.version} - You are at ${sitename} </echo>
  	</target>
  </project>
  ```

  运行结果:

  ```powershell
  G:\test>ant
  Buildfile: G:\test\build.xml

  info:
       [echo] Apache Ant Version is Apache Ant(TM) version 1.10.1 compiled on February 2 2017 - You are at ryoukai.xyz

  BUILD SUCCESSFUL
  Total time: 0 seconds
  ```

  ​

### 2.02 Ant属性文件

- 一般情况下，属性文件都被命名为 **build.properties**， 并且与 **build.xml** 存放在同一目录层。 你可以基于部署环境 ——比如： **build.properties.dev** 和 **build.properties.test** 创建多个 **build.properties** 文件

- 例:

  build.xml

  ```xml
  <?xml version="1.0"?>
  <project name="Hello World Project" default="info">

     <property file="build.properties"/>

     <target name="info">
        <echo>Apache Ant version is ${ant.version} - You are at ${sitename} </echo>
     </target>

  </project>
  ```

  build.properties

  ```properties
  # the Site Name
  sitename=ryoukai.xyz
  buildversion=1.0.0
  ```

  运行结果:

  ```powershell
  G:\test>ant
  Buildfile: G:\test\build.xml

  info:
       [echo] Apache Ant version is Apache Ant(TM) version 1.10.1 compiled on February 2 2017 - You are at ryoukai.xyz

  BUILD SUCCESSFUL
  Total time: 0 seconds
  ```

- 将设置属性的信息存储在一个属性文件中，只需要修改属性文件中的属性就行了，提高了构建文件的可重复使用性



### 2.03 Ant数据类型

- Ant 提供一些预定义的数据类型。不要将术语“数据类型”和那些在编程语言中可用的数据类型相混淆，而是将他们视作一组已经在产品中配置好的服务。



#### 文件集

- 文件集的数据类型代表了一个文件集合。它被当作一个过滤器，用来包括或移除匹配某种模式的文件。

- 例:

  ```xml
  <fileset dir="${src}" casesensitive="yes">
    	<include name="**/*.java"/>
    	<exclude name="**/*Stub*"/>
  </fileset>
  ```

  这里，src 属性指向项目的源文件夹。文件集选择源文件夹中所有的 .java 文件，除了那些包含有 'Stub' 单词的文件。

  注意: fileset是能区分大小写的过滤器，这意味着名为 Samplestub.java 的文件将不会被排除在文件集之外



#### 模式集合

- 一个模式集合指的是一种模式，基于这种模式，能够很容易地过滤文件或者文件夹。模式可以使用下述的元字符进行创建。

  - **?**  仅匹配一个字符
  - *****  匹配零个或者多个字符
  - ***\***  递归地匹配零个或者多个目录

- 例:

  ```xml
  <patternset id="java.files.without.stubs">
    	<include name="src/**/*.java"/>
    	<exclude name="src/**/*Stub*"/>
  </patternset>
  ```

  该模式集合能够通过一个类似于下述的文件集进行重用：

  ```xml
  <fileset dir="${src}" casesensitive="yes">
     <patternset refid="java.files.without.stubs"/>
  </fileset>
  ```

  ​

#### 文件列表

- 文件列表数据类型与文件集相类似，除了以下几处不同：

  - 文件列表包含明确命名的文件的列表，同时其不支持通配符。
  - 文件列表数据类型能够被应用于现有的或者还不存在的文件中。

- 例:

  ```xml
  <filelist id="config.files" dir="${webapp.src.folder}">
     <file name="applicationConfig.xml"/>
     <file name="faces-config.xml"/>
     <file name="web.xml"/>
     <file name="portlet.xml"/>
  </filelist>
  ```

  在这个例子中，属性 **webapp.src.folder** 指向该项目中的 Web 应用的源文件夹。



#### 过滤器集合

- 使用一个过滤器集合数据类型与拷贝任务，可以在所有文件中使用一个替换值来替换掉一些与模式相匹配的文本

- 例: 对一个已发行的说明文件追加版本号

  ```xml
  <copy todir="${output.dir}">
     <fileset dir="${releasenotes.dir}" includes="**/*.txt"/>
     <filterset>
        <filter token="VERSION" value="${current.version}"/>
     </filterset>
  </copy>
  ```

  其中:

  - 属性 **output.dir** 指向项目的输出文件夹。
  - 属性 **releasenotes.dir** 指向项目的发行说明文件夹。
  - 属性 **current.version** 指向项目的当前版本文件夹。
  - 拷贝任务，顾名思义，是用来将文件从一个地址拷贝到另一个地址。



#### 路径

- **path** 数据类型通常被用来表示一个类路径。各个路径之间用分号或者冒号隔开。然而，这些字符在运行时被替代为执行系统的路径分隔符

- 例: 将类路径设置为项目中 jar 文件和类文件的列表

  ```xml
  <path id="build.classpath.jar">
     <pathelement path="${env.J2EE_HOME}/${j2ee.jar}"/>
     <fileset dir="lib">
        <include name="**/*.jar"/>
     </fileset>
  </path>
  ```

  其中:

  - 属性 **env.J2EE_HOME** 指向环境变量 J2EE_HOME 。
  - 属性 **j2ee.jar** 指向在 J2EE 基础文件夹下面的名为j2ee.jar 的文件。



### 2.04 编译项目

- 例: 编译Java源文件，并将这些类文件存储在WEB-INF\classes文件夹下

  ```xml
  <?xml version="1.0"?>
  <project name="fax" basedir="." default="build">
     <property name="src.dir" value="src"/>	<!-- src.dir表示java源文件的目录 -->
     <property name="web.dir" value="war"/>	<!-- web.dir表示项目的web文件目录 -->
     <property name="build.dir" value="${web.dir}/WEB-INF/classes"/>	<!-- build.dir表示该项目的输出文件目录 -->
     <property name="name" value="fax"/>

     <!-- master-classpath(意思是控制类路径) 包含了构建文件夹和jar文件夹中的所有类文件 -->
     <path id="master-classpath">
        <fileset dir="${web.dir}/WEB-INF/lib">
           <include name="*.jar"/>
        </fileset>
        <pathelement path="${build.dir}"/>
     </path>

     <target name="build" description="Compile source tree java files">
        <!-- 创建一个构建目录 -->
        <mkdir dir="${build.dir}"/>
        <!-- 目标目录为build.dir，source表示用1.6版本的编译其进行编译，targer表示生成的class文件将保证和1.6版本的虚拟机上运行 -->
        <javac destdir="${build.dir}" source="1.6" target="1.6">
           <!-- 源文件路径为sec.dir -->
           <src path="${src.dir}"/>
           <!-- 类路径为master-classpath -->
           <classpath refid="master-classpath"/>
        </javac>
     </target>

     <!-- 清除构建文件夹下所有文件 -->
     <target name="clean" description="Clean output directories">
        <delete>
           <fileset dir="${build.dir}">
              <include name="**/*.class"/>
           </fileset>
        </delete>
     </target>
  </project>
  ```

  ​

### 2.05 生成JAR文件

- 创建 Ant 中的 JAR 文件十分简单，运用 **jar** 任务来生成 **jar** 包。在 **jar** 任务中常用的属性如下所示：

  |       属性        |                    描述                    |
  | :-------------: | :--------------------------------------: |
  |     basedir     |      表示输出 JAR 文件的基目录。默认情况下，为项目的基目录。      |
  |    compress     |        表示告知 Ant 对于创建的 JAR 文件进行压缩。        |
  | keepcompression |           表示 project 基目录的绝对路径。           |
  |    destfile     |             表示输出 JAR 文件的名字。              |
  |    duplicate    | 表示发现重复文件时 Ant 执行的操作。可以是添加、保存、或者是使该重复文件失效。 |
  |    excludes     |         表示移除的文件列表，列表中使用逗号分隔多个文件。         |
  |  excludesfile   |           与上同，但是使用模式匹配的方式排除文件。           |
  |    inlcudes     |             与 excludes 正好相反。             |
  |  includesfile   | 表示在被归档的文件模式下，打包文件中已有的文件。与 excludesfile 相反。 |
  |     update      |         表示告知 Ant 重写已经建立的 JAR 文件。         |

- 例: 生成util.jar

  ```xml
  <jar destfile = "${web.dir}/lib/util.jar"
     basedir = "${build.dir}/classes"
     includes = "faxapp/util/**"
     excludes = "**/Test.class" />
  ```

  如果想让util.jar成为可执行文件

  ```xml
  <target name="build-jar">
     <jar destfile="${web.dir}/lib/util.jar"
        basedir="${build.dir}/classes"
        includes="faxapp/util/**"
        excludes="**/Test.class">

        <manifest>
           <attribute name="Main-Class" value="com.tutorialspoint.util.FaxUtil"/>
           <attribute name="Class-Path" value="${mf.classpath}">	<!-- mf.classpath为依赖的jar包 -->
        </manifest>

     </jar>
  </target>
  ```



### 2.06 生成WAR文件

- WAR 任务是 JAR 任务的一个扩展，但是其对控制哪些文件进入 WEB-INF/classes 文件夹和生成 web.xml 文件进行了一些很好的补充。WAR 任务对指定 WAR 文件布局是非常有用的。

- 既然 WAR 任务是 JAR 任务的一个扩展，JAR 任务的所有的属性都适用于 WAR 任务。

- WAR任务中补充的属性:

  |   属性    |                 描述                 |
  | :-----: | :--------------------------------: |
  | webxml  |           web.xml 文件的路径            |
  |   lib   |   指定什么文件可以进入 WEB-INF\lib 文件夹的一个组   |
  | classes | 指定什么文件可以进入 WEB-INF\classes 文件夹的一个组 |
  | metainf |       指定生成 MANIFEST.MF 文件的指令       |

- 例:

  ```xml
  <target name="build-war">

     <war destfile="fax.war" webxml="${web.dir}/web.xml">
        <fileset dir="${web.dir}/WebContent">
           <include name="**/*.*"/>
        </fileset>

        <lib dir="thirdpartyjars">
           <exclude name="portlet.jar"/>
        </lib>

        <classes dir="${build.dir}/web"/>
     </war>

  </target>
  ```

  ​

