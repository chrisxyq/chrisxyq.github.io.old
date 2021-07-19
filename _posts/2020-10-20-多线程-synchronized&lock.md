---
layout: post
title: synchronized&lock
catalog: true
tags:
    - 多线程

---

视频来源：https://www.bilibili.com/video/BV1Kw411Z7dF

## 7.3解决线程安全的方法

重票/错票示意

![image-20210117235242040](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210117235242040.png)

### 7.3.1线程同步：synchronized+同步监视器/锁

![1590739017314](https://gitee.com/chrisxyq/picgo/raw/master/img/1590739017314.png)

解决线程安全的方法是：线程同步

当线程同步锁发生嵌套的时候：可能发生死锁

方式1：使用同步代码块，需要显式的指定唯一的锁

方式2：使用同步方法，不需要显示指定锁，默认将this当前对象作为锁

继承thread类创建的线程，使用同步方法进行同步，需要将同步方法定义为static，且共享数据也应是static

实现runnable接口创建的线程，使用同步方法进行同步，不需要将同步方法定义为static，且共享数据不需要是static

### 7.3.2线程同步：Lock

JDK5.0提供了Lock锁的方式实现同步



![1596813967867](https://gitee.com/chrisxyq/picgo/raw/master/img/1596813967867.png)

![1596814033948](https://gitee.com/chrisxyq/picgo/raw/master/img/1596814033948.png)

![1596814077209](https://gitee.com/chrisxyq/picgo/raw/master/img/1596814077209.png)

### 7.3.3线程同步：synchronized和Lock的区别

首选lock，由于lock是5.0新增的，且更灵活

其次用同步代码块，因为同步代码块在方法体内部才分配相应的资源

最后才考虑用同步方法。

![1596814458536](https://gitee.com/chrisxyq/picgo/raw/master/img/1596814458536.png)

![1590747022340](https://gitee.com/chrisxyq/picgo/raw/master/img/1590747022340.png)

## 7.5线程死锁示例

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