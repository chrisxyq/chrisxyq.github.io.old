---
layout: post
title: 中间件-Redis-init
date:  2022-10-10
catalog: true
tags:
    - Redis

---

[windows安装redis](https://github.com/dmajkic/redis/downloads)

## 使用Jedis测试Redis的api

### pom依赖

```xml
    <dependencies>
        <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <version>3.2.0</version>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.62</version>
        </dependency>
    </dependencies>
```

### string测试

```java
    /**
     * 待补充api测试代码
     * 需要启动E:\mysoftware\redis-2.4.5-win32-win64\64bit\redis-server.exe
     */
    @Test
    public void test() {
        Jedis jedis = new Jedis("127.0.0.1", 6379);
        System.out.println(jedis.ping());
    }
```

### set测试



### list测试



### hash测试

### 事务测试

```java
    @Test
    public void test2() {
        Jedis jedis = new Jedis("127.0.0.1", 6379);
        jedis.flushDB();
        JSONObject jsonObject = new JSONObject();
        jsonObject.put("hello", "world");
        jsonObject.put("name", "kuangshen");
        Transaction multi = jedis.multi();
        String s = jsonObject.toJSONString();
//        jedis.watch(s);
        try {
            multi.set("user1", s);
            multi.set("user2", s);
            int i = 1 / 0;
            multi.exec();
        } catch (Exception e) {
            multi.discard();
            e.printStackTrace();
        } finally {
            System.out.println(jedis.get("user1"));
            System.out.println(jedis.get("user2"));
            jedis.close();
        }
    }
```

## Redis集成SpringBoot

### pom

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
```

### RedisTemplate



### RedisUtil





