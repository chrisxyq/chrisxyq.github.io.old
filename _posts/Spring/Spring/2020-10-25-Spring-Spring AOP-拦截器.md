---
layout: post
title: Spring AOP-拦截器
catalog: true
tags:
    - Spring

---



## 10.拦截器：AOP

![1603638976290](https://gitee.com/chrisxyq/picgo/raw/master/img/1603638976290.png)

### 1.实现拦截器的示例

#### 1.在applicatiioncontext.xml配置拦截器：bean的class和拦截路径（类似过滤器的配置

![1603639598748](https://gitee.com/chrisxyq/picgo/raw/master/img/1603639598748.png)

#### 2.实现接口：写一个拦截器

![1603639858913](https://gitee.com/chrisxyq/picgo/raw/master/img/1603639858913.png)

将在controller执行前后运行

![1603639785275](https://gitee.com/chrisxyq/picgo/raw/master/img/1603639785275.png)

### 2.登录判断验证

![1603640035045](https://gitee.com/chrisxyq/picgo/raw/master/img/1603640035045.png)

登录和注销的controller：通过存取session实现

![1603640934125](https://gitee.com/chrisxyq/picgo/raw/master/img/1603640934125.png)

登录拦截器：点击主页按钮，若服务器没有session，将转发到登录页

![1603641032275](https://gitee.com/chrisxyq/picgo/raw/master/img/1603641032275.png)

