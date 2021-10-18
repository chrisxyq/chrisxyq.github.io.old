---
layout: post
title: ORM框架-MyBatisPlus
date:  2021-10-10
catalog: true
tags:
    - ORM框架
---
[MyBatisPlus官方文档](ORM框架)

# MyBatisPlus与spring集成demo

## 项目搭建

### pom依赖：

```xml
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.4.3.4</version>
            <!--            <version>${mybatis-plus.version}</version>-->
        </dependency>
```

### 建表

```
CREATE TABLE `usertbl` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT '主键ID',
  `name` varchar(30) DEFAULT NULL COMMENT '姓名',
  `age` int(11) DEFAULT NULL COMMENT '年龄',
  `email` varchar(50) DEFAULT NULL COMMENT '邮箱',
  `create_time` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `update_time` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=7 DEFAULT CHARSET=utf8

```

### application.properties配置数据源和日志控制台输出

```properties
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/school?useUnicode=true&characterEncoding=utf-8&useSSL=false&allowMultiQueries=true&serverTimezone=Asia/Shanghai
spring.datasource.username=root
spring.datasource.password=123456
#配置日志:控制台输出
mybatis-plus.configuration.log-impl=org.apache.ibatis.logging.stdout.StdOutImpl

```

## 主键策略

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Usertbl {
    //    @TableId(type = IdType.NONE)//默认
    //自增适合使用该注解，在数据库上一个最大的id基础上加1
    @TableId(type = IdType.INPUT)
    private Long id;
    private String name;
    private Integer age;
    private String email;
    //自动会进行驼峰转换，字段映射
    @TableField(fill = FieldFill.INSERT)
    private Date createTime;
    @TableField(fill = FieldFill.INSERT_UPDATE)
    //无需在数据库级别设置更新时候更新该字段的触发器
    private Date updateTime;
}

```

## 字段自动填充

### 实现元对象处理器接口

```java
@Slf4j
@Component
public class MyMetaObjectHandler implements MetaObjectHandler {
    @Override
    public void insertFill(MetaObject metaObject) {
        log.info("start insert fill");
        this.setFieldValByName("createTime", new Date(), metaObject);
    }

    @Override
    public void updateFill(MetaObject metaObject) {
        log.info("start update fill");
        this.setFieldValByName("updateTime", new Date(), metaObject);

    }
}
    /**
     * 测试 MyMetaObjectHandler
     * @Component注解一定要加到MyMetaObjectHandler上
     * 使得MyMetaObjectHandler填充器生效
     */
    @Test
    void test1() {
        Usertbl user = new Usertbl();
        user.setId(6L);
        user.setEmail("updated");
        userMapper.updateById(user);
    }
```

## 逻辑删除

application配置

```properties
mybatis-plus.global-config.db-config.logic-delete-field=deleted
mybatis-plus.global-config.db-config.logic-delete-value=1
mybatis-plus.global-config.db-config.logic-not-delete-value=0

```

实体类

```java
    @TableLogic
    private Integer deleted;
```

测试

```java
    /**
     * ==>  Preparing: UPDATE usertbl SET deleted=1 WHERE id=? AND deleted=0
     * ==> Parameters: 3(Long)
     */
    @Test
    void test6() {
        userMapper.deleteById(3L);
    }
```



## 插件

### 注册插件

```java
//可以放到主配置类，也可以放到这里
@MapperScan("com.example.mybatisplusdemo.mapper")
@EnableTransactionManagement
@Configuration
public class MyBatisPlusConfig {
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {
        MybatisPlusInterceptor mybatisPlusInterceptor = new MybatisPlusInterceptor();
        //分页
        mybatisPlusInterceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.MYSQL));
        //乐观锁
        mybatisPlusInterceptor.addInnerInterceptor(new OptimisticLockerInnerInterceptor());
        return mybatisPlusInterceptor;
    }
}
```

### 乐观锁测试

![image-20211017114123659](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20211017114123659.png)

```java

    /**
     * 测试乐观锁成功
     */
    @Test
    void test2() {
        Usertbl usertbl = userMapper.selectById(1L);
        usertbl.setEmail("updated");
        userMapper.updateById(usertbl);
    }

    /**
     * 测试按条件查询
     * ==>  Preparing: UPDATE usertbl SET name=?, age=?, email=?, create_time=?, update_time=?, version=? WHERE id=? AND version=?
     * ==> Parameters: Jone(String), 18(Integer), updated2(String), 2021-10-16 18:37:27.0(Timestamp), 2021-10-17 11:58:33.967(Timestamp), 3(Integer), 1(Long), 2(Integer)
     * <==    Updates: 1
     * Closing non transactional SqlSession [org.apache.ibatis.session.defaults.DefaultSqlSession@20184ade]
     * Creating a new SqlSession
     * SqlSession [org.apache.ibatis.session.defaults.DefaultSqlSession@27ec0d06] was not registered for synchronization because synchronization is not active
     * 2021-10-17 11:58:34.099  INFO 21804 --- [           main] c.e.m.handler.MyMetaObjectHandler        : start update fill
     * JDBC Connection [HikariProxyConnection@645323114 wrapping com.mysql.cj.jdbc.ConnectionImpl@727dcc64] will not be managed by Spring
     * ==>  Preparing: UPDATE usertbl SET name=?, age=?, email=?, create_time=?, update_time=?, version=? WHERE id=? AND version=?
     * ==> Parameters: Jone(String), 18(Integer), updated1(String), 2021-10-16 18:37:27.0(Timestamp), 2021-10-17 11:58:34.099(Timestamp), 3(Integer), 1(Long), 2(Integer)
     * <==    Updates: 0
     * Closing non transactional SqlSession [org.apache.ibatis.session.defaults.DefaultSqlSession@27ec0d06]
     */
    @Test
    void test4() {
        Map<String, Object> map = new HashMap<>();
        map.put("id",1);
        map.put("name","Jone");
        List<Usertbl> usertbls = userMapper.selectByMap(map);
        usertbls.forEach(System.out::println);
    }
