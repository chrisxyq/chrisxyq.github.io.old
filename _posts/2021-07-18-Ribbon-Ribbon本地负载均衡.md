---
layout: post
title: Ribbon本地负载均衡
catalog: true
tags:
    - Ribbon
---

Ribbon本地负载均衡/进程内：（与Nginx服务端负载均衡/集中式不同）从注册中心获取注册信息，缓存到jvm内，在本地rpc使用restTemplate远程调用服务

![image-20210730150511810](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210730150511810.png)

