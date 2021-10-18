---
layout: post
title: Java并发编程-ArrayList实现阻塞队列
date:  2022-10-10
catalog: true
tags:
    - Java并发编程

---

## 阻塞队列

### 阻塞队列的好处

当队列为空的，从队列获取元素的操作将被阻塞

当队列为满的，从队列添加元素的操作将被阻塞

阻塞队列的好处是我们不需要关心什么时候阻塞线程，什么时候唤醒线程

### 阻塞队列分类



### 阻塞队列核心方法

![image-20211007130401956](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20211007130401956.png)

```java
public class BlockingQueueDemo {
    public static void main(String[] args) {
        ArrayBlockingQueue queue = new ArrayBlockingQueue<>(3);
        System.out.println(queue.add("a"));
        System.out.println(queue.add("b"));
        System.out.println(queue.add("c"));
        //a
        System.out.println(queue.element());
        //java.lang.IllegalStateException: Queue full
        System.out.println(queue.add("a"));
    }
```



### ArrayList实现阻塞队列

阻塞队列不用java 提供的自己怎么实现，condition 和wait 不能用

成员变量

![image-20210728110226112](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210728110226112.png)

向队列添加和取出元素的方法

![image-20210728110242538](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210728110242538.png)

测试方法

![image-20210728110255644](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210728110255644.png)