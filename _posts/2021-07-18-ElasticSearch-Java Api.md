---
layout: post
title: Java API
catalog: true
tags:
    - ElasticSearch



---



## esClient.indices()索引增删查

### 创建索引esClient.indices().create(CreateIndexRequest

![image-20210720114254569](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210720114254569.png)

### 查询索引esClient.indices().get(GetIndexRequest

![image-20210720133616655](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210720133616655.png)

### 删除索引esClient.indices().delete(DeleteIndexRequest

![image-20210720134054943](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210720134054943.png)

## esClient.index()/update索引内数据的新增/修改

### 索引内新增数据esClient.index(IndexRequest)

![image-20210720135925550](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210720135925550.png)



### 索引内数据修改esClient.update(UpdateRequest)

![image-20210720140628114](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210720140628114.png)



### 索引内数据删除esClient.delete(DeleteRequest)

![image-20210720141249927](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210720141249927.png)

### 索引内批量操作数据esClient.bulk(BulkRequest)

返回花费时间和多个响应

批量插入举例如下：

![image-20210720141924779](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210720141924779.png)

批量删除举例如下：

![image-20210720142217829](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210720142217829.png)

### 指定查询esClient.get(GetRequest)

![image-20210720141146552](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210720141146552.png)

### 索引内数据查询esClient.search SearchSourceBulider.query(传入的对象不同)

#### 全量查询QueryBuilders.matchAllQuery

全量/条件/分页查询的区别在于SearchSourceBulider不同

![image-20210720144017109](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210720144017109.png)

#### 条件查询QueryBuilders.termQuery

![image-20210720144324228](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210720144324228.png)





#### 组合查询QueryBuilders.boolQuery must且、should或者

![image-20210720150447494](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210720150447494.png)

#### 范围查询QueryBuilders.rangeQuery、gte大于等于、lte小于等于

![image-20210720150929171](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210720150929171.png)

#### 模糊查询QueryBuilders.fuzzyQuery

![image-20210720153645333](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210720153645333.png)



### 排序SearchSourceBuilder.sort/指定查询字段SearchSourceBuilder.fetchSource/分页查询SearchSourceBuilder.from、size/高亮SearchSourceBuilder.highlighter/聚合查询SearchSourceBuilder.aggregation

排序

![image-20210720145558401](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210720145558401.png)

指定查询字段

![image-20210720145636319](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210720145636319.png)

分页查询

![image-20210720145137847](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210720145137847.png)

高亮查询

![image-20210720154202805](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210720154202805.png)

聚合查询：分组/最值/均值

max最值

![image-20210720194231810](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210720194231810.png)

terms分组

![image-20210720194403248](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210720194403248.png)

