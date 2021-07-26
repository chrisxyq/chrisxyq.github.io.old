---
layout: post
title: synchronized&lock
catalog: true
tags:
    - 多线程

---

视频来源：https://www.bilibili.com/video/BV1Kw411Z7dF

## 多线程编程步骤

![image-20210721135039523](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210721135039523.png)

注意：调用线程的start方法，不一定会立即创建该线程。线程的start方法底层调用了native的start0()方法，因此由操作系统决定

基于以上情况，因此无法确定线程的执行顺序。若要实现线程间的通信，则需要第二步，在资源类指定操作方法





### 多线程卖票：syn&lock

重票/错票示意

![image-20210117235242040](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210117235242040.png)

#### synchronized+同步监视器/锁

![1590739017314](https://gitee.com/chrisxyq/picgo/raw/master/img/1590739017314.png)

解决线程安全的方法是：线程同步

当线程同步锁发生嵌套的时候：可能发生死锁

方式1：使用同步代码块，需要显式的指定唯一的锁

方式2：使用同步方法，不需要显示指定锁，默认将this当前对象作为锁

继承thread类创建的线程，使用同步方法进行同步，需要将同步方法定义为static，且共享数据也应是static

实现runnable接口创建的线程，使用同步方法进行同步，不需要将同步方法定义为static，且共享数据不需要是static

#### Lock

JDK5.0提供了Lock锁的方式实现同步



![1596813967867](https://gitee.com/chrisxyq/picgo/raw/master/img/1596813967867.png)

![1596814033948](https://gitee.com/chrisxyq/picgo/raw/master/img/1596814033948.png)

![1596814077209](https://gitee.com/chrisxyq/picgo/raw/master/img/1596814077209.png)

#### synchronized和Lock的区别

首选lock，由于lock是5.0新增的，且更灵活

其次用同步代码块，因为同步代码块在方法体内部才分配相应的资源

最后才考虑用同步方法。

![1596814458536](https://gitee.com/chrisxyq/picgo/raw/master/img/1596814458536.png)

![1590747022340](https://gitee.com/chrisxyq/picgo/raw/master/img/1590747022340.png)

### 生产者消费者：线程通信

#### syn+wait/notify

资源类

![image-20210721140532312](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/SYdgAzZ3Op4slEJ.png)

测试类：启动两个线程

![image-20210721140854168](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/SYdgAzZ3Op4slEJ.png)

#### wait的虚假唤醒

注意：若将以上两个线程扩充为四个线程，两个加1，两个减1，则不会得到想要的结果

这是由于notifyAll方法不确定会唤醒哪个线程，举例线程1、3负责加1，线程2、4负责减去1

线程1执行后，唤醒线程3，线程3进行if判断后，执行wait进行等待。

这时，若线程1重新执行，并再次唤醒线程3，则由于wait方法是哪里休息哪里唤醒

因此线程3将执行加1操作

因此资源类的判断逻辑应该使用while替代if，防止虚假唤醒

（下飞机后还要再次安检、不可重入锁

#### lock+await/signalAll（单个condition

资源类的方法

![image-20210721145040806](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/5CGM8ZY3BDfb7dx.png)

### 线程定制化通信：轮流打印（绑定多个condition

引言：之前的四个线程的例子，两个累加线程的先后顺序难以控制，因此需要定制化通信

例子：ABC线程轮流打印

资源类的打印方法

![image-20210721151823250](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/oYxHdgRnfCzqlui.png)





## 线程死锁示例

一人一只筷子

![1596812920164](https://gitee.com/chrisxyq/picgo/raw/master/img/1596812920164.png)

![1596812990231](https://gitee.com/chrisxyq/picgo/raw/master/img/1596812990231.png)

在两个锁之间加上sleep，以增大死锁概率

![1596813075985](https://gitee.com/chrisxyq/picgo/raw/master/img/1596813075985.png)

1.wait和sleep的区别

![1601466686292](https://gitee.com/chrisxyq/picgo/raw/master/img/1601466686292.png)

2.lock接口

三个实现类

可重入锁

读锁

写锁