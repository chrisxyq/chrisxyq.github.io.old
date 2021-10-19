---
layout: post
title: 分库分表-sharding-jdbc
date:  2022-01-01
catalog: true
tags:
    - 分库分表

---

[sharding-jdbc](https://shardingsphere.apache.org/document/legacy/4.x/document/cn/quick-start/sharding-jdbc-quick-start/)

# 分库分表

## pom依赖

```xml
    <dependencies>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.1.20</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.shardingsphere</groupId>
            <artifactId>sharding-jdbc-spring-boot-starter</artifactId>
            <version>4.0.0-RC1</version>
        </dependency>
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.0.5</version>
        </dependency>
        <dependency>
            <groupId>org.projectlombok
            </groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.20</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

## 水平分表：springboot+mybatisplus+sharding-jdbc

### 创建数据库&数据表

course_db：course_1（存放偶数cid）和course_2（存放奇数cid）

![image-20211019112302891](https://gitee.com/chrisxyq/picgo/raw/master/image-20211019112302891.png)

```sql
CREATE TABLE IF NOT EXISTS `course_1`(
   `cid` BIGINT(20) PRIMARY KEY,
   `cname` VARCHAR(50) NOT NULL,
   `user_id` BIGINT(20) NOT NULL,
   `cstatus` VARCHAR(10) NOT NULL
)ENGINE=InnoDB DEFAULT CHARSET=utf8;
CREATE TABLE IF NOT EXISTS `course_2`(
   `cid` BIGINT(20) PRIMARY KEY,
   `cname` VARCHAR(50) NOT NULL,
   `user_id` BIGINT(20) NOT NULL,
   `cstatus` VARCHAR(10) NOT NULL
)ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

### 水平分表application.proper配置

```properties
#配置数据源，给数据源起名字
spring.shardingsphere.datasource.names=m1
#一个实体类对应两张表，覆盖
spring.main.allow-bean-definition-overriding=true
#配置数据源具体内容，包含连接池，驱动，地址，用户名和密码(mysql8)
spring.shardingsphere.datasource.m1.type=com.alibaba.druid.pool.DruidDataSource
spring.shardingsphere.datasource.m1.driver-class-name=com.mysql.cj.jdbc.Driver
spring.shardingsphere.datasource.m1.url=jdbc:mysql://localhost:3306/course_db?serverTimeZone=GMT%2B8
spring.shardingsphere.datasource.m1.username=root
spring.shardingsphere.datasource.m1.password=123456
#指定course表分布情况，配置表在哪个数据库里面，表名称都是什么，m1.course_1,m1.course_2
spring.shardingsphere.sharding.tables.t_order.actual-data-nodes=m1.course_$->{1..2}
#指定course表主键cid，生成策略，snowflake
spring.shardingsphere.sharding.tables.course.key-generator.column=cid
spring.shardingsphere.sharding.tables.course.key-generator.type=SNOWFLAKE
#指定分片策略 约定cid值偶数添加到 course_1,奇数添加到 course_2
spring.shardingsphere.sharding.tables.course.table-strategy.inline.sharding-column=cid
spring.shardingsphere.sharding.tables.course.table-strategy.inline.algorithm-expression=course_$->{cid % 2+1}
#打开sql输出日志
spring.shardingsphere.props.sql.show=true
```

### 测试水平分表

```java
    /**
     * 批量插入数据
     */
    @Test
    void test1() {
        for (int i = 0; i < 10; i++) {
            final Course course = new Course();
            course.setCname("java"+i);
            course.setUserId(100L);
            course.setCstatus("Normal"+i);
            courseMapper.insert(course);
        }
    }
    /**
     * 查找某数据
     */
    @Test
    void test2() {
        final QueryWrapper<Course> queryWrapper = new QueryWrapper<>();
        queryWrapper.eq("cid",657220793951846400L);
        final Course course = courseMapper.selectOne(queryWrapper);
        System.out.println(course);
    }
```

## 水平分库：springboot+mybatisplus+sharding-jdbc

### 创建数据库&数据表

创建两个数据库edu_db_1、edu_db_2(两个数据库内均有两张表course_1、course_2)

数据库规则：userid为偶数，添加edu_db_1、为奇数则添加到edu_db_2

表规则：cid为偶数，添加course_1、为奇数则添加到course_2

### 水平分库application.proper配置

```properties
#======水平分库配置（配置两个数据源）========
#配置数据源，给数据源起名字
spring.shardingsphere.datasource.names=m1,m2
#一个实体类对应两张表，覆盖
spring.main.allow-bean-definition-overriding=true
#配置数据源具体内容，包含连接池，驱动，地址，用户名和密码(mysql8)
spring.shardingsphere.datasource.m1.type=com.alibaba.druid.pool.DruidDataSource
spring.shardingsphere.datasource.m1.driver-class-name=com.mysql.cj.jdbc.Driver
spring.shardingsphere.datasource.m1.url=jdbc:mysql://localhost:3306/edu_db_1?serverTimeZone=GMT%2B8
spring.shardingsphere.datasource.m1.username=root
spring.shardingsphere.datasource.m1.password=123456

spring.shardingsphere.datasource.m2.type=com.alibaba.druid.pool.DruidDataSource
spring.shardingsphere.datasource.m2.driver-class-name=com.mysql.cj.jdbc.Driver
spring.shardingsphere.datasource.m2.url=jdbc:mysql://localhost:3306/edu_db_2?serverTimeZone=GMT%2B8
spring.shardingsphere.datasource.m2.username=root
spring.shardingsphere.datasource.m2.password=123456
#指定数据库分布情况，数据库里表的分布情况
spring.shardingsphere.sharding.tables.course.actual-data-nodes=m$->{1..2}.course_$->{1..2}
#指定course表主键cid，生成策略，snowflake
spring.shardingsphere.sharding.tables.course.key-generator.column=cid
spring.shardingsphere.sharding.tables.course.key-generator.type=SNOWFLAKE
#指定表分片策略 约定cid值偶数添加到 course_1,奇数添加到 course_2
spring.shardingsphere.sharding.tables.course.table-strategy.inline.sharding-column=cid
spring.shardingsphere.sharding.tables.course.table-strategy.inline.algorithm-expression=course_$->{cid % 2+1}
#指定course表所在数据库分片策略 约定 user_id 值偶数添加到 m1,奇数添加到 m2
spring.shardingsphere.sharding.tables.course.database-strategy.inline.sharding-column=user_id
spring.shardingsphere.sharding.tables.course.database-strategy.inline.algorithm-expression=m$->{user_id % 2+1}
#指定数据库默认分片策略
#spring.shardingsphere.sharding.default-database-strategy.inline.sharding-column=user_id
#spring.shardingsphere.sharding.default-database-strategy.inline.algorithm-expression=m$->{user_id % 2+1}
#打开sql输出日志
spring.shardingsphere.props.sql.show=true
```

### 测试水平分库

```java
    /**
     * 测试水平分库：插入某数据
     */
    @Test
    void test3() {
        final Course course = new Course();
        course.setCname("javademo");
        //插入edu_db_1库
        course.setUserId(100L);
        course.setCstatus("Normal");
        courseMapper.insert(course);
        final Course course1 = new Course();
        course1.setCname("javademo1");
        //插入edu_db_2库
        course1.setUserId(111L);
        course1.setCstatus("Normal1");
        courseMapper.insert(course1);
    }
    /**
     * 测试水平分库：查询数据
     */
    @Test
    void test4() {
        final QueryWrapper<Course> queryWrapper = new QueryWrapper<>();
        queryWrapper.eq("cid",657231018981326849L);
        queryWrapper.eq("user_id",100L);
        final Course course = courseMapper.selectOne(queryWrapper);
        System.out.println(course);
    }
```

## 垂直分库：springboot+mybatisplus+sharding-jdbc

### 创建数据库&数据表

数据库user_db建表t_user

```sql
CREATE TABLE IF NOT EXISTS `t_user`(
   `user_id` BIGINT(20) PRIMARY KEY,
   `username` VARCHAR(100) NOT NULL,
   `ustatus` VARCHAR(50) NOT NULL
)ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

### 垂直分库application.proper配置

```properties
#======水平分库配置（配置两个数据源）、垂直分库配置========
#配置数据源，给数据源起名字
spring.shardingsphere.datasource.names=m1,m2,m0
#一个实体类对应两张表，覆盖
spring.main.allow-bean-definition-overriding=true
#配置数据源具体内容，包含连接池，驱动，地址，用户名和密码(mysql8)
spring.shardingsphere.datasource.m1.type=com.alibaba.druid.pool.DruidDataSource
spring.shardingsphere.datasource.m1.driver-class-name=com.mysql.cj.jdbc.Driver
spring.shardingsphere.datasource.m1.url=jdbc:mysql://localhost:3306/edu_db_1?serverTimeZone=GMT%2B8
spring.shardingsphere.datasource.m1.username=root
spring.shardingsphere.datasource.m1.password=123456

spring.shardingsphere.datasource.m2.type=com.alibaba.druid.pool.DruidDataSource
spring.shardingsphere.datasource.m2.driver-class-name=com.mysql.cj.jdbc.Driver
spring.shardingsphere.datasource.m2.url=jdbc:mysql://localhost:3306/edu_db_2?serverTimeZone=GMT%2B8
spring.shardingsphere.datasource.m2.username=root
spring.shardingsphere.datasource.m2.password=123456

spring.shardingsphere.datasource.m0.type=com.alibaba.druid.pool.DruidDataSource
spring.shardingsphere.datasource.m0.driver-class-name=com.mysql.cj.jdbc.Driver
spring.shardingsphere.datasource.m0.url=jdbc:mysql://localhost:3306/user_db?serverTimeZone=GMT%2B8
spring.shardingsphere.datasource.m0.username=root
spring.shardingsphere.datasource.m0.password=123456

#配置user_db数据库里面的 t_user 专库专表
spring.shardingsphere.sharding.tables.t_user.actual-data-nodes=m$->{0}.t_user
spring.shardingsphere.sharding.tables.t_user.key-generator.column=user_id
spring.shardingsphere.sharding.tables.t_user.key-generator.type=SNOWFLAKE
spring.shardingsphere.sharding.tables.t_user.table-strategy.inline.sharding-column=user_id
spring.shardingsphere.sharding.tables.t_user.table-strategy.inline.algorithm-expression=t_user
```

### 测试垂直分库

```java
    /**
     * 测试垂直分库:添加
     */
    @Test
    void test5() {
        final User user = new User();
        user.setUsername("lucy");
        user.setUstatus("a");
        userMapper.insert(user);
    }
    /**
     * 测试垂直分库:查询
     */
    @Test
    void test6() {
        final QueryWrapper<Course> queryWrapper = new QueryWrapper<>();
        queryWrapper.eq("user_id",657239864349032449L);
        final Course course = courseMapper.selectOne(queryWrapper);
        System.out.println(course);
    }
```

## 公共表

公共表：存储固定数据的表（如状态），表数据很少发生变化，但是查询时候经常进行关联

### 在每个数据库创建相同结构的公共表

在user_db、edu_db_1、edu_db_2创建相同的表t_udict

```sql
CREATE TABLE IF NOT EXISTS `t_udict`(
   `dictid` BIGINT(20) PRIMARY KEY,
   `ustatus` VARCHAR(100) NOT NULL,
   `uvalue` VARCHAR(100) NOT NULL
)ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

### 公共表application.proper配置

```properties
#配置公共表
spring.shardingsphere.sharding.broadcast-tables=t_udict
spring.shardingsphere.sharding.tables.t_udict.key-generator.column=dictid
spring.shardingsphere.sharding.tables.t_udict.key-generator.type=SNOWFLAKE
```

### 测试公共表

三个库中的表t_udict均同时插入或修改

```java
    /**
     * 测试公共表:添加
     * 2021-10-19 15:42:34.910  INFO 26352 --- [           main] ShardingSphere-SQL                       : Actual SQL: m1 ::: INSERT INTO t_udict   (ustatus, uvalue, dictid) VALUES (?, ?, ?) ::: [a, lucy, 657246408708980737]
     * 2021-10-19 15:42:34.910  INFO 26352 --- [           main] ShardingSphere-SQL                       : Actual SQL: m2 ::: INSERT INTO t_udict   (ustatus, uvalue, dictid) VALUES (?, ?, ?) ::: [a, lucy, 657246408708980737]
     * 2021-10-19 15:42:34.910  INFO 26352 --- [           main] ShardingSphere-SQL                       : Actual SQL: m0 ::: INSERT INTO t_udict   (ustatus, uvalue, dictid) VALUES (?, ?, ?) ::: [a, lucy, 657246408708980737]
     */
    @Test
    void test7() {
        final Udict udict = new Udict();
        udict.setUstatus("a");
        udict.setUvalue("lucy");
        udictMapper.insert(udict);
    }
    /**
     * 测试公共表:查询
     */
    @Test
    void test8() {
        final QueryWrapper<Udict> queryWrapper = new QueryWrapper<>();
        queryWrapper.eq("dictid",657239864349032449L);
        final Udict udict = udictMapper.selectOne(queryWrapper);
        System.out.println(udict);
    }
```

# 读写分离

增删改：操作主库、查询：操作从库

读写分离原理：主从复制：从库实时监控主库的binlog的变化，读取日志解析，完成数据的同步

sharding-jdbc负责解析sql语句，当遇到增删改，则路由到主库执行，当遇到查询，路由到从库执行

## mysql配置读写分离

![image-20211019155610407](D:\Users\yuanqixu\AppData\Roaming\Typora\typora-user-images\image-20211019155610407.png)

修改从库的my.ini



事务

