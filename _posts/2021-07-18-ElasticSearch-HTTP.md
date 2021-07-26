---
layout: post
title: ElasticSearch基础
catalog: true
tags:
    - ElasticSearch


---

## 数据格式

索引、文档、字段

![image-20210719150005935](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/V6WHhoLrw48BTZA.png)

![image-20210719150441122](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210719150441122.png)



## HTTP

### 创建索引/文档

#### put创建索引：（数据库：向es服务器发送put请求

![image-20210719151126566](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210719151126566.png)



#### post创建文档：为索引创建文档（文档以json传递

![image-20210719151753575](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210719151753575.png)

- 注意这里不能是put请求，因为put请求是幂等性的，即只能添加一次。而post可以添加多次
- 这里的type可以随意指定

- 注意到这里的文档id是随机生成的，因此可以在创建时，指定文档id

#### post创建文档时，指定id

![image-20210719152314176](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210719152314176.png)

### put更新

#### put根据id更新文档

![image-20210719153306068](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210719153306068.png)

#### put设置字段查询属性properties、type（字段是否可以被分词、index（字段是否可以被查询

name可以被查询、可以被分词

sex可以被查询、不可以被分词

tel：不可以被查询、不可以被分词

![image-20210719164719451](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210719164719451.png)

### get查询

#### get查询索引：当前的所有索引

![image-20210719151352490](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210719151352490.png)

#### get根据id查询文档

![image-20210719152809366](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210719152809366.png)

#### get查询索引下的所有文档（_search

返回查询时间、是否超时、命中结果

![image-20210719152959992](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210719152959992.png)



#### get条件查询（match匹配查询json

![image-20210719154018464](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210719154018464.png)

#### get分页查询（match_all、from、size

from是起始页码，比如第二页：from就是（2-1）*size

![image-20210719154726393](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210719154726393.png)

#### 排序sort和查询指定字段_source

![image-20210719154935002](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210719154935002.png)

#### 多条件查询（bool、must/should、match

must：and

![image-20210719155704728](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210719155704728.png)

should：or

![image-20210719155821957](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210719155821957.png)

#### 范围查询（filter（与bool并列使用）、range、gt

![image-20210719160053749](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210719160053749.png)

#### 完全匹配查询match_phrase

注：match为全文检索匹配，即将搜索词进行分词后进行搜索，如下图查询，使用match_phrase将查不到数据、match将可以查到

![image-20210719160544290](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210719160544290.png)

#### 高亮显示highlight

对查询的结果字段进行高亮显示

![image-20210719160837914](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210719160837914.png)

#### 聚合分组（aggs、terms/avg、field：分组/平均值

![image-20210719161255243](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210719161255243.png)

注意：以上将在hits查出原始数据，若不要原始数据，可指定size：0

![image-20210719161405659](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210719161405659.png)



![image-20210726193231483](C:\Users\13692\AppData\Roaming\Typora\typora-user-images\image-20210726193231483.png)
