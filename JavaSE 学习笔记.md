# JavaSE 学习笔记

Tags: Java JavaSE 学习

[TOC]

## 1. 面向对象

### 1.01 面向对象概念

如何在内存中区分类和对象：

* 类是静态的概念，位于代码区。
* 对象是new出来的，位于堆内存，类的每个成员变量在不同的对象中都有不同的值（除了静态变量），而方法只有一份，执行的时候才占内存。

```java
public class Dog {
	static int furcolor;	//成员变量自动初始化
	float height;
	float weight;
	
	void catchMouse(Mouse m) {
	}
	
	public static void main(String[] args) {
		// int i;	//局部变量不会自动初始化
		// System.out.println(i);//报错：可能尚未初始化变量i
		System.out.println(furcolor);	//输出0
	}
}

class Mouse {
	
}
```



### 1.02 命名规则

约定俗成的命名规则

- 类名的首字母大写，例如：CatchMouse
- 变量名和方法名的首字母小写，例如：catchMouse
- 运用驼峰标识



### 1.03 内存解析

```java
class BirthDate {
    private int day;
    private int month;
    private int year;

    public BirthDate(int d, int m, int y) {
        day = d;
        month = m;
        year = y;
    }

    public void setDay(int d) {
        day = d;
    }

    public void setMonth(int m) {
        month = m;
    }

    public void setYear(int y) {
        year = y;
    }

    public int getDay() {
        return day;
    }

    public int getMonth() {
        return month;
    }

    public int getYear() {
        return year;
    }

    public void display() {
        System.out.println(day + " - " + month + " - " +year);
    }
}

public class Test {
    public static void main(String[] args) {
        Test test = new Test();
        int date = 9;
        BirthDate d1 = new BirthDate(7, 7, 1970);
        BirthDate d2 = new BirthDate(1, 1, 2000);
        test.change1(date);
        test.change2(d1);
        test.change3(d2);
        System.out.println("date=" + date);
        d1.display();
        d2.display();
    }

    public void change1(int i) {    //date把值拷贝给了i，i的值为9
        i = 1234;   //i的值为1234，date值不会改变仍为9
    }

    public void change2(BirthDate b){   //b指向d1指向的对象
        b = new BirthDate(22, 2, 2004); //b指向了一个新对象，运行结束后b所占内存被释放，其指向的对象被垃圾回收器回收
    }

    public void change3(BirthDate b) {
        b.setDay(22);   //b指向的对象调用了自己的setDay()方法，改变了day的值，运行结束后b所占内存被释放
    }
}
```



### 1.04 例子学习：Point

```java
class Point {
	private double x;
	private double y;
	private double z;
	
	public Point() {}
	
	public Point(double _x, double _y, double _z) {
		x = _x;
		y = _y;
		z = _z;
	}
	
	public void setX(double x) {
		this.x = x;
	}
	
	public void setY(double y) {
		this.y = y;
	}
	
	public void setZ(double z) {
		this.z = z;
	}
	
	public double getX() {
		return this.x;
	}
	
	public double getY() {
		return this.y;
	}
	
	public double getZ() {
		return this.z;
	}
	
	//计算当前点距离原点距离的平方
	public double getDistance() {
		return this.x * this.x + this.y * this.y + this.z * this.z;
	}
}

public class Test {
	public static void main(String[] args) {
		Point p = new Point(2, 2, 2);
		System.out.println(p.getX());
		p.setX(3);
		System.out.println(p.getX());
		System.out.println(p.getDistance());
	}
}
```



### 1.05 方法的重载

1. 方法的重载是指一个类中可以定义有相同的名字，但参数不同的多个方法。调用时，会根据不同的参数表选择对应的方法。
2. 构造方法也可以重载。

```java
public class TestOverLoad{
	void max(int a, int b) {
		System.out.println(a > b ? a : b);
	}
	
	void max(float a, float b) {	//方法重载
		System.out.println(a > b ? a : b);
	}
	
	void max(int a, int b, int c) {	//方法重载
		System.out.println((a > b ? a : b) > c ? (a > b ? a : b) : c);
	}
	
	// int max(int a, int b) {	//方法重名，这是不允许的
		// return a > b ? a : b;
	// }
	
	public static void main(String[] args) {
		TestOverLoad t = new TestOverLoad();
		t.max(3, 4);
		t.max(1.2F, 2.3F);
		t.max(1, 2, 3);
	}
}
```



### 1.06 对象的创建与使用

```java
class Point {
	private double x;
	private double y;
	
	Point(double x1, double y1) {
		x = x1;
		y = y1;
	}
	
	public double getX() { return x; }
	public double getY() { return y; }
	public void setX(double i) { x = i;}
	public void setY(double i) { y = i;}
}

class Circle {
	private Point o;
	private double radius;
	
	Circle(Point p, double r) {
		o = p;
		radius = r;
	}
	Circle(double r) {
		o = new Point(0.0, 0.0);
		radius = r;
	}
	
	boolean contains(Point p) {
		double x = p.getX() - o.getX();
		double y = p.getY() - o.getY();
		if(x*x + y*y > radius * radius)
			return false;
		else
			return true;
	}
	
	public void setO(double x, double y) {
		o.setX(x);
		o.setY(y);
	}
	public Point getO() { return o; }
	public double getRadius() { return radius; }
	public void setRadius(double r) { radius = r;}
	public double area() {
		return 3.14 * radius * radius;
	}
}

public class Test {
	public static void main(String[] args) {
		Circle c1 = new Circle(new Point(1.0, 2.0), 2.0);
		Circle c2 = new Circle(5.0);
		System.out.println("c1:(" + c1.getO().getX() + "," + c1.getO().getY() + ")," + c1.getRadius());
		System.out.println("c2:(" + c2.getO().getX() + "," + c2.getO().getY() + ")," + c2.getRadius());
		System.out.println("c1 area = " + c1.area());
		System.out.println("c2 area = " + c2.area());
		c1.setO(5, 6);
		c2.setRadius(9.0);
		System.out.println("c1:(" + c1.getO().getX() + "," + c1.getO().getY() + ")," + c1.getRadius());
		System.out.println("c2:(" + c2.getO().getX() + "," + c2.getO().getY() + ")," + c2.getRadius());
		System.out.println("c1 area = " + c1.area());
		System.out.println("c2 area = " + c2.area());
		
		Point p1 = new Point(5.2, 6.3);
		System.out.println(c1.contains(p1));
		System.out.println(c1.contains(new Point(10.0, 9.0)));
	}
}
```



### 1.07 例子学习：TestCircle

```java
class Point {
	private double x;
	private double y;
	
	Point(double x1, double y1) {
		x = x1;
		y = y1;
	}
	
	public double getX() { return x; }
	public double getY() { return y; }
	public void setX(double i) { x = i;}
	public void setY(double i) { y = i;}
}

class Circle {
	private Point o;
	private double radius;
	
	Circle(Point p, double r) {
		o = p;
		radius = r;
	}
	Circle(double r) {
		o = new Point(0.0, 0.0);
		radius = r;
	}
	
	boolean contains(Point p) {
		double x = p.getX() - o.getX();
		double y = p.getY() - o.getY();
		if(x*x + y*y > radius * radius)
			return false;
		else
			return true;
	}
	
	public void setO(double x, double y) {
		o.setX(x);
		o.setY(y);
	}
	public Point getO() { return o; }
	public double getRadius() { return radius; }
	public void setRadius(double r) { radius = r;}
	public double area() {
		return 3.14 * radius * radius;
	}
}

public class Test {
	public static void main(String[] args) {
		Circle c1 = new Circle(new Point(1.0, 2.0), 2.0);
		Circle c2 = new Circle(5.0);
		System.out.println("c1:(" + c1.getO().getX() + "," + c1.getO().getY() + ")," + c1.getRadius());
		System.out.println("c2:(" + c2.getO().getX() + "," + c2.getO().getY() + ")," + c2.getRadius());
		System.out.println("c1 area = " + c1.area());
		System.out.println("c2 area = " + c2.area());
		c1.setO(5, 6);
		c2.setRadius(9.0);
		System.out.println("c1:(" + c1.getO().getX() + "," + c1.getO().getY() + ")," + c1.getRadius());
		System.out.println("c2:(" + c2.getO().getX() + "," + c2.getO().getY() + ")," + c2.getRadius());
		System.out.println("c1 area = " + c1.area());
		System.out.println("c2 area = " + c2.area());
		
		Point p1 = new Point(5.2, 6.3);
		System.out.println(c1.contains(p1));
		System.out.println(c1.contains(new Point(10.0, 9.0)));
	}
}
```



### 1.08 this关键字

1. 在类的方法定义中使用的this关键字代表使用该方法的对象的引用.
2. 当必须指出当前使用方法的对象是谁时要使用this.
3. 有时使用this可以处理方法中成员变量和参数重名的情况.
4. this可以看作是一个变量, 它的值是当前对象的引用.



### 1.09 static关键字

