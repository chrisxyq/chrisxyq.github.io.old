---
layout: post
title: ArrayList实现阻塞队列
catalog: true
tags:
    - 多线程

---



阻塞队列不用java 提供的自己怎么实现，condition 和wait 不能用

成员变量

![image-20210728110226112](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210728110226112.png)

向队列添加和取出元素的方法

![image-20210728110242538](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210728110242538.png)

测试方法

![image-20210728110255644](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210728110255644.png)