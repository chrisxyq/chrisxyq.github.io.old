---
layout: post
title: RPC-DUBBO-init
date:  2022-10-10
catalog: true
tags:
    - RPC

---

## 组件安装

### 注册中心zookeeper安装

[zookeeper-3.4.11下载](https://archive.apache.org/dist/zookeeper/zookeeper-3.4.11/)

D:\mySoftware\zookeeper-3.4.11\conf新建zoo.cfg（复制自zoo_sample.cfg）

修改zoo.cfg文件配置为dataDir=../data（需要新建相应文件夹）

### dubbo监控中心安装

[下载incubator-dubbo-ops-master](https://gitee.com/core-reach/incubator-dubbo-ops-master/repository/archive/master.zip)

#### dubbo-admin 注册中心使用

确保D:\mySoftware\incubator-dubbo-ops-master-master\dubbo-admin\src\main\resources路径下的application.properties的dubbo.registry.address=zookeeper://127.0.0.1:2181

在D:\mySoftware\incubator-dubbo-ops-master-master\dubbo-admin路径下使用mvn clean package命令打包

D:\mySoftware\incubator-dubbo-ops-master-master\dubbo-admin\target路径下运行：java -jar dubbo-admin-0.0.1-SNAPSHOT.jar

访问localhost:7001、账号密码都是root、即可进入dubbo的管理控制台（备注zookeeper也一定要启动）

#### dubbo-monitor-simple 监控中心使用

D:\mySoftware\incubator-dubbo-ops-master-master\dubbo-monitor-simple路径下使用mvn package命令打包

不要直接运行jar文件，将生成的压缩包解压后放到software目录下：D:\mySoftware\dubbo-monitor-simple-2.0.0

修改确认文件D:\mySoftware\dubbo-monitor-simple-2.0.0\conf\dubbo.properties的配置为：dubbo.registry.address=zookeeper://127.0.0.1:2181

启动D:\mySoftware\dubbo-monitor-simple-2.0.0\assembly.bin\start.bat后，访问localhost:8080即为监控中心

## dubbo的maven项目搭建

将接口、实体、异常放到公共包下

### 父工程引入pom

```xml
            <!--  引入dubbo      -->
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>dubbo</artifactId>
                <version>2.6.2</version>
            </dependency>
            <!-- 注册中心使用的是zookeeper,引入操作zookeeper的客户端       -->
            <dependency>
                <groupId>org.apache.curator</groupId>
                <artifactId>curator-framework</artifactId>
                <version>2.12.0</version>
            </dependency>
```

### 服务提供者创建dubbo配置文件provider.xml：暴露服务

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
       xsi:schemaLocation="http://www.springframework.org/schema/beans        http://www.springframework.org/schema/beans/spring-beans-4.3.xsd        http://dubbo.apache.org/schema/dubbo        http://dubbo.apache.org/schema/dubbo/dubbo.xsd">

    <!-- 1、指定当前服务/应用的名字（同样的服务名字相同，不要和别的服务同名） -->
    <dubbo:application name="user-service-provider"></dubbo:application>

    <!-- 2、指定注册中心的位置 -->
     <dubbo:registry address="zookeeper://127.0.0.1:2181"></dubbo:registry>

    <!-- 3、指定与消费者的通信规则（通信协议？通信端口） -->
    <dubbo:protocol name="dubbo" port="20882"></dubbo:protocol>

    <!-- 4、暴露服务 interface为接口的全类名  ref：指向服务的真正的实现对象，即下文注册的bean的id -->
    <dubbo:service interface="service.UserService"
                   ref="userServiceImpl" version="1.0.0">
        <dubbo:method name="getUserAddressList"></dubbo:method>
    </dubbo:service>
    <!-- 服务的实现 -->
    <bean id="userServiceImpl" class="service.impl.UserServiceImpl"></bean>
</beans>
```

测试服务是否成功注册zookeeper

```java
/**
 * @author yuanqixu
 * 创建这个主启动类是为了测试dubbo服务提供者是否注册到了注册中心
 测试可能出现问题：org.apache.curator.CuratorConnectionLossException: KeeperErrorCode = ConnectionLoss
 排查：
 1.dubbo-admin\src\main\resources路径下的application.properties的dubbo.registry.address=zookeeper://127.0.0.1:2181
 2.dubbo监控中心在运行：java -jar dubbo-admin-0.0.1-SNAPSHOT.jar
 3.zookeeper在运行：zkServer.cmd
 4.provider.xml配置注册中心位置：<dubbo:registry address="zookeeper://127.0.0.1:2181"></dubbo:registry>
 */
public class MainApplication {
    public static void main(String[] args) throws IOException {
        ClassPathXmlApplicationContext applicationContext = new ClassPathXmlApplicationContext("provider.xml");
        applicationContext.start();
        System.in.read();
    }
}
```

### 服务消费者consumer.xml：消费服务

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd
		http://dubbo.apache.org/schema/dubbo http://dubbo.apache.org/schema/dubbo/dubbo.xsd
		http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd">
    <!--    扫描spring的service注解-->
    <context:component-scan base-package="service.impl"></context:component-scan>

    <!-- 1、指定当前服务/应用的名字（同样的服务名字相同，不要和别的服务同名） -->
    <dubbo:application name="order-service-consumer"></dubbo:application>

    <dubbo:registry address="zookeeper://127.0.0.1:2181"></dubbo:registry>

    <!--声明需要调用的远程服务的接口；生成远程服务代理  -->
    <dubbo:reference interface="service.UserService"
                     id="userService" timeout="5000" retries="3" version="*">
        <!-- <dubbo:method name="getUserAddressList" timeout="1000"></dubbo:method> -->
    </dubbo:reference>

    <!-- 连接监控中心 -->
    <dubbo:monitor protocol="registry"></dubbo:monitor>
    <!-- <dubbo:monitor address="127.0.0.1:7070"></dubbo:monitor> -->

</beans>
```

```java
//Spring注解
@Service
public class OrderServiceImpl implements OrderService {
    @Autowired
    UserService userService;

    @Override
    public List<UserAddress> initOrder(String userId) {
        System.out.println("用户id：" + userId);
        //1.查询用户的收货地址
        List<UserAddress> addresses = userService.getUserAddressList(userId);
        for (UserAddress address : addresses) {
            System.out.println(address.getUserAddress());
        }
        return addresses;
    }
}
```

测试是否可成功调用远程服务

```java
public class MainApplication {

    @SuppressWarnings("resource")
    public static void main(String[] args) throws IOException {
        // 获取服务消费者 xml 配置，获取远程提供的服务信息
        ApplicationContext ac = new ClassPathXmlApplicationContext("consumer.xml");
        // 获取Spring 容器中的 订单服务Bean
        OrderService orderService = ac.getBean(OrderService.class);
        // 初始化订单时，调用远程服务 用户服务获取用户地址
        orderService.initOrder("1");
        System.out.println("调用完成...");
        System.in.read();
    }
}
```

### 在服务端和客户端的dubbo配置文件配置监控中心：

```xml
    <!-- 连接监控中心 -->
    <dubbo:monitor protocol="registry"></dubbo:monitor>
    <!-- <dubbo:monitor address="127.0.0.1:7070"></dubbo:monitor> -->
```

![image-20211028143221891](https://gitee.com/chrisxyq/picgo/raw/master/image-20211028143221891.png)

## dubbo的springboot项目搭建

### pom依赖

```xml
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.4.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>        
	<dependency>
        <groupId>com.alibaba.boot</groupId>
        <artifactId>dubbo-spring-boot-starter</artifactId>
        <version>0.2.0</version>
	</dependency>
```

### 服务端dubbo配置&服务启动类：暴露服务

```properties
dubbo.application.name=user-service-provider

dubbo.registry.address=127.0.0.1:2181
dubbo.registry.protocol=zookeeper

dubbo.protocol.name=dubbo
dubbo.protocol.port=20880

dubbo.monitor.protocol=registry

```

```java
// @Service dubbo暴露服务
@Service
public class UserServiceImpl implements UserService {

	@Override
	public List<UserAddress> getUserAddressList(String userId) {
		System.out.println("调用地址服务获取地址列表...");
		UserAddress address1 = new UserAddress(1, "北京市昌平区宏福科技园综合楼3层", "1", "李老师", "010-56253825", "Y");
		UserAddress address2 = new UserAddress(2, "深圳市宝安区西部硅谷大厦B座3层（深圳分校）", "1", "王老师", "010-56253825", "N");

		return Arrays.asList(address1,address2);
	}
	
/**
 * @EnableDubbo开启dubbo注解扫描
 */
@EnableDubbo
@SpringBootApplication
public class BootUserServiceProviderApplication {

    public static void main(String[] args) {
        SpringApplication.run(BootUserServiceProviderApplication.class, args);
    }

}	
```

### 消费端dubbo配置&dubbo启动类：引用服务

```properties
dubbo.application.name=order-service-consumer

dubbo.registry.address=127.0.0.1:2181
dubbo.registry.protocol=zookeeper

dubbo.protocol.name=dubbo
dubbo.protocol.port=20880

dubbo.monitor.protocol=registry
server.port=8081
```

```java
@Service
public class OrderServiceImpl implements OrderService {

    @Reference
    UserService userService;

    @Override
    public List<UserAddress> initOrder(String userId) {
        System.out.println("用户id："+userId);
        //1.查询用户的收货地址
        List<UserAddress> addresses = userService.getUserAddressList(userId);
        for (UserAddress address : addresses) {
            System.out.println(address.getUserAddress());
        }
        return addresses;
    }
 /**
 * @EnableDubbo开启dubbo注解扫描
 */
@EnableDubbo
@SpringBootApplication
public class BootOrderServiceConsumerApplication {

    public static void main(String[] args) {
        SpringApplication.run(BootOrderServiceConsumerApplication.class, args);
    }

}
```

### 测试消费端暴露给用户的接口

![image-20211028155026758](https://gitee.com/chrisxyq/picgo/raw/master/image-20211028155026758.png)

## dubbo多版本支持

服务端dubbo配置

```xml
    <!-- 4、暴露服务 interface为接口的全类名  ref：指向服务的真正的实现对象，即下文注册的bean的id -->
    <dubbo:service interface="service.UserService"
                   ref="userServiceImpl" version="1.0.0">
        <dubbo:method name="getUserAddressList"></dubbo:method>
    </dubbo:service>
    <!-- 服务的实现 -->
    <bean id="userServiceImpl" class="service.impl.UserServiceImpl"></bean>

    <dubbo:service interface="service.UserService"
                   ref="userServiceImpl2" version="2.0.0">
        <dubbo:method name="getUserAddressList"></dubbo:method>
    </dubbo:service>
    <!-- 服务的实现 -->
    <bean id="userServiceImpl2" class="service.impl.UserServiceImpl2"></bean>
```

客户端调用时的dubbo配置：指定版本

```xml
    <!--    version="*"：新旧版本随机调用-->
    <dubbo:reference interface="service.UserService"
                     id="userService" timeout="5000" retries="3" version="2.0.0">
        <!-- <dubbo:method name="getUserAddressList" timeout="1000"></dubbo:method> -->
    </dubbo:reference>
```

问题：以上dubbo的多版本支持依赖于多个版本的服务代码在service端同时存在

soa如何保证引用不同的契约调用不同的代码逻辑的