1. 在类中, 用static声明的成员变量为静态成员变量, 它为该类的公用变量, 在第一次使用时被初始化, 对于该类的所有对象来说, static成员变量只有一份.
2. 用static声明的方法为静态方法, 在调用该方法时, 不会将对象的引用传递给它, 所以在static方法中不可访问非static的成员. (静态方法不再是针对某个对象调用, 所以不能访问非静态成员)
3. 可以通过对象引用或类名(不需要实例化)访问静态成员.

```java
public class Cat {
    private static int sid = 0;
    private String name;
    int id;

    Cat(String name) {
        this.name = name;
        id = sid++;
    }

    public void info() {
        System.out.println("My name is " + name + "No." + id);
    }

    public static void main(String[] args) {
        Cat.sid = 100;
        Cat mimi = new Cat("mimi");
        Cat pipi = new Cat("pipi");
        mimi.info();
        pipi.info();
    }
}
```



### 1.10 package和import语句

#### package语句和import语句

- package语句作为Java源文件的第一条语句，指明该文件中定义的类所在的包。（若缺省该语句，则指定为无名包）
- 如果想将一个类放入包中，在这个类源文件第一句话写package。
- 用了package，那么这个类文件必须位于package后所写的目录中。
- 另外的类想访问的话，须使用该类的全名（例如：com.neuedu.changhang142041.Cat），也可用import引入该类（例如：import com.neuedu.changhang142041.*; 或import com.neuedu.changhang142041.Cat;）
- 访问同一个包中的类不需要引入也不需要写全名。
- class文件的最上层包的父目录必须位于classpath下。
- 执行一个类也需要写全包名。
- 可用jar命令将文件打包成jar包。（例如：jar cvf Cat.jar com 把com文件夹打包成Cat.jar包）
- 把jar包加入classpath中，jar包中所有的类都可以使用。

```java
package com.neuedu.changhang142041;	//命名规则：公司域名倒过来写，后接项目名等

public class Cat {	
}
```



#### jdk中主要的包

- java.lang 包含一些java语言的核心类，如String、Math、Integer、System和Thread，提供常用功能。（java.lang不需要引入，其他都需要）
- java.awt 包含了构成抽象窗口工具集的多个类，这些类被用来构建个管理应用程序的图形用户界面（GUI）。
- java.applet 包含applet运行所需的一些类。
- java.net 包含执行与网络相关的操作的类。
- java.io 包含能提供多种输入/输出功能的类。
- java.util 包含一些实用工具集，如定义系统特性、使用与日期日历相关的函数。



### 1.11 继承和权限控制 

1. 通过继承，子类自动拥有了基类的所有成员（成员变量和方法）。
2. Java只支持单继承，不允许多重继承（C++允许多重继承），一个子类只能有一个基类，一个基类可以派生出多个子类。

访问权限：

|    修饰符    | 类内部  | 同一个包 |  子类  | 任何地方 |
| :-------: | :--: | :--: | :--: | :--: |
|  private  | yes  |      |      |      |
|  default  | yes  | yes  |      |      |
| protected | yes  | yes  | yes  |      |
|  public   | yes  | yes  | yes  | yes  |

- 对于class的权限修饰只可以用public和default



TestAccess：

```java
public class TestAccess {
	
}

class T {
	private int i = 0;
	protected int k = 0;
	public int m = 0;
	int j = 0;	//缺省修饰符为default
}

class TT {
	public void m() {
		T t = new T();
		// System.out.println(t.i);	//报错，无法访问其他类的私有成员
		System.out.println(t.j);	//同一个包内可以访问
	}
}
```



TestExtends：

```java
public class TestExtends {
	
}

class Parent {
	private int n_private = 1;
	int n_defauly = 2;
	protected int n_protected = 3;
	public int n_public = 4;
}

class Chile extends Parent {
	public void f() {
		// n_private = 10;	//错误，继承时子类拥有基类的私有成员，但是不能使用它。
		n_defauly = 20;
		n_protected = 30;
		n_public = 40;
		// System.out.println(n_private);	//错误
		System.out.println(n_defauly);
		System.out.println(n_protected);
		System.out.println(n_public);
	}
}
```



TestPerson：

```java
class Person {
	private String name;
	private int age;
	
	public void setName(String name) {
		this.name = name;
	}
	public void setAge(int age) {
		this.age = age;
	}
	public String getName() {
		return name;
	}
	public int getAge() {
		return age;
	}
}

class Student extends Person {
	private String school;
	
	public String getSchool() {
		return school;
	}
	public void setSchool(String school) {
		this.school = school;
	}
}

public class TestPerson {
	public static void main(String[] arg) {
		Student student = new Student();
		student.setName("John");
		student.setAge(18);
		student.setSchool("NEU");
		System.out.println(student.getName());
		System.out.println(student.getAge());
		System.out.println(student.getSchool());
	}
}
```



### 1.12 方法的重写

1. 在子类中根据需要对从基类继承来的方法进行重写。
2. 重写方法必须和被重写方法具有相同的方法名称、参数列表和返回类型。
3. 重写方法不能使用比被重写方法更严格的访问权限。

```java
class Person {
	private String name;
	private int age;
	
	public void setName(String name) {
		this.name = name;
	}
	public void setAge(int age) {
		this.age = age;
	}
	public String getName() {
		return name;
	}
	public int getAge() {
		return age;
	}

	public String getInfo() {
		return "Name: " + name + "\n" + "Age: " + age;
	}
}

class Student extends Person {
	private String school;
	
	public String getSchool() {
		return school;
	}
	public void setSchool(String school) {
		this.school = school;
	}

	public String getInfo() {
		return "Name: " + getName() + "\nAge: " + getAge() + "\nSchool: " + getSchool();
	}
	// protected String getInfo() {	//错误，不能使用比被重写方法更严格的访问权限
	// 	return "Name: " + getName() + "\nAge: " + getAge() + "\nSchool: " + getSchool();
	// }
}

public class TestOverWrite {
	public static void main(String[] arg) {
		Student student = new Student();
		Person person = new Person();
		person.setName("none");
		person.setAge(1000);
		student.setName("John");
		student.setAge(18);
		student.setSchool("NEU");
		System.out.println(person.getInfo());
		System.out.println(student.getInfo());
	}
}
```



### 1.13 super关键字

```java
class Person {
	private String name;
	private int age;
	
	public void setName(String name) {
		this.name = name;
	}
	public void setAge(int age) {
		this.age = age;
	}
	public String getName() {
		return name;
	}
	public int getAge() {
		return age;
	}

	public String getInfo() {
		return "Name: " + name + "\n" + "Age: " + age;
	}
}

class Student extends Person {
	private String school;
	
	public String getSchool() {
		return school;
	}
	public void setSchool(String school) {
		this.school = school;
	}

	public String getInfo() {
		return "Name: " + getName() + "\nAge: " + getAge() + "\nSchool: " + getSchool();
	}
	// protected String getInfo() {	//错误，不能使用比被重写方法更严格的访问权限
	// 	return "Name: " + getName() + "\nAge: " + getAge() + "\nSchool: " + getSchool();
	// }
}

public class TestOverWrite {
	public static void main(String[] arg) {
		Student student = new Student();
		Person person = new Person();
		person.setName("none");
		person.setAge(1000);
		student.setName("John");
		student.setAge(18);
		student.setSchool("NEU");
		System.out.println(person.getInfo());
		System.out.println(student.getInfo());
	}
}
```



### 1.14 构造方法

1. 构造方法就是把自己构建成一个新对象
2. 构造方法与类同名且没有返回值
3. 当没有指定构造方法的时候，编译器为类自动添加形如  类名() { }  的构造函数

```java
public class Person {
    int id;
    int age = 20;
    Person(int _id, int _age) {
        id = _id;
        age = _age;
    }

    public static void main(String[] args) {
        //调用构造方法
        Person tom = new Person(1, 25);
        Person john = new Person(2, 27);
        // Person jerry = new Person();    //报错：找不到符号
        Point p = new Point();  //调用Point类的构造方法
    }
}

class Point {
    int x;
    int y;
}

// public class Person {
//     int id;
//     int age = 20;

//     //该方法不是构造方法，只是一个普通方法
//     void Person(int _id, int _age) {
//         id = _id;
//         age = _age;
//     }

//     public static void main(String[] args) {
//         // Person tom = new Person(1, 25); //报错：找不到符号
//         Person jerry = new Person();    //不报错，因为编译器自动生成了构造方法 Person(){}
//     }
// }

```



### 1.15 继承中的构造方法

1. 子类的构造过程中必须调用其基类的构造方法。
2. 子类可以在自己的构造方法中使用super调用基类的构造方法，如果调用super，必须写在子类构造方法的第一行。（使用this调用本类的另外的构造方法）
3. 如果子类的构造方法中没有显式地调用基类构造方法，则系统默认调用基类无参数的构造方法。
4. 如果子类构造方法中既没有显式调用基类构造方法，而基类中又没有无参的构造方法，则编译出错。

