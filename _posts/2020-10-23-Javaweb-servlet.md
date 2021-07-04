---
layout: post
title: Servlet
catalog: true
tags:
    - Javaweb
---
# 1.servlet：运行在服务器上的java小程序

## 1.手动实现servlet程序：实现servlet接口

![1600132921850](https://gitee.com/chrisxyq/picgo/raw/master/img/1600132921850.png)

### 1.1实现servlet接口，重写service方法用于处理请求和响应

![1603337110649](https://gitee.com/chrisxyq/picgo/raw/master/img/1603337110649.png)

![1603337154571](https://gitee.com/chrisxyq/picgo/raw/master/img/1603337154571.png)

### 1.2在web.xml配置servlet和servlet-mapping标签：配置servlet程序别名以及访问地址

url-pattern配置的就是servlet的访问地址，以斜杠打头

下图的两个servlet-name需要保持一致

![1600133357161](https://gitee.com/chrisxyq/picgo/raw/master/img/1600133357161.png)

## 2.url地址如何定位到servlet

url地址提供了服务器ip以及端口号、以及服务器工程路径下的具体资源路径

然后根据客户端请求的资源路径在web.xml里面找到映射的servlet名字

然后执行相应的servlet功能代码

![1600133841551](https://gitee.com/chrisxyq/picgo/raw/master/img/1600133841551.png)

## 3.servlet生命周期

1.执行构造器方法

2.执行init初始化方法

===以上，在第一次访问时，创建servlet时候被调用，因此servlet是单例的===

3.执行service方法：每次访问都被调用

4.执行destroy销毁方法：在web工程停止时候，被调用



## 4.servlet中完成get和post请求的分发处理

### 4.1使用httpServletRequest的getMethod方法判断是get请求还是post请求

servletrequest有个子类叫httpServletRequest

通过使用httpServletRequest的getMethod方法，可以获取前端（form表单）提交的请求类型是get/post

![1600134532398](https://gitee.com/chrisxyq/picgo/raw/master/img/1600134532398.png)

![1603345741224](https://gitee.com/chrisxyq/picgo/raw/master/img/1603345741224.png)

### 4.2实际开发：继承httpservletrequest类、并重写doget和dopost



## 5.整个servlet类的继承体系

![1600135622605](https://gitee.com/chrisxyq/picgo/raw/master/img/1600135622605.png)

# 2.servletConfig类的方法/作用：每个servlet程序对应一个servletconfig对象

servlet的init方法中，将传入servletconfig对象

## 2.1.获取servlet在web.xml配置的别名

![1600135991319](https://gitee.com/chrisxyq/picgo/raw/master/img/1600135991319.png)

## 2.2获取servlet在web.xml配置的init-param初始化参数

![1600136184096](https://gitee.com/chrisxyq/picgo/raw/master/img/1600136184096.png)

## 2.3获取servletcontext对象

![1603348469504](https://gitee.com/chrisxyq/picgo/raw/master/img/1603348469504.png)

注意：

每个servlet程序对应一个servletconfig对象

![1603349074751](https://gitee.com/chrisxyq/picgo/raw/master/img/1603349074751.png)

servlet程序中所获取的servletconfig对象是在父类genericservlet的init方法中生成并保存的，因此重写init方法一定要调用父类的init方法

![1600136526171](https://gitee.com/chrisxyq/picgo/raw/master/img/1600136526171.png)

# 3.servletContext类：一个web工程只有一个servletContext实例

## 1.servletContext是一个域对象：域指的是存取数据的操作范围为web工程

![1600136729358](https://gitee.com/chrisxyq/picgo/raw/master/img/1600136729358.png)

## 2.作用

### 2.1获取web.xml配置的上下文参数context-param

context-param属于整个web工程，可以配置监听器、过滤器等

![1603349818548](https://gitee.com/chrisxyq/picgo/raw/master/img/1603349818548.png)

红框：

根据父类genericservlet的getservletconfig方法获取servletconfig对象

根据servletconfig对象获取servletcontext对象

![1603349920240](https://gitee.com/chrisxyq/picgo/raw/master/img/1603349920240.png)

### 2.2像map一样存取数据

![1603350625550](https://gitee.com/chrisxyq/picgo/raw/master/img/1603350625550.png)

# 4.http协议

## 1.get请求的内容：请求行、请求头

第一行为请求行

后面为请求头：键值对

![1600138067084](https://gitee.com/chrisxyq/picgo/raw/master/img/1600138067084.png)

![1603351060096](https://gitee.com/chrisxyq/picgo/raw/master/img/1603351060096.png)

## 2.post请求的内容：请求行、请求头、请求体

请求体：提交表单的隐藏域

![1600138557051](https://gitee.com/chrisxyq/picgo/raw/master/img/1600138557051.png)

## 3.响应的http协议格式

![1600139805845](https://gitee.com/chrisxyq/picgo/raw/master/img/1600139805845.png)

# 5.httpServletRequest类：tomcat将http请求信息封装进httpServletRequest类

![1600140380002](https://gitee.com/chrisxyq/picgo/raw/master/img/1600140380002.png)

## 5.1httpServletRequest类的方法

![1603352315212](https://gitee.com/chrisxyq/picgo/raw/master/img/1603352315212.png)

![1600140726436](https://gitee.com/chrisxyq/picgo/raw/master/img/1600140726436.png)

## 5.2httpServletRequest类获取请求的参数值

![1603353877973](https://gitee.com/chrisxyq/picgo/raw/master/img/1603353877973.png)

![1600141337461](https://gitee.com/chrisxyq/picgo/raw/master/img/1600141337461.png)

![1600141609226](https://gitee.com/chrisxyq/picgo/raw/master/img/1600141609226.png)

# 6.httpServletResponse类

![1603354608201](https://gitee.com/chrisxyq/picgo/raw/master/img/1603354608201.png)

![1603354651818](https://gitee.com/chrisxyq/picgo/raw/master/img/1603354651818.png)

![1600150151591](https://gitee.com/chrisxyq/picgo/raw/master/img/1600150151591.png)

![1603354814745](https://gitee.com/chrisxyq/picgo/raw/master/img/1603354814745.png)

解决回传字符中文乱码方法1

![1600150329067](https://gitee.com/chrisxyq/picgo/raw/master/img/1600150329067.png)

解决回传字符中文乱码方法2

![1600150427181](https://gitee.com/chrisxyq/picgo/raw/master/img/1600150427181.png)

# 7.请求转发：共享request域的数据

![1600141905531](https://gitee.com/chrisxyq/picgo/raw/master/img/1600141905531.png)

![1600148505120](https://gitee.com/chrisxyq/picgo/raw/master/img/1600148505120.png)

![1600148635314](https://gitee.com/chrisxyq/picgo/raw/master/img/1600148635314.png)

![1600148720726](https://gitee.com/chrisxyq/picgo/raw/master/img/1600148720726.png)

请求转发的特点：

不能访问外部的资源

![1600148942062](https://gitee.com/chrisxyq/picgo/raw/master/img/1600148942062.png)

## 请求转发中的base标签的作用

![1600149528943](https://gitee.com/chrisxyq/picgo/raw/master/img/1600149528943.png)

base标签可以指定页面跳转时候的参照路径，而忽略浏览器的地址。



# 8.请求重定向：不共享request域的数据

![1600150699813](https://gitee.com/chrisxyq/picgo/raw/master/img/1600150699813.png)

![1600150801429](https://gitee.com/chrisxyq/picgo/raw/master/img/1600150801429.png)

![1600150835026](https://gitee.com/chrisxyq/picgo/raw/master/img/1600150835026.png)

![1600151082010](https://gitee.com/chrisxyq/picgo/raw/master/img/1600151082010.png)

请求重定向的第二种方案（推荐

![1600151166565](https://gitee.com/chrisxyq/picgo/raw/master/img/1600151166565.png)

# 9.JavaEE三层架构

![1600151564587](https://gitee.com/chrisxyq/picgo/raw/master/img/1600151564587.png)

