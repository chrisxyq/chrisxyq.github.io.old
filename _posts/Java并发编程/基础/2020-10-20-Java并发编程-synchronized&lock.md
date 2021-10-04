---
layout: post
title: synchronized&lock
catalog: true
tags:
    - Java并发编程

---

视频来源：https://www.bilibili.com/video/BV1Kw411Z7dF

## 多线程卖票：syn&lock

demo演示三个线程卖30张票

### syn版本资源类ticket

```java
public class SynchronizedTicket {
    private int number = 30;

    public synchronized void sale() {
        if (number > 0) {
            System.out.println(Thread.currentThread().getName() + ":卖出：" + number-- + "剩下：" + number);
        }
    }
}

```

### lock版本资源类ticket

```java
public class LockTicket {
    private int number = 30;
    Lock lock = new ReentrantLock();

    public void sale() {
        lock.lock();
        try {
            if (number > 0) {
                System.out.println(Thread.currentThread().getName() + "\t卖出第：" + (number--) + "\t还剩下：" + number);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
}
```

### 测试方法

```java
public class SaleTicketBySynchronized {
    public static void main(String[] args) {
        SynchronizedTicket ticket = new SynchronizedTicket();
        //runnable方式创建线程
        new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i = 0; i < 40; i++) {
                    ticket.sale();
                }
            }
        }, "aa").start();
        new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i = 0; i < 40; i++) {
                    ticket.sale();
                }
            }
        }, "bb").start();
        new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i = 0; i < 40; i++) {
                    ticket.sale();
                }
            }
        }, "cc").start();
    }
}
```

## synchronized和Lock的区别

|      | synchronized                           | lock                          |
| ---- | -------------------------------------- | ----------------------------- |
|      | 有代码块锁和方法锁                     | lock是5.0新增的，只有代码块锁 |
|      | 发生异常会自动释放锁，不会导致死锁现象 |                               |
|      | 一直等待，不会中断                     | 可以中断                      |
|      | 不可以                                 | 可以知道有没有成功获取锁      |
|      |                                        | 线程竞争资源激烈时，效率较高  |



注意：调用线程的start方法，不一定会立即创建该线程。线程的start方法底层调用了native的start0()方法，因此由操作系统决定

基于以上情况，因此无法确定线程的执行顺序。

若要实现（指定线程的执行顺序）线程间的通信，则需要在资源类的操作方法指定之（判断干活通知）

## 线程通信demo：初始值为0的变量，多个线程交替+1，-1

### synchronized+wait+notify实现

资源类

```java
public class Share {
    private int number=0;
    public synchronized void incr() throws InterruptedException {
        //等待
        if(number!=0){
            this.wait();
        }
        //干活
        number++;
        System.out.println(Thread.currentThread().getName() + "::" + number);
        //通知其他线程
        this.notifyAll();
    }
    public synchronized void decr() throws InterruptedException {
        //等待
        if(number!=1){
            this.wait();
        }
        //干活
        number--;
        System.out.println(Thread.currentThread().getName() + "::" + number);
        //通知其他线程
        this.notifyAll();
    }
}
```

demo

```java
public class IncrAndDecrTest {
    /**
     * 一个线程+1，一个线程-1
     * 交替执行十次
     */
    public static void main(String[] args) {
        Share share = new Share();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    share.incr();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "aa").start();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    share.decr();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "bb").start();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    share.incr();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "cc").start();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    share.decr();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "dd").start();
    }
```



### wait+notify虚假唤醒的原因&解决

以上demo可能会出现如下情况：

```
aa::1
bb::0
cc::1
aa::2
bb::1
cc::2
```

原因cc线程抢到锁执行加1操作之后，aa线程抢到锁，判断当前数为1，因此aa线程执行到wait后在此处休眠

cc又抢到线程锁，cc休眠

aa抢到线程锁，而wait是在哪休眠就在哪唤醒，因此aa线程得以继续往下执行+1操作

解决：将if判断改成while，使得线程不满足while条件时才能继续往下执行，防止虚假唤醒现象（类似于下飞机之后再上飞机，依旧需要安检）

### lock+await/signalAll实现（单个condition

资源类：（注意方法不要再用synchronized修饰