```java
class SuperClass {
	private int n;

	SuperClass() {
		System.out.println("SuperClass()");
	}

	SuperClass(int n) {
		System.out.println("SuperClass(" + n + ")");
		this.n = n;
	}
}

class SubClass extends SuperClass {
	private int n;

	SubClass(int n) {
		System.out.println("SubClass(" + n + ")");
		this.n = n;
	}

	SubClass() {
		super(300);
		System.out.println("SubClass()");
	}
}

public class TestSuperSub {
	public static void main(String[] args) {
		SubClass sc1 = new SubClass();
		SubClass sc2 = new SubClass(400);
	}
}
```



另一个例子：

```java
class Person {
    private String name;
    private String location;

    Person(String name) {
        this.name = name;
        location = "beijing";
    }
    Person(String name, String location) {
        this.name = name;
        this.location = location;
    }
    public String info() {
        return "name: " + name + " location: " + location;
    }
}

class Student extends Person {
    private String school;
    Student(String name, String school) {
        this(name, "beijing", school);
    }
    Student(String n, String c, String shcool){
        super(n, c);
        this.school = school;
    }
    public String info() {
        return super.info() + " school: " + school;
    }
}

public class Test {
    public static void main(String[] args) {
        Person p1 = new Person("A");
        Person p2 = new Person("B", "shanghai");
        Student s1 = new Student("C", "S1");
        Student s2 = new Student("C", "shanghai", "S2");
        System.out.println(p1.info());
        System.out.println(p2.info());
        System.out.println(s1.info());
        System.out.println(s2.info());
    }
}
```



### 1.16 Object类

Object类是所有Java类的根基类

如果在类的声明中未使用extends关键字指明其基类，则默认基类为Object类
例如：
```java
public class Person {
	...
}
```
等价于：
```java
public class Person extends Object {
	...
}
```


#### Object类一些常用方法：

##### equals方法

1. Object类中定义有public boolean equals(Object obj)方法，提供定义对象是否“相等”的逻辑。
2. Object的equals方法定义为：x.equals(y) 当x和y是同一个对象的引用时返回true否则返回false。
3. java sdk提供的一些类，如String，Date等，重写了Object的equals方法，调用这些类的equals方法，x.equals(y)，当x和y所引用的对象时同一类对象且属性内容相等时（并不一定是相同对象），返回true否则返回false。
4. 可以根据需要在用户自定义类型中重写equals方法。

```java
public class TestEquals {
	public static void main(String[] args) {
		Cat c1 = new Cat(1, 2);
		Cat c2 = new Cat(1, 2);

		System.out.println(c1 == c2);	//equals默认的实现就是c1 == c2
		System.out.println(c1.equals(c2));

		String s1 = new String("hello");
		String s2 = new String("hello");
		System.out.println(s1 == s2);
		System.out.println(s1.equals(s2));	//String类重写了equals方法，如果s2非空且s2和s1的字符序列相同，则返回true
	}
}

class Cat {
	int color;
	int height;

	public Cat(int color, int height) {
		this.color = color;
		this.height = height;
	}

	public boolean equals(Object obj) {
		if(obj != null){
			if(obj instanceof Cat) {
				Cat c = (Cat) obj;
				if(this.color == c.color && this.height == c.height)
					return true;
			}
		}
		return false;
	}
}
```



##### toString方法

1. Object类中定义有public String toString()方法，其返回值是String类型，描述当前对象的有关信息。
2. 在进行String与其它类型数据的连接操作时（如：System.out.println("info" + person)），将自动调用该对象类的toString()方法。
3. 可以根据需要在用户自定义类型中重写toString()方法。

```java
public class TestToString {
	public static void main(String[] args) {
		Dog d = new Dog();
		System.out.println("d:=" + d);	//相当于System.out.println("d:=" + d.toString());
	}
}

class Dog {
	public String toString() {	//若不对toString()进行重写，main方法中输出的是类名@哈希码 例如（Dog@1e78fc6）
		return "I'm a cool dog!";
	}
}
```



#### hashcode解释

- hashcode（哈希码）独一无二地代表了一个对象，并且通过该hashcode可以找到该对象所在位置



### 1.17 对象转型(casting)

1. 一个基类的引用类型变量可以“指向”其子类的对象。
2. 一个基类的引用不可以访问其子类对象新增加的成员（属性和方法）。
3. 可以使用引用变量 instanceof 类名 来判断该引用型变量所“指向”的对象是否属于该类或该类的子类。
4. 子类的对象可以当作基类的对象来使用称作向上转型（upcasting），反之称为向下转型（downcasting）。

```java
class Animal {
	public String name;

	Animal(String name) {
		this.name = name;
	}
}

class Dog extends Animal{
	public String furcolor;

	Dog(String name, String furcolor) {
		super(name);
		this.furcolor = furcolor;
	}
}

class Cat extends Animal{
	public String eyescolor;
	Cat(String name, String eyescolor) {
		super(name);
		this.eyescolor = eyescolor;
	}
}

// public class TestCasting {
// 	public static void main(String[] args) {
// 		Animal a = new Animal("name");
// 		Dog d = new Dog("dogname", "black");

// 		System.out.println(a instanceof Animal);	//true
// 		System.out.println(d instanceof Animal);	//true
// 		System.out.println(a instanceof Dog);		//false

// 		a = new Dog("bigyellow", "yellow");
// 		System.out.println(a.name);	//bigyellow
// 		// System.out.println(a.furcolor);	//error!
// 		System.out.println(a instanceof Animal);	//true
// 		System.out.println(a instanceof Dog);	//true

// 		Dog d1 = (Dog)a;	//强制转换
// 		System.out.println(d1.furcolor);	//yellow
// 	}
// }

public class TestCasting {
	public static void main(String[] args) {
		TestCasting test = new TestCasting();
		Animal a = new Animal("name");
		Dog d = new Dog("dogname", "black");
		Cat c = new Cat("catname", "blue");
		test.f(a);
		test.f(d);
		test.f(c);
	}

	public void f(Animal a) {
		System.out.println("name:" + a.name);
		if(a instanceof Dog) {
			Dog dog = (Dog)a;
			System.out.println(" " + dog.furcolor + " fur");
		}
		else if(a instanceof Cat) {
			Cat cat = (Cat)a;
			System.out.println(" " + cat.eyescolor + " eyes");
		}
	}
}
```



### 1.18 多态

- 动态绑定（多态）是指“在执行期间（而非编译期）判断所引用对象的实际类型，根据其实际的类型调用其相应的方法。”
- 多态的存在有三个必要条件：
  1. 要有继承
  2. 要有重写
  3. 父类引用指向子类对象

```java
class Animal {
	private String name;

	Animal(String name) {
		this.name = name;
	}

	public void enjoy() {
		System.out.println("叫声...");
	}
}

class Dog extends Animal{
	private String furcolor;

	Dog(String name, String furcolor) {
		super(name);
		this.furcolor = furcolor;
	}

	public void enjoy() {
		System.out.println("狗叫声...");
	}
}

class Cat extends Animal{
	private String eyescolor;
	Cat(String name, String eyescolor) {
		super(name);
		this.eyescolor = eyescolor;
	}

	public void enjoy() {
		System.out.println("猫叫声...");
	}
}

class Bird extends Animal{
	Bird() {
		super("bird");
	}

	public void enjoy() {
		System.out.println("鸟叫声...");
	}
}

class Lady {
	private String name;
	private Animal pet;

	Lady(String name, Animal pet) {
		this.name = name;
		this.pet = pet;
	}

	public void myPetEnjoy() {
		pet.enjoy();
	}
}

public class Test {
	public static void main(String[] args) {
		Dog d = new Dog("dogname", "balck");
		Cat c = new Cat("catname", "blue");
		Bird b = new Bird();

		Lady l1 = new Lady("l1", d);
		Lady l2 = new Lady("l2", c);
		Lady l3 = new Lady("l3", b);

		l1.myPetEnjoy();	//狗叫声...
		l2.myPetEnjoy();	//猫叫声...
		l3.myPetEnjoy();	//鸟叫声...
	}
}
```



### 1.19 抽象类

1. 用abstract关键字来修饰一个类时，这个类叫做抽象类；用abstract来修饰一个方法时，该方法叫做抽象方法。
2. 含有抽象方法的类必须被声明为抽象类，抽象类必须被继承，抽象方法必须被重写。
3. 抽象类不能被实例化。
4. 抽象方法只需声明，而不需实现。

```java
abstract class Animal {
	private String name;

	Animal(String name) {
		this.name = name;
	}

	// public void enjoy() {
	// 	System.out.println("叫声...");
	// }

	public abstract void enjoy();
}

class Dog extends Animal{
	private String furcolor;

	Dog(String name, String furcolor) {
		super(name);
		this.furcolor = furcolor;
	}

	public void enjoy() {
		System.out.println("狗叫声...");
	}
}

class Cat extends Animal{
	private String eyescolor;
	Cat(String name, String eyescolor) {
		super(name);
		this.eyescolor = eyescolor;
	}

	public void enjoy() {
		System.out.println("猫叫声...");
	}
}

class Bird extends Animal{
	Bird() {
		super("bird");
	}

	public void enjoy() {
		System.out.println("鸟叫声...");
	}
}

class Lady {
	private String name;
	private Animal pet;

	Lady(String name, Animal pet) {
		this.name = name;
		this.pet = pet;
	}

	public void myPetEnjoy() {
		pet.enjoy();
	}
}

public class Test {
	public static void main(String[] args) {
		Dog d = new Dog("dogname", "balck");
		Cat c = new Cat("catname", "blue");
		Bird b = new Bird();

		Lady l1 = new Lady("l1", d);
		Lady l2 = new Lady("l2", c);
		Lady l3 = new Lady("l3", b);

		l1.myPetEnjoy();	//狗叫声...
		l2.myPetEnjoy();	//猫叫声...
		l3.myPetEnjoy();	//鸟叫声...
	}
}
```



