---
layout: post
title: Spring-AOP SpringSecurity
catalog: true
tags:
    - Spring
---
基于aop的拦截器和安全框架

## SpringSecurity

![1600739505412](https://gitee.com/chrisxyq/picgo/raw/master/img/1600739505412.png)

### 1.授权设置：http安全策略

![1600688537311](https://gitee.com/chrisxyq/picgo/raw/master/img/1600688537311.png)

![1600689375590](https://gitee.com/chrisxyq/picgo/raw/master/img/1600689375590.png)

### 2.用户认证

![1600688965286](https://gitee.com/chrisxyq/picgo/raw/master/img/1600688965286.png)

.roles("vip1")设置用户权限

使用jdbc数据源进行用户认证

![1600689132580](https://gitee.com/chrisxyq/picgo/raw/master/img/1600689132580.png)

### 3.注销

前端html

![1600689423030](https://gitee.com/chrisxyq/picgo/raw/master/img/1600689423030.png)

指定注销之后返回的页面：在configure(http)方法里面添加这句话

![1600689675721](https://gitee.com/chrisxyq/picgo/raw/master/img/1600689675721.png)

记住我功能：

![1600738880423](https://gitee.com/chrisxyq/picgo/raw/master/img/1600738880423.png)

### 4.security-thymeleaf：菜单根据用户角色动态实现

![1600690393095](https://gitee.com/chrisxyq/picgo/raw/master/img/1600690393095.png)

未登录和登录后页面上方差异化显示

![1600690502935](https://gitee.com/chrisxyq/picgo/raw/master/img/1600690502935.png)

semantic ui

## apache shiro

### 1.拦截

与上述类似，在config类里面配置

![1600742335859](https://gitee.com/chrisxyq/picgo/raw/master/img/1600742335859.png)

拦截：若无权限，则默认返回到首页

### 2.认证：realm

![1600742776310](https://gitee.com/chrisxyq/picgo/raw/master/img/1600742776310.png)

若用户点击登录，则执行认证方法

![1600745586966](https://gitee.com/chrisxyq/picgo/raw/master/img/1600745586966.png)

认证里面的token是从controller里面的方法传过来的

![1600743243909](https://gitee.com/chrisxyq/picgo/raw/master/img/1600743243909.png)

异常由配置类判断，由controller抛出

### 3.授权：realm

在config类里面配置授权检验以及未授权访问页面

![1600753048867](https://gitee.com/chrisxyq/picgo/raw/master/img/1600753048867.png)

进入需要授权的页面时，将自动执行授权方法，只有具有字符串授权的用户才能访问

![1600746347819](https://gitee.com/chrisxyq/picgo/raw/master/img/1600746347819.png)

若要访问，需要在controller方法中，为用户添加字符串授权

实际应该在数据库添加权限字段

![1600746441558](https://gitee.com/chrisxyq/picgo/raw/master/img/1600746441558.png)

### 4.整合thymeleaf

![1600754013541](https://gitee.com/chrisxyq/picgo/raw/master/img/1600754013541.png)

## 异步任务：@Async

目标：网页立即响应字符串，数据处理过程在后台执行

![1600758701071](https://gitee.com/chrisxyq/picgo/raw/master/img/1600758701071.png)

![1600758761228](https://gitee.com/chrisxyq/picgo/raw/master/img/1600758761228.png)

## 定时任务：@Scheduled以及cron表达式

![1600760467110](https://gitee.com/chrisxyq/picgo/raw/master/img/1600760467110.png)