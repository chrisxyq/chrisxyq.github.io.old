---
layout: post
title: Java并发编程-Thread
date:  2020-10-10
catalog: true
tags:
    - Java并发编程
---
[视频来源](https://www.bilibili.com/video/BV1Kw411Z7dF)

## 

## 守护线程demo

设置aa为守护线程之前，程序不会结束

```java
    public static void main(String[] args) {
        Thread aa = new Thread(() -> {
            System.out.println(Thread.currentThread().getName() + "::" + Thread.currentThread().isDaemon());
            while (true) {

            }
        }, "aa");
        aa.start();
        System.out.println(Thread.currentThread().getName() + "结束");
    }
```

设置aa为守护线程之后，程序结束

```java
    public static void main(String[] args) {
        Thread aa = new Thread(() -> {
            System.out.println(Thread.currentThread().getName() + "::" + Thread.currentThread().isDaemon());
            while (true) {

            }
        }, "aa");
        aa.setDaemon(true);
        aa.start();
        System.out.println(Thread.currentThread().getName() + "结束");
    }
```

**

## 创建线程的四种方法

### 继承Thread类（new  MyThread().start()

![1590735086663](https://gitee.com/chrisxyq/picgo/raw/master/img/1590735086663.png)

使用示例

![image-20210117225724261](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210117225724261.png)

匿名子类

![image-20210117230423014](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210117230423014.png)

三个窗口卖票问题：如下实现有线程安全问题，即有重票/错票现象

![image-20210117232330012](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210117232330012.png)

若不想使用static，采用实现runnable接口的方式

### 实现Runnable接口（new Thread(runnable).start()

```java
class RunnableThread implements Runnable{
    @Override
    public void run() {

    }
}
    @Test
    public void test(){
        new Thread(new RunnableThread(),"aa").start();
    }
```

### 实现Callable接口（new Thread(futureTask).start()

问题：thread的构造函数只能传入runnable不能传入callable

解决：使用futureTask作为适配器，futureTask构造示例如下

```java
    @Test
    public void test2(){
        FutureTask<Integer> task = new FutureTask<>(new CallableThread());
        //lam表达式
        FutureTask<Integer> task2 = new FutureTask<>(()->{
            return 1024;
        });
    }
```

runnable接口有实现类futureTask

futureTask的构造函数可以传入callable（也可以传入runnable）

```java
class CallableThread implements Callable<Integer>{

    @Override
    public Integer call() throws Exception {
        System.out.println("******come in call method()");
        try {
            TimeUnit.SECONDS.sleep(2 );
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return 1080;
    }
}
```

```java
public class CallableTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        FutureTask<Integer> futureTask = new FutureTask(new CallableThread());
        new Thread(futureTask, "A").start();
        System.out.println(Thread.currentThread().getName() + "******计算完成");
        while (!futureTask.isDone()) {
            System.out.println("wait...");
        }
        System.out.println(futureTask.get());
        System.out.println(futureTask.get());
    }
```

第一次获取futureTask.get()需要等待计算完成，第二次获取futureTask.get()，则直接返回结果，无需等待

### 使用线程池

![1590761700714](https://gitee.com/chrisxyq/picgo/raw/master/img/1590761700714.png)

![image-20210118000825613](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210118000825613.png)

使用示例

![image-20210118001253869](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210118001253869.png)





## 一些面试题

### callable和runnable的区别

工具类 Executors 可以实现 Runnable 对象和 Callable 对象之间的相互转换   Executors.callable（Runnable task

|              | callable                         | runnable                                                     |
| ------------ | -------------------------------- | ------------------------------------------------------------ |
| 是否有返回值 | 有                               | 无                                                           |
| 是否抛出异常 | Callable的call方法分可以抛出异常 | Runnable的run方法不能抛出异常<br />父类Runnable接口中，run方法没有抛出异常，则实现Runnable的子类就无法抛出异常（子类在重写父类方法时只能够抛出与父类相同的异常或者父类异常的子类）<br />**在重写的run方法中，我们只能够进行异常的捕获而不能够抛出异常** |
|              |                                  |                                                              |



### wait和sleep的异同

![1601466686292](https://gitee.com/chrisxyq/picgo/raw/master/img/1601466686292.png)

![1590752867761](https://gitee.com/chrisxyq/picgo/raw/master/img/1590752867761.png)

|            | Sleep                                                        | Wait                                                         |
| ---------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
|            | 线程类（Thread）的静态方法，只让出了CPU，但监控依然保持，<br /> |                                                              |
| 是否释放锁 | 不会释放对象锁，也不需要占用锁                               | Object的方法，调用会放弃对象锁，进入等待队列，待调用notify()/notifyAll()唤醒指定的线程或者所有线程 |
| 使用限制   | 可以在任何地方使用                                           | 只能在同步方法或同步块中使用   （notify/notifyAll和wait方法依赖于monitor对象，我们知道monitor 存在于对象头的Mark Word 中(存储monitor引用指针)，而synchronized关键字可以获取 monitor |
| 用途       | sleep() 通常被用于暂停执行                                   | wait() 通常被用于线程间交互/通信   不会自动苏醒，需要别的线程调用同一个对象上的 notify() 或者 notifyAll() 方法  或使用 wait(long timeout) 超时后线程会自动苏醒 |



**守护线程**

| 为何要用守护线程 | 我们知道静态变量是ClassLoader级别的，如果Web应用程序停止，这些静态变量也会从JVM中清除。但是线程则是JVM级别的，如果你在Web 应用中启动一个线程，这个线程的生命周期并不会和Web应用程序保持同步。也就是说，即使你停止了Web应用，这个线程依旧是活跃的。正是因为这个很隐晦   的问题，所以很多有经验的开发者不太赞成在Web应用中私自启动线程。 |
| ---------------- | ------------------------------------------------------------ |
| 用法             | daemonThread.setDaemon(true);                                |
| 举例             | l  守护线程最典型的应用就是 GC (垃圾回收器)，它就是一个很称职的守护者   l  消息推送线程设置为守护线程，服务于ChatServlet的servlet用户线程，在servlet的init启动消息线程，servlet一旦初始化后，一直存在服务器，servlet摧毁后,消息线程自动退出 |



### 线程的五种状态/生命周期和线程通信

Thread的内部类State定义了线程的几种状态

![1596791611715](https://gitee.com/chrisxyq/picgo/raw/master/img/1596791611715.png)

![1596798231562](https://gitee.com/chrisxyq/picgo/raw/master/img/1596798231562.png)

- 运行到就绪：通过yield()暂时失去cpu的执行权，可能下一秒又获得执行权

- 阻塞到就绪：notify()唤醒当前的wait()阻塞状态的线程

- 运行到阻塞：wait()使得线程进入阻塞状态并且释放锁，sleep()使得线程进入阻塞状态但不会释放锁
- wait/notify、suspend/resume

![1590752697844](https://gitee.com/chrisxyq/picgo/raw/master/img/1590752697844.png)



### **守护线程**

| 为何要用守护线程 | 我们知道静态变量是ClassLoader级别的，如果Web应用程序停止，这些静态变量也会从JVM中清除。但是线程则是JVM级别的，如果你在Web 应用中启动一个线程，这个线程的生命周期并不会和Web应用程序保持同步。也就是说，即使你停止了Web应用，这个线程依旧是活跃的。正是因为这个很隐晦   的问题，所以很多有经验的开发者不太赞成在Web应用中私自启动线程。 |
| ---------------- | ------------------------------------------------------------ |
| 用法             | daemonThread.setDaemon(true);                                |
| 举例             | l  守护线程最典型的应用就是 GC (垃圾回收器)，它就是一个很称职的守护者   l  消息推送线程设置为守护线程，服务于ChatServlet的servlet用户线程，在servlet的init启动消息线程，servlet一旦初始化后，一直存在服务器，servlet摧毁后,消息线程自动退出 |

### Java内存模型

| 可见性（volatile可保证）     | 当一个共享变量被 volatile 修饰时，它会保证修改的值会立即被更新到主存，当有其他线程需要读取时，它会去内存中读取新值. |
| ---------------------------- | ------------------------------------------------------------ |
| 原子操作                     | 原子操作是指不会被线程调度机制打断的操作   只有简单的读取、赋值（而且必须是将数字赋值给某个变量，变量之间的相互赋值不是原子操作）才是原子操作 |
| 顺序一致性（volatile可保证） | 防止 JVM 的指令重排                                          |

进程线程

进程：正在执行的程序，是资源调度的基本单位、每个进程有独立的内存区域（方法区和堆空间）

线程：一个程序内部的一条执行路径，每个线程有独立的虚拟机栈和程序计数器，一个进程的多个线程共享同一个堆和方法区、因此若一个进程有一个实例变量，多个线程都可进行访问修改、jvm调优即为对线程共享的区域（堆进行优化

相较于进程，同一进程的线程之间通信的方便之处，类似于合租室友访问同一个厨房进行交流

![image-20210117224738752](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210117224738752.png)



### 线程的方法和优先级

![1596788027094](https://gitee.com/chrisxyq/picgo/raw/master/img/1596788027094.png)

注：yield方法释放当前线程的执行权，但有可能再次获得执行权

![1596787943764](https://gitee.com/chrisxyq/picgo/raw/master/img/1596787943764.png)

同一个进程的线程共享方法区和堆，每个线程有独立的虚拟机栈和程序计数器

子类中抛的异常不能比父类的大，父类没有抛异常，则子类重写的方法也不能抛异常。只能用try catch。