### 1.20 final关键字

- final的变量的值不能被改变。
- final的方法不能被重写。
- final的类不能被继承。

```java
class T {
	final int i = 8;

	public void m(final int j) {
		// j = 9;	//error!
	}

	public final void n() {}
}

class TT extends T {
	// public void n() {}	//error!
}

final class D {}

class DD extends D {}	//error!

public class TestFinal {
	public static void main(String[] args) {
		T t = new T();
		// t.i = 8;	//error!
	}
}
```



### 1.21 接口

1. 多个无关的类可以实现同一个接口。
2. 一个类可以实现多个无关的接口。
3. 与继承关系类似，接口与实现类之间存在多态性。
4. 接口（interface）是抽象方法和常量值的定义的集合。
5. 从本质上讲，接口是一种特殊的抽象类，这种抽象类中只包含常量和方法的定义，而没有变量和方法的实现。



- 特性：
  1. 接口可以多重实现。
  2. 接口中声明的属性默认为public static final的，也只能是public static final的。
  3. 接口中只能定义抽象方法，而这些方法默认为public的，也只能是public的。
  4. 接口可以继承其它的接口，并添加新的属性和抽象方法。



```java
interface Singer {
	public void sing();
	public void sleep();
}

interface Painter {
	public void paint();
	public void eat();
}

class Student implements Singer {
	private String name;

	Student(String name) {
		this.name = name;
	}

	public void study() {
		System.out.println("studying");
	}

	public String getName() {
		return name;
	}

	public void sing() {
		System.out.println("student is singing");
	}

	public void sleep() {
		System.out.println("student is sleeping");
	}
}

class Teacher implements Singer,Painter {
	private String name;

	Teacher(String name) {
		this.name = name;
	}

	public void teach() {
		System.out.println("teaching");
	}

	public String getName() {
		return name;
	}

	public void sing() {
		System.out.println("teacher is singing");
	}

	public void sleep() {
		System.out.println("teacher is sleeping");
	}

	public void paint() {
		System.out.println("teacher is painting");
	}

	public void eat() {
		System.out.println("teacher is eating");
	}
}

public class Test {
	public static void main(String[] args) {
		Singer s1 = new Student("le");
		s1.sing();
		s1.sleep();
		Singer s2 = new Teacher("steven");
		s2.sing();
		s2.sleep();
		Painter p1 = (Painter)s2;
		p1.paint();
		p1.eat();
	}
}
```



例子：TestInterface

```java
public interface Valuable {
	public double getMoney();
}

interface Protectable {
	public void beProtectable();
}

interface A extends Protectable {
	void m();
	double getMoney();
	// void getMoney();
}

abstract class Animal {
	private String name;

	abstract void enjoy();
}

class GoldenMonkey extends Animal implements Valuable, Protectable {
	public double getMoney() {
		return 10000;
	}

	public void beProtectable() {
		System.out.println("live in the room");
	}

	public void enjoy() {

	}

	public void test() {
		Valuable v = new GoldenMonkey();
		v.getMoney();
		Protectable p = (Protectable)v;
		p.beProtectable();
	}
}

class Hen implements A,Valuable {
	public void m() {}

	public void beProtectable() {}

	public double getMoney() {
		return 10;
	}

	// public void getMoney() {}
}
```



例子：AttendPet

```java
public interface AttendPet {
	void feeding();
	void play();
}

class Labour implements AttendPet {
	public void feeding() {
		System.out.println("feeding oil");
	}

	public void play() {
		System.out.println("play with screwdirver");
	}
}

class Farmer implements AttendPet {
	public void feeding() {
		System.out.println("feeding vegetables");
	}

	public void play() {
		System.out.println("play with hoe");
	}
}

class Cadre implements AttendPet {
	public void feeding() {
		System.out.println("feeding money");
	}

	public void play() {
		System.out.println("play with money");
	}
}
```



## 2. 异常处理

### 2.01 异常的概念

1. Java程序执行过程中如果出现异常事件，可以生成一个异常类对象，该异常对象封装了异常事件的信息并提交给Java运行时系统，这个过程称为抛出(throw)异常。
2. 当Java运行时系统接收到异常对象时，会寻找能处理这一异常的代码并把当前一场对象交给其处理，这一过程称为捕获(catch)异常。

```java
public class TestEx {
	public static void main(String[] args) {
		int[] a = {0, 1};
		try {
			System.out.println(a[3]);
		} catch(ArrayIndexOutOfBoundsException ae) {
			ae.printStackTrace();
			System.out.println("出错了");
		}
	}
}
```



#### try语句

* try{...}语句指定了**一次**捕获并处理例外的范围。
* 在执行过程中，该段代码可能会产生并抛出一种或几种类型的异常对象，它后面的catch语句要分别对这些异常做相应的处理。
* 如果没有例外产生，所有的catch代码段都被略过不执行。

#### catch语句

* 每个try语句块可以伴随一个或多个catch语句，用于处理可能产生的不同类型的异常对象。
* 在catch中声明的异常对象*catch(SomeException e)*封装了异常事件发生的信息，在catch语句块中可以使用这个对象的一些方法获取这些信息。
* catch声明的异常对象包含的一些方法：
  * getMessage() 方法，用来得到有关异常事件的信息。
  * printStackTrace() 方法，用来跟踪异常事件发生时执行堆栈的内容。



### 2.02 异常的分类

* 所有异常类都来源于Throwable(可被抛出的)类

* Throwable下面分两类：Error(系统错误，不能处理)和Exception(可以处理)

* Exception下面又分两类：RuntimeException(运行时错误，可以catch也可以不catch)和其他Exception(必须catch)

* 方法后可接throws SomeException来声明该方法可能抛出异常
```java
public void someMethod() throws SomeException {
    if(someCondition()) {
      throw new SomeException("错误原因");
    }
}
```


### 2.03 声明方法抛出的异常

```java
public class TestEx {
	//main()方法也可抛出异常，此时异常会交给java运行时系统，java运行时系统会把堆栈信息打印出来
	public static void main(String[] args) throws FileNotFoundException, IOException {
		// try {
		// 	new TestEx().f2();
		// } catch (FileNotFoundException e) {
		// 	e.printStackTrace();
		// } catch (IOException e) {
		// 	System.out.println(e.getMessage());
		// }
		te.f2();
	}

	//FileNotFoundException属于IOException，只需要抛出IOException就不会报错，但是为了可以对不同异常做不同处理，最好每个都抛出
	void f() throws FileNotFoundException, IOException {
		//会抛FileNotFoundException
		FileInputStream in = new FileInputStream("myfile.txt");
		int b;
		//read()会抛IOException
		b = in.read();
		while (b != -1) {
			System.out.print((char) b);
			b = in.read();
		}
	}

	//不想对异常进行处理，可继续抛出异常
	void f2() throws FileNotFoundException, IOException {
		// try {
		// 	f();
		// } catch (FileNotFoundException e) {
		// 	e.printStackTrace();
		// } catch (IOException e) {
		// 	System.out.println(e.getMessage());
		// }
		f();
	}
}
```



### 2.04 finally语句

```java
try {
	语句1;	//若语句1出错，语句2不会执行
	语句2;
} catch(SomeException1 e) {	//根据相应的异常执行相应的catch中的内容
	...
} catch(SomeException2 e) {
	...
} finally { //finally无论如何都会执行
	...
}
```

* finally语句为异常处理提供一个统一的出口，使得在控制流程转到程序的其他部分以前，能对程序的状态作统一的管理。
* 无论try所指定的程序块是否抛出例外，finally所指定的代码都要被执行。
* 通常在finally语句中可以进行资源的清除工作，例如：
  * 关闭打开的文件
  * 删除临时文件



### 2.05 异常的其它问题

```java
FileInputStream in = null;

try {
	//会抛出FileNotFoundException
	in = new FileInputStream("myfile.txt");
	int b;
	//read()会抛出IOException
	b = in.read();
	while (b != -1) {
		System.out.print((char) b);
		b = in.read();
	}
	//FileNotFoundException是一种IOException，所以先catch IOException的话永远也不能catch到FileNotFoundException，编译也会出错
} catch (IOException e) {
	e.printStackTrace();
} catch (FileNotFoundException e) {
	System.out.println(e.getMessage());
}
```
#### 使用自定义的异常

* 通过继承java.lang.Exception类声明自己的异常类(必须catch)，也可继承java.lang.RunTimeException类(必须catch)
* 在方法适当的位置生成自定义异常的实例，并用throw语句抛出
* 在方法的声明部分用throws语句声明该方法可能抛出异常

