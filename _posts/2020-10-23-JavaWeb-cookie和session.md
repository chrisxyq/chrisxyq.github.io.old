---
layout: post
title: cookie和session
catalog: true
tags:
    - JavaWeb
---
# cookie

## 概念

![1603421233642](https://gitee.com/chrisxyq/picgo/raw/master/img/1603421233642.png)

![1600154731698](https://gitee.com/chrisxyq/picgo/raw/master/img/1600154731698.png)

## 服务器创建cookie并通知客户端保存cookie

![1600155345567](https://gitee.com/chrisxyq/picgo/raw/master/img/1600155345567.png)

## 服务器获取客户端所有的cookie

![1600155590453](https://gitee.com/chrisxyq/picgo/raw/master/img/1600155590453.png)

服务器需要遍历才能查找到指定的cookie

## cookie值的修改

![1603423033477](https://gitee.com/chrisxyq/picgo/raw/master/img/1603423033477.png)

![1603423102623](https://gitee.com/chrisxyq/picgo/raw/master/img/1603423102623.png)

## cookie的存活周期的设置

cookie的默认存活周期为session级别的，即浏览器一关，cookie就没了

![1600156915911](https://gitee.com/chrisxyq/picgo/raw/master/img/1600156915911.png)

## cookie的有效路径path的设置：过滤哪些cookie可以发送给服务器

![1600157056952](https://gitee.com/chrisxyq/picgo/raw/master/img/1600157056952.png)

![1600157254345](https://gitee.com/chrisxyq/picgo/raw/master/img/1600157254345.png)

## 免用户名登录

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

# session会话

![1600158029045](https://gitee.com/chrisxyq/picgo/raw/master/img/1600158029045.png)

## 创建/获取session会话

![1603431241004](https://gitee.com/chrisxyq/picgo/raw/master/img/1603431241004.png)

## session域数据的存取

![1603431831841](https://gitee.com/chrisxyq/picgo/raw/master/img/1603431831841.png)

## 浏览器与session关联的技术内幕

![1603434223814](https://gitee.com/chrisxyq/picgo/raw/master/img/1603434223814.png)

