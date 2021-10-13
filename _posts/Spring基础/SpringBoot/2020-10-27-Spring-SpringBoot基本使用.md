---
layout: post
title: SpringBoot基本使用
catalog: true
tags:
    - Spring
---
## 1.Springboot配置

### 1.热部署配置：勾选

![1603675713272](https://gitee.com/chrisxyq/picgo/raw/master/img/1603675713272.png)

### 2.修改springboot自动配置的默认值：application.properties/yaml

properties只能保存键值对

yaml![1603676054869](https://gitee.com/chrisxyq/picgo/raw/master/img/1603676054869.png)

#### yaml用于给pojo赋值：@configurationproperties

首先介绍一些spring的注解

在实体类上使用@component注解：表示是一个spring的组件

@value给spring的属性赋值

![1603676386559](https://gitee.com/chrisxyq/picgo/raw/master/img/1603676386559.png)

但是每个属性都用value赋值比较麻烦，因此考虑使用yaml从外部进行赋值，示例如下

![1603676739168](https://gitee.com/chrisxyq/picgo/raw/master/img/1603676739168.png)

注：@configurationproperties支持松散绑定

![1603682985929](https://gitee.com/chrisxyq/picgo/raw/master/img/1603682985929.png)

#### properties给pojo赋值：@propertysource

![1603681775341](https://gitee.com/chrisxyq/picgo/raw/master/img/1603681775341.png)

### 3.JSR303校验：@validated和@Email

name属性需要为邮箱格式，自定义报错提示信息

![1603683369217](https://gitee.com/chrisxyq/picgo/raw/master/img/1603683369217.png)

![1603683662556](https://gitee.com/chrisxyq/picgo/raw/master/img/1603683662556.png)

### 4.application.properties/yaml的四个配置位置优先级

项目config文件夹下>项目根目录下>resources/config>resources目录下

![1603690844203](https://gitee.com/chrisxyq/picgo/raw/master/img/1603690844203.png)

### 5.springboot的多环境配置

#### application.properties配置

![1603691036682](https://gitee.com/chrisxyq/picgo/raw/master/img/1603691036682.png)

#### application.yaml配置

![1603691787477](https://gitee.com/chrisxyq/picgo/raw/master/img/1603691787477.png)

## 2.静态资源位置

静态资源存放路径：

![1603693437610](https://gitee.com/chrisxyq/picgo/raw/master/img/1603693437610.png)

根路径下的请求，localhost:8080/即可直接访问红框内的资源

注意：

- 其中，classpath类路径指的是java和resources
- 其中static文件夹是自带的，public和resources文件夹需要自己新建。且**访问优先级resources>static>public**
- 一般在public文件夹下放一些公共的资源如.js、static文件夹放图片等静态资源、resources文件夹放一些upload上传的文件
- 如果把首页（index.html）放到public或者static文件夹下，则可通过localhost:8080/即可直接访问首页（index.html）
- 如果希望首页（index.html）**通过controller来跳转，可将首页放到classpath/templates下**，该功能需要模板引擎的支持，需要导入thymeleaf的依赖，

- 自定义图标：新版本的springboot的icon需要放到resources/public文件夹下

## 3.thymeleaf

### html默认放置路径

thymeleaf渲染的页面需要放在类路径下的templates里面

![1591623146230](https://gitee.com/chrisxyq/picgo/raw/master/img/1591623146230.png)

### thymeleaf解析后端传来的model/map中的值

html需要导入thymeleaf命名空间

![1603696254955](https://gitee.com/chrisxyq/picgo/raw/master/img/1603696254955.png)

controller

![1603696347357](https://gitee.com/chrisxyq/picgo/raw/master/img/1603696347357.png)

html

![1603696108030](https://gitee.com/chrisxyq/picgo/raw/master/img/1603696108030.png)

注意：

- model.addattribute()：往前台传数据，可以传对象，可以传List，通过el表达式 ${}可以获取到
- model.addAttribute()方法不允许添加null的key，但是map.put()方法可以
- 如果没有修改controller里面的代码，只改了html的话，只需要对html使用ctrl+F9刷新就可以

### thymeleaf语法：转义、each、

![1603696717179](https://gitee.com/chrisxyq/picgo/raw/master/img/1603696717179.png)

## 4.扩展springmvc

### 自定义一个视图解析器

![1600668028657](https://gitee.com/chrisxyq/picgo/raw/master/img/1600668028657.png)

### 自定义视图跳转

## 5.springboot整合jdbc

将查出的数据发送到前端

![1600679001429](https://gitee.com/chrisxyq/picgo/raw/master/img/1600679001429.png)

## 6.springboot整合druid数据源

druid数据源与jdbc在使用无明显区别，优秀之处在于其自带日志以及sql监控系统

需要做如下配置

![1600680328393](https://gitee.com/chrisxyq/picgo/raw/master/img/1600680328393.png)

需要注意，方法也要注册到bean

![1600680379153](https://gitee.com/chrisxyq/picgo/raw/master/img/1600680379153.png)

![1600680462189](https://gitee.com/chrisxyq/picgo/raw/master/img/1600680462189.png)

![1600680410950](https://gitee.com/chrisxyq/picgo/raw/master/img/1600680410950.png)

## 7.springboot整合mybatis框架

配置数据库

![1600681217938](https://gitee.com/chrisxyq/picgo/raw/master/img/1600681217938.png)

![1600681693828](https://gitee.com/chrisxyq/picgo/raw/master/img/1600681693828.png)

repository保证这个类在spring里面，可以使得被autowired到controller里面

![1600681868001](https://gitee.com/chrisxyq/picgo/raw/master/img/1600681868001.png)

![1600682088075](https://gitee.com/chrisxyq/picgo/raw/master/img/1600682088075.png)

