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
  > 实现多态的技术：动态绑定，是指在执行期间判断所引用多项的实际类型，根据其实际的类型调用其相应的方法
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




### 7. 一个".java"源文件中是否可以包括多个类(不是内部类)？有什么限制？

- 可以包含多个类，但是只允许有一个public类，并且该public类必须与文件名一致




### 8. Java有没有goto

- Java有goto，但是java的goto是**保留字**
- **保留字**的意思是可能以后会启用，变成关键字
- java的保留字:
  - cast、byValue、future、generic、inner、operator、outer、rest、var、goto、const




### 9. 说说&和&&的区别

- &&具有短路的功能，如果&&前面的表达式为false，则不去计算&&后面的表达式




### 10. 在JAVA中如何跳出当前的多重嵌套循环

- 在Java中，要想跳出多重循环，可以在外面的循环语句前定义一个标号，然后在里层循环体的代码中使用带有标号的break 语句，即可跳出外层循环

  ```java
  ok:
  	for(int i = 0; i < 10; i++) {
        	for(int j = 0; j < 10; j++) {
              System.out.println("i=" + i + ",j=" + j);
            	if(j==5)
                	break ok;
          }
  	}
  ```




### 11. switch语句能否作用在byte上，能否作用在long上，能否作用在String上

- switch可作用于char、byte、short、int以及char、byte、short、int对应的包装类
- switch不可作用于long、double、float、boolean，包括他们的包装类
- switch中可以是字符串类型,String(jdk1.7之后才可以作用在string上)
- switch中可以是枚举类型




### 12. short s1= 1; s1 = 1 + 1;有什么错? short s1 = 1; s1 += 1;有什么错?

- s1+1是int类型，而等号左边的是short类型，所以需要强转
- s1+=1;没有错，复合赋值表达式自动地将所执行计算的结果转型为其左侧变量的类型




### 13. char型变量中能不能存贮一个中文汉字?为什么

- java采用unicode，2个字节(16位)来表示一个字符， 无论是汉字还是数字字母，或其他语言。char 在java中是2个字节，所以可以存储中文




### 14. 用最有效率的方法算出2乘以8等於几

```java
int i = 2; 
int j = i << 3;
```



### 15. 使用final关键字修饰一个变量时，是引用不能变，还是引用的对象不能变

- 使用final关键字修饰一个变量时，是指引用变量不能变，引用变量所指向的对象中的内容还是可以改变的




### 16. 静态变量和实例变量的区别

- 在语法定义上的区别：静态变量前要加static关键字，而实例变量前则不加
- 在程序运行时的区别：实例变量属于某个对象的属性，必须创建了实例对象，其中的实例变量才会被分配空间，才能使用这个实例变量。静态变量不属于某个实例对象，而是属于类，所以也称为类变量，只要程序加载了类的字节码，不用创建任何实例对象，静态变量就会被分配空间，静态变量就可以被使用了。
- 实例变量必须创建对象后才可以通过这个对象来使用，静态变量则可以直接使用类名来引用




### 17. 是否可以从一个static方法内部发出对非static方法的调用

- 不可以。因为非static方法是要与对象关联在一起的，必须创建一个对象后，才可以在该对象上进行方法调用，而static方法调用时不需要创建对象，可以直接调用。也就是说，当一个static方法被调用时，可能还没有创建任何实例 对象，如果从一个static方法中发出对非static方法的调用，那个非static方法是关联到哪个对象上的呢？这个逻辑无法成立




### 18. Integer与int的区别

- int 是基本数据类型，Integer是其包装类
- int的初值为0，Ingeter的初值为null




### 19. Overload和Override的区别？Overloaded的方法是否可以改变返回值的类型?

- Overload: 重载，同一个类中，方法名相同，参数列表不同。与返回值类型无关。
- Override: 重写，存在于子父类，或者子父接口中，方法声明相同。
- Overload的方法可以改变返回值的类型，因为它与返回值类型无关。




### 20. 接口是否可继承接口?抽象类是否可实现(implements)接口?抽象类是否可继承具体类(concreteclass)?抽象类中是否可以有静态的main方法？

- 接口可以继承接口
- 抽象类可以继承、实现接口
- 抽象类是否可继承实体类，但前提是实体类必须有明确的构造函数
- 抽象类中可以有静态的main方法




### 21. abstract的method是否可同时是static,是否可同时是native，是否可同时是synchronized

