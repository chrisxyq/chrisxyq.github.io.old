---
layout: post
title: Spring IOC-基本介绍
catalog: true
tags:
    - Spring IOC
---

# Spring ioc引入示例：解除类之间的耦合

## 编写JDBC的工程代码

![1604717623988](https://gitee.com/chrisxyq/picgo/raw/master/img/1604717623988.png)

## 编译期异常

注意：上面的mysql爆红是由于项目没有找到相应mysql版本的jar包，若编译项目将出现以下编译期异常

![1604717905587](https://gitee.com/chrisxyq/picgo/raw/master/img/1604717905587.png)

可以在pom.xml里面修改mysql的依赖的版本解决这个问题

也可通过反射，将编译期依赖转为运行时依赖（错误error转为运行时异常exception

![1604724042102](https://gitee.com/chrisxyq/picgo/raw/master/img/1604724042102.png)

具体修改如下

![1604724198040](https://gitee.com/chrisxyq/picgo/raw/master/img/1604724198040.png)

## 解决类之间依赖：使用创建bean对象的工厂

问题：若表现层调业务层，业务层调用持久层都是使用new的方式，则耦合性强

javabean：java语言编写的可重用组件

### 使用配置文件配置service和dao，使用唯一标识key映射全限定类名value

![1604730257669](https://gitee.com/chrisxyq/picgo/raw/master/img/1604730257669.png)

### 通过读取配置文件获取创建对象的全限定类名

![1604730713818](https://gitee.com/chrisxyq/picgo/raw/master/img/1604730713818.png)

### 通过反射创建对象，避免使用new关键字

beanfactory创建工厂方法

注意由于beanfactory已经将配置文件读取到了props属性里面，且getbean方法里面有相应的通过props将beanname转成beanpath的步骤，因此向beanfactory传入beanname即可。

![1604730879511](https://gitee.com/chrisxyq/picgo/raw/master/img/1604730879511.png)

通过使用工厂模式解决类之间的依赖，当找不到相应的类路径时，将报运行时异常，即classnotfoundexception

### 实现单例：定义一个map用于存放要创建的对象，称为容器

以上模式每次都需要重新创建一个新的对象，为了提高效率，在newinstance之后，将其存起来，之后使用直接调用即可

首先在beanfactory类里面定义一个map容器

![1604743703732](https://gitee.com/chrisxyq/picgo/raw/master/img/1604743703732.png)

将map容器的实例化过程放到beanfactory类的静态代码块里面，使得类加载时，map容器便已经准备好了，且只加载一次

（取出配置文件所有的key放到枚举类里面，遍历枚举类）

![1604745155903](https://gitee.com/chrisxyq/picgo/raw/master/img/1604745155903.png)

由于有了map容器，因此getbean方法可以简化为

![1604745248170](https://gitee.com/chrisxyq/picgo/raw/master/img/1604745248170.png)







注意：service和dao中，由于是单例模式，不允许存在成员变量，以防止线程安全问题

![1604742591984](https://gitee.com/chrisxyq/picgo/raw/master/img/1604742591984.png)





# SpringIOC：inversion of control

## 作用：

将MVC结构体系中，层与层之间的关系进行解耦，由直接变成间接。耦合性高的意思是：A和B是直接的关系。解耦的意思是A要调B的功能，让C创建好B的对象给A。由两角关系转成三角关系。

## applicationcontext与beanfactory的区别

![1604770173169](https://gitee.com/chrisxyq/picgo/raw/master/img/1604770173169.png)

applicationcontext在读取配置文件即创建了对象

适用于创建单例对象

![1604770512408](https://gitee.com/chrisxyq/picgo/raw/master/img/1604770512408.png)

beanfactory在使用对象（打印对象）时，才创建对象

适用于创建多例对象

![1604770414217](https://gitee.com/chrisxyq/picgo/raw/master/img/1604770414217.png)

## spring对bean的管理细节：创建bean的三种方式

### 默认通过无参构造器方式创建

![1604771264122](https://gitee.com/chrisxyq/picgo/raw/master/img/1604771264122.png)

### 创建jar包中的字节码文件的类的bean：使用工厂类的工厂方法创建对象

![1604771632683](https://gitee.com/chrisxyq/picgo/raw/master/img/1604771632683.png)

### 静态工厂模式：工厂创建对象的方法为静态方法，创建对象不需要创建工厂对象。

![1604771804724](https://gitee.com/chrisxyq/picgo/raw/master/img/1604771804724.png)

## spring对bean的管理细节：bean对象作用范围：scope属性的不同取值

![1604772498279](https://gitee.com/chrisxyq/picgo/raw/master/img/1604772498279.png)

global-session：当负载均衡多个服务器接收请求时，多个服务器均拥有用户的验证码session与用户进行比对，则称之为global-session



## spring对bean的管理细节：bean对象生命周期：单例对象和多例对象

![1604773133477](https://gitee.com/chrisxyq/picgo/raw/master/img/1604773133477.png)



## IOC原理示意图

![1598629892991](https://gitee.com/chrisxyq/picgo/raw/master/img/1598629892991.png)

传统的servlet调用service层，很多servlet调用很多service。如果要修改service，需要修改所有的servlet代码。首先让所有的service实现同一个service接口，使得所有的service的方法保持一致。然后将所有的service对象交给IOC容器进行创建，通过读取文件反射进行service对象的创建。

## 代码演示SpringIOC为servlet传入service

在项目根路径src下面，写一个给spring容器对象看的xml配置文件

bean即为业务层的对象

里面写service的键值对（类似于写一个给tomcat服务器看的web.xml）

![1598631134334](https://gitee.com/chrisxyq/picgo/raw/master/img/1598631134334.png)

然后创建spring容器对象、从spring容器对象获取业务层对象、调用业务层方法完成业务处理

在servlet获得对象后强转成myservice接口对象

![1598631330815](https://gitee.com/chrisxyq/picgo/raw/master/img/1598631330815.png)

通过这种方式，只需在xml中修改bean的键值对，即可同步更新所有的servlet对于service的调用



# IOC的依赖注入DI：dependency injection

![1599056969220](https://gitee.com/chrisxyq/picgo/raw/master/img/1599056969220.png)

![1599057001624](https://gitee.com/chrisxyq/picgo/raw/master/img/1599057001624.png)

## 概念

在配置文件中，一次配置，多次调用

当需要创建引用数据类型属性有值的对象时，需要使用依赖注入

![1599093283886](https://gitee.com/chrisxyq/picgo/raw/master/img/1599093283886.png)

## 依赖注入能注入的三类数据和三种注入方式

![1604773661952](https://gitee.com/chrisxyq/picgo/raw/master/img/1604773661952.png)

![1604773675347](https://gitee.com/chrisxyq/picgo/raw/master/img/1604773675347.png)

如果是用户注册信息这种经常变化的数据不适合采用注入的方式

### 使用构造函数注入：constructor-arg（不常用

constructor-arg标签内的五个属性如下

（type和index标签在注入属性时，指代不明，不常用

value标签可自动将字符串转成int类型，但不能将字符串转成date类型

要注入date类型的日期对象，可采用如下方式：将日期对象配置成bean，再通过ref标签引用关联的bean对象

![1604812801332](https://gitee.com/chrisxyq/picgo/raw/master/img/1604812801332.png)

缺点在于：所有的属性都必须注入值，否则对象无法创建，即使用不到这些数据，也必须提供。

### set方法注入：对象的类需要有set方法

![1604813550312](https://gitee.com/chrisxyq/picgo/raw/master/img/1604813550312.png)

需要注意：name需要与类的set方法的名字保持一致。如类的set方法名为setUsername，则property name=“username”

### 集合类型的注入：结构相同，标签可以互换

![1604814354883](https://gitee.com/chrisxyq/picgo/raw/master/img/1604814354883.png)

集合类型的注入只能通过xml实现

以注入map类型为例说明

![1604814562842](https://gitee.com/chrisxyq/picgo/raw/master/img/1604814562842.png)

# spring ioc的常用注解：实现自动注入

## 要使用注解的前提配置：

需要导入context约束，

**此外，bean.xml需要配置包扫描，以扫描到注解，具体配置如下**（自动注入必做的一步

指定要扫描的包，这个包下的注解将会生效

![1604822654798](https://gitee.com/chrisxyq/picgo/raw/master/img/1604822654798.png)

并且开启注解的支持

![1606313918962](https://gitee.com/chrisxyq/picgo/raw/master/img/1606313918962.png)

## ioc的常用注解：按照作用类型分类

![1604821693608](https://gitee.com/chrisxyq/picgo/raw/master/img/1604821693608.png)

### @component注解的使用（用于创建对象的

@component注解的使用：该注解有value属性，可以用于配置bean在容器中的key，当不配置value属性时，将默认为类名（首字母小写）

![1604822830057](https://gitee.com/chrisxyq/picgo/raw/master/img/1604822830057.png)



这样在读取bean.xml配置文件时，上下文容器才能有被@component注解托管的对象

![1604822774703](https://gitee.com/chrisxyq/picgo/raw/master/img/1604822774703.png)

注：

![1604823056825](https://gitee.com/chrisxyq/picgo/raw/master/img/1604823056825.png)

![1606318358178](https://gitee.com/chrisxyq/picgo/raw/master/img/1606318358178.png)

使用这些注解的类，则将作为spring的组件，注册到spring的容器中。

### @autowired、@qualifier、@resource（用于注入其他bean类型的数据

@autowired：自动按照类型注入

![1604823276736](https://gitee.com/chrisxyq/picgo/raw/master/img/1604823276736.png)

**注：使用autowired注解进行自动注入时，相应的属性不需要提供set方法**，需要提供get方法

@qualifier（value="")：在按照类型注入的基础上，按照名称注入

@qualifier在给类的属性注入时，必须要与@autowired同时使用，否则无法注入成功

![1604824066127](https://gitee.com/chrisxyq/picgo/raw/master/img/1604824066127.png)

![1604824514730](https://gitee.com/chrisxyq/picgo/raw/master/img/1604824514730.png)

@resource（name="")：相当于@autowired、@qualifier合用

注意：如果有两个cat类型的bean，cat1和cat2，如果person只使用@resource注入cat的话，将报错如下

![1606315508223](https://gitee.com/chrisxyq/picgo/raw/master/img/1606315508223.png)

![1604824492333](https://gitee.com/chrisxyq/picgo/raw/master/img/1604824492333.png)

注意：

![1604824555081](https://gitee.com/chrisxyq/picgo/raw/master/img/1604824555081.png)

### @resource和@autowired区别

![1606317589596](https://gitee.com/chrisxyq/picgo/raw/master/img/1606317589596.png)

### @value(value=“”）（用于注入基本/string类型的数据，支持el表达式

![1606318170925](https://gitee.com/chrisxyq/picgo/raw/master/img/1606318170925.png)

### @scope（改变作用范围

## xml与注解的结合使用：最佳实践

![1606399582199](https://gitee.com/chrisxyq/picgo/raw/master/img/1606399582199.png)

# 使用@configuration配置类全面接管xml创建bean的工作

@configuration修饰配置类，是spring的一个组件

即使使用了注解实现自动注入，xml文件里面依旧有两部分内容：1.component-scan的package扫描自动注入的注解、2.jar包里的类的bean配置

要想不使用xml，则需要解决以上两个问题

## @configuration、@componentscan、@bean：通过使用配置类，代替beans.xml

三个注解的介绍

![1604828007659](https://gitee.com/chrisxyq/picgo/raw/master/img/1604828007659.png)

@configuration、@componentscan：实现package扫描自动注入的注解

![1604828257063](https://gitee.com/chrisxyq/picgo/raw/master/img/1604828257063.png)

@bean：实现jar包里的类的对象创建，并将对象作为bean加到spring容器中

![1606400712775](https://gitee.com/chrisxyq/picgo/raw/master/img/1606400712775.png)

![1604828322766](https://gitee.com/chrisxyq/picgo/raw/master/img/1604828322766.png)

注：关于@bean注解的补充说明

![1604828417752](https://gitee.com/chrisxyq/picgo/raw/master/img/1604828417752.png)

此外，通过这种方式创建的bean对象，默认是单例的，可通过@scope注解来改变

## 

## 通过反射读取配置类创建注解配置上下文

![1606401896407](https://gitee.com/chrisxyq/picgo/raw/master/img/1606401896407.png)

![1604828757310](https://gitee.com/chrisxyq/picgo/raw/master/img/1604828757310.png)

@propertysource和@value：读取配置文件的value

对于上面的配置类，jdbc数据源的属性可以以键值对的形式写到properties文件里面，然后使用注解读取和加载

![1604839351646](https://gitee.com/chrisxyq/picgo/raw/master/img/1604839351646.png)

## 在配置类上使用@propertysource注解

![1604839292110](https://gitee.com/chrisxyq/picgo/raw/master/img/1604839292110.png)

## 在属性上使用@value注解获取配置文件配置的值

![1604839450833](https://gitee.com/chrisxyq/picgo/raw/master/img/1604839450833.png)

## 配置类引用另一个配置类：@import注解

![1606400927386](https://gitee.com/chrisxyq/picgo/raw/master/img/1606400927386.png)



## 测试service的方法的测试类示例

这样写就不用在每个测试方法都获取ac和as对象，减少重复代码

![1604840441014](https://gitee.com/chrisxyq/picgo/raw/master/img/1604840441014.png)

这里是spring没有整合junit的操作，由于junit没有整合spring，因此不会读取@autowired注解，也不会自动创建spring容器

## @runwith、@contextconfiguration进一步优化：spring整合junit的配置

![1604841191741](https://gitee.com/chrisxyq/picgo/raw/master/img/1604841191741.png)

进一步优化的结果

![1604841520909](https://gitee.com/chrisxyq/picgo/raw/master/img/1604841520909.png)

