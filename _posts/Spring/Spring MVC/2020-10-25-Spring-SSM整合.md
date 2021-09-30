---
layout: post
title: SSM整合
catalog: true
tags:
    - Spring
---
## 1.mybatis层

### 1.database.properties：关联数据库：驱动、url、用户名、密码

![1603546716156](https://gitee.com/chrisxyq/picgo/raw/master/img/1603546716156.png)

### 2.mybatis的核心配置文件：mybatis-config.xml

a.配置数据源，交给spring去做

b.给pojo起别名

![1603547322421](https://gitee.com/chrisxyq/picgo/raw/master/img/1603547322421.png)

c.将3中的mapper.xml注册到mybatis-config.xml配置文件中

![1603547380621](https://gitee.com/chrisxyq/picgo/raw/master/img/1603547380621.png)

### 3.mapper.xml：mapper接口方法的形参的@param注解的使用

仿照mapper接口来写xml，使用@param注解，将形参映射到pojo的字段上

![1603547107203](https://gitee.com/chrisxyq/picgo/raw/master/img/1603547107203.png)

备注：mapper.xml的前缀和mybatis-config.xml相似

备注：service层调dao层

![1603547976716](https://gitee.com/chrisxyq/picgo/raw/master/img/1603547976716.png)

## 2.spring层

### 1.spring配置文件：applicationcontext.xml

也包含3.2的spring-mvc.xml

![1603554021036](https://gitee.com/chrisxyq/picgo/raw/master/img/1603554021036.png)

### 2.spring整合dao层：spring-dao.xml：关联数据库配置文件、连接池、sqlsessionfactory（递进

a.关联1.1的数据库配置文件，这样数据库配置文件就可以通过spring来读取。注意import标签只能导入spring的配置文件

![1603548227672](https://gitee.com/chrisxyq/picgo/raw/master/img/1603548227672.png)

b.配置c3p0数据库连接池，连接池的属性的值要引入database.properties的四项（**备注：spring-dao.xml、applicationcontext.xml需要在同一个applicationcontext里面**）

![1603548751288](https://gitee.com/chrisxyq/picgo/raw/master/img/1603548751288.png)

**c.再然后配置sqlsessionfactory**

**其属性datasource，通过ref去引用前面配置的datasource**

**sqlsessionfactory也要绑定加载mybatis的核心配置文件：mybatis-config.xml**

![1603551236534](https://gitee.com/chrisxyq/picgo/raw/master/img/1603551236534.png)

**d.最后，为了让spring动态生成dao接口的实现类，并注册到spring，需要做如下配置**

![1603551628628](https://gitee.com/chrisxyq/picgo/raw/master/img/1603551628628.png)

将sqlsessionfactory动态注入到mapperscannerconfigurer，并指定要扫描的dao package

### 3.spring整合service层：spring-service.xml：

a.使用component-scan扫描service的package，这样的话只要service下的包有注解，如@service，就会自动扫描进spring容器

![1603551907847](https://gitee.com/chrisxyq/picgo/raw/master/img/1603551907847.png)

**b.将mapper注入到serviceimpl，并配置serviceimpl为bean**

方法1：在xml配置

（**备注：spring-service.xml、spring-dao.xml、applicationcontext.xml需要在同一个applicationcontext里面**）以获取到spring-dao.xml配置的bookmapper

![1603552048153](https://gitee.com/chrisxyq/picgo/raw/master/img/1603552048153.png)

![1603552203639](https://gitee.com/chrisxyq/picgo/raw/master/img/1603552203639.png)

方法2：使用注解

![1603552443379](https://gitee.com/chrisxyq/picgo/raw/master/img/1603552443379.png)

c.为了让增删改自动提交，需要进行声明式事务配置，配置jdbc包下的事务管理器

![1603553823578](https://gitee.com/chrisxyq/picgo/raw/master/img/1603553823578.png)



## 3.springmvc层

### 1.web.xml：配置dispatcherservlet和乱码过滤

**配置dispatcherservlet：绑定spring-mvc.xml**

loadonstartup=1：让dispatcherservlet与tomcat一起启动

![1603554264225](https://gitee.com/chrisxyq/picgo/raw/master/img/1603554264225.png)

乱码过滤

![1603554348505](https://gitee.com/chrisxyq/picgo/raw/master/img/1603554348505.png)

配置session过期时间为15分钟

![1603554360148](https://gitee.com/chrisxyq/picgo/raw/master/img/1603554360148.png)

### 2.spring-mvc.xml：扫描controller包和视图解析器

![1603554661477](https://gitee.com/chrisxyq/picgo/raw/master/img/1603554661477.png)

## 4.查询书籍功能

### 1.index.jsp页面

![1603555403290](https://gitee.com/chrisxyq/picgo/raw/master/img/1603555403290.png)

### 2.controller

@qualifier注入的impl对应2.3.b的配置

![1603555229713](https://gitee.com/chrisxyq/picgo/raw/master/img/1603555229713.png)

返回到allbook页面

### 3.allbook.jsp页面

![1603555430310](https://gitee.com/chrisxyq/picgo/raw/master/img/1603555430310.png)

@qualifier注入的impl对应2.3.b的配置

### 4.500异常：找不到service bean

![1603555563699](https://gitee.com/chrisxyq/picgo/raw/master/img/1603555563699.png)

写测试类发现运行正常

![1603557285560](https://gitee.com/chrisxyq/picgo/raw/master/img/1603557285560.png)

错误原因是web.xml中dispatcherservlet加载的配置文件应该是applicationcontext.xml而不应该是springmvc-config.xml，因此找不到service的bean

![1603557504331](https://gitee.com/chrisxyq/picgo/raw/master/img/1603557504331.png)

### 5.bootstrap美化jsp页面

将4.2的model的booklist显示在前端

![1603558158089](https://gitee.com/chrisxyq/picgo/raw/master/img/1603558158089.png)

## 5.添加书籍功能

先在addbook.jsp页面填写表单

表单的name属性对应于pojo的属性

表单required表示填写字段不允许为空

点击submit提交，则发送action请求提交，执行addbook的controller，执行完之后重定向到allbook请求，返回书籍列表显示页，实现请求的复用。

![1603558746439](https://gitee.com/chrisxyq/picgo/raw/master/img/1603558746439.png)

## 6.修改书籍功能

修改需要从前端传递bookid的参数

### 1.allbook.jsp页面：传递bookid的参数给controller

![1603559372194](https://gitee.com/chrisxyq/picgo/raw/master/img/1603559372194.png)

### 2.controller：查出book对象，放入model

controller根据前端传递的bookid查出book对象，将book对象放入model，前端从model取出book对象并在页面进行展示

![1603559624211](https://gitee.com/chrisxyq/picgo/raw/master/img/1603559624211.png)

### 3.updatebook.jsp页面：取model数据，jsp页面的value标签设置默认值

![1603559782277](https://gitee.com/chrisxyq/picgo/raw/master/img/1603559782277.png)

### 4.出现的问题：updatebook.jsp页面提交修改失败

发现sql语句执行失败，需要前端传递隐藏域给controller，即可解决

![1603560750117](https://gitee.com/chrisxyq/picgo/raw/master/img/1603560750117.png)

注：可以考虑在mybatis-config.xml配置日志

![1603560878696](https://gitee.com/chrisxyq/picgo/raw/master/img/1603560878696.png)

## 7.删除书籍功能

allbook.jsp

![1603561171203](https://gitee.com/chrisxyq/picgo/raw/master/img/1603561171203.png)

controller

![1603561108191](https://gitee.com/chrisxyq/picgo/raw/master/img/1603561108191.png)

## 9.ajax：异步的javascript和xml：实现局部更新网页

### 1.整体介绍

使用jquery库实现ajax：点击输入框以及输入框内容改变均会发送一个事件

ajax请求：不刷新页面，只实现前后端交换数据，如果让后端的controller接管视图，则无论是重定向或者转发都将刷新视图，要实现不刷新视图，需要让前端接管视图，这是通过ajax的callback实现的

整体流程：控件onblur属性绑定function，function通过ajax发送post请求。post请求包括url，data和callback，url负责映射到后端的controlller，data负责传递数据到controller，controller返回json数据给callback，callback的function负责前端托管视图

### 2.示例1：input控件：input-onblur示例

jsp控件：页面导入jquery库

![1603636982575](https://gitee.com/chrisxyq/picgo/raw/master/img/1603636982575.png)

![1603637183786](https://gitee.com/chrisxyq/picgo/raw/master/img/1603637183786.png)

function：success和error属性分别表示请求成功与失败之后的回调函数

![1603637018523](https://gitee.com/chrisxyq/picgo/raw/master/img/1603637018523.png)

后端controller：接收function的post请求传递的参数，通过response回传数据给前端用于视图显示，而无需返回和刷新视图

controller的形参与data的键值一致

![1603637302361](https://gitee.com/chrisxyq/picgo/raw/master/img/1603637302361.png)

### 3.示例2：button控件：button-click示例

jsp的btn：

![1603638106493](https://gitee.com/chrisxyq/picgo/raw/master/img/1603638106493.png)

ajax function：

post的url调用后端的

![1603638144694](https://gitee.com/chrisxyq/picgo/raw/master/img/1603638144694.png)

controller

![1603638290728](https://gitee.com/chrisxyq/picgo/raw/master/img/1603638290728.png)