- static是静态的，是一种属于类而不属于对象的方法或者属性
- synchronized 是同步，是一种相对线程的锁
- native 本地方法，这种方法和抽象方法及其类似，它也只有方法声明，没有方法实现，但是它与抽象方法不同的是，它把具体实现移交给了本地系统的函数库，而没有通过虚拟机，可以说是Java与其它语言通讯的一种机制
- abstract和这些关键字都不能同时使用
  - abstract与static: 声明static说明可以直接用类名调用该方法，声明abstract说明需要子类重写该方法，如果同时声明static和abstract，用类名调用一个抽象方法肯定不行
  - abstract与synchronized: 同步是需要有具体操作才能同步的，如果像abstract只有方法声明，那同步一些什么东西就会成为一个问题了，当然抽象方法在被子类继承以后，可以添加同步
  - abstract与native: native本身就和abstract冲突，他们都是方法的声明，只是一个吧方法实现移交给子类，另一个是移交给本地操作系统。如果同时出现，就相当于即把实现移交给子类，又把实现移交给本地操作系统，那到底谁来实现具体方法呢
- 不能放在一起的修饰符: final和abstract，private和abstract，static和abstract，因为abstract修饰的方法是必须在其子类中实现(覆盖)，才能以多态方式调用，以上修饰符在修饰方法时期子类都覆盖不了这个方法，final是不可以覆盖，private是不能够继承到子类，所以也就不能覆盖，static是可以覆盖的，但是在调用时会调用编译时类型的方法，因为调用的是父类的方法，而父类的方法又是抽象的方法，又不能够调用，所以上的修饰符不能放在一起。




### 22. 内部类可以引用它的包含类的成员吗？有没有什么限制？

- 完全可以。如果不是静态内部类，那没有什么限制
- 在静态内部类下，不可以访问外部类的普通成员变量，而只能访问外部类中的静态成员




### 23. String s="hello";s=s+"world",这两行代码执行后,原始的String对象中的内容到底变了没有

- 没有改变，因为String被设计为不可变类，所以它的所有对象都是不可变的对象。
- 这段代码中，s原先只想一个String对象，内容是hello，然后对s进行+操作，这时s不再指向原来那个对象了，而指向另一个String对象




### 24. ArrayList和Vector的区别

- 相同点: 
  - ArrayList和Vector都继承自相同的父类(AbstractList)，实现了相同的接口(List)
  - 底层都是数组实现
  - 初始默认长度都为10
- 不同点: 
  - Vector的方法都是同步的(Synchronized)，是线程安全的，而ArrayList的方法不是，由于线程的同步必然要影响性能，因此,ArrayList的性能比Vector好。
  - 当Vector或ArrayList中的元素超过它的初始大小时,Vector会将它的容量翻倍,而ArrayList只增加50%的大小，这样,ArrayList就有利于节约内存空间。




### 25. HashMap和Hashtable的区别 

- Hashtable继承自Dictionary而HashMap继承自AbstractMap
- HashMap是Hashtable的轻量级实现(非线程安全的实现)，他们都实现了Map接口
- HashMap允许空(null)键值(key),由于非线程安全，效率上可能高于Hashtable
- HashMap允许将null作为一个entry的key或者value，而Hashtable不允许
- HashMap把Hashtable的contains方法去掉了，改成containsvalue和containsKey。因为contains方法容易让人引起误解
- Hashtable的方法是Synchronize的，而HashMap不是，在多个线程访问Hashtable时，不需要自己为它的方法实现同步，而HashMap就必须为之提供外同步




### 26. 说出ArrayList,Vector,LinkedList的存储性能和特性

- ArrayList 采用的是数组形式来保存对象的，这种方式将对象放在连续的位置中，所以最大的缺点就是插入删除时非常麻烦
- LinkedList 采用的将对象存放在独立的空间中，而且在每个空间中还保存下一个链接的索引，但是缺点就是查找非常麻烦，要丛第一个索引开始
- ArrayList和Vector都是用数组方式存储数据,此数组元素数要大于实际的存储空间以便进行元素增加和插入操作，他们都允许直接用序号索引元素，但是插入数据元素涉及到元素移动等内存操作，所以索引数据快而插入数据慢
- Vector使用了sychronized方法(线程安全),所以在性能上比ArrayList要差些
- LinkedList使用双向链表方式存储数据,按序号索引数据需要前向或后向遍历数据，所以索引数据慢,是插入数据时只需要记录前后项即可,所以插入的速度快




### 27. 去掉一个Vector集合中重复的元素