```

### 分页测试

```java
    /**
     * 测试分页查询
     * JDBC Connection [HikariProxyConnection@892091760 wrapping com.mysql.cj.jdbc.ConnectionImpl@4c13ca07] will not be managed by Spring
     * ==>  Preparing: SELECT COUNT(*) FROM usertbl
     * ==> Parameters:
     * <==    Columns: COUNT(*)
     * <==        Row: 6
     * <==      Total: 1
     * ==>  Preparing: SELECT id,name,age,email,create_time,update_time,version FROM usertbl LIMIT ?
     * ==> Parameters: 5(Long)
     */
    @Test
    void test5() {
        Page<Usertbl> page = new Page<>(1,5);
        userMapper.selectPage(page,null);
        page.getRecords().forEach(System.out::println);
    }
```

## 条件查询器wrapper：支持复杂查询

wrapper类的方法与usermapper类的方法搭配使用，共同组成一条sql语句

### 通过id进行排序

![1600857025651](https://gitee.com/chrisxyq/picgo/raw/master/img/1600857025651.png)

### 多条件查询：ge：大于等于

条件查询器wrapper支持链式编程

![1600857158159](https://gitee.com/chrisxyq/picgo/raw/master/img/1600857158159.png)

### 查询单条：eq：等于

注意：查询一条用selectone、多个用selectlist或者selectmap

![1600857228819](https://gitee.com/chrisxyq/picgo/raw/master/img/1600857228819.png)

### 查询符合某范围的条数：between方法内传上下限参数

![1600857305399](https://gitee.com/chrisxyq/picgo/raw/master/img/1600857305399.png)

### 模糊查询：likeright：以XX开头、notlike：不包含XX

查询名字不包含e、且email以t开头的

![1600857407318](https://gitee.com/chrisxyq/picgo/raw/master/img/1600857407318.png)

### 子查询：下面的查询，使用ge方法也可以实现

![1600857560480](https://gitee.com/chrisxyq/picgo/raw/master/img/1600857560480.png)

## 代码自动生成器autoGenerator

```java
public class CodeGenerator {

    /**
     * <p>
     * 读取控制台内容
     * </p>
     */
    public static String scanner(String tip) {
        Scanner scanner = new Scanner(System.in);
        StringBuilder help = new StringBuilder();
        help.append("请输入" + tip + "：");
        System.out.println(help.toString());
        if (scanner.hasNext()) {
            String ipt = scanner.next();
            if (StringUtils.isNotBlank(ipt)) {
                return ipt;
            }
        }
        throw new MybatisPlusException("请输入正确的" + tip + "！");
    }

    public static void main(String[] args) {
        // 代码生成器
        AutoGenerator mpg = new AutoGenerator();

        // 全局配置
        GlobalConfig gc = new GlobalConfig();
        String projectPath = System.getProperty("user.dir");
        gc.setOutputDir(projectPath + "/spring-demos/mybatisplus-demo/src/main/java");
        gc.setAuthor("chrisxyq");
        gc.setOpen(false);
        gc.setIdType(IdType.INPUT);
        gc.setFileOverride(true);
        // gc.setSwagger2(true); 实体属性 Swagger2 注解
        mpg.setGlobalConfig(gc);

        // 数据源配置
        DataSourceConfig dsc = new DataSourceConfig();
        dsc.setUrl("jdbc:mysql://localhost:3306/school?useUnicode=true&useSSL=false&characterEncoding=utf8");
        // dsc.setSchemaName("public");
        dsc.setDriverName("com.mysql.cj.jdbc.Driver");
        dsc.setUsername("root");
        dsc.setPassword("123456");
        dsc.setDbType(DbType.MYSQL);
        mpg.setDataSource(dsc);

        // 包配置
        PackageConfig pc = new PackageConfig();
//        pc.setModuleName(scanner("ModuleName"));
        pc.setModuleName("generate");
        pc.setParent("com.example.mybatisplusdemo");
        pc.setEntity("entity");
        pc.setService("service");
        pc.setMapper("mapper");
        pc.setController("controller");
        mpg.setPackageInfo(pc);

        StrategyConfig strategyConfig = new StrategyConfig();
//        strategyConfig.setInclude("usertbl");
        strategyConfig.setInclude(scanner("表名，多个英文逗号分割").split(","));
        strategyConfig.setEntityLombokModel(true);
        strategyConfig.setColumnNaming(NamingStrategy.underline_to_camel);
        strategyConfig.setNaming(NamingStrategy.underline_to_camel);
        strategyConfig.setLogicDeleteFieldName("deleted");
//        // 策略配置
//        strategyConfig.setSuperEntityClass("你自己的父类实体,没有就不用设置!");
//        // 公共父类
//        strategyConfig.setSuperControllerClass("你自己的父类控制器,没有就不用设置!");
//        // 写于父类中的公共字段
//        strategyConfig.setSuperEntityColumns("id");
        TableFill createTime = new TableFill("create_Time", FieldFill.INSERT);
        TableFill updateTime = new TableFill("update_Time", FieldFill.INSERT_UPDATE);
        List<TableFill> list = new ArrayList<>();
        list.add(createTime);
        list.add(updateTime);
        strategyConfig.setTableFillList(list);
        strategyConfig.setVersionFieldName("version");
        strategyConfig.setRestControllerStyle(true);
        strategyConfig.setControllerMappingHyphenStyle(true);
        mpg.setStrategy(strategyConfig);
        mpg.execute();
    }

}
```