```java
class MyException extends Exception {
	private int id;
	public MyException(String message, int id) {
		//调用父类中设置message的构造方法
		super(message);
		//每个异常会有自己的id号
		this.id = id;
	}
	public int getId() {
		return id;
	}
}

public class Test {
	public void regist(int num) throws MyException {
		if (num < 0) {
			//方法如果throw了异常，后面的语句不会继续执行
			throw new MyException("人数为负值，不合理", 3);	
		}
		System.out.println("登记人数 "+num);
	}
	public void manager() {
		try {
			regist(100);
		} catch (MyException e) {
			System.out.println("登记失败，出错类型码=" + e.getId());
			e.printStackTrace();
		}
		System.out.println("操作结束");
	}
	public static void main(String[] args) {
		Test t = new Test();
		t.manager();
	}
}
```


#### 注意

**重写方法需要抛出与原方法所抛出异常类型一致或不抛出异常**

```java
class A {
	public void method() throws IOException {...}
}
//报错
class B1 extends A {
	public void method() throws FileNotFoundException {...}
}
//报错
class B2 extends A {
	public void method() throws Exception {...}
}
//报错
class B3 extends A {
	public void method() throws IOException, Exception {...}
}
//不报错
class B4 extends A {
	public void method() {...}
}
```

## 3. 数组
### 3.01 一维数组内存分析
- 在C/C++中，数组可以分配在栈上，而在Java中，数组为对象，只能分配在堆中
- 在Java中，数组变量为引用类型，数组中每个元素相当于数组这个对象的成员变量
- 数组中的元素可以是任何类型，包括基本类型和引用类型

#### 一维数组的声明
1. 一维数组的声明方式：
   > type var[]; 或 type[] var;
2. Java语言中声明数组时不能指定其长度，例如：
   > int a[5]; //错误

#### 数组对象的创建
- Java中使用new关键字创建数组对象，格式为：
  > 数组名 = new 数组元素的类型[数组元素的个数]

```java
public class Test {
	public static void main(String[] args) {
		//s为引用类型，存在于栈中，此时还没有分配空间，s中的值为null
		int[] s;
		//s指向了一个数组对象，该数组对象有5个int型成员变量，每个成员变量初始值为0
		s = new int[5];
		for(int i = 0; i < 5; i++) {
			s[i] = 2 * i + 1;
		}
	}
}
```

#### 元素为引用数据类型的数组
* 注意：元素为引用数据类型的数组中的每一个元素都需要实例化

```java
public class Test {
	public static void main(String[] args) {
		//days为引用类型，存在于栈中，此时未分配空间
		Date[] days;
		//days指向了堆中新生成的数组对象，该对象有5个引用类型的成员变量，初始值为null
		days = new Date[5];
		for(int i = 0; i < 5; i++) {
			//days中每个元素指向了堆中新生成的Date对象
			days[i] = new Date(2017, 1, i+1);
		}
	}
}

class Date {
	int year;
	int month;
	int day;
	Date (int y, int m, int d) {
		year = y;
		month = m;
		day = d;
	}
}
```


### 3.02 数组元素的创建和使用

#### 数组初始化
- 动态初始化：数组定义与数组元素分配空间和赋值的操作分开进行，例如：
```java
int a[];
a = new int[3];
a[0] = 0; a[1] = 1; a[2] = 2;
```

- 静态初始化：在定义数组的同时就为数组元素分配空间并赋值，例如：
```java
int a[] = {3, 9, 8};
Date days[] = {
	new Date(1, 1, 2017),
	new Date(2, 1, 2017),
	new Date(3, 1, 2017)
};
```

#### 数组元素默认初始化
- 数组是对象，它的元素相当于类的成员变量，因此数组分配空间后，每个元素也被按照成员变量的规则被隐式初始化
```java
public class Test {
	public static void main(String[] args) {
		int a[] = new int[3];
		Date days[] = new Date[3];
		System.out.println(a[2]);
		System.out.println(days[2]);
	}
}

class Date {
	int year;
	int month;
	int day;
	Date (int y, int m, int d) {
		year = y;
		month = m;
		day = d;
	}
}
```
输出：
	> 0
	> null

#### 数组元素的引用
- 定义并用运行算符new位置分配空间后，才可以引用数组中的每个元素
  - 引用方式，例如：a[3],b[i];
- 每个数组都有一个属性length指明其长度(元素个数)。
  - 例如：a.length

### 3.03 数组排序
#### 普通整形数组选择排序
```java
public class NumSort {
	//接收输入的9个数进行排序
	public static void main(String[] args) {
		int[] a = new int[args.length];
		for(int i = 0; i < args.length; i++) {
			//把输入的数转换为int型
			a[i] = Integer.parseInt(args[i]);
		}
		//打印输入的数
		print(a);

		// selectionSort(a);	//选择排序
		selectionSort1(a);	//改进后的选择排序

		//打印排序后的数
		print(a);
	}

	//选择排序，每遍历外层循环一次，从待排序匀速中选出最小的数
	private static void selectionSort(int[] a) {
		int temp;
		for(int i = 0; i < a.length; i++) {
			for(int j = i + 1; j < a.length; j++) {
				if(a[j] < a[i]) {
					temp = a[i];
					a[i] = a[j];
					a[j] = temp;
				}
			}
		}
	}

	//改进后的选择排序，效率更高
	private static void selectionSort1(int[] a) {
		int min;
		int temp;
		for(int i = 0; i < a.length; i++) {
			min = i;
			for(int j = min + 1; j < a.length; j++) {
				if(a[j] < a[min]) {
					min = j;
				}
			}
			if(min != i) {
				temp = a[i];
				a[i] = a[min];
				a[min] = temp;
			}
		}
	}

	//打印数组
	private static void print(int[] a) {
		for(int i = 0; i < a.length; i++) {
			System.out.print(a[i] + "");
		}
		System.out.println();
	}
}
```

#### 同一个类不同对象冒泡排序
```java
public class TestDateSort {
    public static void main(String[] args) {
        Date[] days = new Date[5];
        days[0] = new Date(2016, 5, 4);
        days[1] = new Date(2016, 7, 4);
        days[2] = new Date(2018, 5, 4);
        days[3] = new Date(2014, 5 ,9);
        days[4] = new Date(2014, 5, 4);

        bubbleSort(days);

        for(int i = 0; i < days.length; i++) {
            System.out.println(days[i]);
        }
    }

    //冒泡排序
    public static Date[] bubbleSort(Date[] a) {
        int len = a.length;
        Date temp;
        for(int i = len - 1; i >= 1; i--) {
            for(int j = 0; j <= i - 1; j++) {
                if(a[j].compare(a[j+1]) > 0) {
                    temp = a[j];
                    a[j] = a[j+1];
                    a[j+1] = temp;
                }
            }
        }
        return a;
    }
}

class Date {
    int year, month, day;
    Date(int y, int m, int d) {
        year = y; month = m; day = d;
    }
    //比较Date类对象的方法
    public int compare(Date date) { 
        return year > date.year ? 1 
            : year < date.year ? -1 
            : month > date.month ? 1
            : month < date.month ? -1
            : day > date.day ? 1
            : day < date.day ? -1 : 0;
    }

    public String toString() {
        return "Year:Month:Day -- " + year + "-" + month + "-" + day;
    }
}
```

### 3.04 练习
- 题目：数三退一
  共有500个人，第一个人数1，第二个人数2，第三个人数3，第三个人退出，然后从第四个人开始数，第四个人数1，第五个人数2，第六个人数3，第六个人退出......每个数到三的人退出，直到剩下最后一个人，试写一个程序，求出最后一个人原来位置（是500个人中第几个人）
```java
public class Count3Quit {
    public static void main(String[] args) {
        boolean[] arr = new boolean[500];
        for(int i = 0; i < arr.length; i++) {
            arr[i] = true;
        }

        //当前人数
        int leftCount = arr.length;
        //当前计数
        int countNum = 0;
        //当前数组下标
        int index = 0;

        while(leftCount > 1) {
            //如果该位置上有人
            if(arr[index] == true) {
                //计数增加
                countNum ++;
                //如果计数为3，该位置人推出，计数归零，总人数减少
                if(countNum == 3) {
                    arr[index] = false;
                    countNum = 0;
                    leftCount --;
                }
            }

            index ++;

            //循环遍历，直至人数为1
            if(index == arr.length) {
                index = 0;
            }
        }

        //找出最后剩下的人，打印其位置
        for(int i = 0; i < arr.length; i++) {
            if(arr[i] == true) {
                //其位置为数组下标加1
                System.out.println(i + 1);
            }
        }
    }
}
```