```java
Vector newVector = new Vector();
For (int i=0;i<vector.size();i++) {
Object obj = vector.get(i);
    if(!newVector.contains(obj);
    newVector.add(obj);
}
```

或

```java
HashSet set = new HashSet(vector);
```



### 28. Collection和Collections的区别

- java.util.Collection 是一个**集合接口**。它提供了对集合对象进行基本操作的通用接口方法。
- java.util.Collections 是一个包装类。它包含有各种有关集合操作的**静态多态方法，用于实现对各种集合的搜索、排序、线程安全化等操作。**此类**不能实例化**，就像一**个工具类**，服务于Java的Collection框架




### 29. Set里的元素是不能重复的，那么用什么方法来区分重复与否呢?是用==还是equals()?它们有何区别?

- 使用equals()来区分是否重复，==是用来判断两者是否是同一对象（同一事物），而equals是用来判断是否引用同一个对象，set里面存放的是对象的引用，所以当两个元素只要满足了equals()时就已经指向同一个对象，set里面存放的是对象的引用，所以当两个元素只要满足了equals()时就已经指向同一个对象




### 30. String s = new String("xyz");创建了几个StringObject？是否可以继承String类?

- 一个或两个
  - 一个的话就一个句柄
  - 两个的话，一个句柄，一个常量对象




### 31. 下面这条语句一共创建了多少个对象：String s="a"+"b"+"c"+"d";

- 两个，s一个，"abcd"一个
- javac编译可以对字符串常量直接相加的表达式进行优化，不必要等到运行期去进行加法运算处理，而是在编译时去掉其中的加号，直接将其编译成一个这些常量相连的结果。




### 32. Java 中堆和栈有什么区别

- 在函数中定义的一些基本类型的变量和对象的引用变量都在函数的**栈**内存中分配
  - 当在一段代码块定义一个变量时，Java就在栈中为这个变量分配内存空间，当超过变量的作用域后，Java会自动释放掉为该变量所分配的内存空间，该内存空间可以立即被另作他用
- **堆**内存用来存放由new创建的对象和数组
  - 在堆中分配的内存，由Java虚拟机的自动垃圾回收器来管理
  - 在堆中产生了一个数组或对象后，还可以在栈中定义一个特殊的变量，让栈中这个变量的取值等于数组或对象在堆内存中的首地址，栈中的这个变量就成了数组或对象的引用变量
  - 引用变量就相当于是为数组或对象起的一个名称，以后就可以在程序中使用栈中的引用变量来访问堆中的数组或对象
- java中变量在内存中的分配
  - 类变量(static修饰的变量): 在程序加载时系统就为它在堆中开辟了内存，堆中的内存地址存放于栈以便于高速访问。静态变量的生命周期--一直持续到整个"系统"关闭
  - 实例变量: 当你使用java关键字new的时候，系统在堆中开辟并不一定是连续的空间分配给变量(比如说类实例)，然后根据零散的堆内存地址，通过哈希算法换算为一长串数字以表征这个变量在堆中的"物理位置"。 实例变量的生命周期--当实例变量的引用丢失后，将被GC(垃圾回收器)列入可回收"名单"中，但并不是马上就释放堆中内存
  - 局部变量: 局部变量，由声明在某方法，或某代码段里(比如for循环)，执行到它的时候在栈中开辟内存，当局部变量一但脱离作用域，内存立即释放
- java的内存机制
  - Java 把内存划分成两种：一种是栈内存，另一种是堆内存。
  - 在函数中定义的一些基本类型的变量和对象的引用变量都是在函数的栈内存中分配，当在一段代码块定义一个变量时，Java 就在栈中为这个变量分配内存空间，当超过变量的作用域后，Java 会自动释放掉为该变量分配的内存空间，该内存空间可以立即被另作它用。
  - 堆内存用来存放由 new 创建的对象和数组，在堆中分配的内存，由 Java 虚拟机的自动垃圾回收器来管理。在堆中产生了一个数组或者对象之后，还可以在栈中定义一个特殊的变量，让栈中的这个变量的取值等于数组或对象在堆内存中的首地址，栈中的这个变量就成了数组或对象的引用变量，以后就可以在程序中使用栈中的引用变量来访问堆中的数组或者对象，引用变量就相当于是为数组或者对象起的一个名称。引用变量是普通的变量，定义时在栈中分配，引用变量在程序运行到其作用域之外后被释放。而数组和对象本身在堆中分配，即使程序运行到使用 new 产生数组或者对象的语句所在的代码块之外，数组和对象本身占据的内存不会被释放，数组和对象在没有引用变量指向它的时候，才变为垃圾，不能在被使用，但仍然占据内存空间不放，在随后的一个不确定的时间被垃圾回收器收走(释放掉)




