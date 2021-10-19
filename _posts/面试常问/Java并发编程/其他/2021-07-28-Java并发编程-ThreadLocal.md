---
layout: post
title: Java并发编程-ThreadLocal
date:  2022-10-10
catalog: true
tags:
    - Java并发编程

---



**Threadlocal**

| 概念                 | ThreadLocal的作用是提供线程内的局部变量，这种变量在线程的生命周期内起作用，减少同一个线程内多个函数或者组件之间一些公共变量的传递的复杂度。 |
| -------------------- | ------------------------------------------------------------ |
| 应用场景             | 保持dao对象是单例的，但是不把connection定义成dao的成员变量，而是定义成线程的变量。这样同一个线程的dao对象的所有方法都可以使用同一个connection, 并且connection不会有线程安全问题。而把把变量定义成线程的变量就是靠threadlocal来完成的。 |
| 代码举例             |                                                              |
| 内存泄漏   问题描述  | 在ThreadLocalMap中，entry的key是弱引用，value仍然是一个强引用。当某一条线程中的ThreadLocal使用完毕，没有强引用指向它的时候，这个key指向的对象就会被垃圾收集器回收，从而这个key就变成了null   所以entry就变成了(null,  value), 而entry 和 value 都是强引用，并且只要entry还在，value就一直存在。所以如果我们不手动清理掉这些键为空的entry， 在线程执行完毕之前，这个entry就一直处于内存泄漏的状态。线程生命周期越长，内存泄漏的就越多。 |
| 为什么要设置成弱引用 | 使用弱引用可以多一层保障：(null, value),对应的value在下一次ThreadLocalMap调用set,get,remove的时候会被清除。 |
| 解决内存泄漏         | 需要程序员在完成ThreadLocal的使用后要养成手动调用remove的习惯   ThreadLocal内存泄漏的根源是：由于ThreadLocalMap的生命周期跟Thread一样长，如果没有手动删除对应key就会导致内存泄漏，而不是因为弱引用。 |



daper、cat全链路服务追踪使用Threadlocal





