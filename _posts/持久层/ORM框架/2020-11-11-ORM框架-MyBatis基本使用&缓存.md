---
layout: post
title: MyBatis基本使用&缓存
catalog: true
tags:
    - ORM框架

---

[mysql安装参考](https://zhuanlan.zhihu.com/p/37152572)

[Mybatis](https://mybatis.org/mybatis-3/zh/index.html)

## mybatis-demo

### mysql数据源&mybatis配置

pom依赖

mysql数据源配置

mybatis配置文件引入mysql数据源配置：mybatis默认jdbc事务管理器，连接池pooled

记得向mybatis配置文件中注册mapper.xml

```xml
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
        </dependency>
```

```properties
driver=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/test_db?useUnicode=true&characterEncoding=utf-8&useSSL=false&allowMultiQueries=true&serverTimezone=Asia/Shanghai
username=root
password=123456
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
    <!--    关于mybatis的核心配置文件的数据库连接的部分，可以抽取到db.properties文件内。
    然后通过mybatis-config.xml引入db.properties。将db.properties抽取后，再引用-->
    <properties resource="db.properties"></properties>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <!--        每个mapper.xml都需要在此处注册-->
        <mapper resource="daoImpl/CouponBrandMapper.xml"/>
        <package name="daoImpl"/>
    </mappers>
</configuration>
```

### mybatis工具类

```java
/**
 * 写一个工具类，以获取sqlsession
 */
public class MyBatisUtils {
    private static SqlSessionFactory sqlSessionFactory;

    /**
     * 第一步：通过读取mybatis配置文件，获取sqlSessionFactory：红框的声明是为了提升作用域。
     * sqlSessionFactory在静态代码块中被初始化。
     * 获取sqlSessionFactory对象的三句代码是死的，因此写到工具类里面
     */
    static {
        try{
            String resource = "mybatis-config.xml";
            InputStream inputStream =
                    Resources.getResourceAsStream(resource);
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
            System.out.println(sqlSessionFactory);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    /**
     * 第二步：从sqlSessionFactory.opensession()获取sqlSession对象
     * 这一部分写到静态方法里面
     sqlSessionFactory.openSession(true)表示自动提交事务
     * @return
     */
    public static SqlSession getSqlSession(){
        return sqlSessionFactory.openSession(true);
    }
```

### 测试类

#### 查询

```java
public class CouponBrandDaoTest {
    @Test
    public void testGetEntityById(){
        final SqlSession sqlSession = MyBatisUtils.getSqlSession();
        final CouponBrandDao couponBrandDao = sqlSession.getMapper(CouponBrandDao.class);
        final CouponBrandEntity entityById = couponBrandDao.getEntityById(2685L);
        System.out.println(entityById);
        sqlSession.close();
    }
    
```

#### 插入

```java
public interface CouponBrandDao {
    @Select("select * from coupon_brand where id = #{UID}")
    CouponBrandEntity getEntityById(@Param("UID") Long id);
	//类似地，使用注解开发，需要在mybatis配置文件中配置接口
    @Insert("INSERT INTO test_db.coupon_brand (brandID,couponClassID,storeID) VALUES (#{brandID},#{couponClassID},#{storeID})")
    void addCouponBrand(CouponBrandEntity couponBrand);
```

```java
    @Test
    public void testAddCouponBrand(){
        final SqlSession sqlSession = MyBatisUtils.getSqlSession();
        final CouponBrandDao couponBrandDao = sqlSession.getMapper(CouponBrandDao.class);
        couponBrandDao.addCouponBrand(new CouponBrandEntity(2L,2L,2L));
        sqlSession.commit();
        sqlSession.close();
    }
```

#### map传递数据

![1600393969759](https://gitee.com/chrisxyq/picgo/raw/master/img/1600393969759.png)

![1600394214392](https://gitee.com/chrisxyq/picgo/raw/master/img/1600394214392.png)

多个参数用map或者注解

#### 模糊查询

![1600394597543](https://gitee.com/chrisxyq/picgo/raw/master/img/1600394597543.png)

java代码使用通配符%

![1600394740974](https://gitee.com/chrisxyq/picgo/raw/master/img/1600394740974.png)

#### 类型别名：两种方式

类型别名：即给xml的resultType的实体类起别名，使得resultType后跟别名即可，而不是全限定类名

起别名前：全限定类名、后：别名

![1600396268741](https://gitee.com/chrisxyq/picgo/raw/master/img/1600396268741.png)

在mybatis-config.xml中配置，起别名的两种方式：直接起别名和指定包名并扫描包的形式

![1600397374015](https://gitee.com/chrisxyq/picgo/raw/master/img/1600397374015.png)

注意：扫描包的形式，其实体类的别名默认为小写形式。若要人工指定别名，在实体类上添加注解。如下

![1600397545053](https://gitee.com/chrisxyq/picgo/raw/master/img/1600397545053.png)

![1600397592171](https://gitee.com/chrisxyq/picgo/raw/master/img/1600397592171.png)

#### 动态sql

#### 结果集映射resultmap：解决属性名和字段名不一致的问题

一般使用resulttype来写sql语句返回值的类型

当数据库中的字段pwd与实体类的属性password不一致，若不使用resultmap映射，则不一致的属性将查询为空

一种简单的处理方式是sql中起别名

![1603190487675](https://gitee.com/chrisxyq/picgo/raw/master/img/1603190487675.png)

或者在usermapper.xml中增加resultmap标签，将数据库的字段一一映射为实体类的属性

![1600399650057](https://gitee.com/chrisxyq/picgo/raw/master/img/1600399650057.png)

#### 分页

limit、rowbounds、pagehelper

### mybatis执行流程

step1：通过读取mybatis配置文件，创建sqlsessionfactory

step2：获取sqlsession，sqlsession包括执行sql所需的所有方法

step3：利用sqlsession.getmapper方法，传入mapper类名，通过反射获得mapper对象

![image-20210619130148689](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210619130148689.png)

|                          | 定义                      | 生命周期                                               | 备注                                                         |
| ------------------------ | ------------------------- | ------------------------------------------------------ | ------------------------------------------------------------ |
| sqlSessionFactoryBuilder | 用于创建sqlSessionFactory | 局部变量                                               |                                                              |
| sqlSessionFactory        | 类似于数据库连接池        | 应用作用域                                             | 单例模式<br />一个sqlsessionfactory可以产生很多sqlsession    |
| sqlSession               | 连接到连接池的一个请求    | 请求作用域<br />线程不安全<br />用完要关闭防止占用资源 | sqlsession包含执行sql的所有方法<br />***使用sqlsession.getmapper()方法，传入dao接口的类，得到dao接口的对象***<br />每个sqlsession可以连接许多的mapper<br />每个线程都应有其sqlsession的一个实例 |

### LOG4j日志集成

## 缓存

### 一级缓存：单次请求的sql session内部的

一级缓存是sqlsession级别的，默认开启

即在同一个sqlsession中连续执行相同的查询操作，日志输出将返回相同的对象

![1603259098943](https://gitee.com/chrisxyq/picgo/raw/master/img/1603259098943.png)

注意：增删改操作可能会改变原来的数据，因此将刷新缓存，使得缓存失效

![1603259834992](https://gitee.com/chrisxyq/picgo/raw/master/img/1603259834992.png)

### 一级缓存失效场景

- 两次查询的参数不一致

- 两次相同查询之间，存在增删改语句（由于增删改可能会修改数据

- 查询不同的mapper

- 调用sqlsession.clearCache手动清理缓存

一级缓存默认是开启的

另：手动清理缓存

![1603259950161](https://gitee.com/chrisxyq/picgo/raw/master/img/1603259950161.png)

### 二级/全局缓存：mapper /namespace级别的

二级缓存是基于namespace级别的缓存

开启二级缓存的方法：在当前mapper.xml中使用二级缓存![1603260205063](https://gitee.com/chrisxyq/picgo/raw/master/img/1603260205063.png)

手动开启二级缓存的配置

![image-20210619133641170](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210619133641170.png)

![image-20210619134347512](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210619134347512.png)

测试发现：两次sqlsession只执行一次sql，即一次sqlsession关闭后会将一级缓存的内容转而放到二级缓存

需要将实体类序列化

备注：sqlsession的缓存首先是被存储到一级缓存的，当第一个sqlsession会话关闭或者提交时，该sqlsession的一级缓存将被转移到二级缓存，二级缓存将被传到另一个sqlsession

需要注意，两个sql语句需要在同一个namespace里面，即同一个mapper.xml

![1603260983636](https://gitee.com/chrisxyq/picgo/raw/master/img/1603260983636.png)

注意：我们需要将sql对应的实体类序列化，即实现序列化接口，否则开启二级缓存将报错

### 缓存原理

![1603261640793](https://gitee.com/chrisxyq/picgo/raw/master/img/1603261640793.png)

### 随机生成UUID

![1600423227724](https://gitee.com/chrisxyq/picgo/raw/master/img/1600423227724.png)



