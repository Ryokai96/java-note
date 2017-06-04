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
        var arr2 = new Array(2, 3, 4, 5);
    </script>
</head>
<body>
<script language="JavaScript">
    document.write(arr[3] + "<br>");	//4
    document.write(arr.length + "<br>");	//4
    arr.length = 10;
    document.write(arr.length + "<br>");	//10
    var i = 0;
    for(i = 0; i < arr2.length; i++) {
        document.write(arr2[i] + "<br>");	//2 3 4 5
    }
</script>
</body>
</html>
```



### 2.03 字符串

#### 示例: TestString.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>字符串操作</title>
    <script language="JavaScript">
        var hello, greeting;
        hello = "Hello!";
        greeting = "Welcome to the JS!";
    </script>
</head>
<body>
<script language="JavaScript">
    document.write(hello + greeting + "<br>");   //字符串拼接
    document.write(greeting.substring(3, 9) + "<br>");   //字符串截取，截取从第4个字符起到第九个字符，所以这里输出的是 come t
    var i = 0;
    while(i < greeting.length) {
        document.write(greeting.charAt(i) + "<br>");    //greeting.charAt(i)表示greeting的第i个字符
    }
</script>
</body>
</html>
```



### 2.04 函数

#### 示例: TestFunc.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>函数</title>
    <script language="JavaScript">
        function test() {   //不带参数
            document.write("a");
            document.write("b");
        }
        function test2(x) { //带参数
            document.write(x);
        }
        function test3(y) { //带参数和返回值
            return y * 3.14;
        }
    </script>
</head>
<body>
<script language="JavaScript">
    test();
    test2("Hey Ryoukai!");
    document.write(test3(2));
</script>
</body>
</html>
```



### 2.05 JavaScript事件处理

- OnFocus: 事件在对象获得焦点时发生

#### 示例: TestOnFocus.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>OnFocus</title>
</head>
<body>
<form action="" name="test">
    <!-- onfocus事件在对象获得焦点时发生 -->
    <input type="text" name="userName" value="SXT" onfocus="JavaScript:alert(document.test.userName.value);">
</form>
</body>
</html>
```



- onclick: 事件会在对象被点击时发生

#### TestOnClick.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>OnClick</title>
</head>
<body>
<!--onclick 事件会在对象被点击时发生-->
<img src="https://ss1.bdstatic.com/70cFuXSh_Q1YnxGkpoWK1HF6hhy/it/u=1839352044,827442581&fm=117&gp=0.jpg" onclick="alert('ok');">
</body>
</html>
```



- onload、onunload:
  -  onload 属性在对象已加载时触发
  - onunload 事件在用户退出页面时发生

#### 示例: TestOnLoad.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>OnLoad</title>
</head>
<!--onload 属性在对象已加载时触发，onunload 事件在用户退出页面时发生-->
<body onload="javascript:alert('hello');" onunload="javascript:alert('bye-bye');">
    你好
</body>
</html>
```



- onmouseover、onmouseout: 
  - onmouseover 属性在鼠标指针移动到元素上时触发
  - onmouseout 事件会在鼠标指针移出指定的对象时发生

#### 示例: TestOnMouse.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>OnMouse</title>
</head>
<body>
<!--onmouseover 属性在鼠标指针移动到元素上时触发，onmouseout 事件会在鼠标指针移出指定的对象时发生-->
<img src="https://ss1.bdstatic.com/70cFuXSh_Q1YnxGkpoWK1HF6hhy/it/u=1839352044,827442581&fm=117&gp=0.jpg" onmouseover="alert('over')" onmouseout="alert('out')">
</body>
</html>
```



- onselect: onselect 事件会在文本框中的文本被选中时发生

#### 示例: TestOnSelect.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>OnSelect</title>
</head>
<body>
<!--onselect 事件会在文本框中的文本被选中时发生-->
<input type="text" value="SXT" onselect="alert('ok')">
</body>
</html>
```



- onsubmit: onsubmit 事件会在表单中的确认按钮被点击时发生

#### 示例: OnSubmit.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>OnSubmit</title>
</head>
<body>
<!--onsubmit 事件会在表单中的确认按钮被点击时发生-->
<form action="TestJavaScript.html" onsubmit="return false">
    <!--会根据onsubmit返回值来决定是否提交，这里return false，则表单不会提交，也就不会跳转到TestJavaScript.html页面-->
    <input type="submit" value="ok">
</form>
</body>
</html>
```



## 3. DOM、BOM

