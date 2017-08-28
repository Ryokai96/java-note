## Java 基础问题

### 1. 抽象类与接口的区别，多态和面向接口编程是什么概念；

- 抽象类与接口的区别
  - 语法层面
    - 抽象类可以有自己的数据成员，以及非abstract的成员方法，而接口中只能有static final的数据成员和abstract的成员方法
    - 抽象类在Java中表示的是一种继承关系，一个类只能继承于一个父类，但是一个类可以实现多个接口
    - 在抽象类中，方法可以有默认的行为，接口中不行，为了绕过这个限制，必须使用委托，但这会增加复杂性，会造成维护上的麻烦，比如，添加新的方法或者给已用的方法中添 加新的参数时，就会非常的麻烦，可能要花费很多的时间(对于派生类很多的情况，尤为如此)，但是如果界面是通过abstract class来实现的，那 么可能就只需要修改定义在abstract class中的默认行为就可以了
    - 在abstract class和interface间进行选择时要非常的小心
  - 设计理念层面
    - abstract class在Java语言中体现了一种继承关系，要想使得继承关系合理，父类和派生类之间必须存在"is-a"关系，即父类和派生类在概念本质上应该是相同的。对于interface来说则不然，并不要求interface的实现者和interface定义在概念本质上是一致的， 仅仅是实现了interface定义的契约而已


- 多态

  > 定义：指允许不同类的对象对同一消息做出响应。即同一消息可以根据发送对象的不同而采用多种不同的行为方式。（发送消息就是函数调用）
  >
  > 实现多态的技术：动态绑定，是指在执行期间判断所引用多想的实际类型，根据其实际的类型调用其相应的方法
  >
  > 作用：消除类型之间的耦合关系
  >
  > 多态存在的三个必要条件：1. 要有继承  2. 要有重写  3. 父类引用指向子类对象
  >
  > Java中多态的实现方式：接口实现、继承父类进行方法重写、同一个类中进行方法重载
  >
  > 通过例子理解多态：鸟类(Bird)能飞，所以Bird类有一个方法fly()，麻雀(Sparrow)和鹦鹉(Parrot)都是鸟类(继承于Bird)，它们都能飞，但飞的姿势不同(各自重写了fly()方法)，现有一只鸟(一个bird对象)，这只鸟可能是只麻雀(Bird bird = new Sparrow())，或者是一只鹦鹉(Bird bird = new Parrot())，我并不知道bird到底是Sparrow还是Parrot，我只想让它飞，于是我执行bird.fly()，这只鸟会以自己的姿势飞起来(执行属于Sparrow或Parrot的fly()方法)



### 2. Java异常机制

- finally执行时机

  题目: try{}里有一个return语句，那么紧跟在这个try后的finally {}里的code会不会被执行，什么时候被执行，在return前还是后?

  ```java
  public  class Test {
      public static void main(String[] args) {
         System.out.println("main x is :" + new Test().test());
      }
      static int test()
      {
         int x = 1;
         try
         {
             x++;
             System.out.println("try x is: " + x);
             return x;
         }
         finally
         {
             ++x;
             System.out.println("finally x is :" + x);
         }
      }
  }
  ```

  程序运行结果:

  > try x is: 2
  > finally x is :3
  > main x is :2

  解释: 主函数调用子函数并得到结果的过程，主函数在内存开辟了一块新的空间，当子函数要返回结果时，先把结果放在新的空间里，然后子函数说"我不运行了，你主函数继续运行吧"，finally执行的时机是在把结果放入这个新开辟的空间之后，子函数说"我不运行了"之前

- try和finally中都有return

  题目: 下面这段代码的运行结果是什么?

  ```java
  public class Test {
  	
  	public static void main(String[] args) {
      	System.out.println("main is run, and result is: " + new Test().test());
      }
      
      static int test()
      {
         try
         {
      	   System.out.println("try is run");
             return 1;
         }
         finally
         {
      	   System.out.println("finally is run");
             return 2;
         }
      }
  }
  ```

  运行结果: 

  > try is run
  > finally is run
  > main is run, and result is: 2

  解释: 和上一题一个道理，try中return时，把结果放入函数栈中，然后运行finally，而finally中也有return，于是也把结果放入了函数栈中，覆盖了try中放入的结果，然后子函数停止运行，主函数接着运行，输出的结果是finally放入函数栈中的结果

- error和exception有什么区别?

  - error 表示恢复不是不可能但很困难的情况下的一种严重问题。比如说内存溢出。不可能指望程序能处理这样的情况
  - exception 表示一种设计或实现问题。也就是说，它表示如果程序运行正常，从不会发生的情况

