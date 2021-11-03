---
layout: post
title: 事务-springBoot中使用事务
date:  2022-01-01
catalog: true
tags:
    - 事务


---

[参考博客](https://mrbird.cc/Spring%E5%A3%B0%E6%98%8E%E5%BC%8F%E4%BA%8B%E5%8A%A1%E5%8E%9F%E7%90%86.html)

### 事务失效场景1：默认情况下，Spring事务只对RuntimeException或者Error类型异常（错误）进行回滚

#### 解决1：@Transactional(rollbackFor = Exception.class)

#### 解决2：自定义异常

```java
    @Override
    @Transactional
    public void saveUser3(User user) {
        userMapper.save(user);
        // 测试事务回滚
        if (!StringUtils.hasText(user.getUsername())) {
            throw new ParamInvalidException("username不能为空");
        }
    }
    public class ParamInvalidException extends RuntimeException{

        public ParamInvalidException(String message) {
            super(message);
        }
    }
```

### 事务失效场景2：非事务方法直接通过this调用本类事务方法

原理都是使用代理对象来替代this

#### 解决1：从IOC容器中获取UserService Bean

```java
/**
 * 获取上下文工具类
 */
@Component
public class SpringUtil implements ApplicationContextAware {
    private static ApplicationContext applicationContext;
    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        if(SpringUtil.applicationContext == null) {
            SpringUtil.applicationContext = applicationContext;
        }
    }
    public static ApplicationContext getApplicationContext() {
        return applicationContext;
    }

    /**
     * 根据名称取Spring容器中的实例
     * @param name
     * @return
     */
    public static Object getBean(String name){
        return getApplicationContext().getBean(name);
    }

    /**
     * 根据类型取Spring容器中的实例
     * @param clazz
     * @param <T>
     * @return
     */
    public static <T> T getBean(Class<T> clazz){
        return getApplicationContext().getBean(clazz);
    }

    /**
     * 根据名称和类型取Spring容器中的实例
     * @param name
     * @param clazz
     * @param <T>
     * @return
     */
    public static <T> T getBean(String name,Class<T> clazz){
        return getApplicationContext().getBean(name, clazz);
    }
}
```

```java
    @Override
    public void saveUser5(User user) {
        UserService userService = SpringUtil.getBean(UserService.class);
        userService.saveUser3(user);
    }
```

#### 解决2：从AOP上下文中取出当前代理对象

```xml
        <!--        从AOP上下文中取出当前代理对象-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-aop</artifactId>
        </dependency>
```

```java
/**
 * 在SpringBoot入口类中通过注解@EnableAspectJAutoProxy(exposeProxy = true)
 * 将当前代理对象暴露到AOP上下文中（通过AopContext的ThreadLocal实现）
 */
@SpringBootApplication
@EnableTransactionManagement
@EnableAspectJAutoProxy(exposeProxy = true)
public class SpringbootTransactionApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootTransactionApplication.class, args);
    }

}
```

```java
    @Override
    public void saveUser6(User user) {
        UserService userService = (UserService) AopContext.currentProxy();
        userService.saveUser3(user);
    }
```

