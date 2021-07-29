---
layout: post
title: String
catalog: true
tags:
    - Java

---

## 1.3String类

### 1.3.1理解string的不可变性

![1590763872817](https://gitee.com/chrisxyq/picgo/raw/master/img/1590763872817.png)

#### 1.3.1.1字面量方式实例化的内存解析

![image-20210123155221325](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210123155221325.png)

string类有一个final类型的属性为一个string[]数组，string类的值即存储在此数组中，第三行代码运行时，将在常量池新建hello，并改变s1指针的位置，将其指向hello。（字符串拼接以及replace字符串的数组，也是如此，而不是在原有字符串位置后面新增/修改）

即一个变量的修改，不会影响其他string变量

### 1.3.2string两种实例化方式的区别：字面量/new+构造器

#### 1.3.2.1通过字面量定义的，其数据都在常量池中，记录的是常量池的地址值

举例如下

![image-20210123165400399](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210123165400399.png)

#### 1.3.2.2new+构造器实例化的string：记录的是堆的地址值

两个例子如下

![image-20210123165020792](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210123165020792.png)

![image-20210123170139139](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210123170139139.png)

注：string的equals是比较值，==则是比较地址值

#### 1.3.2.3java值传递&string不可变性的举例

![1590834888305](https://gitee.com/chrisxyq/picgo/raw/master/img/1590834888305.png)

### 1.3.3String常用方法

![1590836172775](https://gitee.com/chrisxyq/picgo/raw/master/img/1590836172775.png)

![image-20210123174814218](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210123174814218.png)

![image-20210123175249602](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210123175249602.png)

- tolowercase不会改变原string、该方法需要返回值接收

- contains/indexof判断一个字符串是否包含某字符序列

  ![image-20210123174658848](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210123174658848.png)

1.3.4string与基本数据类型包装类之间的转换

![1590836130610](https://gitee.com/chrisxyq/picgo/raw/master/img/1590836130610.png)

![1590836319545](https://gitee.com/chrisxyq/picgo/raw/master/img/1590836319545.png)



## 1.4StringBuffer、StringBuilder

### 1.4.1StringBuffer常用方法

![1590839374366](https://gitee.com/chrisxyq/picgo/raw/master/img/1590839374366.png)

![1590839454122](https://gitee.com/chrisxyq/picgo/raw/master/img/1590839454122.png)

注：

- string拼接用+或者concat、stringbuffer拼接用append



### 1.4.2StringBuffer、StringBuilder异同

注：

- idea：ctrl+shift+T全局搜索类、可看源码

- jdk5.0的stringbuilder类的方法没有synchronized修饰，因此是线程不安全的
- stringbuffer和stringbuilder的类中，用于存储的char[]数组没有被final修饰，因此是可变的
- 以setcharat方法举例，由于string是不可变的，因此需要返回值来接收。而stringbuffer可变，直接原地修改，没有返回值

### 1.4.3StringBuffer、StringBuilder底层char[]数组的初始化和扩容

为支持原地动态扩容，stringbuffer的无参和带参构造器所构造的char[]数组均有16的富余

![image-20210123204541318](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210123204541318.png)

![image-20210123204817503](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210123204817503.png)

![1590839151834](https://gitee.com/chrisxyq/picgo/raw/master/img/1590839151834.png)

扩容源码：

![image-20210123211057748](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210123211057748.png)

![1590839184521](https://gitee.com/chrisxyq/picgo/raw/master/img/1590839184521.png)

备注：

![image-20210123233619181](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210123233619181.png)

![image-20210123233849295](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210123233849295.png)

![image-20210123204817503](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210123204817503.png)

由于调用null.length，因此将抛出空指针异常

## 1.5string算法举例

### 1.5.1局部反转string

![image-20210123215849421](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210123215849421.png)

![image-20210123220150439](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210123220150439.png)

![image-20210123220822542](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210123220822542.png)

### 1.5.2获取字符串出现的次数

![image-20210123225936153](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210123225936153.png)

![image-20210123225909380](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210123225909380.png)

### 1.5.3最大相同子串

![image-20210123231537332](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210123231537332.png)

![image-20210123232154262](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210123232154262.png)

**1.String StringBuffer 和 StringBuilder 的区别是什么?**

| String                     | StringBuffer                                                 | StringBuilder            |
| -------------------------- | ------------------------------------------------------------ | ------------------------ |
| private final char value[] | private char value[]                                         | private char value[]     |
| 线程安全                   | 线程安全   对方法加了synchronized同步锁                      | 线程不安全               |
|                            | 对StringBuffer 对象本身进行操作，而不是生成新的对象并改变对象引用 | 10%~15% 左右的性能提升   |
|                            | 初始容量16   扩容：2倍+2                                     | 初始容量16   扩容：2倍+2 |