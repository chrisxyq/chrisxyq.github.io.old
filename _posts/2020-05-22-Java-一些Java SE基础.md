---
layout: post
title: 一些Java SE基础
catalog: true
tags:
    - Java
---
# 面向对象

## 封装

封装性的体现：对属性或者方法的权限修饰

![image-20210110215735431](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210110215735431.png)



## 继承

继承是多态的前提

封装性：解决的是可不可见的问题；继承性：能不能拿到的问题

![image-20210110215806788](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210110215806788.png)

![image-20210110215923118](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210110215923118.png)

![image-20210110220019781](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210110220019781.png)

## 重写

1.重写的可见性权限修饰符不能更小，类似摊煎饼果子覆盖

2.父类的private的方法不能被重写、static的方法也不能被重写

## 多态

![image-20210110220042048](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210110220042048.png)

对象的多态性：父类的引用指向子类的对象

编译时候认为是person、运行时候认为是man、因此将重写的方式称为运行时多态

如果没有多态性，只能在父类中去写重载的方法

有了多态性就省去了重载的方法，可以在父类中将方法的形参声明为一个抽象的类型如object，然后调用方法的时候传入子类的对象

举例

![image-20210110220058470](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210110220058470.png)

建立多种数据库连接

建立mysql连接，具体调用mysql重写的方法

注意：多态性只适用于方法不适用于属性

证明多态是运行时行为：使用随机数作为flag

只要不是动态绑定就不是多态，即重写不是多态

![image-20210110220112250](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210110220112250.png)

## 属性与局部变量的区别

声明位置不同，属性直接声明在类的{}里面

局部变量不允许使用权限修饰符，其权限由方法确定

属性不需要初始化值，局部变量需要

非static属性加载到堆空间，局部变量加载到栈空间



# 常用类

## Object类

![image-20210110214745887](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210110214745887.png)

### equals()

![image-20210110214803059](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210110214803059.png)

###  toString()

![image-20210110215202136](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210110215202136.png)

## 包装类

![image-20210110215222781](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210110215222781.png)

如equals()只能用于引用数据类型，因此int这种基本数据类型通过包装类才能用equals()方法

### 基本数据类型与包装类的转换

![image-20210110215237807](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210110215237807.png)

当一些方法的形参是类类型的，需要将基本数据类型转成包装类

JDK5.0自动装箱

![image-20210110215253167](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210110215253167.png)

![image-20210110215309901](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210110215309901.png)

当包装类需要进行四则运算时候，需要转成基本数据类型

![image-20210110215329060](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210110215329060.png)

![image-20210110215344791](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210110215344791.png)

### 基本数据类型与string的转换

![image-20210110215402274](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210110215402274.png)

必须是子父类之间关系的才能强转(),string不能强转成int

测试：![image-20210110215422039](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210110215422039.png)



## 对象实现排序

如商家按照距离、评分等排序

### comparable接口：适用于自定义类的排序：实现comparable接口、重写compareto方法

![image-20210124000613623](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210124000613623.png)

![image-20210124000909353](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210124000909353.png)

测试：调用对象数组的sort方法即可，按照对象类中自己重写的compareto规则进行排序

![image-20210124001023584](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210124001023584.png)

### comparator接口:适用于已有类的排序，不方便实现comparable接口的情况

![image-20210124020522691](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210124020522691.png)



# 常识

1. 在地址栏直接输入cmd，可以在指定地址打开cmd

2. cmd切换盘符直接输入E:/即可

3. Java字符char是基本数据类型，字符串是类类型，引用数据类型

4. string用“”，char用''

5. string与int等其它数据类型运算结果还是string，表示字符串拼接

6. string不能通过强制类型转换直接转为int

   10.方法中使用可变个数的形参

   Java5.0新特性

   方法：使用...

   可变个数的形参的方法与本类中方法名相同、参数个数或者类型不同的方法之间构成重载

   与传统的传递数组的方法不构成重载，不能共存

   下图中遍历时候，可以将strs看成数组

   可变个数形参必须声明在形参的末尾，且只能有一个

   应用场景：sql语句的条件，条件可能有多个

   

   11.Java值传递机制

   ![image-20210117222715171](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210117222715171.png)

   

   传值与传引用数据类型的区别：

   传值相当于是在栈区复制一份变量的内存，在新的方法中无法改变原方法中的值

   传引用即在形参中传递引用数据类型，即传递的是堆内存中的引用，在新方法中改变堆内存的值之后，原方法的值同时被改变

   12.打印数组名：输出地址值

   打印字符数组名：输出字符数组

   ![image-20210117222734885](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210117222734885.png)

   

   17.![image-20210117222756558](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210117222756558.png)

   


## Java命名规范



![image-20210109195629276](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210109195629276.png)

## Java八种基本数据类型

![image-20210109200545416](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210109200545416.png)

## 自动类型提升和强制类型转换

自动类型提升：

- 容量小的和容量大的数据类型计算，容量小的将自动转化为容量大的。容量大小指的是数的表示范围大小，而不是字节数的大小。如float容量要大于long的容量。

![image-20210109221953184](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210109221953184.png)

- short、byte、char运算后结果均为int
- 定义long数据类型时候，数据末尾要加l，若不加l则编译器会认为是int，由于自动类型提升，声明为long不会报错，但若数据超出int的范围，则必须要加上l，否则会编译报错超出int的范围
- 定义float类型时候一定要加f，若12.3后面不加f，则编译器认为是double，若赋值给float，则编译报错精度会有损失

强制类型转换：数据截断

如数据库的count(*)的long转为int

+=和=的区别：

![image-20210109230336092](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210109230336092.png)

## 逻辑运算符

![image-20210109230603592](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210109230603592.png)

## 使用scanner从键盘获取变量



## switch-case

注：switch内的表达式支持以下数据类型：byte、short、int、char、枚举（jdk5.0支持）、string（jdk6支持）

若所有的case都不匹配，则进入default的语句

case只能用于等于的情况，不能写范围语句

![image-20210109234044969](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210109234044969.png)

## while(true)+break：将跳出循环的控制权交给break语句

while(true)本身是个死循环

![image-20210117110155014](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210117110155014.png)

备注：

- break是结束当前循环、continue是结束/跳过当次循环

- 带标签的break，以结束指定的for循环

  ![image-20210117181311408](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210117181311408.png)

## 数组

### 一维数组的声明和初始化

- 数组本身为引用数据类型，数组的元素既可以为基本也可以为引用数据类型

- 数组的声明和静态/动态初始化

  ![image-20210117181412173](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210117181412173.png)

- 编译只是生成字节码文件，只有解释运行时候才将数组加载到内存中，因此数组角标越界，属于运行时错误

- 获取数组长度：.length

- 举例说明一维数组的内存解析

![image-20210117183536427](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210117183536427.png)

当没有栈空间的对象指向堆空间的string[4]，垃圾回收算法将在某个时间，将string[4]回收。当main方法结束，栈空间的变量生命周期结束，堆空间的对象也将会被垃圾回收

### 二维数组的声明和初始化

![image-20210117184536661](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210117184536661.png)

## Arrays工具类

![image-20210117221523717](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210117221523717.png)

![image-20210117221725001](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210117221725001.png)
