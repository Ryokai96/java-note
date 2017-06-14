# Java 正则表达式

[TOC]

## 1. 简介

- 正则表达式(Regular Expression)
- 用途
  - 字符串匹配(字符匹配)
  - 字符串查找
  - 字符串替换
- 例如
  - IP地址是否正确
  - 从网页中揪出email地址
  - 从网页中揪出链接等
- 类
  - java.lang.String
  - java.util.regex.Pattern
  - java.util.regex.Matcher



- 示例: TestRegExp.java

  ```java
  import java.util.regex.Matcher;
  import java.util.regex.Pattern;

  public class TestRegExp {

  	public static void main(String[] args) {
  		//String.matches() 方法返回是否匹配指定的字符串，如果匹配则为true,否则为false
  		p("abc".matches("..."));	//...表示由三个字符构成，abc符合，所以返回true
  		
  		//String.replaceAll(String s1, String s2) 方法用于把符合s1的字符都替换成s2
  		p("a8729a".replaceAll("\\d", "-"));	// \d 代表一位数字
  		
  		//Pattern.compile(String regex) 方法把 正则表达式regex编译后返回一个匹配模式Pattern
  		Pattern p = Pattern.compile("[a-z]{3}");	//[a-z]{3} 表示三个字符组成的字符串，其中每个字符都是a-z中的一个字符
  		//Matcher类表示一个匹配器，matcher()方法用于匹配一个字符串，返回一个Matcher对象
  		Matcher m = p.matcher("fgh");
  		//Matcher.matches() 用于返回匹配结果，匹配返回true，否则返回false
  		p(m.matches());
  	}
  	
  	public static void p(Object o) {
  		System.out.println(o);
  	}
  }

  ```

  ​

## 2. 简单正则表达式

### 2.01 . * +

- 例: TestRegExp.java

```java
public class TestRegExp {

	public static void main(String[] args) {
		p("a".matches("."));	//.表示一个字符    true
		p("aa".matches("aa"));	//按字符匹配    true
		p("aaaa".matches("a*"));	//a*表示零个或多个a  true
		p("aaaa".matches("a+"));	//a+表示一个或多个a  true
		p("".matches("a*"));	//true
		p("aaaa".matches("a?"));	//a?表示一个或零个a  false
		p("".matches("a?"));	//true
		p("a".matches("a?"));	//true
		p("201323131231".matches("\\d{3,100}"));	// X{3,100}表示X出现3-100次，\d{3,100}表示3-100位的数字    true
		p("192.168.0.aaa".matches("\\d{1,3}\\.\\d{1,3}\\.\\d{1,3}\\.\\d{1,3}"));	//false
	}
	
	public static void p(Object o) {
		System.out.println(o);
	}
}
```



### 2.02 []范围

- 例: TestRegExp.java

```java
public class TestRegExp {

	public static void main(String[] args) {
		p("192".matches("[0-2][0-9][0-9]"));	//[0-2]表示范围0-2  true
		p("a".matches("[abc]"));	//[abc]表示abc中的一个字符    true
		p("a".matches("[^abc]"));	// ^位于[]中表示取反，[^abc]表示除abc外的一个字符    false
		p("A".matches("[a-zA-Z]"));	//[a-zA-Z]表示a-z或A-Z中的一个字符    true
		p("A".matches("[a-z]|[A-Z]"));	//与上一条一样    true
		p("A".matches("[a-z[A-Z]]"));	//与上一条一样    true
		p("R".matches("[A-Z&&[RFG]]"));	//[A-Z&&[RFG]]表示在A-Z中并且是RFG中的一个字符    true
	}
	
	public static void p(Object o) {
		System.out.println(o);
	}
}
```



### 2.03 \s \w \d \

- 例: TestRegExp.java

```java
public class TestRegExp {

	public static void main(String[] args) {
		p(" \n\r\t".matches("\\s{4}"));	// \s表示空白字符，\s{4}表示4个空白字符    true
		p(" ".matches("\\S"));	// \S表示非空白字符    false
		p("a_8".matches("\\w{3}"));	// \w表示字母、数字以及下划线_  true
		p("abc888&^%".matches("[a-z]{1,3}\\d+[&^#%]+"));	//a-z之间的字母出现1-3次，\\d+表示数字出现一次或多次，[&^#%]+表示&^#%中的出现一次或多次
//		p("\\".matches("\\"));	// \在正则表达式中也代表转义字符    报错
		p("\\".matches("\\\\"));	//true
	}
	
	public static void p(Object o) {
		System.out.println(o);
	}
}
```



### 2.04 Posix Style

- 例: TestRegExp.java

```java
public class TestRegExp {

	public static void main(String[] args) {
		p("a".matches("\\p{Lower}"));	// \p{Lower}代表小写字母    true
	}
	
	public static void p(Object o) {
		System.out.println(o);
	}
}
```



### 2.05 边界处理

- 例: TestRegExp.java

```java
public class TestRegExp {

	public static void main(String[] args) {
		p("hello sir".matches("^h.*"));	// ^表示一行的开头，^h.*表示以h开头，后面有零个或多个字符    true
		p("hello sir".matches(".*ir$"));	// $表示一行的结尾，.*ir$表示以ir结尾，前面有零个或多个字符    true
		p("hello sir".matches("^h[a-z]{1,3}o\\b.*"));	// \b表示一个单词的边界    true
		p("hellosir".matches("^h[a-z]{1,3}o\\b.*"));	// false
	}
	
	public static void p(Object o) {
		System.out.println(o);
	}
}

```



### 2.06 空行

- 例: TestRegExp.java

