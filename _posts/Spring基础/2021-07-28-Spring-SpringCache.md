---
layout: post
title: Spring-SpringCache
date:  2022-10-10
catalog: true
tags:
    - Spring

---

![image-20210728160408553](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210728160408553.png)

| 配置 | application.properties增加配置spring.cache.type=caffeine   spring.cache.type = caffeine  spring.cache.caffeine.spec =  initialCapacity=500,maximumSize=4000,expireAfterWrite=1d  caffeine.cache.config.initialCapacity =  100  caffeine.cache.config.maximnumSize = 1000  caffeine.cache.config.expireAfterWrite =  5 |
| ---- | ------------------------------------------------------------ |
| 使用 | @CacheConfig(cacheNames =   {"commision:rule"})：用在service上  @Cacheable(cacheNames =  "policy", key = "#polno")：用在方法  @CacheEvict(cacheNames =  "policy", key = "#polno")：用在方法 |

**Caffeine怎么保证两次并发查询时，只查询一次数据库**

