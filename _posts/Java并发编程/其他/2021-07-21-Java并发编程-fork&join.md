---
layout: post
title: fork&join
catalog: true
tags:
    - Java并发编程


---

![image-20210721103417240](C:\Users\13692\AppData\Roaming\Typora\typora-user-images\image-20210721103417240.png)

## 基于fork&join实现1累加到100

### 继承RecursiveTask<返回值类型>类，类的成员变量如下

![image-20210721104538133](https://i.loli.net/2021/07/21/4gCfM7ITQ12qUny.png)

### 实现compute方法如下

![image-20210721104703922](C:\Users\13692\AppData\Roaming\Typora\typora-user-images\image-20210721104703922.png)

![image-20210721104731728](C:\Users\13692\AppData\Roaming\Typora\typora-user-images\image-20210721104731728.png)

### 测试：forkJoinPool.submit/get

![image-20210721105058358](C:\Users\13692\AppData\Roaming\Typora\typora-user-images\image-20210721105058358.png)

