# JavaScript 笔记

[TOC]

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
    - DOM 是 W3C 的标准 (所有浏览器公共遵守的标准)
    - DOM 是HTML和XML的应用程序接口(API)
  - BOM(Browser Object Model) 浏览器对象模型
    - BOM 是 各个浏览器厂商根据 DOM在各自浏览器上的实现(表现为不同浏览器定义有差别，实现方式不同)
    - BOM 主要处理浏览器窗口和框架，不过通常浏览器特定的JavaScript扩展都被看做 BOM 的一部分，这些扩展包括:
      - 弹出新的浏览器窗口
      - 移动、关闭浏览器窗口以及调整窗口大小
      - 提供Web浏览器详细信息的定位对象
      - 提供用户屏幕分辨率详细信息的屏幕对象
      - 对cookie的支持
    - BOM包含了DOM(对象)，浏览器提供出来给予访问的是BOM对象，从BOM对象再访问到DOM对象，从而js可以操作浏览器以及浏览器读取到的文档





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
  -  onunload 事件在用户退出页面时发生

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

### 3.01 JavaScript的对话框

- alert 警告框: 出现一个提示信息
- prompt 询问框: 返回输入的值


- confirm 确认框: 根据不同的选择，返回true/false

#### 示例: TestPrompt.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Prompt</title>
    <script language="JavaScript">
        //prompt() 方法用于显示可提示用户进行输入的对话框
        var username = prompt("请输入你的名字:");
        document.write("你好！" + username);
    </script>
</head>
<body>
</body>
</html>
```



#### 示例: TestConfirm.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Confirm</title>
    <script language="JavaScript">
        function confirmt() {
            //confirm() 方法用于显示一个带有指定消息和 OK 及取消按钮的对话框
            if(confirm("是否跳转？")) {
                document.test.submit();
            }
        }
    </script>
</head>
<body>
<form action="TestJavaScript.html" method="post" name="test">
    <input type="button" value="跳转" onclick="javascipt:confirmt()" name="aaa">
</form>
</body>
</html>
```



### 3.02 JavaScript内置对象

- this: 指的是当前的对象
- for...in: for...in 语句用于遍历数组或者对象的属性(对数组或者对象的属性进行循环操作)
- with: 为一段代码建立一个缺省的对象，任何无对象的属性引用，都将使用该缺省的对象
- new: 用于生成一个新的对象



#### 示例: TestThis.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>This</title>
</head>
<body>
<img src="https://ss1.bdstatic.com/70cFuXSh_Q1YnxGkpoWK1HF6hhy/it/u=1839352044,827442581&fm=117&gp=0.jpg" onclick="alert(this.src);">
<form name="ttt">
    <input type="text" name="ddd" value="ddaaa" onclick="checkit(this)">
</form>
</body>
</html>
<script language="JavaScript">
    function checkit(obj){
        if(obj.value == "") {
            alert("空值");
        } else {
            alert(obj.value);
        }
    }
</script>
```



#### 示例: TestForIn.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>For...In</title>
</head>
<body>
<script language="JavaScript">
    a = new Array("alex", "mark", "sean", "victor", "lynn");
    //for...in 语句用于遍历数组或者对象的属性(对数组或者对象的属性进行循环操作)
    for(eee in a ) {
        document.write(eee + "----");   //eee是数组a当前下标的值
        document.write(a[eee] + "<br>");
    }
</script>
</body>
</html>
```



#### 示例: TestWith.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>With</title>
</head>
<body>
<script language="JavaScript">
    //with 为一段代码建立一个缺省的对象，任何无对象的属性引用，都将使用该缺省的对象
    with (document) {
        write("This is a default document action");
        write("<br>");
        write("<font color=\"red\">");
        write("this is a default document action too");
        write("</font>");
    }
</script>
</body>
</html>
```



### 3.03 窗口中的对象和元素

- window:
  - 当前窗口
  - 新开窗口
  - 通过本地窗口控制新开窗口
- location: 获取或设置现有文档的URL
- history:
  - 先前访问过的URL的历史列表
  - 常用方法: back(), go(number)
- document: 当前的文档对象
  - document.write(): 向客户端浏览器输出内容
  - document.formName: 可以用这个方法得到表单名称
  - document.referrer



#### 示例: TestBOM.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Window</title>
</head>
<body>
<script language="JavaScript">
    //Window 对象表示浏览器中打开的窗口，status 属性可设置或返回窗口状态栏(status bar)中的文本
    window.status="Hello, please took hear!";

    //open() 方法用于打开一个新的浏览器窗口或查找一个已命名的窗口
    var abc = window.open("TestJavaScript.html", "newWin", "toolbar=no, left=200, top=100, menubar=no, width=500, height=500, resizable=no");

    //Location 对象包含有关当前 URL 的信息
    alert(window.location); //alert(document.location);
    function go2(){
        window.location="TestJavaScript.html";
    }

    //History 对象包含用户(在浏览器窗口中)访问过的 URL
    function goBack() {
        //back方法加载 history 列表中的前一个 URL
        history.back();
    }
</script>
<input type="button" onclick="javascipt:abc.close();" value="关闭弹窗">
<input type="button" value="跳转" onclick="JavaScript:go2()">
<input type="button" value="返回" onclick="JavaScript:goBack()">
</body>
</html>
```



