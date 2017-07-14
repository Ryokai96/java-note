### Servlet 生命周期

1. 加载 ClassLoader
2. 实例化 new
3. 初始化 init(ServletConfig)
4. 处理请求 service doGet doPost
5. 退出服务 destroy()
6. 只有一个对象



### Cookie

- 关闭web页面时，这个页面和服务器的socket连接就断开了，为了让服务器获取web页面关闭前的内容，要求出现一种保存c(客户端)/s(服务端)间状态的机制，这种机制需要满足：

  1. 服务器可以向客户端写内容，但只能是文本内容


    2. 客户端可以阻止服务器写入内容
    3. 服务器只能从客户端拿自己的webapp写入的内容

- Cookie: 保存到客户端的一个文本文件，与特定客户相关

- Cookie以"名-值"对的形式保存数据

- Cookie分为两种: 

  - 属于窗口/子窗口
  - 属于文本

- 一个servlet/jsp设置的cookie能够被同一个路径下面或者子路径下面的servlet/jsp读到(这里的路径指的是URL，并不是指真实的路径)

- 创建Cookie: new Cookie(name, value)



### Session

- Session记录在服务端
- Session的实现:
  1. 通过Cookie来实现: 如果浏览器支持Cookie，创建Session的时候会把SessionID保存在Cookie里
  2. 通过URL来实现: 如果浏览器不支持Cookie，必须自己编程使用URL重写的方式实现Session
     - response.encodeURL()
       - 转码
       - URL后面加入SessionID
- 可以在web.xml中配置Session的过期时间
- Session不像Cookie拥有路径访问的问题
  - 同一个application下的servlet/jsp可以共享同一个session，前提是同一个客户端窗口



### Application

- Application中的内容所有客户端都可以访问




### javaBean

- 广义javaBean: 普通java类
- 狭义javaBean: 符合Sun JavaBean标准的类
- 在Servlet中使用Bean和在通常程序中使用Bean类似
  - 属性名称第一个字母必须小写，比如：private productId
  - 一般具有 getters and setters
  - Bean不应具有GUI表现
  - 一般是用来实现某一业务逻辑或取得特定结果