- 最常见到的5个runtime exception

  - ClassCastException(类转换异常)
  - IndexOutOfBoundsException(数组越界)
  - NullPointerException(空指针)
  - ArrayStoreException(数据存储异常，操作数组时类型不一致)
  - BufferOverflowException(缓冲区上溢异常)

- 常见的http异常代码:

  - 200 - 服务器成功返回网页
  - 404 - 请求的网页不存在
  - 500 （服务器内部错误） 服务器遇到错误，无法完成请求。
  - 503 - 服务不可用





### 3. List、Set、Map的区别及其相关子类

- list称为有序的Collection。它按对象进入的顺序保存对象，所以它能够对列表中的每个元素的插入和删除位置进行精确的控制。同时，它可以保存重复的对象。List接口里面有LinkedList、ArrayList和Vector

  - LinkedList和ArrayList的区别在于LinkedList是采用双向列表实现的，所以在指定位置进行插入或者删除的时候效率比较高，ArrayList更适合查找元素
  - ArrayList和Vector的区别在于Vector是线程安全的。但两者都是基于存储元素的object[] array来实现的。因为ArrayList不是线程安全的，所以效率会比Vector高
- set在数学上表示集合的概念，最主要的特点就是在集合中的元素不能有重复的存在，因此存入Set的每个元素都必须定义equals()方法偶来确保对象的唯一性。该接口有两个实现类：HashSet和TreeSet。其中TreeSet实现了SortedSet接口，因此TreeSet中的元素是有序的

  - hashSet是为快速查找而设计的Set，存入HashSet的元素必须定义为hashCode()。所以如果没有其他的限制，HashSet就是默认的选择，因为对速度进行了优化
  - TreeSet是保持了次序的set，底层实现为树结构，使用它可以从set中提取有序的序列，其元素必须实现comparable接口
  - 还有一种是LinkedHashSet，其是具有HashSet的查询速度，并且内部使用链表维护元素的顺序(插入的顺序)，于是在使用的时候(迭代器遍历set时)，结果会按照元素插入的次序显示。同时，元素也必须定义为hashcode()方法
- Map提供了一个从键映射到值的数据结构。它用于保存键值对，其中值可以重复，但是键是唯一的，不能重复。java中有多个市县该接口的类：HashMap、TreeMap、LinkedHashMap、WeakHashMap和IdentityHashMap。虽然它们都实现了相同的接口，但执行效率却不是相同的
  - HashMap: 如无限制，应该是默认的选择。因为对速度进行了优化。Map是基于散列表的实现，插入和查询“键值对”的开销是固定的。可以通过构造器设置容量和负载因子，以调整容器性能
  - LinkedHashMap: 类似于HashMap，但是迭代器遍历它时，取得“键值对”的顺序是其插入次序，或者是最近最小使用的次序。因此，只比HashMap慢一点，而在迭代访问时反而更快。因为使用是链表维护内部次序
  - TreeMap: 基于红黑树的实现，查看键或键值的时候，他们会被排序。TreeMap的特点在于，所得到的结果是经过排序的。TreeMap是唯一的带有subMap()方法的map，可以返回一个子树
  - WeakHashMap: 弱键映射，允许释放映射所指向的对象，这是为解决某类特殊问题而设计的。如果没有映射之外没有引用指向某个键，则此键被垃圾回收器回收
  - ConcurrentHashMap: 一种线程安全的Map，不涉及同步加锁
  - identityHashMap: 使用==代替equals()对键进行比较的散列映射
  - **注意: 任何键都必须具有一个equals（）方法，如果键被用于散列map，那么它必须还具有恰当的hashCode（）方法，如果用于TreeMap（），还必须实现comparable**



### 4. java流Inputstream、Outpustream、Reader、Writer的理解

- 字节流和字符流:
  - 字节流: 以字节为单位，每次读入或读出是8位数据。可以读任何类型数据。
    - InputStream
    - OutputStream
  - 字符流: 以字符为单位，每次读入或读出是16位数据。其只能读取字符类型数据。
    - Reader
    - Writer
- 输入流和输出流: 
  - 输出流: 从内存读出到文件，只能进行写操作。
    - OutputStream
    - Writer
  - 输入流: 从文件读入到内存，只能进行读操作。
    - InputStream
    - Reader
  - 注: 这里的出和入都是相对于系统内存而言的。
- 节点流和处理流:
  - 节点流: 直接与数据源相连，读入或读出。
  - 处理流: 与节点流一块使用，在节点流的基础上，再套接一层，套接在节点流上的就是处理流。
  - 处理流是“连接”在已存在的流(节点流或处理流)之上，通过对数据的处理为程序提供更为强大的读写功能
