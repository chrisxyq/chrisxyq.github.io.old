---
layout: post
title: jsp
catalog: true
tags:
    - JavaWeb

---

# jsp：代替servlet回传html页面的数据

![image-20210103154342842](https://gitee.com/chrisxyq/picgo/raw/master/img/1600154147868.png)

![1603355599087](https://gitee.com/chrisxyq/picgo/raw/master/img/1603355599087.png)

![1603355599087](https://gitee.com/chrisxyq/picgo/raw/master/img/1603355599087.png)

![1600154610901](https://gitee.com/chrisxyq/picgo/raw/master/img/1600154610901.png)

## jsp声明脚本

可以在jsp文件里面声明类属性、静态代码块、类方法、静态内部类等

则可以将在jsp文件里声明的这些东西，自动包含到生成的java类

![1603356049581](https://gitee.com/chrisxyq/picgo/raw/master/img/1603356049581.png)

## jsp表达式脚本：在jsp页面输出数据

![1603356513798](https://gitee.com/chrisxyq/picgo/raw/master/img/1603356513798.png)

## jsp代码脚本：编写需要的功能

if语句、for循环

![1603356686664](https://gitee.com/chrisxyq/picgo/raw/master/img/1603356686664.png)

## jsp九大内置对象

tomcat将jsp页面翻译成java类对象

其中jspservice方法比较关键

![1603356957633](https://gitee.com/chrisxyq/picgo/raw/master/img/1603356957633.png)

## jsp四大域对象：包含于九大内置对象

![1603357104849](https://gitee.com/chrisxyq/picgo/raw/master/img/1603357104849.png)

假设原本四大域对象均有值

请求转发：仅pagecontext无效（页面级别

然后刷新页面：request无效（请求级别

重启浏览器：session无效（会话级别

![1603357587892](https://gitee.com/chrisxyq/picgo/raw/master/img/1603357587892.png)

## jsp静态包含、动态包含、请求转发

![1603357875946](https://gitee.com/chrisxyq/picgo/raw/master/img/1603357875946.png)

![1603357939671](https://gitee.com/chrisxyq/picgo/raw/master/img/1603357939671.png)

一般用静态包含，用于输出页面数据

![1603358044874](https://gitee.com/chrisxyq/picgo/raw/master/img/1603358044874.png)

## jsp中请求转发的流程/使用说明

![1603358862704](https://gitee.com/chrisxyq/picgo/raw/master/img/1603358862704.png)

servlet将sql查询到的信息保存到request域中，并通过请求转发携带到jsp

![1603359315179](https://gitee.com/chrisxyq/picgo/raw/master/img/1603359315179.png)

jsp负责从request域中获取信息，并遍历，将数据输出到前端客户端显示

![1603359416805](https://gitee.com/chrisxyq/picgo/raw/master/img/1603359416805.png)

## EL表达式：代替表达式脚本输出数据

![1603360599319](https://gitee.com/chrisxyq/picgo/raw/master/img/1603360599319.png)

