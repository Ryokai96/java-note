# JavaSE 学习笔记

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

![容器API的类图结构](https://github.com/Ryokai96/myjavanote.github.io/blob/master/%E5%AE%B9%E5%99%A8API%E7%9A%84%E7%B1%BB%E5%9B%BE%E7%BB%93%E6%9E%84.jpg?raw=true)

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



 ### 5.04 Iterator 接口

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



### 5.05 EnhancedFor(JDK1.5 增强的for循环)

- 增强的for循环对于遍历array或Collection的时候相当简便

```java
import java.util.*;

public class EnhancedFor {
    public static void main(String[] args) {
        int[] arr = {1, 2, 3, 4, 5};
        //将arr中所有元素按顺序赋值给i
        for(int i : arr) {
            System.out.println(i);
        }

        Collection c = new ArrayList();
        c.add(new String("aaa"));
        c.add(new String("bbb"));
        c.add(new String("ccc"));
        //将c中所有元素按顺序赋值给o
        for(Object o : c) {
            System.out.println(o);
        }
    }
}
```

- 缺陷：

  - 数组：
    - 不能方便的访问下标值
  - 集合：
    - 与使用Iterator相比，不能方便的删除集合中的内容
    - 在内部也是调用Iterator

- 总结：

  除了简单遍历并读出其中的内容外，不建议使用增强的for循环



### 5.06 Set 接口

- Set接口是Collection的子接口，Set接口没有提供额外的方法，但实现Set接口的容器类中的元素是没有有顺序的，而且不可以重复
- Set容器可以与数学中“集合”的概念相对应
- J2SDK API中所提供的Set容器类有HashSet，TreeSet等



```java
public static void main(String[] args) {
  	Set s = new HashSet();
  	s.add("hello");
  	s.add("world");
  	//这里的Name类重写了equals方法
  	s.add(new Name("f1", "f2"));
  	s.add(new Integer(100));
  	//相同元素不会被加入
  	s.add(new Name("f1", "f2"));
  	//相同元素不会被加入
  	s.add("hello");
  	System.out.println(s);
}
```

输出结果：

> [100, hello, world, f1 f2]



```java
import java.util.*;

public class Test {
    public static void main(String[] args) {
        Set s1 = new HashSet();
        Set s2 = new HashSet();
		
      	//添加元素时，通过hash算法在集合内部进行排序保存
        s1.add("a");
        s1.add("b");
        s1.add("c");

        s2.add("d");
        s2.add("a");
        s2.add("b");

        //Set和List容器类都具有Constructor(Collection c)
        //构造方法用以初始化容器类
        Set sn = new HashSet(s1);
        sn.retainAll(s2);
        Set su = new HashSet(s1);
        su.addAll(s2);
      
        System.out.println(sn);
        System.out.println(su);
    }
}
```

输出结果：

> [a, b]
> [a, b, c, d]



### 5.07 List 接口

- List接口是Collection的子接口，实现List接口的容器类中的元素是有顺序的	，而且可以重复
- List容器中的元素都对应一个整数型的序号记载其在容器中的位置，可以根据序号存取容器中的元素
- J2SDk所提供的List容器类有ArrayList，LinkedList等

  > <u>E</u> get(int index);
>
> <u>E</u> set(int index, Object element);	//返回值为该位置上原来的元素
>
> void add(int index, Object element);
>
> <u>E</u> remove(int index);
>
> int indexOf(Object o);
>
> int lastIndexOf(Object o);



```java
import java.util.*;

public class Test {
    public static void main(String[] args) {
        List l1 = new LinkedList();
        for(int i = 0; i <= 5; i++) {
            l1.add("a" + i);
        }
        System.out.println(l1);
        l1.add(3, "a100");
        System.out.println(l1);
        l1.set(6, "a200");
        System.out.println(l1);
        System.out.print((String)l1.get(2) + " ");
        System.out.println(l1.indexOf("a3"));
        l1.remove(1);
        System.out.println(l1);
    }
}
```

输出结果：

> [a0, a1, a2, a3, a4, a5]
> [a0, a1, a2, a100, a3, a4, a5]
> [a0, a1, a2, a100, a3, a4, a200]
> a2 4
> [a0, a2, a100, a3, a4, a200]



#### List 常用算法

- 类 **java.util.Collections** 提供了一些静态方法实现了基于List容器的一些常用算法

  > void sort(List)	对List容器内的元素排序
  >
  > void shuffle(List)	对List容器内的对象进行随机排序
  >
  > void reverse(List)	对List容器内的对象进行逆序排列
  >
  > void fill(List, Object)	用一个特定的对象重写整个List容器
  >
  > void copy(LIst dest, List src)	将src List容器内容拷贝到dest List容器
  >
  > int binarySearch(List, Object)	对于顺序的List容器，采用折半查找的方法查找特定对象

```java
import java.util.*;

public class Test {
    public static void main(String[] args) {
        List l1 = new LinkedList();
        List l2 = new LinkedList();
        for(int i = 0; i < 10; i++) {
            l1.add("a" + i);
        }
        System.out.println(l1);
        //随机排序
        Collections.shuffle(l1);
        System.out.println(l1);
        //逆序
        Collections.reverse(l1);
        System.out.println(l1);
        //排序
        Collections.sort(l1);
        System.out.println(l1);
        //折半查找
        System.out.println(Collections.binarySearch(l1, "a5"));
    }
}
```

输出结果：

> [a0, a1, a2, a3, a4, a5, a6, a7, a8, a9]
> [a3, a8, a5, a7, a0, a1, a9, a6, a4, a2]
> [a2, a4, a6, a9, a1, a0, a7, a5, a8, a3]
> [a0, a1, a2, a3, a4, a5, a6, a7, a8, a9]
> 5



### 5.08 Comparable 接口

- 所有可以排序的类都实现了**java.lang.Comparable**接口，Comparable接口中只有一个方法

  > public int compareTo(<u>T</u> o);

  - 返回0表示 this == o
  - 返回正数表示 this > o
  - 返回负数表示 this < o

- 实现了Comparable接口的类通过实现compareTo方法从而确定该类对象的排序方式

- 改写Name类让其实现Comparable接口：

  ```java
  import java.util.*;

  public class Test {
      public static void main(String[] args) {
          List l1 = new LinkedList();
          l1.add(new Name("Jone", "Banks"));
          l1.add(new Name("PhotoShop", "Adobe"));
          l1.add(new Name("Java", "Oracle"));
          System.out.println(l1);
          Collections.sort(l1);
          System.out.println(l1);
      }
  }

  class Name implements Comparable {
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

      public int compareTo(Object o) {
          Name n = (Name)o;
          int lastCmp = lastName.compareTo(n.lastName);
          return (lastCmp != 0 ? lastCmp : firstName.compareTo(n.firstName));
      }
  }
  ```

  输出结果：

  > [Jone Banks, PhotoShop Adobe, Java Oracle]
  > [PhotoShop Adobe, Jone Banks, Java Oracle]




### 5.09 如何选择数据结构

- 衡量标准：读的效率和改的效率
  - Array 读快改慢
  - Linked 改快读慢
  - Hash 两者之间



### 5.10 Map 接口

- 实现Map接口的类用来存储 键—值(key—value) 对
- Map接口的实现类有HashMap和TreeMap等
- Map类中存储的 键—值 对通过键来标识，所以键值不能重复

  > Object put(Object key, Object value);//返回的是原本的value
>
> Object get(Object key);
>
> Object remove(Object key);
>
> boolean containsKey(Object key);	//是否包含key
>
> boolean containsValue(Object value);	//是否包含value
>
> int size();
>
> boolean isEmpty();
>
> void putAll(Map t);
>
> void clear();



- TestMap.java:

```java
import java.util.*;

public class TestMap {
    public static void main(String[] args) {
        Map m1 = new HashMap();
        Map m2 = new TreeMap();
        m1.put("one", new Integer(1));
        m1.put("two", new Integer(2));
        m1.put("three", new Integer(3));
        m2.put("A", new Integer(1));
        m2.put("B", new Integer(2));
        System.out.println(m1.size());
        System.out.println(m1.containsKey("one"));
        System.out.println(m2.containsValue(new Integer(1)));
        if(m1.containsKey("two")) {
          	//Map.get()方法返回的是Object类型的对象，所以要强制类型转换
            int i = ((Integer)m1.get("two")).intValue();
            System.out.println(i);
        }
        Map m3 = new HashMap(m1);
        m3.putAll(m2);
        System.out.println(m3);
    }
}
```

输出结果：

> 3
> true
> true
> 2
> {A=1, B=2, two=2, three=3, one=1}



#### Auto-boxing/unboxing(自动打包、解包)

- 在合适的时机自动打包、解包
  - 自动将基础类型转换为对象(打包boxing)
  - 自动将对象转换为基础类型(解包unboxing)
- 上面的TestMap.java也可写为：

```java
import java.util.*;

public class TestMap {
    public static void main(String[] args) {
        Map m1 = new HashMap();
        Map m2 = new TreeMap();
        m1.put("one", 1);
        m1.put("two", 2);
        m1.put("three", 3);
        m2.put("A", 1);
        m2.put("B", 2);
        System.out.println(m1.size());
        System.out.println(m1.containsKey("one"));
        System.out.println(m2.containsValue(1));
        if(m1.containsKey("two")) {
            //Integer对象才能自动解包成int类型
            int i = (Integer)m1.get("two");
            System.out.println(i);
        }
        Map m3 = new HashMap(m1);
        m3.putAll(m2);
        System.out.println(m3);
    }
}
```



TestArgsWords.java

```java
import java.util.*;

public class TestArgsWords {
    private static final Integer ONE = 1;

    public static void main(String[] args) {
        Map m = new HashMap();
        for(int i = 0; i < args.length; i++) {
            Integer freq = (Integer)m.get(args[i]);
            m.put(args[i],(freq == null ? ONE : (freq + 1)));
        }
        System.out.println(m.size() + " distinct words detected:");
        System.out.println(m);
    }
}
```

输入及输出：

> java TestArgsWords aaa aa bc ccc aaa ccc ccc
> 4 distinct words detected:
> {aaa=2, aa=1, bc=1, ccc=3}



### 5.11 泛型

- 起因：JDK1.4及以前类型不明确
  - 装入集合的类型都被当作Object对待，从而失去自己的实际类型
  - 从集合中取出时往往需要转型，效率低，容易产生错误
- 解决方法：
  - 在定义集合的时候同时定义集合中对象的类型
    - 可以在定义Collection的时候指定
    - 也可以在循环时用Iterator指定
- 好处：增强程序的可读性和稳定性



BasicGeneric.java

```java
import java.util.*;

public class BasicGeneric {
    public static void main(String[] args) {
        //指定List中的对象只能为String类型
        List<String> c = new ArrayList<String>();
        c.add("aaa");
        c.add("bbb");
        c.add("ccc");
        for(int i = 0; i < c.size(); i++) {
            String s = c.get(i);
            System.out.println(s);
        }

        Collection<String> c2 = new HashSet<String>();
        c2.add("aaa");
        c2.add("bbb");
        c2.add("ccc");
        for(Iterator<String> it = c2.iterator(); it.hasNext();) {
            String s = it.next();
            System.out.println(s);
        }
    }
}

class MyName implements Comparable<MyName> {
    int age;

    public int compareTo(MyName mn) {
        if(this.age > mn.age) {
            return 1;
        } else if(this.age < mn.age) {
            return -1;
        } else {
            return 0;
        }
    }
}
```



TestMap2.java

```java
import java.util.*;

public class TestMap2 {
    public static void main(String[] args) {
        Map<String, Integer> m1 = new HashMap<String, Integer>();
        m1.put("one", 1);
        m1.put("two", 2);
        m1.put("three", 3);

        System.out.println(m1.size());
        System.out.println(m1.containsKey("one"));

        if(m1.containsKey("two")) {
            int i = m1.get("two");
            System.out.println(i);
        }
    }
}
```



TestArgsWords2.java

```java
import java.util.*;

public class TestArgsWords2 {
    private static final int ONE = 1;

    public static void main(String[] args) {
        Map<String, Integer> m = new HashMap<String, Integer>();
        for(int i = 0; i < args.length; i++) {
            if(!m.containsKey(args[i])) {
                m.put(args[i], ONE);
            } else {
                int freq = m.get(args[i]);
                m.put(args[i], freq + 1);
            }
        }

        System.out.println(m.size() + " distinct words detected:");
        System.out.println(m);
    }
}
```



## 6. IO

### 6.01 IO初步

- 在Java程序中，对于数据的输入/输出操作以“流”(stream)方式进行，jdk提供了各种各样的“流”类，用以获取不同种类的数据，程序中通过标准的方法输入或输出数据



#### 输入/输出流的分类

- 输入/输出都是相对于程序而言


- java.io包中定义了对各流类型(类或抽象类)来实现输入/输出功能，可以从不同的角度对其进行分类：

  - 按数据流的方向不同可以分为输入流和输出流
  - 按处理数据单位不同可以分为字节流和字符流
  - 按照功能不同可以分为节点流和处理流

- JDK所提供的所有流类型位于包java.io内都分别继承自以下四种抽象流类型

  |      |     字节流      |  字符流   |
  | :--: | :----------: | :----: |
  | 输入流  | InputStream  | Reader |
  | 输出流  | OutputStream | Writer |



#### 节点流和处理流

- 节点流为可以从一个特定的数据源(节点)读写数据(如：文件，内存)

- 节点流类型：

  |      类型       |                  字符流                  |                   字节流                    |
  | :-----------: | :-----------------------------------: | :--------------------------------------: |
  |   File(文件)    |      FileReader<br />FileWriter       |  FileInputStream<br />FileOutputStream   |
  | Memory Array  | CharArrayReader<br  />CharArrayWriter | ByteArrayInputStream<br />ByteArrayOutputStream |
  | Memory String |    StringReader<br />StringWriter     |                    —                     |
  |   Pipe(管道)    |     PipedReader<br />PipedWriter      | PipedInputStream<br />PipedOutputStream  |

- 处理流是“连接”在已存在的流(节点流或处理流)之上，通过对数据的处理为程序提供更为强大的读写功能

- 处理流类型：

  |                  处理类型                  |                   字符流                    |                   字节流                    |
  | :------------------------------------: | :--------------------------------------: | :--------------------------------------: |
  |               Buffering                |    BufferedReader<br />BufferedWriter    | BufferedInputStream<br />BufferedOutputStream |
  |               Filtering                |      FilterReader<br />FilterWriter      | FilterInputStream<br />FilterOutputStream |
  | Converting between bytes and character | InputStreamReader<br />OutputStreamWriter |                    —                     |
  |          Object Serialization          |                    —                     | ObjectInputStream<br />ObjectOutputStream |
  |            Date conversion             |                    —                     |  DateInputStream<br />DateOutputStream   |
  |                Counting                |             LineNumberReader             |          LineNumberInputStream           |
  |             Peeking ahead              |              PushbackReader              |           PushbackInputStream            |
  |                Printing                |               PrintWriter                |               PrintStream                |

  ​



#### InputStream

- 继承自InputStream的流都是用于向程序中输入数据，且数据的单位为字节(8bit)

- InputStream的基本方法

  > //读取一个字节并以整数的形式返回(0~255)
  >
  > //如果返回-1则已到输入流的末尾
  >
  > int read() throws IOException

  ​

  > //读取一系列字节并存储到一个数组buffer
  >
  > //返回实际读取的字节数，如果读取前已到输入流的末尾则返回-1
  >
  > int read(byte[] buffer) throws IOException

  ​

  > //读取length个字节，并从offset位置开始存储到一个字节数组buffer
  >
  > //返回实际读取的字节数，如果读取前已到输入流的末尾返回-1
  >
  > int read(byte[] buffer, int offset, int length) throws IOException

  ​

  > //关闭流释放内存资源
  >
  > void close() throws IOException

  ​

  > //跳过n个字节不读，返回实际跳过的字节数
  >
  > long skip(long n) throws IOException

  ​



#### OutputStream

- 继承自OutputStream的流是用于程序中输出数据，且数据的单位为字节(8bit)

- OutputStream的基本方法

  > //向输出流写入一个字节数据，该字节数据为参数b的低8位
  >
  > void write(int b) throws IOException

  ​

  > //将一个字节类型的数组中的数据写入输出流
  >
  > int write(byte[] b) throws IOException

  ​

  > //将一个字节类型的数组中的从指定位置(off)开始的len个字节写入到输出流
  >
  > void write(byte[] b, int off, int len) throws IOException

  ​

  > //关闭流释放内存资源
  >
  > void close() throws IOException

  ​

  > //将输出流中缓冲的数据全部写出到目的地
  >
  > void flush() throws IOException

  ​



#### Reader

- 继承自Reader的流都是用于向程序中输入数据，且数据的单位为字符(两个字节 16bit)

- Reader的基本方法

  > //读取一个字符并以整数的形式返回(0~255)
  >
  > //如果返回-1则已到输入流的末尾
  >
  > int read() throws IOException

  ​

  > //读取一系列字符并存储到一个数组cbuf
  >
  > //返回实际读取的字符数，如果读取前已到输入流的末尾则返回-1
  >
  > int read(char[] cbuf) throws IOException

  ​

  > //读取length个字节，并从offset位置开始存储到一个数组cbuf
  >
  > //返回实际读取的字符数，如果读取前已到输入流的末尾返回-1
  >
  > int read(char[] cbuf, int offset, int length) throws IOException

  ​

  > //关闭流释放内存资源
  >
  > void close() throws IOException

  ​

  > //跳过n个字符不读，返回实际跳过的字符数
  >
  > long skip(long n) throws IOException

  ​



#### Writer

- 继承自Writer的流都是用于程序中输出数据，且数据的单位为字符(16 bit)

- Writer的基本方法

  > //向输出流中写入一个字符数据，该字符数据为参数b的低16位
  >
  > void write(int c) throws IOException

  ​

  > //将一个字符类型的数组中的数据写入输出流
  >
  > void write(char[] cbuf) throws IOException

  ​

  > //将一个字符类型的数组中的从指定位置(offset)开始的length个字符写入到输出流
  >
  > void write(char[] cbuf, int offset, int length) throws IOException

  ​

  > //将一个字符串中的字符写入到输出流
  >
  > void write(String string) throws IOException

  ​

  > //将一个字符串从offset开始的length个字符写入到输出流
  >
  > void write(String string, int offset, int length) throws IOException

  ​

  > //关闭流释放内存资源
  >
  > void close() throws IOException

  ​

  > //将输出流中缓存的数据全部写出到目的地
  >
  > void flush() throws IOException



### 6.02 FileInputStream & FileOutputStream

- FileInputStream和FileOutputStream分别继承自InputStream和OutputStream用于向文件中输入和输出字节

- FileInputStream和FileOutputStream的常用构造方法：

  > FileInputStream(String name) throws FileNotFoundException
  >
  > FileInputStream(File file) throws FileNotFoundException
  >
  > FileOutputStream(String name) throws FileNotFoundException
  >
  > FileOutPutStream(File file) throws FileNotFoundException
  >
  > FileOutputStream(File file, boolean append) throws FileNotFoundException

- FileInputStream和FileOutputStream类支持其父类InputStream和OutputStream所提供的数据读写方法

- 注意：

  - 在实例化FileInputStream和FileOutputStream流时要用try-catch语句以处理其可能抛出的FileNotFoundException
  - 在读写数据时也要用try-catch语句以处理可能抛出的IOException
  - FileNotFoundException是IOException的子类

- TestFileInputStream.java:

  ```java
  import java.io.*;

  public class TestFileInputStream {
      public static void main(String[] args) {
          int b = 0;
          FileInputStream in = null;
          try {
              in = new FileInputStream(args[0]);
          } catch (FileNotFoundException e) {
              System.out.println("找不到指定文件");
              System.exit(-1);
          }

          try {
              long num = 0;
              while((b = in.read()) != -1) {
                  System.out.print((char) b);
                  num ++;
              }
              in.close();
              System.out.println();
              System.out.println("共读取了 " + num + " 个字节");
          } catch(IOException e1) {
              System.out.println("文件读取错误");
              System.exit(-1);
          }
      }
  }
  ```

- TestFileOutputStream.java

  ````java
  import java.io.*;

  public class TestFileOutputStream {
      public static void main(String[] args) {
          int b = 0;
          FileInputStream in = null;
          FileOutputStream out = null;
          try {
              in = new FileInputStream(args[0]);
              out = new FileOutputStream(args[1]);
              while((b = in.read()) != -1) {
                  out.write(b);
              }
              in.close();
              out.close();
          } catch(FileNotFoundException e2) {
              System.out.println("找不到指定文件");
              System.exit(-1);
          } catch(IOException e1) {
              System.out.println("文件复制错误");
              System.exit(-1);
          }

          System.out.println("文件已复制");
      }
  }
  ````



### 6.03 FileReader & FileWriter

- TestFileReader.java

  ```java
  import java.io.*;

  public class TestFileReader {
      public static void main(String[] args) {
          FileReader fr = null;
          int c = 0;
          try {
              fr = new FileReader(args[0]);
              int ln = 0;
              while((c = fr.read()) != 01) {
                  System.out.print((char)c);
              }
              fr.close();
          } catch(FileNotFoundException e) {
              System.out.println("找不到指定文件");
          } catch(IOException e) {
              System.out.println("文件读取错误");
          }
      }
  }
  ```

- TestFileWriter.java

  ```java
  import java.io.*;

  public class TestFileWriter {
      public static void main(String[] args) {
          FileWriter fw = null;
          try {
              fw = new FileWriter(args[0]);
              for(int c = 0; c <= 50000; c++) {
                  fw.write(c);
              }
              fw.close();
          } catch(IOException e) {
              e.printStackTrace();
              System.out.println("文件写入错误");
              System.exit(-1);
          }
      }
  }
  ```



### 6.04 缓冲流 

- 缓冲流要”套接“在相应的节点流之上，对读写的数据提供了缓冲的功能，提高了读写的效率，同时增加了一些新的方法

- J2SDK提供了四种缓冲流，其常用的构造方法为：

  > BufferedReader(Reader in)
  >
  > BufferedReader(Reader in, int size)	//size为自定义缓存区的大小
  >
  > BufferedWriter(Writer out)
  >
  > BufferedInputStream(InputStream in)
  >
  > BufferedInputStream(InputStream in, int size)
  >
  > BufferedOutputStream(OutputStream out)
  >
  > BufferedOutputStream(OutputStream out, int size)

- 缓冲输入流支持其父类的mark和reset方法

- BufferedReader提供了readLine方法用于读取一行字符串(以\r或\n分隔)

- BufferedWriter提供了newLine用于写入一个行分隔符

- 对于输出的缓冲流，写出的数据会先在内存中缓存，使用flush方法将会使内存中的数据立刻写出

- TestBufferStream1.java

  ```java
  import java.io.*;

  public class TestBufferStream1 {
      public static void main(String[] args) {
          try {
              FileInputStream fis = new FileInputStream(args[0]);
              BufferedInputStream bis = new BufferedInputStream(fis);
              int c = 0;
              System.out.println(bis.read());
              System.out.println(bis.read());
              bis.mark(100);
              for(int i = 0; i <= 10 && (c = bis.read()) != -1; i++) {
                  System.out.print(c + " ");
              }
              System.out.println();
              //回到mark的位置
              bis.reset();
              for(int i = 0; i <= 10 && (c = bis.read()) != -1; i++) {
                  System.out.print(c + " ");
              }
              bis.close();
          } catch(IOException e) {
              e.printStackTrace();
          }
      }
  }
  ```

- TestBufferStream2.java

  ```java
  import java.io.*;

  public class TestBufferStream2 {
      public static void main(String[] args) {
          try {
              BufferedWriter bw = new BufferedWriter(new FileWriter(args[0]));
              BufferedReader br = new BufferedReader(new FileReader(args[0]));
              String s = null;
              for(int i = 1; i < 100; i++) {
                  s = String.valueOf(Math.random());
                  bw.write(s);
                  bw.newLine();
              }
              bw.flush();
              while((s = br.readLine()) != null) {
                  System.out.println(s);
              }
              bw.close();
              br.close();
          } catch(IOException e) {
              e.printStackTrace();
          }
      }
  }
  ```



### 6.05 转换流 

- InputStreamReader和OutputStreamWriter用于字节数据到字符数据之间的转换

- InputStreamReader需要和InputStream”套接“

- OutputStreamWriter需要和OutputStream”套接“

- 转换流在构造时可以指定其编码集合，例如：

  > InputStream isr = new InputStreamReader(System.in, "ISO8859_1");

- TestTransform1.java

  ```java
  import java.io.*;

  public class TestTransform1 {
      public static void main(String[] args) {
          try {
              OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream(args[0]));
              osw.write("oraclejavaubuntu");
              System.out.println(osw.getEncoding());
              osw.close();
              //追加写入内容
              osw = new OutputStreamWriter(new FileOutputStream(args[0], true), "ISO8859_1");
              osw.write("oraclejavaubuntu");
              System.out.println(osw.getEncoding());
              osw.close();
          } catch(IOException e) {
              e.printStackTrace();
          }
      }
  }
  ```

- TestTransform2.java

  ```java
  import java.io.*;

  public class TestTransform2 {
      public static void main(String[] agrs) {
          InputStreamReader isr = new InputStreamReader(System.in);
          BufferedReader br = new BufferedReader(isr);
          String s = null;
          try {
              s = br.readLine();
              while(s != null) {
                  if(s.equalsIgnoreCase("exit")) {
                      break;
                  }
                  //变为大写字母
                  System.out.println(s.toUpperCase());
                  s = br.readLine();
              }
              br.close();
          } catch(IOException e) {
              e.printStackTrace();
          }
      }
  }
  ```



### 6.06 数据流

- DataInputStream和DataOutputStream分别继承自InputStream和OutputStream，它属于处理流，需要分别套接在InputStream和OutputStream类型的节点流上

- DataInputStream和DataOutputStream提供了可以存取与机器无关的Java原始类型数据(如:int,double等)的方法

- DataInputStream和DataOutputStream的构造方法为：
  - DataInputStream(InputStream in)
  - DataOutputStream(OutputStream out)

- TestDataStream.java

  ```java
  import java.io.*;

  public class TestDataStream {
      public static void main(String[] args) {
          ByteArrayOutputStream baos = new ByteArrayOutputStream();
          DataOutputStream dos = new DataOutputStream(baos);

          try {
              dos.writeDouble(Math.random());
              dos.writeBoolean(true);
              ByteArrayInputStream bais = new ByteArrayInputStream(baos.toByteArray());
              //available()返回可读字节数
              System.out.println(bais.available());
              DataInputStream dis = new DataInputStream(bais);
              System.out.println(dis.readDouble());
              System.out.println(dis.readBoolean());
              dos.close();
              dis.close();
          } catch(IOException e) {
              e.printStackTrace();
          }
      }
  }
  ```




### 6.07 Print 流

- PrintWriter和PrintStream都属于输出流，分别针对与字符和字节

- PrintWriter和PrintStream提供了重载的print

- Println方法用于多种数据类型的输出

- PrintWriter和PrintStream的输出操作不会抛出异常，用户通过检测错误状态获取错误信息

- PrintWriter和PrintStream有自动flush功能

- PrintWriter和PrintStream常用构造方法：

  > PrintWriter(Writer out)
  >
  > PrintWriter(Writer out, boolean autoFlush)
  >
  > PrintWriter(OutputStream out)
  >
  > PrintWriter(OutputStream out, boolean autoFlush)
  >
  > PrintStream(OutputStream out)
  >
  > PrintStream(OutputStream out, boolean autoFlush)

- TestPrintStream1.java

  ```java
  import java.io.*;

  public class TestPrintStream1 {
      public static void main(String[] args) {
          PrintStream ps = null;
          try {
              FileOutputStream fos = new FileOutputStream(args[0]);
              ps = new PrintStream(fos);
          } catch (IOException e) {
              e.printStackTrace();
          }
          if(ps != null) {
              //重新分配标准输出流out为ps
              System.setOut(ps);
          }
          int ln = 0;
          for(char c = 0; c <= 60000; c++) {
              System.out.println(c + " ");
              if(ln++ >= 100) {
                  System.out.println();
                  ln =0;
              }
          }
      }
  }
  ```

- TestPrintStream2.java

  ```java
  import java.io.*;

  public class TestPrintStream2 {
      public static void main(String[] args) {
          String filename = args[0];
          if(filename != null) {
              list(filename, System.out);
          }
      }

      public static void list(String f, PrintStream fs) {
          try {
              BufferedReader br = new BufferedReader(new FileReader(f));
              String s = null;
              while((s = br.readLine()) != null) {
                  fs.println(s);
              }
              br.close();
          } catch(IOException e) {
              fs.println("无法读取文件");
          }
      }
  }
  ```

- TestPrintStream3.java

  ```java
  import java.util.*;
  import java.io.*;

  public class TestPrintStream3 {
      public static void main(String[] args) {
          String s = null;
          BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
          try {
              FileWriter fw = new FileWriter(args[0], true);
              PrintWriter log = new PrintWriter(fw);
              while((s = br.readLine()) != null) {
                  if(s.equalsIgnoreCase("exit")) {
                      break;
                  }
                  System.out.println(s.toUpperCase());
                  log.println("-----");
                  log.println(s.toUpperCase());
                  log.flush();
              }
              log.println("===" + new Date() + "===");
              log.flush();
              log.close();
          } catch(IOException e) {
              e.printStackTrace();
          }
      }
  }
  ```

  ​

### 6.08 Object 流

- 直接将Object写入或读出

- TestObjectIO.java

  ```java
  import java.io.*;

  public class TestObjectIO {
      public static void main(String[] args) throws Exception {
          T t = new T();
          t.k = 8;
          FileOutputStream fos = new FileOutputStream(args[0]);
          ObjectOutputStream oos = new ObjectOutputStream(fos);
          oos.writeObject(t);
          oos.flush();
          oos.close();

          FileInputStream fis = new FileInputStream(args[0]);
          ObjectInputStream ois = new ObjectInputStream(fis);
          T tReaded = (T)ois.readObject();
          System.out.println(tReaded.i + " " + tReaded.j + " " + tReaded.d + " " + tReaded.k);
      }
  }

  //Serializable为标记性的接口，没有定义方法，作用为告诉编译器这个类可以序列化，Serializable有一个子接口Externalizable，这个子接口中有控制写入和输出的序列化过程的方法
  class T implements Serializable {
      int i = 10;
      int j = 9;
      double d = 2.3;
      //transient(透明的) 修饰的变量在序列化的时候不予以考虑
      transient int k = 15;
  }
  ```




## 7. 线程

### 7.01 线程的创建

- 进程：一个程序的执行
- 线程：程序中单个顺序的流程控制称为线程
- 一个进程中可以含有多个线程，这些线程：
  - 分享CPU(并发的或以时间片的方式)
  - 共享内存(如多个线程访问统一对象)
- Java从语言级别支持多线程
  - 如Object中wait(), notify()
- java.lang中的类 Thread



#### 线程体

- 线程体---**run()**方法来实现的 (线程体的本质就是run()方法)
- 线程启动后，系统就自动调用run()方法
- 通常，run()方法执行一个时间较长的操作
  - 如一个循环
  - 显示一系列图片
  - 下载一个文件



#### 创建线程的两种方法

1. 通过**继承Thread类**创建线程

   ```java
   class MyThread extends Thread {
     	public void run() {
         	for(int i = 0; i < 100; i++) {
             	System.out.print(" " + i);
         	}
     	}
   }
   ```

2. 通过向Thread()构造方法**传递Runnable对象**来创建线程

   ```java
   class MyTask implements Runnable {
     	public void run() {
         	...
     	}
   }
   Thread thread = new Thread(mytask);
   thread.start();
   ```

   ​

#### 匿名类及lambda表达式

- 可用匿名类来实现Runnable

  ```java
  new Thread() {
    	public void run() {
        	for(int i = 0; i < 10; i++) {
            	System.out.println(i);
        	}
    	}
  }.start();
  ```

- 或者用Lambda表达式(Java8以上)

  ```java
  new Thread(()->{...}).start();
  ```

  例如：

  ```java
  new Thread(()-> {
    	System.out.println("Hello world!");
  }).start();
  ```

  ​

#### 使用多个线程

- TestThread.java

  ```java
  import java.util.*;
  import java.text.*;

  class Counter implements Runnable {
      int id;
      Counter(int id) {
          this.id = id;
      }
      public void run() {
          int i = 0;
          while(i++ <= 10) {
              System.out.println("ID: " + id + "  No. " + i);
              try {
                  Thread.sleep(10);
              } catch(InterruptedException e) { }
          }
      }
  }

  class TimeDisplay implements Runnable {
      public void run() {
          int i = 0;
          while (i++ <= 3) {
              System.out.println(new SimpleDateFormat().format(new Date()));
              try {
                  //40毫秒
                  Thread.sleep(40);
              } catch (InterruptedException e) { }
          }
      }
  }

  public class TestThread {
      public static void main(String[] args) {
          Counter c1 = new Counter(1);
          Thread t1 = new Thread(c1);
          Thread t2 = new Thread(c1);
          Thread t3 = new Thread(c1);
          Counter c2 = new Counter(2);
          Thread t4 = new Thread(c2);
          Thread t5 = new Thread(c2);
          Thread t6 = new Thread(c2);
          TimeDisplay timer = new TimeDisplay();
          Thread t7 = new Thread(timer);
          t1.start();
          t2.start();
          t3.start();
          t4.start();
          t5.start();
          t6.start();
          t7.start();
      }
  }
  ```




### 7.02 线程的控制

- 线程的状态与生命周期

  ```mermaid
  graph TB
  	A((创建))-->|start| B[就绪状态]
  	B---|调度| C[运行状态]
  	D[阻塞状态]-->|阻塞解除|B
  	C-->|致阻塞的状态|D
  	C-->F((终止))
  ```

- 对线程的基本控制

  - 线程的启动：start()

  - 线程的结束：设定一个**标记变量**，以结束相应的循环及方法

  - 暂时阻止线程的执行：

    ```java
    try {
      	Thread.sleep(1000);
    } catch (InterruptedException e) { }
    ```

- 线程的优先级

  - 设定线程的优先级：
    - setPriority(int priority)方法
    - MIN_PRIORITY(最小优先级 1), MAX_PRIORITY(最大优先级 10), NORM_PRIORITY(普通优先级 5)
    - 一个线程的缺省优先级是5

- 线程有两种

  - 普通线程(非Daemon线程)

    - 在Java程序中，若还有非Daemon线程，则整个程序就不会结束

  - Daemon线程(守护线程，后台线程)

    - 如果普通线程结束了，则后台线程自动终止
    - 注：垃圾回收线程是后台线程

  - 使用 **setDaemon(true);** 将线程设为Daemon线程

  - 例子:

    ```java
    import java.util.*;

    public class TestThreadDaemon {
        public static void main(String[] args) {
            Thread t = new MyThread();
            t.setDaemon(true);
            t.start();

            System.out.println("Main--" + new Date());
            try {
                Thread.sleep(500);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            System.out.println("Main End");
        }
    }

    class MyThread extends Thread {
        public void run() {
            for(int i = 0; i < 10; i++) {
                System.out.println(i + "--" + new Date());
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }
    ```

  ​

### 7.03 线程的同步

- 同时运行的线程需要共享数据就必须考虑其他线程的状态与行为，这时就需要实现同步

- Java引入了互斥锁的概念，来保证共享数据操作的完整性

  - **每个对象**都对应于一个monitor(监视器)，它上面一个称为”互斥锁(lock, mutex)“的标记，这个标记用来保证在任一时刻，只能有一个线程访问该对象
  - 关键字**synchronized**用来与对象的互斥锁联系。当调用方法时，调用线程进入对象监视器，对象监视器锁住对象，在对象被锁时，其他线程不能进入方法，也不能进入对象定义的其他同步方法；当线程从方法返回时，监视器为对象解锁，允许下一个线程使用对象

- **synchronized**的用法

  - 对代码片段：

    ```java
    synchronized(对象){...}
    ```

  - 在类中创建synchronized方法

    - synchronized 放在方法声明中，例如：

      ````java
      public synchronized void push(char c) {...}
      ````

    - 相当于synchronized(this)，表示整个方法为同步方法

  - 当需要对某些不被synchronized修饰的方法的访问进行同步，只需要把这个方法的调用放入synchronized代码块中即可

    ```java
    synchronized(对象){...}
    ```

  - 例：SyncCounter.java

    ```java
    public class SyncCounter {
        public static void main(String[] args) {
            Num num = new Num();
            Thread counter1 = new Counter(num);
            Thread counter2 = new Counter(num);
            for(int i = 0; i < 10; i++) {
              	//在num对象上对testEquals()的调用被同步
                synchronized(num) {
                    num.testEquals();
                }
                try {
                    Thread.sleep(100);
                } catch(InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    class Num {
        private int x = 0;
        private int y = 0;
      	//increase()被同步
        synchronized void increase() {
            x++;
            y++;
        }
      	//testEquals()没有被同步
        boolean testEquals() {
            boolean ok = (x==y);
            System.out.println(x + "," + y + " : " + ok);
            return ok;
        }
    }

    class Counter extends Thread {
        Counter(Num num) {
            num.increase();
        }
    }
    ```

- Java使用wait()、notify()和notifyAll()方法支持线程间通信

  - 所有对象都有wait()、notify()和notifyAll()方法，因为它们是由Object类实现的，这些方法只能在同步环境中被调用

    ```java
    //一直等待，直到其他线程调用此对象的notify()或notifyAll()方法
    public final void wait() throws InterruptedException
    //一直等待，直到其他线程调用此对象的notify()或notifyAll()方法，或超过指定的时间量 timeout (以毫秒为单位)
    public final void wait(long timeout) throws InterruptedException
    //一直等待，直到其他线程调用此对象的notify()或notifyAll()方法，或超过指定的时间量 timeout*1000000+nanos (以纳秒为单位)
    public final void wait(long timeout, int nanos) throws InterruptedException
    //恢复一个等待线程
    public final void notify()
    //通知所有线程，具有最高优先级的线程获得对象的访问权
    public final void notifyAll()
    ```

  - 在极少数情况下，等待线程会被伪装的唤醒任务唤醒（虚假唤醒），由于存在这种伪装唤醒的可能性，应该将对wait()的调用放在一个循环中，该循环会检查线程被唤醒的条件，若条件不满足，则继续等待

    ```java
    synchronized(obj) {
      	while(<condition does not hold>) {
          	obj.wait();
      	}
      	...
    }
    ```

  - Java里面可以将wait和notify放在synchronized里面，是因为Java是这样处理的：

    - 在synchronized代码被执行期间，线程调用对象的wait()方法，会释放对象锁标志，然后进入等待状态，然后由其它线程调用notify()或者notifyAll()方法通知正在等待的线程。

  - ThreadCom.java

    ```java
    //模拟钟表声音"Tick" "Tock"
    class TickTock {
        String state;
        synchronized void tick(boolean running) {
            if(!running) {
                state = "ticked";
                notify();
                return ;
            }
            System.out.print("Tick ");
            state = "ticked";
            //tick()通知tock()
            notify();
            try {
                while(!state.equals("tocked")){
                    //tick()等待tock()
                    wait();
                }
            } catch(InterruptedException e) {
                e.printStackTrace();
            }
        }

        synchronized void tock(boolean running) {
            if(!running) {
                state = "tocked";
                notify();
                return;
            }
            System.out.println("Tock");
            state = "tocked";
            //tock()通知tick()
            notify();
            try {
                while(!state.equals("ticked")) {
                    //tock()等待tick()
                    wait();
                }
            } catch(InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    class MyThread implements Runnable {
        Thread thrd;
        TickTock ttOb;

        MyThread(String name, TickTock tt) {
            //分配新的Thread对象，将this作为其运行对象，将name作为其名称
            thrd = new Thread(this, name);
            ttOb = tt;
            thrd.start();
        }

        public void run() {
            if(thrd.getName().compareTo("Tick") == 0) {
                for(int i = 0; i < 5; i++) {
                    ttOb.tick(true);
                }
                ttOb.tick(false);
            } else {
                for(int i = 0; i < 5; i++) {
                    ttOb.tock(true);
                }
                ttOb.tock(false);
            }
        }
    }

    class ThreadCom {
        public static void main(String[] args) {
            TickTock tt = new TickTock();
            MyThread mt1 = new MyThread("Tick", tt);
            MyThread mt2 = new MyThread("Tock", tt);

            try {
                //join()方法用来合并线程，即等待该线程终止，再恢复当前线程的运行
                mt1.thrd.join();
                mt2.thrd.join();
            } catch(InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
    ```

- 死锁：一个线程等待另一个线程，而后者又在等待前者，因此两个线程都被挂起，相互等待，谁也执行不了

- 竞争条件(race condition)：当两个(或更多个)线程尝试同时访问共享资源，但是又没有进行合适的同步时，就会发生竞争条件

  例如：当一个线程增加变量的当前值时，另一个线程可能在向这个变量写入新值，如果没有同步，变量的新值取决于线程的执行顺序(是第一个线程先写入值，还是第二个线程先写入值？)。发生这样的情况，就称这两个线程在“相互竞争”




#### 使用主线程

所有的Java程序都至少有一个执行线程，名为主线程，它是程序在开始运行时自动分配给程序的。

- 访问主线程，必须有一个引用主线程的Thread对象，这可以通过调用Thread的静态成员方法currentThread()来完成：

  ```java
  static Thread currentThread()
  ```


- UseMain.java

  ```java
  class UseMain {
      public static void main(String[] args) {
          Thread thrd;

          thrd = Thread.currentThread();

          //打印线程名称
          System.out.println("Main thread is called: " + thrd.getName());
          //打印线程优先级
          System.out.println("Priority: " + thrd.getPriority());
          
          System.out.println();

          System.out.println("Setting name and priority.\n");
          thrd.setName("Thread #1");
          //设置主线程优先级为 普通优先级+4 
          thrd.setPriority(Thread.NORM_PRIORITY + 4);

          System.out.println("Main thread is now called: " + thrd.getName());
          System.out.println("Priority is now: " + thrd.getPriority());
      }
  }
  ```

  程序输出：

  > Main thread is called: main
  > Priority: 5
  >
  > Setting name and priority.
  >
  > Main thread is now called: Thread #1
  > Priority is now: 9

- 对于主线程上执行的操作，要特别小心

  例如：在UseMain.java的main()末尾添加如下代码

  ```java
  try {
    	thrd.join();
  } catch(InterruptedException e) {
    	e.printStackTrace();
  }
  ```

  那么，程序永远不会终止，因为它会一直等待主线程结束





## 8. 网络编程

### 8.01 网络基础

- TCP/IP参考模型：

  |   应用层    |
  | :------: |
  |   传输层    |
  |   网络层    |
  | 物理+数据链路层 |

- IP(Internet Protool)：提供了独一无二的IP地址

- TCP(transmission control protool)：专门设计用于在不可靠的因特网上提供可靠的、端到端的字节流通信的协议。它是一种面向连接的协议。TCP连接是字节流而非报文流。TCP协议可靠，但是速度慢

- UDP(user data protool)：UDP向应用程序提供了一种发送封装的原始IP数据报的方法、并且发送时无需建立连接。是一种不可靠的连接(会产生丢包)，但是速度快



### 8.02 Socket

- 两个Java应用程序可通过一个双向的网络通信连接实现数据交换，这个双向链路的一端称为一个Socket

- Socket通常用来实现client-server连接

- java.net包中定义的两个类Socket和ServerSocket，分别用来实现双向连接的client和server端

- 建立连接时所需的寻址信息为远程计算机的IP地址和端口号(Port number)

  - TCP和UDP端口是分开的，各有65536个端口

- TCPServer.java  TCPClient.java

  ```java
  import java.net.*;
  import java.io.*;

  public class TCPServer {
      public static void main(String[] args) throws Exception {
          //创建绑定到特定端口(端口号6666)的服务器套接字
          ServerSocket ss = new ServerSocket(6666);
          while(true) {
              //accept()方法侦听并接受到此套接字的连接。此方法在连接传入之前一直阻塞。
              Socket s = ss.accept();
              System.out.println("a client connect!");
              DataInputStream dis = new DataInputStream(s.getInputStream());
              //readUTF()方法也是阻塞的
              System.out.println(dis.readUTF());
              dis.close();
              s.close();
          }
      }
  }
  ```

  ​

  ```java
  import java.net.*;
  import java.io.*;

  public class TCPClient {
      public static void main(String[] args) throws Exception {
          //创建一个流套接字并将其连接到指定主机上(IP地址：127.0.0.1(本机IP))的指定端口号(6666)。
          Socket s = new Socket("127.0.0.1", 6666);
          //getOutputStream()用于返回此套接字的输出流。
          OutputStream os = s.getOutputStream();
          DataOutputStream dos = new DataOutputStream(os);
          dos.writeUTF("hello server!");
          dos.flush();
          dos.close();
          s.close();
      }
  }
  ```

- TestServer.java  TestClient.java

  ```java
  import java.net.*;
  import java.io.*;

  public class TestServer {
      public static void main(String[] args) {
          try {
              ServerSocket ss = new ServerSocket(8888);
              while(true) {
                  Socket s = ss.accept();
                  OutputStream os = s.getOutputStream();
                  DataOutputStream dos = new DataOutputStream(os);
                  dos.writeUTF("Hello," + s.getInetAddress() + "port#" + s.getPort() + " bye-bye!");
                  dos.close();
                  s.close();
              }
          } catch(IOException e) {
              e.printStackTrace();
          }
      }
  }
  ```

  ```java
  import java.net.*;
  import java.io.*;

  public class TestClient {
      public static void main(String[] args) {
          try {
              Socket s = new Socket("127.0.0.1", 8888);
              InputStream is = s.getInputStream();
              DataInputStream dis = new DataInputStream(is);
              System.out.println(dis.readUTF());
              dis.close();
              s.close();
          } catch(ConnectException e) {
              e.printStackTrace();
          } catch(IOException e) {
              e.printStackTrace();
          }
      }
  }
  ```

  ​

### 8.03 UDP

- TestUDPServer.java

  ```java
  import java.net.*;

  public class TestUDPServer {
      public static void main(String[] args) throws Exception {
          byte[] buf = new byte[1024];
          DatagramPacket dp = new DatagramPacket(buf, buf.length);
          DatagramSocket ds = new DatagramSocket(5678);
          while(true) {
              //接收数据，是阻塞方法
              ds.receive(dp);
              //getLength()方法返回实际接收了多少数据
              System.out.println(new String(buf, 0, dp.getLength()));
          }
      }
  }
  ```

- TestUDPClient.java

  ```java
  import java.net.*;

  public class TestUDPClient {
      public static void main(String[] args) throws Exception {
          byte[] buf = (new String("Hello")).getBytes();
          DatagramPacket dp = new DatagramPacket(buf, buf.length, new InetSocketAddress("127.0.0.1", 5678));
          DatagramSocket ds = new DatagramSocket(9999);
          ds.send(dp);
          ds.close();
      }
  }
  ```

  ​



## 9. GUI(Graphics User Interface 图形用户界面)

### 9.01 实现界面的三步曲

1. 创建**组件**(Component)
   - 穿件组成界面的各种元素，如按钮、文本框等
2. 指定**布局**(Layout)
   - 根据具体需要排列它们的位置关系
3. 响应**事件**(Event)
   - 定义图形用户界面的时间和各界面元素对不同事件的响应，从而实现图形用户界面与用户的交互功能



#### Eclipse中的窗体设计

- 项目上右键 New->Other->Windows Builder->—Swing Designer—Jframe

  其顶部可切换source/design模式

- design模式下：

  - 在窗体上右键，Layout
  - 加上按钮等组件
  - 添加事件 Add New Event Handler



### 9.02 AWT(Abstract Window Toolkit)

- AWT包括了很多类和接口，用于Java Application的GUI编程
- 使用AWT所涉及的类一般在java.awt包及其子包中
- Component(所有图形元素的父类，抽象类)和Container(容器，Component的一个子类)是AWT中的两个核心类
- AWT并未完全跨平台

#### Component & Container

- Java图形用户界面的最基本组成部分是Component，Component类及其子类的对象用来描述以图形化的方式显示在屏幕上并能与用户进行交互的GUI元素，例如一个按钮、一个标签等
- 一般的Component对象不能独立地显示出来，必须将“放在”某一的Container对象中才可以显示出来
- Container是Component子类，Container子类对象可以“容纳”别的Component对象
- Container对象可使用方法add(...)向其中添加其他Component对象
- Container是Component的子类，因此Container对象也可以被当作Component对象添加到其他Container对象中
- 有两种常用的Container
  - Window：其对象表示自由停泊的顶级窗口
  - Panel：其对象可作为容纳其他Component对象，但不能独立存在，必须添加到其他Container中(如Window或Applet)



### 9.03 Frame & Panel

#### Frame

- Frame是Window的子类，由Frame或其子类创建的对象为一个窗体，Frame是一个顶级窗口，缺省布局管理器为BorderLayout

- Frame的常用构造方法：

  > Frame()
  >
  > Frame(String s)  创建标题栏为字符串s的窗口

- TestFrame.java

  ```java
  import java.awt.*;

  public class TestFrame {
      public static void main(String[] args) {
          Frame f = new Frame("My First Test");
          //设置窗口宽度为170,高度为100,单位为像素
          f.setSize(170, 100);
          //设置窗口出现的位置，原点在左上角，默认出现位置为左上角(0, 0)
          f.setLocation(300, 300);
          //设置背景色
          f.setBackground(Color.blue);
          //设置为不能改变窗口大小
          f.setResizable(false);
          //设置是窗口可见
          f.setVisible(true);
      }
  }
  ```

- TestMultiFrame.java

  ```java
  import java.awt.*;

  public class TestMultiFrame {
      public static void main(String[] args) {
          MyFrame f1 = new MyFrame(100, 100, 200, 200, Color.BLUE);
          MyFrame f2 = new MyFrame(300, 100, 200, 200, Color.YELLOW);
          MyFrame f3 = new MyFrame(100, 300, 200, 200, Color.GREEN);
          MyFrame f4 = new MyFrame(300, 300, 200, 200, Color.MAGENTA);
      }
  }

  class MyFrame extends Frame {
      static int id = 0;
      MyFrame(int x, int y, int w, int h, Color color) {
          super("MyFrame " + (++id));
          setBackground(color);
          //设置布局管理器为空
          setLayout(null);
          //设置出现的位置(x, y)和窗口大小(w, h)
          setBounds(x, y, w, h);
          setVisible(true);
      }
  }
  ```



#### Panel

- Panel对象可以看成是可以容纳Component的空间，但Panel无法单独显示，必须添加到某个容器中

- Panel对象可以拥有自己的布局管理器，默认布局管理器为FlowLayout

- Panel的构造方法

  > Panel()  使用默认的FlowLayout类布局管理器初始化
  >
  > Panel(LayoutManager layout)  使用指定的布局管理器初始化

- TestPanel.java

  ```java
  import java.awt.*;

  public class TestPanel {
      public static void main(String[] args) {
          Frame f = new Frame("Java Frame with Panel");
          Panel p = new Panel(null);
          f.setLayout(null);
          f.setBounds(300, 300, 500, 500);
          f.setBackground(new Color(0, 0, 102));
          p.setBounds(50, 50, 400, 400);
          p.setBackground(new Color(204, 204, 255));
          f.add(p);
          f.setVisible(true);
      }
  }
  ```

- TestMultiPanel.java

  ```java
  import java.awt.*;

  public class TestMultiPanel {
      public static void main(String[] args) {
          new MyFrame("MyFrameWithPanel", 300, 300, 400, 300);
      }
  }

  class MyFrame extends Frame {
      private Panel p1, p2, p3, p4;

      MyFrame(String s, int x, int y, int w, int h) {
          super(s);
          setLayout(null);
          p1 = new Panel(null);
          p2 = new Panel(null);
          p3 = new Panel(null);
          p4 = new Panel(null);
          p1.setBounds(0, 0, w/2, h/2);
          p2.setBounds(0, h/2, w/2, h/2);
          p3.setBounds(w/2, 0, w/2, h/2);
          p4.setBounds(w/2, h/2, w/2, h/2);
          p1.setBackground(Color.BLUE);
          p2.setBackground(Color.GREEN);
          p3.setBackground(Color.YELLOW);
          p4.setBackground(Color.MAGENTA);
          add(p1);
          add(p2);
          add(p3);
          add(p4);
          setBounds(x, y, w, h);
          setVisible(true);
      }
  }
  ```

  ​

### 9.04 布局管理器

- Java语言中，提供了布局管理器类的对象

  - 管理Component在Container中的布局，负责各个组件的大小和位置，用户无法在这种情况下设置组件大小和位子属性，如果试图使用setLocation()，setSize()，setBounds()等方法，会被布局管理器覆盖
  - 每个Container都有一个布局管理器对象，当容器需要对某个组件进行定位或判断其大小尺寸时，就会调用其对应的布局管理器，调用Container的setLayout方法改变其布局管理器对象

- AWT提供了5种布局管理器类：

  > FlowLayout
  >
  > BorderLayout
  >
  > GridLayout
  >
  > GardLayout
  >
  > GridBagLayout



#### FlowLayout

- FlowLayout是Panel类的默认布局管理器

  - FlowLayout布局管理器对组件逐行定位，行内从左到右，一行排满后换行
  - 不改变组件的大小，按组件原有尺寸显示组件，可设置不同的组件间距，行距以及对齐方式

- FlowLayout布局管理器默认的对其方式是居中

- FlowLayout的构造方法

  ```java
  //右对齐，组件之间水平间距20个像素，垂直间距40个像素
  new FlowLayout(FlowLayout.RIGHT, 20, 40);
  //左对齐，水平和垂直间距为缺省值(5)
  new FlowLayout(FlowLayout.LEFT);
  //使用缺省的居中对齐方式，水平和垂直间距为缺省值(5)
  new FlowLayout();
  ```

- TestFlowLayout.java

  ```java
  import java.awt.*;

  public class TestFlowLayout {
      public static void main(String[] args) {
          Frame f = new Frame("Flow Layout");
          Button button1 = new Button("ok");
          Button button2 = new Button("open");
          Button button3 = new Button("close");
          f.setLayout(new FlowLayout());
          f.add(button1);
          f.add(button2);
          f.add(button3);
          f.setSize(300, 100);
          f.setVisible(true);
      }
  }
  ```

- TestFlowLayout2.java

  ```java
  import java.awt.*;

  public class TestFlowLayout2 {
      public static void main(String[] args) {
          Frame f = new Frame("Java Frame");
          FlowLayout fl = new FlowLayout(FlowLayout.CENTER, 20, 40);
          f.setLayout(fl);
          f.setLocation(300, 400);
          f.setSize(300, 200);
          f.setBackground(new Color(204, 204, 255));
          for(int i = 1; i <= 7; i++) {
              f.add(new Button("BUTTON" + i));
          }
          f.setVisible(true);
      }
  }
  ```

  ​

#### BorderLayout

- BorderLayout是Frame类的默认布局管理器

- BorderLayout将整个容器的布局划分成**东(EAST)**、**西(WEST)**、**南(SOUTH)**、**北(NORTH)**、**中(CENTER)**五个区域，组件只能被添加到指定的区域

- 如不指定组件的加入部位，则默认加入到CENTER区

- 每个区域只能加入一个组件，如加入多个，则先前加入的会被覆盖

- BorderLayout型布局容器尺寸缩放原则：

  - 北、南两个区域在水平方向缩放
  - 东、西两个区与在垂直方向缩放
  - 中部可在两个方向上缩放

- TestBorderLayout.java

  ```java
  import java.awt.*;

  public class TestBorderLayout {
      public static void main(String[] args) {
          Frame f = new Frame("Border Layout");
          Button bn = new Button("BN");
          Button bs = new Button("BS");
          Button bw = new Button("BW");
          Button be = new Button("BE");
          Button bc = new Button("BC");

          f.add(bn, BorderLayout.NORTH);
          f.add(bs, BorderLayout.SOUTH);
          f.add(bw, BorderLayout.WEST);
          f.add(be, BorderLayout.EAST);
          f.add(bc, BorderLayout.CENTER);

          //也可以这么写，但是不推荐，因为若第二个参数写错，如NORTH写成NOTH，编译时不会报错，运行时会出错，而上面那种方法若写错编译时就会报错
          /*
          f.add(bn, "NORTH");
          f.add(bs, "SOUTH");
          f.add(bw, "WEST");
          f.add(be, "EAST");
          f.add(bc, "CENTER");
          */

          f.setSize(200, 200);
          f.setVisible(true);
      }
  }
  ```



#### GridLayout

- GridLayout型布局管理器将空间划分成规则的矩形网格，每个单元格区域大小相等。组件被添加到每个单元格中，先从左到右填满一行后换行，再从上到下

- GridLayout构造方法

  > GridLayout()  默认的网格布局，每个组件占据一行一列
  >
  > GridLayout(int rows, int cols)  创建具有指定行数和列数的网格布局，给布局中所有组件分配相等的大小，rows和cols中一个可以为零(但不能两者同时为零)
  >
  > GridLayout(int rows, int cols, int hgap, int vgap)  创建具有指定行数和列数的网格布局，给布局中所有组件分配相等的大小，此外，将水平间距(列与列之间)和垂直间距(行与行之间)设置为指定值

- TestGridLayout.java

  ```java
  import java.awt.*;

  public class TestGridLayout {
      public static void main(String[] args) {
          Frame f = new Frame("GridLayout Example");
          Button b1 = new Button("b1");
          Button b2 = new Button("b2");
          Button b3 = new Button("b3");
          Button b4 = new Button("b4");
          Button b5 = new Button("b5");
          Button b6 = new Button("b6");

          f.setLayout(new GridLayout(3, 2));
          f.add(b1);
          f.add(b2);
          f.add(b3);
          f.add(b4);
          f.add(b5);
          f.add(b6);

          //根据窗口里面的布局及组件的preferedSize(最佳大小)来设置frame的大小
          f.pack();
          f.setVisible(true);
      }
  }
  ```



#### 例子：TenButtons.java

```java
import java.awt.*;

public class TenButtons {
    public static void main(String[] args) {
        Frame f = new Frame("Java Frame");
        f.setLayout(new GridLayout(2, 1));
        f.setLocation(300, 400);
        f.setSize(300, 200);
        f.setBackground(new Color(204, 204, 255));

        Panel p1 = new Panel(new BorderLayout());
        Panel p2 = new Panel(new BorderLayout());
        Panel p11 = new Panel(new GridLayout(2, 1));
        Panel p21 = new Panel(new GridLayout(2, 2));

        p1.add(new Button("BUTTON"), BorderLayout.WEST);
        p1.add(new Button("BUTTON"), BorderLayout.EAST);
        p11.add(new Button("BUTTON"));
        p11.add(new Button("BUTTON"));
        p1.add(p11, BorderLayout.CENTER);
        p2.add(new Button("BUTTON"), BorderLayout.WEST);
        p2.add(new Button("BUTTON"), BorderLayout.EAST);
        for(int i = 1; i <= 4; i++) {
            p21.add(new Button("BUTTON"));
        }
        p2.add(p21, BorderLayout.CENTER);
        f.add(p1);
        f.add(p2);
        f.setVisible(true);
    }
}
```



### 9.05 事件模型

- 事件监听

  - 想监听某个事件，就必须实现某个一直对应的接口

  ```mermaid
  graph TB
  	A[事件源对象]-->|当某种事件发生时 向监听器传送某种事件对象 进行事件对象后进行某种处理|B[实现了某种监听器接口的类得对象]
  	B-->|告知事件源对象|A
  ```

  ​

  - TestActionEvent.java

    ```java
    //java.awt和java.awt.event是两个不同的包，没有包含关系
    import java.awt.*;
    import java.awt.event.*;

    public class TestActionEvent {
        public static void main(String[] args) {
            Frame f = new Frame("Test");
            Button b = new Button("Press Me!");
            Monitor bh = new Monitor();
            b.addActionListener(bh);
            f.add(b, BorderLayout.CENTER);
            f.pack();
            f.setVisible(true);
        }
    }

    class Monitor implements ActionListener {
        public void actionPerformed(ActionEvent e) {
            System.out.println("a button has been pressed");
        }
    }
    ```

  - TestActionEvent2.java

    ```java
    import java.awt.*;
    import java.awt.event.*;

    public class TestActionEvent2 {
        public static void main(String[] args) {
            Frame f = new Frame("Test");
            Button b1 = new Button("start");
            Button b2 = new Button("stop");
            Monitor2 bh = new Monitor2();

            b1.addActionListener(bh);
            b2.addActionListener(bh);
            //设置此按钮激发的动作事件的命令名称，可用于区分按钮
            b2.setActionCommand("game over");

            f.add(b1, "North");
            f.add(b2, "Center");
            f.pack();
            f.setVisible(true);
        }
    }

    class Monitor2 implements ActionListener {
        public void actionPerformed(ActionEvent e) {
            System.out.println("a button has been pressed," + "the relative info is:\n " + e.getActionCommand());
        }
    }
    ```

    ​

  - TFActionEvent.java

    ```java
    import java.awt.*;
    import java.awt.event.*;

    public class TFActionEvent {
        public static void main(String[] args) {
            new TFFrame();
        }
    }

    class TFFrame extends Frame {
        TFFrame() {
            TextField tf = new TextField();
            add(tf);
            tf.addActionListener(new TFActionListener());
            pack();
            setVisible(true);
        }
    }

    class TFActionListener implements ActionListener {
        public void actionPerformed(ActionEvent e) {
            //getSource()方法返回事件源(getSource()是Object类定义的方法，返回的是Object类型的对象)
            TextField tf = (TextField)e.getSource();
            System.out.println(tf.getText());
            tf.setText("");
        }
    }
    ```

    ​

#### 持有对方引用

- TFMath.java

  ```java
  import java.awt.*;
  import java.awt.event.*;

  public class TFMath {
      public static void main(String[] args) {
          new TFFrame().launchFrame();
      }
  }

  class TFFrame extends Frame {
      TextField num1, num2, num3;

      public void launchFrame() {
          num1 = new TextField(10);
          num2 = new TextField(10);
          num3 = new TextField(15);

          Label lblPlus = new Label("+");
          Button btnEqual = new Button("=");

          btnEqual.addActionListener(new MyMonitor(this));

          setLayout(new FlowLayout());
          add(num1);
          add(lblPlus);
          add(num2);
          add(btnEqual);
          add(num3);
          pack();
          setVisible(true);
      }
  }

  class MyMonitor implements ActionListener {
      TFFrame tf = null;

      //传入TFFrame的引用
      public MyMonitor(TFFrame tf) {
          this.tf= tf;
      }

      public void actionPerformed(ActionEvent e) {
          int n1 = Integer.parseInt(tf.num1.getText());
          int n2 = Integer.parseInt(tf.num2.getText());
          //加上""是为了转换为字符串
          tf.num3.setText("" + (n1 + n2));
      }
  }
  ```



#### 内部类

- TFMath2.java

  ```java
  import java.awt.*;
  import java.awt.event.*;

  public class TFMath2 {
      public static void main(String[] args) {
          new TFFrame().launchFrame();
      }
  }

  //包装类
  class TFFrame extends Frame {
      TextField num1, num2, num3;

      public void launchFrame() {
          num1 = new TextField(10);
          num2 = new TextField(10);
          num3 = new TextField(15);

          Label lblPlus = new Label("+");
          Button btnEqual = new Button("=");

          btnEqual.addActionListener(new MyMonitor());

          setLayout(new FlowLayout());
          add(num1);
          add(lblPlus);
          add(num2);
          add(btnEqual);
          add(num3);
          pack();
          setVisible(true);
      }

      //内部类
      class MyMonitor implements ActionListener {
          public void actionPerformed(ActionEvent e) {
              int n1 = Integer.parseInt(num1.getText());
              int n2 = Integer.parseInt(num2.getText());
              num3.setText("" + (n1 + n2));
          }
      }
  }
  ```

- 好处：

  - 可以方便地访问包装类的成员
  - 可以更清楚地组织逻辑，防止不应该被其他类访问的类进行访问

- 何时使用：

  - 该类不允许或不需要其它类进行访问时



### 9.05 Graphics

- 每个Component都有一个paint(Graphics g)用于实现绘图的目的，每次重画该Component时都自动调用paint方法

- TestPaint.java

  ```java
  import java.awt.*;

  public class TestPaint {
      public static void main(String[] args) {
          new PaintFrame().launchFrame();
      }
  }

  class PaintFrame extends Frame {
      public void launchFrame() {
          setBounds(200, 200, 640, 480);
          setVisible(true);
      }

      //paint()方法是自动执行的
      public void paint(Graphics g) {
          Color c = g.getColor();
          g.setColor(Color.red);
          //fillOval(50, 50, 30, 30)方法画位于(50, 50, 30, 30)的长方形的内切椭圆
          g.fillOval(50, 50, 30, 30);
          g.setColor(Color.green);
          g.fillRect(80, 80, 40, 40);
          g.setColor(c);
      }
  }
  ```

  ​

### 9.06 鼠标事件适配器

- 抽象类java.awt.event.MouseAdapter实现了MouseListener接口，可以使用其子类作为MouseEvent的监听器，只要重写其相应的方法即可

- 对于其他的监听器，也有对应的适配器

- 使用适配器可以避免监听器类定义没有必要的空方法

- repaint()方法先调用了update()方法再调用paint()方法

- MyMouseAdapter.java

  ```java
  import java.awt.*;
  import java.awt.event.*;
  import java.util.*;

  public class MyMouseAdapter {
      public static void main(String[] args) {
          new MyFrame("drawing...");
      }
  }

  class MyFrame extends Frame {
      ArrayList points = null;
      MyFrame(String s) {
          super(s);
          points = new ArrayList();
          setLayout(null);
          setBounds(300, 300, 400, 300);
          this.setBackground(new Color(204, 204, 255));
          setVisible(true);
          this.addMouseListener(new Monitor());
      }

      public void paint(Graphics g) {
          Iterator i = points.iterator();
          while(i.hasNext()) {
              Point p = (Point)i.next();
              g.setColor(Color.BLUE);
              g.fillOval(p.x, p.y, 10, 10);
          }
      }

      public void addPoint(Point p) {
          points.add(p);
      }
  }

  class Monitor extends MouseAdapter {
      public void mousePressed(MouseEvent e) {
          MyFrame f = (MyFrame)e.getSource();
          f.addPoint(new Point(e.getX(), e.getY()));
          //repaint()方法用来对当前窗口进行整个重画 (每加入一个点重画一次)
          f.repaint();
      }
  }
  ```

  ​


### 9.07 Window 事件

- TestWindowClose.java

  ```java
  import java.awt.*;
  import java.awt.event.*;

  public class TestWindowClose {
      public static void main(String[] args) {
          new MyFrame("MyFrame");
      }
  }

  class MyFrame extends Frame {
      MyFrame(String s) {
          super(s);
          setLayout(null);
          setBounds(300, 300, 400, 300);
          this.setBackground(new Color(204, 204, 255));
          setVisible(true);
          //匿名类，将这个类当作WindowAdapter类来用
          this.addWindowListener(new WindowAdapter() {
            	//重写了windowClosing方法
              public void windowClosing(WindowEvent e) {
                  setVisible(false);
                  System.exit(0);
              }
          });
      }
  }
  ```



#### 匿名类

- 匿名类是方法内的类(匿名的局部类)

- 匿名类只能是内部类

- 匿名类有两种创建方式：继承父类、单重实现接口

- TestAnonymous.java

  ```java
  import java.awt.*;
  import java.awt.event.*;

  public class TestAnonymous {
      Frame f = new Frame("Test");
      TextField tf = new TextField(10);
      Button b1 = new Button("Start");

      public TestAnonymous() {
          f.add(b1, "North");
          f.add(tf, "South");

          b1.addActionListener(new ActionListener() {
              private int i;
              public void actionPerformed(ActionEvent e) {
                  tf.setText(e.getActionCommand() + ++i);
              }
          });

          f.addWindowListener(new WindowAdapter() {
              public void windowClosing(WindowEvent e) {
                  System.exit(0);
              }
          });

          f.pack();
          f.setVisible(true);
      }

      public static void main(String[] args) {
          new TestAnonymous();
      }
  }
  ```




## 10. 反射机制

### 10.01 ClassLoader的类加载机制

- 程序的运行过程
    1. ClassLoader将.class文件Load到内存CodeSegment中
    2. 运行环境找到main方法开始执行
    3. 运行过程中会有更多的class被load到内存(原因：动态加载机制)
- ClassLoader的类加载机制
    - 并非一次性加载，用到的时候才加载(运行期间动态加载)
    - static语句块在加载后执行一次
        - 例:
            ```java
            public class Test{
                public class void main(String[] args) {
                    new A();
                    new A();
                }
            }

            class A {
                static {
                    System.out.println("AAAA");
                }
            }
            ```
            > 程序只输出了一条 AAAA

    - dynamic语句块(动态语句块)
        - 每次new新的对象都会执行
        - 例:
            ```java
            public class Test{
                public class void main(String[] args) {
                    new A(1);
                    new A(1);
                }
            }

            class A {
                //构造方法
                D(int i) {}

                //dynamic语句块
                {
                    System.out.println("AAAA");
                }
            }
            ```
            > 程序输出两条AAAA

### 10.02 JDK中的classloader的分类
- JDK中的classloader非常的多
- bootstrap class loader
    - 加载jdk中最核心的类
    - bootstrap class loader一般由C语言这样的本地语言实现
- extension class loader
    - 加载JDK的扩展类(这些扩展类位于jre/lib/ext目录下)
- application class loader
    - 加载用户自己定义的class
- other class loaders
    - SecureClassLoader
    - URLClassLoader
    - ...
- TestJDKClassLoader.java
    ```java
        public class TestJDKClassLoader {

            public static void main(String[] args) {
                // TODO Auto-generated method stub
                System.out.println(String.class.getClassLoader());
                System.out.println(com.sun.crypto.provider.DESedeKeyFactory.class.getClassLoader().getClass().getName());
                System.out.println(TestJDKClassLoader.class.getClassLoader().getClass().getName());
                System.out.println(ClassLoader.getSystemClassLoader().getClass().getName());
            }
        }
    ```
    输出：
    > null
    > sun.misc.Launcher$ExtClassLoader
    > sun.misc.Launcher$AppClassLoader
    > sun.misc.Launcher$AppClassLoader

- 加载过程：首先由bootstrap class loader加载其他的class loader，然后其他的class loader加载其他的class



### 10.03 JDK Class Loader的层次关系

- 所有的Class Loader都由ClassLoader类继承而来
- ClassLoader类有一个方法getParent()，可以得到当前Class Loader对象的引用指向的Class Loader对象(上一层的class loader)，这个对象之间的关系，并不是类的继承
- 加载过程:class loader在load class的时候首先找上一层的loader是否已经load过这个class了，如果已经load了，就不会再次load
    - 这么做的作用:安全性好
- TestClassLoader.java
    ```java
        public class TestClassLoader {
            public static void main(String[] args) {
                ClassLoader c = TestClassLoader.class.getClassLoader();
                while(c != null) {
                    System.out.println(c.getClass().getName());
                    c = c.getParent();
                }
            }
        }
    ```
    输出：
    > sun.misc.Launcher$AppClassLoader
    > sun.misc.Launcher$ExtClassLoader

### 10.04 反射
```java
    import java.lang.reflect.InvocationTargetException;
    import java.lang.reflect.Method;

    public class TestReflection {

        public static void main(String[] args) throws ClassNotFoundException, InstantiationException, IllegalAccessException, IllegalArgumentException, InvocationTargetException {
            // TODO Auto-generated method stub
            String str = "T";
            Class c = Class.forName(str);
            Object o = c.newInstance();
            Method[] methods = c.getMethods();
            for(Method m : methods) {
                if(m.getName().equals("mm")) {
                    m.invoke(o);
                }
                if(m.getName().equals("m1")) {
                    m.invoke(o, 1, 2);
                    for(Class paramType : m.getParameterTypes()) {
                        System.out.println(paramType.getName());
                    }
                }
                if(m.getName().equals("getS")) {
                    Class returnType = m.getReturnType();
                    System.out.println(returnType.getName());
                }
            }
        }

    }

    class T {
        static {
            System.out.println("T loaded!");
        }
        
        public T() {
            System.out.println("T constracted");
        }
        
        int i;
        String s;
        
        public void mm() {
            System.out.println("mm invoked!");
        }
        
        public void m1(int i, int j) {
            this.i = i + j;
            System.out.println(i);
        }
        
        public String getS() {
            return s;
        }
    }
```