- 字符流和字节流转换:
  - 转换流的特点: 
    - 是字符流和字节流之间的桥梁
    - 可对读取到的字节数据经过指定编码转换成字符
    - 可对读取到的字符数据经过指定编码转换成字节
  - 何时使用转换流:
    - 当字节和字符之间有转换动作时
    - 流操作的数据需要编码或解码时
- 缓冲流
  - 缓冲流要”套接“在相应的节点流之上，对读写的数据提供了缓冲的功能，提高了读写的效率，同时增加了一些新的方法
  - jdk中提供了四种缓冲流
    - BufferedReader
    - BufferedWriter
    - BufferedInputStream
    - BufferedOutputStream
  - 对于输出的缓冲流，写出的数据会先在内存中缓存，使用flush方法将会使内存中的数据立刻写出



### 5. 关于JDBC，Connection、Statement、Resultset

1. 加载用于连接某数据库的专用驱动

   ```java
   Class.forName("oracle.jdbc.driver.OracleDriver");
   Class.forName("com.mysql.jdbc.Driver");
   ```

   或

   ```java
   Class.formName("oracle.jdbc.driver.OracleDriver").newInstance();
   Class.formName("com.mysql.jdbc.Driver").newInstance();
   ```

   或

   ```java
   new oracle.jdbc.driver.OracleDriver();
   new com.mysql.jdbc.Driver();
   ```

   实例化时自动向DriverManager注册，不需要显示调用DriverManager.registerDriver方法

2. 获取数据库连接

   ```java
   Connection conn = DriverManager.getConnection("jdbc:oracle:thin:@127.0.0.1:1521:orcl", "scott", "tiger");   //Oracle连接串固定写法: jdbc:oracle:thin:@IP:Oracle端口号:SID

   connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/testmybatis?characterEncoding=utf-8", "root", "root");	//MySQL连接串固定写法jdbc:mysql://IP:端口号/数据库名称?characterEncoding=数据库编码
   ```

3. 获取预处理Statement

   ```java
   PreparedStatement preparedStatement = connection.prepareStatement("select * from user where username=?");
   preparedStatement.setString(1, "张三");
   ```

   - Statement和PreparedStatement的区别

     - Statement为一条Sql语句生成执行计划，一千个Sql语句就生成一千个执行计划

       ```sql
       select colume from table where colume=1;
       select colume from table where colume=2;
       select colume from table where colume=4;
       select colume from table where colume=5;
       ```

     - PreparedStatement用于使用绑定变量重用执行计划

       ```sql
       select colume from table where colume=?;
       ```

     - 是否使用绑定变量对系统影响非常大，生成执行计划极为消耗资源，两种实现 速度差距可能成百上千倍

     - PreparedStatement是预编译的,对于批量处理可以大大提高效率.也叫JDBC存储过程

     - 使用 Statement 对象。在对数据库只执行一次性存取的时侯，用 Statement 对象进行处理。PreparedStatement对象的开销比Statement大，对于一次性操作并不会带来额外的好处。

4. 执行sql语句，返回查询结果集

   ```java
   ResultSet resultSet = preparedStatement.executeQuery();
   //遍历查询结果集
   while(resultSet.next()) {
     	System.out.println(resultSet.getString("id") + " " + resultSet.getString("username"));
   }
   ```



### 6. Java反射机制

- 什么是反射机制:
  - 简单说，反射机制值得是程序在运行时能够获取自身的信息。在java中，只要给定类的名字，那么就可以通过反射机制来获得类的所有信息


- Java反射机制的作用:
  - 在运行时判断任意一个对象所属的类
  - 在运行时判断任意一个类所具有的成员变量和方法
  - 在运行时任意调用一个对象的方法
  - 在运行时构造任意一个类的对象
- 哪里用到反射机制:
  - jdbc中加载数据库驱动Class.forName('com.[MySQL](http://lib.csdn.net/base/mysql).jdbc.Driver.class').newInstance();
  - 很多框架从配置文件中读取配置，然后使用反射机制来创建实例
- 反射机制的优缺点:
  - 优点:
    - 可以实现动态创建对象和编译，体现出很大的灵活性
  - 缺点:
    - 对性能有影响。使用反射基本上是一种解释操作，我们可以告诉JVM，我们希望做什么并且它满足我们的要求。这类操作总是慢于只直接执行相同的操作
    - 由于反射允许代码执行一些在正常情况下不被允许的操作(比如访问私有的属性和方法)，所以使用反射可能会导致意料之外的副作用--代码有功能上的错误，降低可移植性。反射代码破坏了抽象性，因此当平台发生改变的时候，代码的行为就有可能也随着变化