面向对象的写法:
```java
public class Count3Quit_2 {
	public static void main(String[] args) {
		KidCircle kc = new KidCircle(500);
		int countNum = 0;
		Kid k = kc.first;
		while(kc.count > 1) {
			countNum ++;
			if(countNum == 3) {
				countNum = 0;
				kc.delete(k);
			}
			k = k.right;
		}
		System.out.println(kc.first.id + 1);
	}
}

class Kid {
	int id;	//每个kid有自己的id号
	Kid left;	//Kid左边的Kid
	Kid right;	//Kid右边的Kid
}

//Kid围成一圈
class KidCircle {
	int count = 0;	//圈中Kid数量，初始为0
	Kid first, last;	//圈中第一个的Kid和最后一个Kid

	//n代表圈中有多少个Kid
	KidCircle(int n) {
		for(int i = 0; i < n; i++) {
			add();
		}
	}

	//添加一个Kid
	void add() {
		Kid k = new Kid();
		k.id = count;
		//如果是第一次添加Kid
		if(count <= 0) {
			first = k;
			last = k;
			k.left = k;
			k.right = k;
		} else {
			last.right = k;
			k.left = last;
			k.right = first;
			first.left = k;
			last = k;
		}
		count ++;
	}

	//Kid退出
	void delete(Kid k) {
		//如果没有Kid了
		if(count <= 0) {
			return;
		//如果当前只有一个Kid
		} else if(count == 1) {
			first = last = null;
		} else {
			k.left.right = k.right;
			k.right.left = k.left;
			//如果第一个Kid退出
			if(k == first) {
				first = k.right;
			//如果最后一个Kid退出
			} else if(k == last) {
				last = k.left;
			}
		}

		count --;
	}
}
```

### 3.05 数组查找

TestSearch.java
```java
public class TestSearch {
	public static void main(String[] args) {
		int a[] = {1, 3, 6, 8, 9, 10, 12, 18, 20, 34};
		int i = 12;
		System.out.println(binarySearch(a, i));
	}

	public static int search(int[] a, int num) {
		for(int i = 0; i < a.length; i++) {
			if(a[i] == num) 
				return i;
		}
		return -1;
	}

	//二分法查找(折半查找)，返回值是该值在数组中的位置
	public static int binarySearch(int[] a, int num) {
		if(a.length == 0)
			return -1;

		int startPos = 0;
		int endPos = a.length - 1;
		int m = (startPos + endPos) / 2;
		while(startPos <= endPos) {
			if(num == a[m])
				return m;
			if(num > a[m]) {
				startPos = m + 1;
			}
			if(num < a[m]) {
				endPos = m - 1;
			}
			m = (startPos + endPos) / 2;
		}
		return -1;
	}
}
```

在3.03中的TestDateSort.java中加入查找算法

```java
public class TestDateSort {
    public static void main(String[] args) {
        Date[] days = new Date[5];
        days[0] = new Date(2016, 5, 4);
        days[1] = new Date(2016, 7, 4);
        days[2] = new Date(2018, 5, 4);
        days[3] = new Date(2014, 5 ,9);
        days[4] = new Date(2014, 5, 4);

        bubbleSort(days);

        for(int i = 0; i < days.length; i++) {
            System.out.println(days[i]);
        }

        Date day = new Date(2014, 5, 9);
        System.out.println(binarySearch(days, day));
    }

    //冒泡排序
    public static Date[] bubbleSort(Date[] a) {
        int len = a.length;
        Date temp;
        for(int i = len - 1; i >= 1; i--) {
            for(int j = 0; j <= i - 1; j++) {
                if(a[j].compare(a[j+1]) > 0) {
                    temp = a[j];
                    a[j] = a[j+1];
                    a[j+1] = temp;
                }
            }
        }
        return a;
    }

    //折半查找
    public static int binarySearch(Date[] days, Date day) {
        if(days.length == 0)
            return -1;

        int startPos = 0;
        int endPos = days.length - 1;
        int m = (startPos + endPos) / 2;
        while(startPos <= endPos) {
            if(days[m].compare(day) == 0)
                return m;
            if(days[m].compare(day) == 1) {
                endPos = m - 1;
            }
            if(days[m].compare(day) == -1) {
                startPos = m + 1;
            }

            m = (startPos + endPos) / 2;
        }

        return -1;
    }
}

class Date {
    int year, month, day;
    Date(int y, int m, int d) {
        year = y; month = m; day = d;
    }
    //比较Date类对象的方法
    public int compare(Date date) { 
        return year > date.year ? 1 
            : year < date.year ? -1 
            : month > date.month ? 1
            : month < date.month ? -1
            : day > date.day ? 1
            : day < date.day ? -1 : 0;
    }

    public String toString() {
        return "Year:Month:Day -- " + year + "-" + month + "-" + day;
    }
}
```

### 3.06 二维数组
- Java中多维数组的声明和初始化应按从高维到低维的顺序进行：
```java
int a[][] = new int[3][];	//先分配第一维
a[0] = new int[2];
a[1] = new int[4];
a[2] = new int[3];
int t1[][] = new int[][4];	//非法
```

- 静态初始化：
```java
int a[][] = {{1, 2}, {2, 3}, {3, 4, 5}};
int b[3][2] = {{1, 2}, {2, 3}, {4, 5}};	//非法
```
- 动态初始化
```java
int a[][] = new int[3][5];
int b[][] = new int[3][];
b[0] = new int[2];
b[1] = new int[4];
b[2] = new int[3];
```

- 注意
```java
int a[][] = {{1, 2}, {3, 4, 5, 6}, {3, 4, 5}};
System.out.println(a.length);	//输出值为3
```


### 3.07 数组的拷贝
- 使用java.lang.System类的静态方法
  > public static void arraycopy (Object src, int srcPos, Object dest, int destPos, int length);

  - 可以用数组src从第srcPos元素开始的length个元素拷贝到目标数组dest从destPos项开始的length个位置
  - 如果源数据数目超过目标数组边界会抛出IndexOutOfBoundsException异常
```java
public class TestArrayCopy {
    public static void main(String args[]) {
        String[] s = {"Mircosoft", "IBM", "Sun", "Oracle", "Apple"};
        String[] sBak = new String[6];
        System.arraycopy(s, 0, sBak, 0, s.length);
    
        for(int i = 0; i < sBak.length; i++) {
            System.out.print(sBak[i] + " ");
        }
    
        System.out.println();

        int[][] intArray = {{1, 2}, {1, 2, 3}, {3, 4}};
        int[][] intArrayBak = new int[3][];
        System.arraycopy(intArray, 0, intArrayBak, 0, intArray.length);

        intArrayBak[2][1] = 100;
    
        for(int i = 0;i<intArray.length;i++) {
            for(int j =0;j<intArray[i].length;j++) {
                System.out.print(intArray[i][j] + "  "); 
            }
            System.out.println();
        }
    }
}
```

## 4. 常用类
### 4.01 String类
- java.lang.String类代表**不可变**的字符序列
- "xxxx"为该类的一个对象
#### String类常见构造方法：
- String(String original)
  创建一个String对象为original的拷贝
- Stirng(char[] value)
  用一个字符数组创建一个String对象
- String(char[] value, int offset, int count)
  用一个字符数组从offset项开始的count个字符序列创建一个String对象

```java
public class Test {
	public static void main(String[] args) {
		//hello是一个字符串常量，分配在data segment中，s1指向了hello
		String s1 = "hello";
		String s2 = "world";
		//在data segment中已存在hello时，s3直接指向hello，不会新生成一个hello
		String s3 = "hello";
		//打印true
		System.out.println(s1 == s3);
		
		s1 = new String("hello");
		s2 = new String("hello");
		//打印false
		System.out.println(s1 == s2);
		//打印true，String类重写了equals方法，比较s1和s2的字符序列是否相同
		System.out.println(s1.equals(s2));
		
		char c[] = {'s', 'u', 'n', ' ', 'j', 'a', 'v', 'a'};
		String s4 = new String(c);
		//从c[4]开始的4个字符序列创建一个String对象，并让s5指向这个对象
		Stirng s5 = new String(c, 4, 4);
		//打印sun java
		System.out.println(s4);
		//打印java
		System.out.println(s5);
	}
}
```

#### String类常用方法(1)
- public char charAr(int index)
  返回字符串中第index个字符。
- public int length()
  返回字符串的长度。
- public int indexOf(String str)
  返回字符串中出现str的第一个位置，若该对象中不存在str字符串，则返回-1
- public int indexOf(String str, int fromIndex)
  返回字符串中从fromIndex开始出现str的第一个位置，若该对象从fromIndex开始不存在str字符串，则返回-1
- public boolean equalsIgnoreCase(String another)
  比较字符串与another是否一样（忽略大小写）
- public String replace(char oldChar, char newChar)
  在字符串中用newChar字符替换oldChar字符
```java
public class Test {
    public class void main(Srting[] args) {
        String s1 = "sun java";
        String s2 = "Sun Java";
        System.out.println(s1.charAt(1));   //u
        System.out.println(s2.length());    //8
        System.out.println(s1.indexOf("java")); //4
        System.out.println(s1.indexOf("Java")); //-1
        System.out.println(s1.equals(s2));  //false
        System.out.println(s1.euqalsIgnoreCase(s2));    //true

        String s = "我是程序员，我在学java";
        String sr = s.replace('我', '你');
        System.out.println(sr); //你是程序员，你在学java
    }
}
```


#### String类常用方法(2)

- public boolean startsWith(String prefix)

  判断字符串是否以prefix字符串开头

