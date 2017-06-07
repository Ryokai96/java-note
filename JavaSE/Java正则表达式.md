# Java 正则表达式

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

### 2.01 MetaCharacters(元字符) . * +

- 例: TestRegExp.java

```java
package com.ryokai96.test;

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
package com.ryokai96.test;

public class TestRegExp {

	public static void main(String[] args) {
		p("192".matches("[0-2][0-9][0-9]"));	//[0-2]表示范围0-2  true
		p("a".matches("[abc]"));	//[abc]表示abc中的一个字符    true
		p("a".matches("[^abc]"));	//[^abc]表示除abc外的一个字符    false
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

