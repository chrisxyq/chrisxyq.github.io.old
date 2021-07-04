---
layout: post
title: jsp、cookie和session
catalog: true
tags:
    - Javaweb
---
# 10.jsp：代替servlet回传html页面的数据
![image-20210103154342842](https://gitee.com/chrisxyq/picgo/raw/master/img/1600154147868.png)

![1603355599087](https://gitee.com/chrisxyq/picgo/raw/master/img/1603355599087.png)

![1603355599087](https://gitee.com/chrisxyq/picgo/raw/master/img/1603355599087.png)

![1600154610901](https://gitee.com/chrisxyq/picgo/raw/master/img/1600154610901.png)

## 10.1jsp声明脚本

可以在jsp文件里面声明类属性、静态代码块、类方法、静态内部类等

则可以将在jsp文件里声明的这些东西，自动包含到生成的java类

![1603356049581](https://gitee.com/chrisxyq/picgo/raw/master/img/1603356049581.png)

## 10.2jsp表达式脚本：在jsp页面输出数据

![1603356513798](https://gitee.com/chrisxyq/picgo/raw/master/img/1603356513798.png)

## 10.3jsp代码脚本：编写需要的功能

if语句、for循环

![1603356686664](https://gitee.com/chrisxyq/picgo/raw/master/img/1603356686664.png)

## 10.4jsp九大内置对象

tomcat将jsp页面翻译成java类对象

其中jspservice方法比较关键

![1603356957633](https://gitee.com/chrisxyq/picgo/raw/master/img/1603356957633.png)

## 10.5jsp四大域对象：包含于九大内置对象

![1603357104849](https://gitee.com/chrisxyq/picgo/raw/master/img/1603357104849.png)

假设原本四大域对象均有值

请求转发：仅pagecontext无效（页面级别

然后刷新页面：request无效（请求级别

重启浏览器：session无效（会话级别

![1603357587892](https://gitee.com/chrisxyq/picgo/raw/master/img/1603357587892.png)

## 10.6jsp静态包含、动态包含、请求转发

![1603357875946](https://gitee.com/chrisxyq/picgo/raw/master/img/1603357875946.png)

![1603357939671](https://gitee.com/chrisxyq/picgo/raw/master/img/1603357939671.png)

一般用静态包含，用于输出页面数据

![1603358044874](https://gitee.com/chrisxyq/picgo/raw/master/img/1603358044874.png)

## 10.7jsp中请求转发的流程/使用说明

![1603358862704](https://gitee.com/chrisxyq/picgo/raw/master/img/1603358862704.png)

servlet将sql查询到的信息保存到request域中，并通过请求转发携带到jsp

![1603359315179](https://gitee.com/chrisxyq/picgo/raw/master/img/1603359315179.png)

jsp负责从request域中获取信息，并遍历，将数据输出到前端客户端显示

![1603359416805](https://gitee.com/chrisxyq/picgo/raw/master/img/1603359416805.png)

## 10.8EL表达式：代替表达式脚本输出数据

![1603360599319](https://gitee.com/chrisxyq/picgo/raw/master/img/1603360599319.png)

# 14.cookie

## 1.概念

![1603421233642](https://gitee.com/chrisxyq/picgo/raw/master/img/1603421233642.png)

![1600154731698](https://gitee.com/chrisxyq/picgo/raw/master/img/1600154731698.png)

## 2.服务器创建cookie并通知客户端保存cookie

![1600155345567](https://gitee.com/chrisxyq/picgo/raw/master/img/1600155345567.png)

## 3.服务器获取客户端所有的cookie

![1600155590453](https://gitee.com/chrisxyq/picgo/raw/master/img/1600155590453.png)

服务器需要遍历才能查找到指定的cookie

## 4.cookie值的修改

![1603423033477](https://gitee.com/chrisxyq/picgo/raw/master/img/1603423033477.png)

![1603423102623](https://gitee.com/chrisxyq/picgo/raw/master/img/1603423102623.png)

## 5.cookie的存活周期的设置

cookie的默认存活周期为session级别的，即浏览器一关，cookie就没了

![1600156915911](https://gitee.com/chrisxyq/picgo/raw/master/img/1600156915911.png)

## 6.cookie的有效路径path的设置：过滤哪些cookie可以发送给服务器

![1600157056952](https://gitee.com/chrisxyq/picgo/raw/master/img/1600157056952.png)

![1600157254345](https://gitee.com/chrisxyq/picgo/raw/master/img/1600157254345.png)

## 6.免用户名登录

客户端先获取login.jsp登录页面

客户端输入表单，点击提交，运行loginservlet程序

若用户名密码正确，则把用户名保存为cookie发送给**客户端**，浏览器得到了cookie信息

第二次登陆：客户端把cookie信息也发送给服务器

客户端将得到带有用户名的login.jsp登录表单，从而达到免用户名登录的效果

![1600157486752](https://gitee.com/chrisxyq/picgo/raw/master/img/1600157486752.png)

jsp页面

![1603424338320](https://gitee.com/chrisxyq/picgo/raw/master/img/1603424338320.png)

服务器servlet程序

![1603424154870](https://gitee.com/chrisxyq/picgo/raw/master/img/1603424154870.png)

# 15.session会话

![1600158029045](https://gitee.com/chrisxyq/picgo/raw/master/img/1600158029045.png)

## 15.1创建/获取session会话

![1603431241004](https://gitee.com/chrisxyq/picgo/raw/master/img/1603431241004.png)

## 15.2session域数据的存取

![1603431831841](https://gitee.com/chrisxyq/picgo/raw/master/img/1603431831841.png)

## 15.3浏览器与session关联的技术内幕

![1603434223814](https://gitee.com/chrisxyq/picgo/raw/master/img/1603434223814.png)