```java
public class TestRegExp {

	public static void main(String[] args) {
		p(" \n".matches("^[\\s&&[^\\n]]*\\n$"));	// 以空白字符且不是换行符\n开头，这样的字符出现零次或多次，且以换行符\n结尾
	}
	
	public static void p(Object o) {
		System.out.println(o);
	}
}
```



### 2.07 简单匹配email地址

- 例: TestRegExp.java

```java
public class TestRegExp {

	public static void main(String[] args) {
		// [\w[.-]]+表示字母、数字、下划线还有.和-出现一次或多次，\.表示一个.号
		p(".assdsaxcz-sad_@dasd.com".matches("[\\w[.-]]+@[\\w[.-]]+\\.[\\w]+"));	// true
	}
	
	public static void p(Object o) {
		System.out.println(o);
	}
}
```



## 3. Matcher类部分方法的使用

### 3.01 matches()、find()、reset()

- matches: 整个匹配，只有整个字符序列完全匹配成功，才返回True，否则返回False。但如果前部分匹配成功，将移动下次匹配的位置
- find: 部分匹配，从当前位置开始匹配，找到一个匹配的子串，将移动下次匹配的位置
- lookingAt: 部分匹配，总是从第一个字符进行匹配,匹配成功了不再继续匹配，匹配失败了,也不继续匹配
- reset: 给当前的Matcher对象配上个新的目标，目标是就该方法的参数；如果不给参数，reset会把Matcher设到当前字符串的开始处

TestRegExp.java

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class TestRegExp {

	public static void main(String[] args) {
		Pattern p = Pattern.compile("\\d{3,5}");
		String s = "123-34345-234-00";
		Matcher m = p.matcher(s);
		p(m.matches());	//匹配整个字符串，只有整个字符序列完全匹配成功，才返回True，但如果前部分匹配成功，将移动下次匹配的位置    false(下次匹配位置是-)
		p(m.find());	//从当前位置开始匹配，找到一个匹配的子串，将移动下次匹配的位置  true(此时匹配到的是34345)
		p(m.find());	//true(此时匹配到的是234)
		p(m.find());	//false
		p(m.find());	//false
		
		m.reset();	//重置匹配位置
		p(m.find());	//true(此时匹配到的是123)
		p(m.find());	//true(此时匹配到的是34345)
		p(m.find());	//true(此时匹配到的是234)
		p(m.find());	//false
		
		p(m.lookingAt());	//true
		p(m.lookingAt());	//true
		p(m.lookingAt());	//true
		p(m.lookingAt());	//true
	}
	
	public static void p(Object o) {
		System.out.println(o);
	}
}
```



### 3.02 start()、end()、group()

- 当使用matches()、lookingAt()、find()执行匹配操作后,就可以利用start()、end()得到更详细的信息


- start(): 返回匹配到的子字符串的第一个字符在字符串中的索引位置
- end(): 返回匹配到的子字符串的最后一个字符在字符串中的索引位置的下一个字符的索引位置
- group(): 返回匹配到的子字符串

TestRegExp.java

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class TestRegExp {

	public static void main(String[] args) {
		Pattern p = Pattern.compile("\\d{3,5}");
		String s = "123-34345-234-00";
		Matcher m = p.matcher(s);
		
		p(m.find());	//true
		p(m.start() + "-" + m.end());	//0-3
		p(m.group());	//123
		p(m.find());	//true
		p(m.start() + "-" + m.end());	//4-9
		p(m.group());	//34345
		p(m.find());	//true
		p(m.start() + "-" + m.end());	//10-13
		p(m.group());	//234
		p(m.find());	//false
//		p(m.start() + "-" + m.end());	//报异常
//		p(m.group());	//报异常
	}
	
	public static void p(Object o) {
		System.out.println(o);
	}
}
```



### 3.03 字符串操作

TextRegExp.java

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class TestRegExp {

	public static void main(String[] args) {
		Pattern p = Pattern.compile("java", Pattern.CASE_INSENSITIVE);	//CASE_INSENSITIVE表示忽略大小写
		Matcher m = p.matcher("java Java JAVa JaVa IloveJAVA you hateJava dsadasddds");
		
		p(m.replaceAll("JAVA"));	//replaceAll()方法表示替换所有匹配到的子串并返回替换后的字符串    JAVA JAVA JAVA JAVA IloveJAVA you hateJAVA dsadasddds
		
		m.reset();
		
		StringBuffer buf = new StringBuffer();
		int i = 0;
		while(m.find()) {
			i++;
			//第奇数个java大写，第偶数个java小写
			if(i%2 == 0) {
				m.appendReplacement(buf, "java");	//appendReplacement方法表示在Matcher.find()找到匹配的地方用"java"替换掉然后加进buf中去
			} else {
				m.appendReplacement(buf, "JAVA");
			}
		}
		p(buf);	//JAVA java JAVA java IloveJAVA you hatejava
		m.appendTail(buf);	//把未fine()到的字串加到buf中
		p(buf);	//JAVA java JAVA java IloveJAVA you hatejava dsadasddds
	}
	
	public static void p(Object o) {
		System.out.println(o);
	}
}
```



### 3.04 分组

- 正则表达式用 () 进行分组，每个组有自己的组号，左小括号 ( 的起始位置决定了这个组的组号，如一个组的左小括号 ( 为整个正则表达式的第一个左小括号 ( ，则这个组的组号为1

TestRegExp.java

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class TestRegExp {

	public static void main(String[] args) {
		Pattern p = Pattern.compile("(\\d{3,5})([a-z]{2})");	// ()用于分组
		String s = "123aa-34345bb-234cc-00";
		Matcher m = p.matcher(s);
		m.find();
		p(m.group());	//123aa
		p(m.group(1));	//123
		p(m.group(2));	//aa
	}
	
	public static void p(Object o) {
		System.out.println(o);
	}
}

```