### 33. 能将 int 强制转换为 byte 类型的变量吗？如果该值大于 byte 类型的范围，将会出现什么现象？

- 可以转换，如果值大于byte类型的范围，会溢出



### 34. a.hashCode() 有什么用？与 a.equals(b) 有什么关系？

- hashCode() 方法是相应对象整型的 hash 值。它常用于基于 hash 的集合类，如 Hashtable、HashMap、LinkedHashMap等等。它与 equals() 方法关系特别紧密。根据 Java 规范，两个使用 equal() 方法来判断相等的对象，必须具有相同的 hash code。



### 35. 什么是java序列化，如何实现java序列化？或者请解释Serializable接口的作用。

- 我们有时候将一个java对象变成字节流的形式传出去或者从一个字节流中恢复成一个java对象，例如，要将java对象存储到硬盘或者传送给网络上的其他计算机，这个过程我们可以自己写代码去把一个java对象变成某个格式的字节流再传输，但是，jre本身就提供了这种支持，我们可以调用OutputStream的writeObject方法来做，如果要让java 帮我们做，要被传输的对象必须实现serializable接口，这样，javac编译时就会进行特殊处理，编译的类才可以被writeObject方法操作，这就是所谓的序列化。
- 例如，在web开发中，如果对象被保存在了Session中，tomcat在重启时要把Session对象序列化到硬盘，这个对象就必须实现Serializable接口。如果对象要经过分布式系统进行网络传输或通过rmi等远程调用，这就需要在网络上传输对象，被传输的对象就必须实现Serializable接口



### 36. 描述一下JVM加载class文件的原理机制?

- 由于Java的跨平台性，经过编译的Java源程序并不是一个可执行程序，而是一个或多个类文件。当Java程序需要使用某个类时，JVM会确保这个类已经被加载、连接（验证、准备和解析）和初始化。类的加载是指把类的.class文件中的数据读入到内存中，通常是创建一个字节数组读入.class文件，然后产生与所加载类对应的Class对象。加载完成后，Class对象还不完整，所以此时的类还不可用。当类被加载后就进入连接阶段，这一阶段包括验证、准备（为静态变量分配内存并设置默认的初始值）和解析（将符号引用替换为直接引用）三个步骤。最后JVM对类进行初始化，包括：1)如果类存在直接的父类并且这个类还没有被初始化，那么就先初始化父类；2)如果类中存在初始化语句，就依次执行这些初始化语句。 
- 类的加载是由类加载器完成的，类加载器包括：根加载器(BootStrap)、扩展加载器(Extension)、系统加载器(System)和用户自定义类加载器(java.lang.ClassLoader的子类)。



### 37. GC是什么?为什么要有GC?

- GC是垃圾收集的意思，忘记或者错误的内存回收会导致程序或系统的不稳定甚至崩溃，Java提供的GC功能可以自动监测对象是否超过作用域从而达到自动回收内存的目的
- Java语言没有提供释放已分配内存的显示操作方法。Java程序员不用担心内存管理，因为垃圾收集器会自动进行管理。要请求垃圾收集，可以调用下面的方法之一：System.gc()或Runtime.getRuntime().gc()，但JVM可以屏蔽掉显示的垃圾回收调用



### 38. java中会存在内存泄漏吗，请简单描述。

- Java中存在内存泄漏，长生命周期的对象持有短生命周期对象的引用就很可能发生内存泄露，尽管短生命周期对象已经不再需要，但是因为长生命周期对象持有它的引用而导致不能被回收，这就是java中内存泄露的发生场景
  - 集合类: 如果这个集合类是全局性的变量（比如类中的静态属性，全局性的map等即有静态引用或final一直指向它），那么没有相应的删除机制，很可能导致集合所占用的内存只增不减，因此提供这样的删除机制或者定期清除策略非常必要。
  - 单例模式: 单例对象在被初始化后将在JVM的整个生命周期中存在（以静态变量的方式），如果单例对象持有外部对象的引用，那么这个外部对象将不能被jvm正常回收，导致内存泄露
- 在代码复审的时候关注长生命周期对象: 全局性的集合、单例模式的使用、类的static变量等等。在Java的实现过程中，也要考虑其对象释放，最好的方法是在不使用某对象时，显式地将此对象赋空。最好遵循谁创建谁释放的原则。

