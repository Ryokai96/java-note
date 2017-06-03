# JavaScript 笔记

## 1. JavaScript简介

- JavaScript 是一种动态语言，它与HTML结合起来，用于增强功能，并提高交互性能

- 静态语言(如Java、C、C++)需要通过编译器，将代码编译为另外一种代码(如机器码、字节码)

  动态语言(如JavaScript)则通过解释器，直接解析代码并将代码运行结果输出

- JavaScript解析引擎和ECMAScript的关系:

  - ECMAScript定义了JavaScript的标准或规范
  - 通过ECMAScript制定的标准，各浏览器厂商造出了自己的**JavaScript解析引擎(解释器)**，虽然ECMAScript制定了标准，但是浏览器厂商并没有严格按照这个标准，所以同一段JavaScript代码在不同浏览器之间可能会有兼容性问题

- JavaScript的语法:

  - 基础语法
  - DOM(Document Object Model) 文档对象模型
  - BOM(Browser Object Model) 浏览器对象模型



#### 第一个JavaScript示例

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>My First JavaScript</title>
    <script type="text/javascript">
      	//alert() 方法用于显示带有一条指定消息和一个 OK 按钮的警告框(模态的)
        alert("Hello, JavaScript!");
        alert("Ok");
    </script>
</head>
<body>
第一个JavaScript
</body>
</html>
```



## 2. JavaScript 基本语法

### 2.01 变量

- JavaScript是一门弱类型的语言，所有的变量定义均以**var**来实现
- JavaScript区分大小写
- 虽然JavaScript可以不需定义即可直接使用变量，但不建议这么做



#### 示例: TestVar.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>变量定义</title>
    <script language="JavaScript">
        var greeting;
        greeting = "Welcome to the JS!";
        test = "just do it";	//变量未定义也可直接使用，但不建议这么做
    </script>
</head>
<body>
<script language="javascript">
    document.write(greeting + "<br>");
    document.write(test);
</script>
</body>
</html>
```



### 2.02 数组定义

- JavaScript的数组长度是可变的
- var arr = new Array(3)
  - Array相当于一个类，arr相当与Array的一个对象
  - arr[0] = 1; 相当于把arr对象中的一个名为0的成员变量的值设为1
  - 可通过arr.length取得或设置数组长度
  - length表示的是数组所占内存空间的数目，而不是数组中元素的个数

#### 示例: TestArray.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>JavaScript 数组</title>
    <script language="JavaScript">
        var arr = new Array(3);
        arr[0] = 1;
        arr[1] = 2;
        arr[2] = 3;
        arr[3] = 4;
    </script>
</head>
<body>
<script language="JavaScript">
    document.write(arr[3] + "<br>");	//4
    document.write(arr.length + "<br>");	//4
    arr.length = 10;
    document.write(arr.length);	//10
</script>
</body>
</html>
```

