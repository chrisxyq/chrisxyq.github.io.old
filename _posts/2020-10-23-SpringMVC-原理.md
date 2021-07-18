---
layout: post
title: SpringMVC原理
catalog: true
tags:
    - SpringMVC

---



## SpringMVC的流程与配置

### 本质/理论图/流程

![1599139381071](https://gitee.com/chrisxyq/picgo/raw/master/img/1599139381071.png)

springmvc需要我们实现的部分，虚线部分：controller层调业务层、设置视图返回的名字

![1600227020085](https://gitee.com/chrisxyq/picgo/raw/master/img/1600227020085.png)

#### step1：dispatcherServlet适配请求

根据如下的web.xml的配置可知：

![1603438913354](https://gitee.com/chrisxyq/picgo/raw/master/img/1603438913354.png)

dispatcherServlet前置控制器，将匹配所有的请求，举例如下：

![1603439017601](https://gitee.com/chrisxyq/picgo/raw/master/img/1603439017601.png)

#### step234：handlerMapping根据url查找handler：**处理器映射器**

![1603439415449](https://gitee.com/chrisxyq/picgo/raw/master/img/1603439415449.png)

![1603439469453](https://gitee.com/chrisxyq/picgo/raw/master/img/1603439469453.png)

![1603439710189](https://gitee.com/chrisxyq/picgo/raw/master/img/1603439710189.png)

#### step5678：交给controller来做：**处理器适配器**

![1603440038362](https://gitee.com/chrisxyq/picgo/raw/master/img/1603440038362.png)

controller返回modelandview

![1603440089447](https://gitee.com/chrisxyq/picgo/raw/master/img/1603440089447.png)

#### step9、10、11、12：**视图解析器**

为视图拼接前缀和后缀，找到视图

将model数据渲染到这个视图上

![1603440236105](https://gitee.com/chrisxyq/picgo/raw/master/img/1603440236105.png)

### 代码配置

#### 1.web.xml里面配置MVC的dispatchServlet：**关联一个springmvc的配置文件**

配置dispatchServlet启动级别为与服务器一起启动

![1603440884525](https://gitee.com/chrisxyq/picgo/raw/master/img/1603440884525.png)

#### 2.a配置springmvc.xml：三大组件配置：原理讲解版本、未使用注解

在src的resources下新建springmvc.xml

最上面的两个bean是url处理器和url适配器，它们的作用在于搜索项目里面有哪些controller可以处理，匹配到了最下面的hellocontroller处理hello请求

![1603441644316](https://gitee.com/chrisxyq/picgo/raw/master/img/1603441644316.png)

#### 2.b简化后的springmvc.xml：使用注解简化后的版本：实际采用

开启注解驱动：简化配置、基于注解的配置简化

![1600236386007](https://gitee.com/chrisxyq/picgo/raw/master/img/1600236386007.png)

![1603442267481](https://gitee.com/chrisxyq/picgo/raw/master/img/1603442267481.png)

其中，component-scan扫描controller包下的所有注解

default-servlet-handler:过滤静态资源，仅托管jsp和servlet的请求

annotation-driven：通过开启注解驱动，简化配置映射器、适配器等

![1600235534075](https://gitee.com/chrisxyq/picgo/raw/master/img/1600235534075.png)



#### 3.使用@controller注解

除了实现controller接口之外，也可使用@controller注解的方式，加了注解之后，可以自动被spring扫描到，作为一个组件，自动装配。如下图所示：

![1593960080805](https://gitee.com/chrisxyq/picgo/raw/master/img/1593960080805.png)

![1603443827886](https://gitee.com/chrisxyq/picgo/raw/master/img/1603443827886.png)

进入hellocontroller之后，返回模型与视图，模型用于存数据，视图决定跳转到哪个地方。

注意，@requestmapping修饰类和函数，其路径会有父子关系。return的hello会被视图解析器处理，进行视图路径的前缀和后缀拼接。找到jsp页面，并返回jsp页面。

注意：与@requestmapping不同，如果使用注解@restcontroller，则不会被视图解析器解析，return的内容将作为字符串返回。

多个controller可以return同一个视图，说明视图是可以被复用的。因此一个网站不一定有很多网页，只是更新了网页显示的内容。

![1600237819039](https://gitee.com/chrisxyq/picgo/raw/master/img/1600237819039.png)