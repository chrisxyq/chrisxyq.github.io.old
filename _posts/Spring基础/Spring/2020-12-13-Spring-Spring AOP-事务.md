---
layout: post
title: Spring AOP-事务
catalog: true
tags:
    - Spring
---

## jdk动态代理&cglib动态代理

### jdk动态代理



### cglib动态代理



## spring事务控制

### 基于xml的声明式事务控制



### 基于注解的声明式事务控制



### 编程式的事务控制

-----------

# 实现事务：转账案例（aop引入

要实现转账流程的事务一致性，需要将所有的方法共用一个connection，即实现多次操作使用同一个事务，即事务控制在service业务层，而非在持久层。

原beans.xml配置

![1607267111910](https://gitee.com/chrisxyq/picgo/raw/master/img/1607267111910.png)

实现事务控制的思路：

![1607267021834](https://gitee.com/chrisxyq/picgo/raw/master/img/1607267021834.png)

## 写connectionutils工具类

![1607268249035](https://gitee.com/chrisxyq/picgo/raw/master/img/1607268249035.png)

### 获取当前线程上的连接的方法

![1607267810734](https://gitee.com/chrisxyq/picgo/raw/master/img/1607267810734.png)

注：数据源datasource是从spring上注入的

![1607269749141](https://gitee.com/chrisxyq/picgo/raw/master/img/1607269749141.png)

## 和事务相关的工具类：开启、提交、回滚事务和释放连接

从spring中注入connectionutils到事务工具类

然后使用connectionutils的方法获取线程的数据库连接对象

事务工具类的开启方法：将数据库连接对象的自动提交设为false

![1607269626244](https://gitee.com/chrisxyq/picgo/raw/master/img/1607269626244.png)

事务工具类的提交、回滚事务和释放连接直接调用数据库连接对象的提交、回滚事务和释放连接方法即可

注：数据库连接对象的释放连接close方法，并不是真的将数据库连接关闭，而是将连接还回连接池中

![1607269723086](https://gitee.com/chrisxyq/picgo/raw/master/img/1607269723086.png)

## 编写业务和持久层的事务控制代码

### 改造serviceimpl的业务代码：使用transactionmanager对象

为serviceimpl通过依赖注入，注入transactionmanager对象

![1607270221945](https://gitee.com/chrisxyq/picgo/raw/master/img/1607270221945.png)

注：catch后的内容需要抛出异常，不然将由于没有返回值报错

![1607270477287](https://gitee.com/chrisxyq/picgo/raw/master/img/1607270477287.png)

转账事务控制改造如下

![1607270386463](https://gitee.com/chrisxyq/picgo/raw/master/img/1607270386463.png)

### 改造daoimpl的代码：使用connectionutils对象

利用spring自动注入connectionutils对象

![1607270806811](https://gitee.com/chrisxyq/picgo/raw/master/img/1607270806811.png)

改造方法：将数据库连接作为sql传入的参数

![1607270870007](https://gitee.com/chrisxyq/picgo/raw/master/img/1607270870007.png)

## 修改beans.xml

queryrunner不再从数据库中拿连接

![1607271036148](https://gitee.com/chrisxyq/picgo/raw/master/img/1607271036148.png)

将数据源配置从queryrunner中，挪到connectionutils中来

![1607271139368](https://gitee.com/chrisxyq/picgo/raw/master/img/1607271139368.png)

为dao对象注入connectionutils

![1607271177920](https://gitee.com/chrisxyq/picgo/raw/master/img/1607271177920.png)

配置事务管理器的bean，并注入到service

![1607271272729](https://gitee.com/chrisxyq/picgo/raw/master/img/1607271272729.png)

![1607271245558](https://gitee.com/chrisxyq/picgo/raw/master/img/1607271245558.png)



# 实现方法解耦：动态代理（aop引入

## 基于接口的动态代理：使用proxy类的newproxyinstance方法

![1607863968494](https://gitee.com/chrisxyq/picgo/raw/master/img/1607863968494.png)

![1607864667569](https://gitee.com/chrisxyq/picgo/raw/master/img/1607864667569.png)

![1607864752926](https://gitee.com/chrisxyq/picgo/raw/master/img/1607864752926.png)

前面两个入参都是固定写法，invocationhandler中只有一个方法，就是invoke方法，执行被代理对象的任何方法都会经过invoke方法

invoke方法的解释如下

![1607865135934](https://gitee.com/chrisxyq/picgo/raw/master/img/1607865135934.png)

注：匿名内部类访问外部成员变量时，外部成员需要声明成final

代理类的invoke方法的具体实现

![1607865537403](https://gitee.com/chrisxyq/picgo/raw/master/img/1607865537403.png)

被代理的生产厂家的方法

生产厂家只管拿到钱，经销商负责卖这个产品，并挣取差价，通过以上动态代理，实现了不改变源代码实现增强。这种动态代理要求被代理类至少实现一个接口

![1607865639631](https://gitee.com/chrisxyq/picgo/raw/master/img/1607865639631.png)



## 基于子类的动态代理：cglib jar包的enhancer类的create方法

![1607869994955](https://gitee.com/chrisxyq/picgo/raw/master/img/1607869994955.png)

基于接口和基于子类都需要我们自己实现invoke/intercept方法，对被代理对象的方法进行增强

## 使用动态代理实现事务控制

举例：两种动态代理的应用：数据库连接池：对数据库连接池的方法进行增强，不对连接进行关闭，而是将其放回池中

举例：用于创建service的代理对象的工厂

![1607871580617](https://gitee.com/chrisxyq/picgo/raw/master/img/1607871580617.png)

在invoke方法里面添加事务的支持

![1607871868382](https://gitee.com/chrisxyq/picgo/raw/master/img/1607871868382.png)

其中，rtvalue为被代理对象service实际被调用的方法的返回值

备注：setaccountservice方法需要加上final修饰、并需要注入txmanager对象，并提供set方法

相应地，配置文件需要做如下的配置

![1607872142896](https://gitee.com/chrisxyq/picgo/raw/master/img/1607872142896.png)

即首先在容器中配置beanfactory对象，然后通过factory method的方式利用beanfactory创建proxyservice对象，该对象与accountservice均实现了accountserviceimpl接口，一个是通过动态代理实现的，一个本身就是一个实现类

根据以上分析，因此在测试类中，仅仅使用autowired注解按类型注入不能实现注入，因此需要加上qualifier注解，指定我们的动态代理对象进行测试

![1607872459269](https://gitee.com/chrisxyq/picgo/raw/master/img/1607872459269.png)





![1600331676312](https://gitee.com/chrisxyq/picgo/raw/master/img/1600331676312.png)

## aop引入：静态代理：在不改变原有代码的同时扩展原有功能：日志功能实现

![1606402886010](https://gitee.com/chrisxyq/picgo/raw/master/img/1606402886010.png)

# 1.springaop的原理图：aspect oriented programming

aop是面向对象oop的延伸，实现了（切面）代码的可重用性

aop有基于接口和基于子类的动态代理两种实现方式，spring的aop是通过配置文件的形式完成的，且会根据被代理对象是否实现了接口，来判断是通过接口还是通过子类完成动态代理

以上面的动态代理实现事务控制为例说明一些spring aop的基本概念

连接点：被代理的service的所有方法均是连接点

切入点：既被代理也被增强（以实现事务控制）的service的所有方法均是连接点

环绕通知：整个代理方法

![1607873685093](https://gitee.com/chrisxyq/picgo/raw/master/img/1607873685093.png)





![1599548649616](https://gitee.com/chrisxyq/picgo/raw/master/img/1599548649616.png)

写E和F对象，并和A一起配置成bean，为了让spring识别组装顺序，分别将E和F对象分别实现after和before接口，然后声明组装规则，spring容器将自动组装生成B对象，此时spring容器的键名为a，值已经修改为B对象，然后调用扩展后的B对象实现功能扩展。

为spring提供bean原材料和组装规则之后，spring自动生成的B对象与原来的A对象均实现了同一个C接口。因此可以使用C接口来接A或者B对象。

spring容器对象已启动，容器内的a已经是B对象了

![1599551677726](https://gitee.com/chrisxyq/picgo/raw/master/img/1599551677726.png)



# 2.springaop的代码实现（schemabase

面向切面编程实现功能拓展

![1599550059047](https://gitee.com/chrisxyq/picgo/raw/master/img/1599550059047.png)

## 2.2.环绕通知：适合于环绕代码比较少

![1599552825168](https://gitee.com/chrisxyq/picgo/raw/master/img/1599552825168.png)

applicationcontext.xml

![1599552854044](https://gitee.com/chrisxyq/picgo/raw/master/img/1599552854044.png)

# 3.springaop的代码实现（aspectJ

在schemabase里面，每个通知是写到单独的类里面，并且通过实现不同的接口来区分前置还是后置

aspectJ把前置通知、后置通知等所有通知写在同一个类里面

![1599554596609](https://gitee.com/chrisxyq/picgo/raw/master/img/1599554596609.png)

![1599554556560](https://gitee.com/chrisxyq/picgo/raw/master/img/1599554556560.png)

