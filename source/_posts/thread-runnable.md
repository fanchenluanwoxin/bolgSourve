---
title: java多线程实现和启动
---
>java 实现多线程有两种方式。一种继承 java.lang.Thread 类，重写父类的 run() 方法。另一种实现 java.lang.Runnable 接口，实现接口的 run() 方法。  

- Thread 与 Runnable
  1. Thread 用的是继承，java又是单继承，会有很大的极限性。Runnable 避免点继承的局限，一个类可以继承多个接口。
  2. Runnable 同一个子类实列调用 new Thread(Runnable 实列).start() 适合于资源的共享。而 new Thread().run() 执行互补交互。
  3. 源码： public class Thread extends Object implements Runnable 由此可见 Thread 类也是 Runnable 接口的子类。

- start() 方法与 run() 。
  1. start() 方法是 Thread 类所有，但 Thread 累提供了接收 Runnable 实列的构造器，所以可以 new Thread(Runnable 实列).start()。
  2. 调用 start() 方法，会通过JVM找到 run() 方法。也就是在 start() 方法里面是有调用 run() 方法的。
  3. 通常做多线程使用的都是 Runnable 。  

### 例子：  
1.继承 Thread
package org.thread.demo;
class MyThread extends Thread{  
  private String name;  
  public MyThread(String name) {  
  super();  
  this.name = name;  
  }  
  public void run(){  
    for(int i=0;i<10;i++){  
    System.out.println("线程开始："+this.name+",i="+i);  
    }  
  }  
}  

package org.thread.demo;  
public class ThreadDemo01 {  
  public static void main(String[] args) {  
  MyThread mt1=new MyThread("线程a");  
  MyThread mt2=new MyThread("线程b");  
  mt1.run();//调用run方法启动线程
  mt2.run();  
  }  
}  

package org.thread.demo;  
public class ThreadDemo01 {  
  public static void main(String[] args) {  
    MyThread mt1=new MyThread("线程a");  
    MyThread mt2=new MyThread("线程b");  
    mt1.start();//调用start方法启动线程
    mt2.start();  
  }  
}

2.实现 Runnable 接口  
public interface Runnable{  
  public void run();  
}  

package org.runnable.demo;  
class MyThread implements Runnable{  
  private String name;  
  public MyThread(String name) {  
  this.name = name;  
  }
  public void run(){
    for(int i=0;i<100;i++){
    System.out.println("线程开始："+this.name+",i="+i);
    }
  }
}

3.Runnable 适合资源共享 买票事件为例
package org.demo.dff;  
class MyThread extends Thread{  
private int ticket=10;  
  public void run(){  
    for(int i=0;i<20;i++){  
      if(this.ticket>0){  
      System.out.println("卖票：ticket"+this.ticket--);  
      }  
    }  
  }
}  

package org.demo.dff;  
public class ThreadTicket {  
public static void main(String[] args) {  
  MyThread mt1=new MyThread();  
  MyThread mt2=new MyThread();  
  MyThread mt3=new MyThread();  
  mt1.start();//每个线程都各卖了10张，共卖了30张票  
  mt2.start();//但实际只有10张票，每个线程都卖自己的票  
  mt3.start();//没有达到资源共享  
  }  
}  

package org.demo.runnable;  
public class RunnableTicket {  
  public static void main(String[] args) {  
    MyThread mt=new MyThread();  
    new Thread(mt).start();//同一个mt，但是在Thread中就不可以，如果用同一  
    new Thread(mt).start();//个实例化对象mt，就会出现异常  
    new Thread(mt).start();  
  }  
}