## 4. JavaScript实例

### 4.01 Select二级联动菜单

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Relative</title>
</head>
<body onload="changeDrop2()">
<script language="JavaScript">
    function changeDrop2() {
        if(document.form.Drop1.selectedIndex == 0) {
            document.form.Drop2.length = 1;
            document.form.Drop1.selectedIndex = 0;
            document.form.Drop2.selectedIndex = 0;
            document.form.Drop2.options[0].text = "No Product Selected";
            document.form.Drop2.options[0].value = "No Product Selected";
        }
        if(document.form.Drop1.selectedIndex == 1) {
            document.form.Drop2.length = 5;
            document.form.Drop2.selectedIndex = 0;
            document.form.Drop2.options[0].text = "Select Model";
            document.form.Drop2.options[0].value = "Select Model";
            document.form.Drop2.options[1].text = "Celebrity";
            document.form.Drop2.options[1].value = "Celebrity";
            document.form.Drop2.options[2].text = "Legend";
            document.form.Drop2.options[2].value = "Legend";
            document.form.Drop2.options[3].text = "Sundancer";
            document.form.Drop2.options[3].value = "Sundancer";
            document.form.Drop2.options[4].text = "SideKick";
            document.form.Drop2.options[4].value = "SideKick";
        }

        if(document.form.Drop1.selectedIndex == 2) {
            document.form.Drop2.length = 3;
            document.form.Drop2.selectedIndex = 0;
            document.form.Drop2.options[0].text = "Select Model";
            document.form.Drop2.options[0].value = "Select Model";
            document.form.Drop2.options[1].text = "1100";
            document.form.Drop2.options[1].value = "1100";
            document.form.Drop2.options[2].text = "1103";
            document.form.Drop2.options[2].value = "1103";5
        }
        if(document.form.Drop1.selectedIndex == 3) {
            document.form.Drop2.length = 5;
            document.form.Drop2.selectedIndex = 0;
            document.form.Drop2.options[0].text = "Select Model";
            document.form.Drop2.options[0].value = "Select Model";
            document.form.Drop2.options[1].text = "TMR48";
            document.form.Drop2.options[1].value = "TMR48";
            document.form.Drop2.options[2].text = "TMR49";
            document.form.Drop2.options[2].value = "TMR49";
            document.form.Drop2.options[3].text = "TMR348";
            document.form.Drop2.options[3].value = "TMR348";
            document.form.Drop2.options[4].text = "TMR329";
            document.form.Drop2.options[4].value = "TMR329";
        }
    }
</script>
<form name="form">
    <p>
        <select name="Drop1" onchange="changeDrop2()" size="1">
            <option value="Select A Product" selected>Select A Product</option>
            <option value="Scooters">Scooters</option>
            <option value="Jazzy">Jazzy</option>
            <option value="LifeChairs">Life Chairs</option>
        </select>
        <br>
        <select name="Drop2" size="1">
        </select>
    </p>
</form>
</body>
</html>
```



### 4.02 表单验证

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>表单验证</title>
    <script language="JavaScript">
        function checkData() {
            if(!checkUserName(form.username.value.toLowerCase())) {
                alert("请输入正确的用户名!");
                return false;
            }
            return true;
        }
        function checkUserName(ssn){
            if(ssn.length < 3 || ssn.length >18) {
                document.getElementById("usernameErr").innerHTML = "<font color='red'>用户名长度为3-18位</font>";
                form.username.focus();
                return false;
            }
            document.getElementById("usernameErr").innerHTML = "";
            return true;
        }
    </script>
</head>
<body>
<form action="TestJavaScript.html" name="form" method="post" onsubmit="return checkData()">
    <!--onblur 事件会在对象失去焦点时发生-->
    <input type="text" name="username" size="30" maxlength="10" onblur="checkUserName(this.value.toLowerCase())">
    <span id="usernameErr"></span>
    <br>
    <button>提交</button>
</form>
</body>
</html>
```

