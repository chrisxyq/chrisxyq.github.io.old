---
layout: post
title: SpringMVC
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

## 在单元方法里面获取请求参数的方法

#### 1.紧耦合

![1599142063183](https://gitee.com/chrisxyq/picgo/raw/master/img/1599142063183.png)

#### 2.解耦合方式（形参名和请求数据键名一致

![1599142406725](https://gitee.com/chrisxyq/picgo/raw/master/img/1599142406725.png)

#### 3.解耦合方式（形参名和请求数据键名不一致

提交的域名称和处理方法参数不一致

![1599143315510](https://gitee.com/chrisxyq/picgo/raw/master/img/1599143315510.png)

![1599143334472](https://gitee.com/chrisxyq/picgo/raw/master/img/1599143334472.png)

#### 4.解耦合方式（使用实体类接收请求参数

![1599145061723](https://gitee.com/chrisxyq/picgo/raw/master/img/1599145061723.png)

## SpringMVC处理restful风格的请求

### restful传递参数@pathVariable

![1603444602406](https://gitee.com/chrisxyq/picgo/raw/master/img/1603444602406.png)



![1593962427637](https://gitee.com/chrisxyq/picgo/raw/master/img/1593962427637.png)

![1599147373919](https://gitee.com/chrisxyq/picgo/raw/master/img/1599147373919.png)

### 指定请求方式的两种方法

方法1：@requestmapping+请求方式

![1603445172014](https://gitee.com/chrisxyq/picgo/raw/master/img/1603445172014.png)

方法2：组合注解

![1603445057442](https://gitee.com/chrisxyq/picgo/raw/master/img/1603445057442.png)

![1603445068222](https://gitee.com/chrisxyq/picgo/raw/master/img/1603445068222.png)

## springmvc实现转发和重定向

### 方法1：不使用视图解析器

请求转发

![1603446047804](https://gitee.com/chrisxyq/picgo/raw/master/img/1603446047804.png)

重定向

![1603446093579](https://gitee.com/chrisxyq/picgo/raw/master/img/1603446093579.png)

### 方法2：有视图解析器

![1603446122934](https://gitee.com/chrisxyq/picgo/raw/master/img/1603446122934.png)



## controller返回json数据：jackson、fastjson

把对象以json字符串的形式输出



### @RestController@ResponseBody

使用以上注解的controller，不会被视图解析器解析，返回的就是字符串。

传json格式就使用该注解。

由于controller注解返回的字符串默认会被视图解析器解析，因此如果要返回json格式的话，需要使用@RestController注解，从而取消视图解析器解析。

### jackon

传递单个对象

![1600245321329](https://gitee.com/chrisxyq/picgo/raw/master/img/1600245321329.png)

传递arraylist

![1600245555755](https://gitee.com/chrisxyq/picgo/raw/master/img/1600245555755.png)



## 错误状态码

### 500错误：

servlet的request理论上需要传入参数，而实际访问页面时没有携带参数

servlet程序在获取前端参数时候发生错误

![1603436659505](https://gitee.com/chrisxyq/picgo/raw/master/img/1603436659505.png)

### 405:客户端发送了不支持的请求方式

如controller只接受post，但是客户端却发送了get请求