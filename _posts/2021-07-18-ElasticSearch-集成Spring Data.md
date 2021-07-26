---
layout: post
title: 集成Spring Data
catalog: true
tags:
    - ElasticSearch



---

## ES集成Spring Data实例

### ES的配置类

读取前缀为XX的配置文件，赋值给host和port

![image-20210719172753402](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210719172753402.png)

### 实体类的dao（ESRepo

![image-20210719173016369](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210719173016369.png)

### 商品实体类

type决定字段是否可以被分词、index决定字段是否可以被检索

![image-20210719173203821](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210719173203821.png)

### 测试：创建和删除索引restTemplate

![image-20210719173643181](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210719173643181.png)

### 测试：CRUD实体类（ESRepo

![image-20210719174551644](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210719174551644.png)

### 分页查询：pageRequest

![image-20210719175113470](C:\Users\13692\AppData\Roaming\Typora\typora-user-images\image-20210719175113470.png)

### 条件查询：termQueryBuilder

![image-20210719175458101](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210719175458101.png)

条件查询结合分页查询

![image-20210719175730950](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210719175730950.png)

## ES分片策略

- 一个分片底层即为lucene索引，会消耗一定的内存和cpu
- 如果有许多分片，可能影响相关度统计
- 分片数不超过节点数的三倍
- 每个分片占用的硬盘容量不超过ES的最大JVM堆空间设置（32G

