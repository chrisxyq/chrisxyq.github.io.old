---
layout: post
title: ZooKeeper&consul注册中心基础
catalog: true
tags:
    - 注册中心
---

### SpringBoot集成zookeeper

#### 服务端注册zookeeper

##### pom和yml

![image-20210730100944127](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210730100944127.png)

把服务注册到zookeeper的ip和端口号

![image-20210730111241966](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210730111241966.png)

##### 主启动类：EnableDiscoveryClient(用于向consul/zookeeper注册服务)&业务类

![image-20210730101610359](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210730101610359.png)

![image-20210730111443736](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210730111443736.png)

##### 测试

![image-20210730111618899](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210730111618899.png)

查看注册到zookeeper的详细信息，微服务提供者入驻zookeeper

![image-20210730111721078](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210730111721078.png)

#### 客户端注册zookeeper

##### 消费方调用服务方：使用restTemplate

![image-20210730113751830](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210730113751830.png)

![image-20210730114633812](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210730114633812.png)

总结：

consul和zookeeper是CP的（不要高可用，关心是数据的强一致性

consul有可视化界面

![image-20210730141321687](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210730141321687.png)

AP举例：

![image-20210730141707824](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210730141707824.png)