- public boolean endsWith(String suffix)

  判断字符串是否以suffix字符串结尾

- public String toUpperCase()

  返回一个字符串为该字符串的大写形式

- public String toLowerCase()

  返回一个字符串为该字符串的小写形式

- public String substring(int beginIndex)

  返回该字符串从beginIndex开始到结尾的子字符串

- public String substring(int beginIndex, int endIndex)

  返回该字符串从beginIndex开始到endIndex结尾的子字符串

- public String trim()

  返回将该字符串去掉开头和结尾空格后的字符串

```java
public class Test {
    public static void main(String[] args) {
        String s = "Welcome to Java World!";
        String s1 = "   sun java   ";
        System.out.println(s.startsWith("Welcome"));    //true
        System.out.println(s.endsWith("World"));    //false
        String sL = s.toLowerCase();
        String sU = s.toUpperCase();
        System.out.println(sL); //welcome to java world!
        System.out.println(sU); //WELCOME TO JAVA WORLD!
        String subS = s.substring(11);
        System.out.println(subS);   //Java World!
        String sp = s1.trim();
        System.out.println(sp); //sun java
    }
}
```



#### String类常用方法(3)

- 静态重载方法

  - public static String valueOf(...)可以将基本数据类型转换为字符串

    例如：

    - public static String valueOf(double d)
    - public static String value Of(int i)

- 方法publiuc String[] split(String regex)可以将一个字符串按照指定的分隔符分割，返回分隔后的字符串数组

```java
public class Test {
    public static void main(String[] agrs) {
        int j = 123456;
        String sNumber = String.valueOf(j);
        System.out.println("j 是" + sNumber.length() + "位数。");
        String s = "Mary,F,1976";
        String[] sPlit = s.split(",");
        for(int i = 0; i < sPlit.length; i++) {
            System.out.println(sPlit[i]);
        }
    }
}
```



输出结果：

> j 是6位数。
> Mary
> F
> 1976



