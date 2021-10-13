---
layout: post
title: MyBatisPlus
catalog: true
tags:
    - ORM框架
---
[MyBatisPlus官方文档](ORM框架)

## MyBatisPlus与spring集成demo



## MyBatisPlus使用示例

类似jpa(Java Persistence API)Java持久化API

### 导入maven依赖和连接数据库

1.导入依赖注意：注意不要同时导入mybatis和mybatisplus，防止依赖冲突

2.连接数据库配置

![1603263311605](https://gitee.com/chrisxyq/picgo/raw/master/img/1603263311605.png)

![1600848563162](https://gitee.com/chrisxyq/picgo/raw/master/img/1600848563162.png)

### 写mapper接口

mybatisplus提供了basemapper类，在泛型中传入crud的对象

![1603264163307](https://gitee.com/chrisxyq/picgo/raw/master/img/1603264163307.png)

继承mybatisplus提供的basemapper类，泛型中传入编写的user pojo

![1600848842556](https://gitee.com/chrisxyq/picgo/raw/master/img/1600848842556.png)

由于继承了basemapper类，因此继承了父类所有的方法

至此，所有的crud编写完成

### 测试类中使用usermapper方法查询

要让mapper组件生效，需要在主启动类上使用mapperscan注解，扫描mapper文件夹

![1600848870155](https://gitee.com/chrisxyq/picgo/raw/master/img/1600848870155.png)

使用继承的方法selectlist测试查询全部用户，该方法传入参数是一个wrapper，这里传入空

![1600848438016](https://gitee.com/chrisxyq/picgo/raw/master/img/1600848438016.png)

## 配置日志：得知数据库是如何执行

目的：在开发时，根据日志得知数据库是如何执行的

配置方法：

![1600849005885](https://gitee.com/chrisxyq/picgo/raw/master/img/1600849005885.png)

日志输出：

![1603265695861](https://gitee.com/chrisxyq/picgo/raw/master/img/1603265695861.png)

## insert-方法测试：几种主键生成策略

![1603266139075](https://gitee.com/chrisxyq/picgo/raw/master/img/1603266139075.png)

update/insert语句的返回值为受影响的行数

### mybatisPlus的默认主键生成策略

默认是雪花算法，全局唯一id，如下图所示，mybatisPlus默认配置为如下注解：

![1603266574201](https://gitee.com/chrisxyq/picgo/raw/master/img/1603266574201.png)

### mybatisPlus配置主键自增

![1600849581576](https://gitee.com/chrisxyq/picgo/raw/master/img/1600849581576.png)

### mybatisPlus配置主键为手动输入

![1603266945436](https://gitee.com/chrisxyq/picgo/raw/master/img/1603266945436.png)

此时若插入user没有id，则插入的数据id为空。

## update-方法测试：自动实现动态sql

mybatisPlus可以根据用户输入的参数，帮助自动实现动态sql

![1603267338358](https://gitee.com/chrisxyq/picgo/raw/master/img/1603267338358.png)

## update-字段自动填充：用于关于时间的操作

![1603267480320](https://gitee.com/chrisxyq/picgo/raw/master/img/1603267480320.png)

### 数据库实现

注：实际开发不能用这种方法，不允许操作数据库

![1603267662636](https://gitee.com/chrisxyq/picgo/raw/master/img/1603267662636.png)

### 代码实现：

step1：实体类字段属性上增加注解

![1600851786071](https://gitee.com/chrisxyq/picgo/raw/master/img/1600851786071.png)

step2：编写处理器处理该注解

实现mybatisplus的metaobjecthandler接口

重写插入/更新时的字段填充方法

注意：需要为handler增加component注解，以将该组件增加到ioc容器中

![1600851932158](https://gitee.com/chrisxyq/picgo/raw/master/img/1600851932158.png)

## update-乐观锁配置

前提保证数据表与实体类均应有version字段

### step1：为实体类的version字段增加version注解

![1600853190151](https://gitee.com/chrisxyq/picgo/raw/master/img/1600853190151.png)

### step2：注册组件：在mybatisplusconfig配置类中注册乐观锁插件

是一个拦截器，会拦截操作，并进行自动化处理

![1600852832833](https://gitee.com/chrisxyq/picgo/raw/master/img/1600852832833.png)

### step3：模拟测试并发环境下乐观锁的作用：

红框内的sql将执行失败，自旋锁详见JUC

![1600853419509](https://gitee.com/chrisxyq/picgo/raw/master/img/1600853419509.png)

## select-单个/批量/按条件查询

![1603271039654](https://gitee.com/chrisxyq/picgo/raw/master/img/1603271039654.png)

按条件查询可以传入map或者wrapper、这里以传入map为例

![1603271296325](https://gitee.com/chrisxyq/picgo/raw/master/img/1603271296325.png)

## select-分页

![1600853807118](https://gitee.com/chrisxyq/picgo/raw/master/img/1600853807118.png)

### step1：注册组件：在mybatisplusconfig配置类中注册分页插件

与注册乐观锁插件类似，分页插件也是通过拦截器实现的

![1600853914545](https://gitee.com/chrisxyq/picgo/raw/master/img/1600853914545.png)

### step2：使用page对象以及mapper的selectpage方法实现分页查询

mybatisplus通过page对象封装了底层的limit

查出第2页，个数为5的所有数据

还可通过page对象获取总页数：红框：输出一共有多少页

![1600854179007](https://gitee.com/chrisxyq/picgo/raw/master/img/1600854179007.png)

## delete-单个/批量/按条件删除

参考select

## delete-逻辑删除：回收站

![1600854448599](https://gitee.com/chrisxyq/picgo/raw/master/img/1600854448599.png)

### step1：为实体类的逻辑删除属性增加逻辑删除注解

数据库中也应有该字段

![1600855052382](https://gitee.com/chrisxyq/picgo/raw/master/img/1600855052382.png)

### step2：在mybatisplusconfig配置类中：配置逻辑删除拦截器组件

与乐观锁、分页组件配置相似，向bean中配置拦截器

![1600855293700](https://gitee.com/chrisxyq/picgo/raw/master/img/1600855293700.png)

### step3：在application.properties：配置逻辑删除的0/1值

![1600855253105](https://gitee.com/chrisxyq/picgo/raw/master/img/1600855253105.png)

### step4：测试

![1603272962263](https://gitee.com/chrisxyq/picgo/raw/master/img/1603272962263.png)

在log日志中可以看到：删除语句将被解析为update，只是修改了字段的值，并未删除

注：测试操作后是否可以查询到该数据

![1603273081818](https://gitee.com/chrisxyq/picgo/raw/master/img/1603273081818.png)

通过log日志发现，逻辑删除拦截器组件将为查询语句自动拼接deleted

因此将查询不到逻辑删除后的数据

## 性能分析拦截器

### step1：在application.properties：配置环境为开发环境dev

![1600856081712](https://gitee.com/chrisxyq/picgo/raw/master/img/1600856081712.png)



### step2：在mybatisplusconfig配置类中：注册性能分析插件

![1600856040795](https://gitee.com/chrisxyq/picgo/raw/master/img/1600856040795.png)

### step3：测试效果

SQL格式化效果

![1603273783619](https://gitee.com/chrisxyq/picgo/raw/master/img/1603273783619.png)

SQL执行时长拦截效果

![1603273817675](https://gitee.com/chrisxyq/picgo/raw/master/img/1603273817675.png)

## select-querywrapper条件查询器wrapper：支持复杂查询

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

![1600861819995](https://gitee.com/chrisxyq/picgo/raw/master/img/1600861819995.png)

![1600861937346](https://gitee.com/chrisxyq/picgo/raw/master/img/1600861937346.png)

### 全局配置

![1600861982541](https://gitee.com/chrisxyq/picgo/raw/master/img/1600861982541.png)

### 设置数据源

![1600862042915](https://gitee.com/chrisxyq/picgo/raw/master/img/1600862042915.png)

### 包的配置

![1600862078335](https://gitee.com/chrisxyq/picgo/raw/master/img/1600862078335.png)

### 策略配置

![1600862197935](https://gitee.com/chrisxyq/picgo/raw/master/img/1600862197935.png)

![1600862234945](https://gitee.com/chrisxyq/picgo/raw/master/img/1600862234945.png)

