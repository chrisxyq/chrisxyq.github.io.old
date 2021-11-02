---
layout: post
title: SpringCache-SpringCache整合redis
date:  2022-10-10
catalog: true
tags:
    - SpringCache

---

## springBoot+myBatis+springCache+redis整合

### pom依赖

```xml
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.0.1</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.1.10</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-cache</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
```

### application.properties

```properties
spring.redis.host=127.0.0.1
spring.redis.port=6379
server.port=8081
#mapper xml 文件地址
mybatis.mapper-locations=classpath*:mapper/*Mapper.xml
#	数据库url
spring.datasource.url=jdbc:mysql://localhost:3306/test_db?useUnicode=true&characterEncoding=utf-8&useSSL=false&allowMultiQueries=true&serverTimezone=Asia/Shanghai
#	数据库用户名
spring.datasource.username=root
#	数据库密码
spring.datasource.password=123456
#	数据库驱动
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
logging.level.com.example.springbootredisdemo.mapper=debug
#配置缓存相关
cache.default.expire-time=200
cache.user.expire-time=180
cache.user.name=test
```

### RedisConfig

```java
@Configuration
@EnableCaching
public class RedisConfig {

    @Value("${cache.default.expire-time}")
    private int defaultExpireTime;
    @Value("${cache.user.expire-time}")
    private int userCacheExpireTime;
    @Value("${cache.user.name}")
    private String userCacheName;

    /**
     * 缓存管理器
     *
     * @param lettuceConnectionFactory
     * @return
     */
    @Bean
    public CacheManager cacheManager(RedisConnectionFactory lettuceConnectionFactory) {
        RedisCacheConfiguration defaultCacheConfig = RedisCacheConfiguration.defaultCacheConfig();
        // 设置缓存管理器管理的缓存的默认过期时间
        defaultCacheConfig = defaultCacheConfig.entryTtl(Duration.ofSeconds(defaultExpireTime))
                // 设置 key为string序列化
                .serializeKeysWith(RedisSerializationContext.SerializationPair.fromSerializer(new StringRedisSerializer()))
                // 设置value为json序列化
                .serializeValuesWith(RedisSerializationContext.SerializationPair.fromSerializer(new GenericJackson2JsonRedisSerializer()))
                // 不缓存空值
                .disableCachingNullValues();

        Set<String> cacheNames = new HashSet<>();
        cacheNames.add(userCacheName);

        // 对每个缓存空间应用不同的配置
        Map<String, RedisCacheConfiguration> configMap = new HashMap<>();
        configMap.put(userCacheName, defaultCacheConfig.entryTtl(Duration.ofSeconds(userCacheExpireTime)));

        RedisCacheManager cacheManager = RedisCacheManager.builder(lettuceConnectionFactory)
                .cacheDefaults(defaultCacheConfig)
                .initialCacheNames(cacheNames)
                .withInitialCacheConfigurations(configMap)
                .build();
        return cacheManager;
    }
}
```

### mapper接口使用注解

```java
@Mapper
@CacheConfig(cacheNames = "student")
public interface StudentMapper {

	@Update("update student set sname=#{name},ssex=#{sex} where sno=#{sno}")
	int update(Student student);

	@Delete("delete from student where sno=#{sno}")
	void deleteStudentBySno(String sno);

	@Select("select * from student where sno=#{sno}")
	@Results(id = "student", value = { @Result(property = "sno", column = "sno", javaType = String.class),
			@Result(property = "name", column = "sname", javaType = String.class),
			@Result(property = "sex", column = "ssex", javaType = String.class) })
	Student queryStudentBySno(String sno);
}
```

### service接口使用注解

```java
@CacheConfig(cacheNames = "student")
public interface StudentService {
	@CachePut(key = "#student.sno")
	Student update(Student student);

	@CacheEvict(key = "#student.sno", allEntries = true)
	void deleteStudentBySno(String sno);
	
	@Cacheable(key = "#sno")
	Student queryStudentBySno(String sno);
}
```

### 测试

![image-20211102170020343](https://gitee.com/chrisxyq/picgo/raw/master/image-20211102170020343.png)