- 练习：

  - 编写一个程序，输出一个字符串中的大写英文字母数，小写英文字母数以及非英文字母数。

    ```java
    public class TestString {
        public static void main(String[] args) {
            String s = "AaaaABBBcc&^%adfsfdCCOOkk99876 _haHA";
            int lCount = 0, uCount = 0, oCount = 0;
    ```


            // for(int i = 0; i < s.length(); i++) {
            //     char c = s.charAt(i);
            //     if(c >= 'a' && c <= 'z') {
            //         lCount ++;
            //     } else if (c >= 'A' && c <= 'Z') {
            //         uCount ++;
            //     } else {
            //         oCount ++;
            //     }
            // }


            // String sL = "abcdefghijklmnopqrstuvwxyz";
            // String sU = "ABCDEFGHIJKLMNOPQRSTUVWXYZ";
            // for(int i = 0; i < s.length(); i++) {
            //     char c = s.charAt(i);
            //     if(sL.indexOf(c) != -1) {
            //         lCount ++;
            //     } else if (sU.indexOf(c) != -1) {
            //         uCount ++;
            //     } else {
            //         oCount ++;
            //     }
            // }


            for(int i = 0; i < s.length(); i++) {
                char c = s.charAt(i);
                if(Character.isLowerCase(c)) {
                    lCount ++;
                } else if (Character.isUpperCase(c)) {
                    uCount ++;
                } else {
                    oCount ++;
                }
            }


            System.out.println(lCount + " " + uCount + " " + oCount);
        }
    }
    ​```

-   编写一个方法，输出在一个字符串中，指定字符串出现的次数。 

    ```java
    public class TestString {
        public static void main(String[] args) {
            String s = "sunjavahojavaokjavajjavahahajavajavagoodjava";
            
            
            // String s1 = s + "s";
            // String[] sPlit = s1.split("java");
            // if(s1.startsWith("java")) {
            //     System.out.println(sPlit.length);
            // } else {
            //     System.out.println(sPlit.length - 1);
            // }
    ```


            String sToFind = "java";
            int count = 0;
            int index = -1;
            while((index = s.indexOf(sToFind)) != -1) {
                s = s.substring(index + sToFind.length());
                count ++;
            }
    
            System.out.println(count);
        }
    }
    ​```
    
    ​

### 4.02 StringBuffer 类

- java.lang.StringBuffer 代表**可变**的字符序列

- StringBuffer和String类似，但StringBuffer可以对其字符串进行改变

- StringBuffer类的常见构造方法：

  - StringBuffer()

    创建一个不包含字符序列的“空”的StringBuffer对象

  - StringBuffer(String str)

    创建一个StringBuffer对象，包含与String对象str相同的字符序列



#### StringBuffer 常用方法

- 重载方法 **public StringBuffer append(...)** 可以为该StringBuffer 对象添加字符序列，返回添加后的该StringBuffer对象引用，例如：

  > public StringBuffer append(String str)
  >
  > public StringBuffer append(StringBuffer sbuf)
  >
  > public StringBuffer append(char[] str)
  >
  > public StringBuffer append(char[] str, int offset, int len)
  >
  > public StringBuffer append(double d)
  >
  > public StringBuffer append(Object obj)

- 重载方法 **public StringBuffer insert(...)** 可以为该StringBuffer对象在指定位置插入字符序列，返回修改后的该StringBuffer对象引用，例如：

  > public StringBuffer insert(int offset, String str)
  >
  > public StringBuffer insert(int offset, double d)

- 方法 **public StringBuffer delete(int start, int end)** 可以删除从start开始到end-1为止的一段字符序列，返回修改后的该StringBuffer对象引用

- 和String类含义类似的方法：

  > public int indexOf(String str)
  >
  > public int indexOf(String str, int fromIndex)
  >
  > public String substring(int start)
  >
  > public String substring(int start, int end)
  >
  > public int length()

- 方法 **public StringBuffer reverse()** 用于将字符序列逆序，返回修改后的该StringBuffer对象引用

```java
public class Test {
    public static void main(String[] args) {
        String s = "Ubuntu";
        char[] a = {'a', 'b', 'c'};
        StringBuffer sb1 = new StringBuffer(s);
        sb1.append('/').append("Oracle").append('/').append("Sun");
        System.out.println(sb1);
        StringBuffer sb2 = new StringBuffer("数学");
        for(int i = 0; i <= 9; i++) {
            sb2.append(i);
        }
        System.out.println(sb2);
        sb2.delete(8, sb2.length()).insert(0, a);
        System.out.println(sb2);
        System.out.println(sb2.reverse());
    }
}
```



### 4.03 基础数据类型包装类

- 包装类(如：Integer，Double等)封装了一个相应的基本数据类型数值，并为其提供了一系列操作
- 以java.lang.Integer类为例；构造方法：
  - Integer(int value)
  - Integer(String s)
  - public static final int MAX_VALUE 最大的int型数
  - public static final int MIN_VALUE 最小的int型数
  - public long longValue() 返回封装数据的long型值
  - public double doubleValue() 返回封装数据类型的double型值
  - public int intValue() 返回封装数据类型的int型值
  - public static int parseInt(String s) throws NumberFormatException 将字符串解析成int型数据，返回该数据
  - public static Integer valueOf(String s) throws NumberFormatException 返回Integer对象，其中封装的整型数据为字符串s所表示

```java
public class Test {
    public static void main(String[] args) {
        Integer i = new Integer(100);
        Double d = new Double ("123.456");
        int j = i.intValue() + d.intValue();
        float f = i.floatValue() + d.floatValue();
        System.out.println(j);
        System.out.println(f);
        double pi = Double.parseDouble("3.1415926");
        double r = Double.valueOf("2.0").doubleValue();
        double s = pi * r * r;
        System.out.println(s);
        try {
            int k = Integer.parseInt("1.25");
        } catch (NumberFormatException e) {
            System.out.println("数据格式不对！");
        }
        System.out.println(Integer.toBinaryString(123) + "B");
        System.out.println(Integer.toHexString(123) + "H");
        System.out.println(Integer.toOctalString(123) + "O");
    }
}
```



练习：

- 编写一个方法，返回一个double型的二维数组，数组中的元素通过解析字符串参数获得。如字符串参数："1,2;3,4,5;6,7,8"

  对应的数组为：

  d[0, 0] = 1.0	d[0, 1] = 2.0

  d[1, 0] = 3.0	d[1, 1] = 4.0	d[1, 2] = 5.0

  d[2, 0] = 6.0	d[2, 1] = 7.0	d[2, 2] = 8.0

```java
public class ArrayParser {
    public static void main(String[] agrs) {
        String s = "1,2;3,4,5;6,7,8";
        double[][] d;
        String[] sFirst = s.split(";");
        d = new double[sFirst.length][];
        for(int i = 0; i < sFirst.length; i++) {
            String[] sSencond = sFirst[i].split(",");
            d[i] = new double[sSencond.length];
            for(int j = 0; j < sSencond.length; j++) {
                d[i][j] = Double.parseDouble(sSencond[j]);
            }
        }

        for(int i = 0; i < d.length; i++) {
            for(int j = 0; j < d[i].length; j++) {
                System.out.print(d[i][j] + " ");
            }
            System.out.println();
        }
    }
}
```



### 4.04 Math类 和 File类

#### Math类

- java.lang.Math提供了一系列静态方法用于科学计算;其方法的参数和返回值类型一般为double型。

  > abs 绝对值
  >
  > acos,asin,atan,cos,sin,tan
  >
  > sqrt 平方根
  >
  > pow(double a, double b) a的b次幂
  >
  > log 自然对数(以e为底的对数)
  >
  > exp e为底指数
  >
  > max(double a, double b)
  >
  > min(double a, double b)
  >
  > random() 返回0.0到1.0的随机数
  >
  > long round(double a) double型的数据a转换为long型(四舍五入)
  >
  > toDegrees(double angrad) 弧度->角度
  >
  > toRadians(double angdeg) 角度->弧度

```java
public class Test {
    public static void main(String[] agrs) {
        double a = Math.random();
        double b = Math.random();
        System.out.println(Math.sqrt(a * a + b * b));
        System.out.println(Math.pow(a, 8));
        System.out.println(Math.round(b));
        System.out.println(Math.log(Math.pow(Math.E, 15)));
        double d = 60.0;
        double r = Math.PI/4;
        System.out.println(Math.toRadians(d));
        System.out.println(Math.toDegrees(r));
    }
}
```



#### File类

- java.io.File类代表系统文件名(路径和文件名)

- File类的常见构造方法：

  - public File(String pathname)

    以pathname为路径创建File对象，如果pathname是相对路径，则默认的当前路径在系统属性user.dir中存储

  - public File(String parent, String Child)

    以parent为父路径，child为子路经创建File对象

- File的静态属性String separator存储了当前系统的路径分隔符



##### FIle类常用方法

- 通过File对象可以访问文件的属性

  > public boolean canRead()
  >
  > public boolean canWrite()
  >
  > public boolean exists()
  >
  > public boolean isDirectory()
  >
  > public boolean isFile()
  >
  > public boolean isHidden()
  >
  > public long lastModified()  返回上次修改时间
  >
  > public long length()
  >
  > public String getName()
  >
  > public String getPath()

- 通过File创建空文件或目录(在该对象所指的文件或目录不存在的情况下)

  > public boolean createNewFile() throws IOException
  >
  > public boolean delete()
  >
  > public boolean mkdir()
  >
  > public boolean mkdirs()  创建在路径中的一系列目录

```java
import java.io.*;

public class TestFile {
    public static void main(String[] args) {
        String separator = File.separator;
        String filename = "myfile.txt";
        String directory = "mydir1" + separator + "mydir2";
      	//String directory = "mydir1/mydir2";
        File f = new File(directory, filename);
        if(f.exists()) {
            System.out.println("文件名：" + f.getAbsolutePath());
            System.out.println("文件大小：" + f.length());
        } else {
            f.getParentFile().mkdirs();
            try{
                f.createNewFile();
            } catch(IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```



#####  练习

- 编写一个程序，在命令行中以树状结构展现特定的文件夹及其子文件(夹)

```java
import java.io.*;

public class FileList {
    public static void main(String[] args) {
        if(args.length != 1) {
            System.out.println("file pathname error");
        } else {
            File f = new File(args[0]);
            if(f.exists() == false) {
                System.out.println("the file is not exist");
            } else {
                System.out.println(f.getName());
                tree(f, 1);
            }
        }
    }

    //level代表目录层次
    private static void tree(File f, int level) {
        String preStr = "";
        //显示目录层次效果
        for(int i = 0; i < level; i++) {
            preStr += "  ";
        }
        File[] childs = f.listFiles();
        for(int i = 0; i < childs.length; i++) {
            System.out.println(preStr + childs[i].getName());
            if(childs[i].isDirectory()) {
                tree(childs[i], level + 1);
            }
        }
    }
}
```



### 4.05 Enum枚举类型

- 枚举类型：
  - 只能够取特定值中的一个
  - 使用enum关键字
  - 是java.lang.Enum类型

```java
public class TestEnum {
    public enum MyColor { red, green, blue };
    
    public static void main(String[] args) {
        MyColor m = MyColor.red;
        switch(m) {
            case red:
                System.out.println("red");
                break;
            case green:
                System.out.println("green");
                break;
            default:
                System.out.println("default");
        }
        System.out.println(m);
    }
}
```





## 5. 容器

### 5.01 容器的概念

- 容器：Java API 所提供的一系列类的实例，用于在程序中存放对象



### 5.02 容器 API

- J2SDK所提供的容器API位于java.util包内
- 容器API的类图结构：

![幻灯片32](/home/ryokai/git_projects/myjavanote.github.io/幻灯片32.jpg)

- Collection 接口定义了存取一组对象的方法，其自接口Set和List分别定义了存储方式
  - Set中的数据对象没有顺序且不可以重复（重复即指对象之间相互equals）
  - List中的数据对象有顺序且可以重复
- Map接口定义了存储“键(key)—值(value)映射对”的方法



### 5.03 Collection 接口

- Collection 接口中所定义的方法：

  >int size();
  >
  >boolean isEmpty();
  >
  >void clear();
  >
  >boolean contains(Object element);  是否包含(equals)某个对象
  >
  >boolean add(Object element);
  >
  >boolean remove(Object element);
  >
  >Iterator iterator();  返回一个实现了Iterator接口的对象
  >
  >boolean containsAll(Collection c);  是否包含(equals)一个集合中所有元素
  >
  >boolean addAll(Collection c);
  >
  >boolean removeAll(Collection c);
  >
  >boolean retainAll(Collection c);  求当前集合类和传进来的集合类的交集
  >
  >Object[] toArray();  转换为对象类型的数组

```java
import java.util.*;

public class Test {
    public static void main(String[] args) {
        Collection c = new ArrayList();
      	//可以放入不同的对象，但不能放入基础数据类型
        c.add("hello");
        c.add(new Integer(100));
        System.out.println(c.size());
        System.out.println(c);	//打印出了c.toString()
    }
}
```

输出结果：

> 2
> [hello, 100]



- 容器类对象在调用remove、contains等方法时需要比较对象是否相等，这会涉及到对象类型的equals方法和hashCode方法，对于自定义的类型，需要重写equals和hashCode方法以实现自定义的对象相等规则
  - 注意：相等的对象应具有相等的hash codes


```java
import java.util.*;

public class BasicContainer {
    public static void main(String[] args) {
        Collection c = new HashSet();
        c.add("hello");
        c.add(new Name("f1", "l1"));
        c.add(new Integer(100));
        c.remove("hello");
        c.remove(new Integer(100));
        //未重写Name类equals方法时，此句输出false
        System.out.println(c.remove(new Name("f1", "l1")));
        System.out.println(c);
    }
}

class Name {
    private String firstName, lastName;
    public Name(String firstName, String lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
    }
    public String getFirstName() {
        return firstName;
    }
    public String getLastName() {
        return lastName;
    }
    public String toString() {
        return firstName + " " + lastName;
    }

    public boolean equals(Object obj) {
        if(obj instanceof Name) {
            Name name = (Name) obj;
            return (firstName.equals(name.firstName)) && (lastName.equals(name.lastName));
        }
        return super.equals(obj);
    }
    public int hashCode() {
        return firstName.hashCode();
    }
}
```



 ### 5.04  Iterator 接口

- 所有实现了Collection接口的容器类都有一个iterator方法用以返回一个实现了Iterator接口的对象

- Iterator对象称作迭代器，用以方便的实现对容器内元素的遍历操作

- Iterator接口定义了如下方法：

  >boolean hasNext();	//判断游标右边是否有元素
  >
  ><u>E</u> next();			//返回游标右边的元素并将游标移动到下一个位置(jdk1.4及以前返回值为Object)
  >
  >void remove();		//删除游标左面的元素，在执行完next之后该操作只能执行一次

```java
import java.util.*;

public class Test {
    public static void main(String[] args) {
        Collection c = new HashSet();
        c.add(new Name("f1", "l1"));
        c.add(new Name("f2", "l2"));
        c.add(new Name("f3", "l3"));
        Iterator i = c.iterator();
        while(i.hasNext()) {
            Name n = (Name)i.next();
            System.out.print(n.getFirstName() + "  ");
        }
    }
}

class Name {
    private String firstName, lastName;
    public Name(String firstName, String lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
    }
    public String getFirstName() {
        return firstName;
    }
    public String getLastName() {
        return lastName;
    }
    public String toString() {
        return firstName + " " + lastName;
    }
}
```

输出结果：

> f3  f1  f2



- Iterator对象的remove方法是在迭代过程中删除元素的唯一的安全方法

  ```java
  Collection c = new HashSet();
  c.add(new Name("fff1", "lll1"));
  c.add(new Name("f2", "l2"));
  c.add(new Name("fff3", "lll3"));
  for(Iterator i = c.iterator(); i.hasNext();) {
  	Name name = (Name)i.next();
    	if(name.getFirstName().length() < 3) {
        	//如果换成c.remove(name);会产生例外
        	i.remove();
    	}
  }
  System.out.println(c);
  ```

  输出结果：

  > [fff3 lll3, fff1 lll1]	