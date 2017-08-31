## Java进阶问题

### 1. 观察者模式

- 观察者模式中，一个被观察者管理所有相依于它的观察者物件，并且在本身的状态改变时主动发出通知。这通常通过呼叫各观察者所提供的方法来实现。此种模式通常被用来实现事件处理系统。

例子说明: 珠宝商运送一批钻石，有黄金强盗准备抢劫，珠宝商雇佣了私人保镖，于是当运输车上路的时候，强盗保镖都要观察运输车一举一动

- 抽象的观察者

  ```java
  public interface Watcher {
      public void update();
  }
  ```

- 抽象的被观察者

  ```java
  public interface Watched {
      public void addWatcher(Watcher watcher);	//添加观察者
    	public void removeWatcher(Watcher watcher);	//移除观察者
    	public void notifyWatchers();	//通知观察者
  }
  ```

- 具体的观察者

  保镖

  ```java
  public class Security implements Watcher {
      @Override
    	public void update() {
          System.out.println("运输车有行动，报表贴身保护");
      }
  }
  ```

  强盗

  ```java
  public class Thief implements Watcher {
      @Override
    	public void update() {
          System.out.println("运输车有行动，强盗准备动手");
      }
  }
  ```

- 具体的被观察者

  ```java
  public class Transporter implements Watched {
      private List<Watcher> list = new ArrayList<Watcher>();
    	
    	@Override
    	public void addWatcher(Watcher watcher) {
          list.add(watcher);
      }
    
    	@Override
    	public void removeWatcher(Watcher watcher) {
          list.remove(watcher);
      }
    
    	@Override
    	public void notifyWatchers() {
          for(Watcher watcher : list) {
              watcher.update();
          }
      }
  }
  ```

- 测试类

  ```java
  public class Test {
      public static void main(String[] args) {
          Transporter transporter = new Transporter();
        	
        	Security security = new Security();
        	Thief thief = new thief();
        	
        	//添加观察者
        	transporter.addWatcher(security);
        	transporter.addWatcher(thief);
        	
        	//通知观察者
        	transporter.notifyWatchers();
      }
  }
  ```



### 2. Java回调函数

- 回调: A类中调用B类中的某个方法C，然后B类的C方法反过来调用A类中的方法D，D这个方法就叫回调方法

  - Class A实现接口CallBack
  - class A中包含一个class B的引用b
  - class B有一个参数为CallBack的方法f(CallBack callback)
  - A的对象a调用B的方法 f(CallBack callback)
  - 然后b就可以在f(CallBack callback)方法中调用A的方法

- 示例:

  回调接口

  ```java
  public interface CallBack {
      public void testCallBack();
  }
  ```

  Class A

  ```java
  public class A implements CallBack {
    	//持有B的引用
      private B b;
    
    	public A(B b) {
          this.b = b;
      }
    
    	//在此方法中调用B的方法f
    	public void askB() {
        	//使用一个线程，即为异步回调
          new Thread(new Runnable() {
            	@Override
              public void run() {
                  b.f(A.this)
              }
          }).start();
      }
    
    	@Override
    	public void testCallBack() {
          System.out.println("CallBack");
      }
  }
  ```

  Class B

  ```java
  public Class B {
      public void f(CallBack callBack) {
          System.out.println("现在回调");
        	//调用回调方法
        	callBack.testCallBack();
      }
  }
  ```

  测试类

  ```java
  public class Test {
      public static void main(String[] args) {
          B b = new B();
        	A a = new A(b);
        	a.askB();
      }
  }
  ```

  ​