```java
public class ShareByLock {
    private int number = 0;
    private Lock lock = new ReentrantLock();
    private Condition condition =lock.newCondition();

    public void incr() throws InterruptedException {
        //上锁
        lock.lock();
        try{
            //等待
            while (number != 0) {
                condition.await();
            }
            //干活
            number++;
            System.out.println(Thread.currentThread().getName() + "::" + number);
            //通知其他线程
            condition.signalAll();
        }finally {
            lock.unlock();
        }
    }

    public void decr() throws InterruptedException {
        //上锁
        lock.lock();
        try{
            //等待
            while (number != 1) {
                condition.await();
            }
            //干活
            number--;
            System.out.println(Thread.currentThread().getName() + "::" + number);
            //通知其他线程
            condition.signalAll();
        }finally {
            lock.unlock();
        }
    }
```

demo

```java
public class IncrAndDecrByLockTest {
    public static void main(String[] args) {
        ShareByLock share = new ShareByLock();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    share.incr();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "aa").start();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    share.decr();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "bb").start();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    share.incr();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "cc").start();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    share.decr();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "dd").start();
    }
```







#### lock+await/signalAll（单个condition

资源类的方法

![image-20210721145040806](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/5CGM8ZY3BDfb7dx.png)

### 线程定制化通信：轮流打印（绑定多个condition

引言：之前的四个线程的例子，两个累加线程的先后顺序难以控制，因此需要定制化通信

例子：ABC线程轮流打印

资源类的打印方法

![image-20210721151823250](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/oYxHdgRnfCzqlui.png)







1.wait和sleep的区别

![1601466686292](https://gitee.com/chrisxyq/picgo/raw/master/img/1601466686292.png)

### **Lock锁是怎么实现的，notify会唤醒哪个线程**

| AQS: 一个int类型状态值（用于锁的状态变更），一个双向链表（用于存储等待中的线程） |
| ------------------------------------------------------------ |
| 获取锁的·流程     CAS尝试获取锁→获取到锁，更新AQS的状态值  CAS没获取到锁，线程放到链表尾部自旋→自旋锁被释放，CAS尝试获取锁 |
| l  lock获取锁的过程：本质上是通过CAS来获取状态值修改，如果当场没获取到，会将该线程放在线程等待链表中。   l  lock释放锁的过程：修改状态值，调整等待链表。  l  lock大量使用CAS+自旋。因此根据CAS特性，lock建议使用在低锁冲突的情况下 |

notify方法只唤醒一个等待（对象的）线程并使该线程开始执行。所以如果有多个线程等待一个对象，这个方法只会唤醒其中一个线程，选择哪个线程取决于操作系统对多线程管理的实现。

### **synchronized的JVM底层原理**

l synchronized 同步语句块的实现使用的是 monitorenter 和 monitorexit 指令，其中 monitorenter 指令指向同步代码块的开始位置，monitorexit 指令则指明同步代码块的结束位置。

l synchronized 修饰的方法并没有 monitorenter 指令和 monitorexit 指令，取得代之的确实是 ACC_SYNCHRONIZED 标识，该标识指明了该方法是一个同步方法。

l 不过两者的本质都是对对象监视器 monitor 的获取

l 其实wait/notify等方法也依赖于monitor对象，这就是为什么只有在同步的块或者方法中才能调用wait/notify等方法，否则会抛出java.lang.IllegalMonitorStateException的异常的原因。

### **synchronized和 volatile的区别**

synchronized 关键字和 volatile 关键字是两个互补的存在，而不是对立的存在！

|      | volatile                                               | synchronized                                       |
| ---- | ------------------------------------------------------ | -------------------------------------------------- |
| 修饰 | 变量                                                   | 修饰方法以及代码块                                 |
| 作用 | 解决变量在多个线程之间的可见性，但不能保证数据的原子性 | 解决的是多个线程之间访问资源的同步性，两者都能保证 |

### **Synchronized和lock对比**

|              | Synchronized                                                 | lock                                                        |
| ------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| 存在层面     | 关键字，存在于 JVM 层面                                      | 接口                                                        |
| 锁的类型     |                                                              | 可公平锁                                                    |
| 锁的获取:    |                                                              | Lock 中有尝试获取锁的方法，如果尝试获取到锁，则不用一直等待 |
| 锁的释放条件 | 1. 获取锁的线程执行完同步代码后，自动释放；   2. 线程发生异常时，JVM会让线程释放锁 | Lock 必须在 finally 关键字中释放锁，不然容易造成线程死锁    |
| 锁的性能     | 资源竞争很激烈的情况下，Synchronized的性能会下降几十倍       | Lock 锁适用于大量同步阶段                                   |