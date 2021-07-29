---
layout: post
title: GateWay
catalog: true
tags:
    - SpringCloud



---

### 微服务架构网关：路由、断言、过滤

![image-20210726200310342](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210726200310342.png)

gateway的工作流程：路由转发+执行过滤器链

![image-20210726202537353](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210726202537353.png)

![image-20210726202904618](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210726202904618.png)

### 异步非阻塞模型

![image-20210726200731858](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210726200731858.png)

### gateway配置路由的两种方式

#### 代码中注入routeLocator

localhost:9527/guonei将路由至百度新闻

![image-20210727100543576](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210727100543576.png)

#### 配置文件

![image-20210727100857154](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210727100857154.png)

断言成功，将使用网关端口9527替换真实端口8001

### gateway常用的predicate

predicate相当于一种匹配断言

![image-20210727134038501](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210727134038501.png)

例：限制某事件之后、某路径下、且带上cookie的访问才有效果

![image-20210727135034294](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210727135034294.png)

### gateway的自定义filter过滤器

![image-20210727135310280](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210727135310280.png)

