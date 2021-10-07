---
layout: post
title: SpringMVC
catalog: true
tags:
    - Spring
---

## 搭建项目

![image-20211007202847123](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20211007202847123.png)

### pom文件

```xml
  <properties>
    <spring.version>5.0.2.RELEASE</spring.version>
  </properties>

  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-web</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>servlet-api</artifactId>
      <version>2.5</version>
      <scope>provided</scope>
    </dependency>
  </dependencies>
```

### 新建springmvc.xml

```xml
    <!--开启注解扫描-->
    <context:component-scan base-package="controller"></context:component-scan>
    <!--    配置视图解析器-->
    <bean id="internalResourceViewResolver"
          class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/pages/"></property>
        <property name="suffix" value=".jsp"></property>
    </bean>
    <!--    开启springmvc框架的注解支持-->
    <mvc:annotation-driven></mvc:annotation-driven>
```

### web.xml配置

配置dispatcherServlet，并为前端控制器dispatcherServlet指定初始化参数，使得springmvc.xml在容器初始化时被加载，使得controller注解扫描生效

```xml
  <servlet>
    <servlet-name>dispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:springmvc.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
  </servlet>
  <servlet-mapping>
    <servlet-name>dispatcherServlet</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>
    <!--    配置解决中文乱码的过滤器-->
    <filter>
        <filter-name>characterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>characterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
```

### 代码demo

```java
<a href="/hello">入门程序</a>
@Controller
public class HelloController {
    @RequestMapping("/hello")
    public String sayHello(){
        System.out.println("hello spring mvc");
        return "success";
    }
}
```



## 请求&响应

### 封装请求参数到实体类

```jsp
<body>
    <form action="param/saveAccount" method="post">
        姓名：<input type="text" name="username"/><br/>
        密码：<input type="text" name="password"/><br/>
        金额：<input type="text" name="money"/><br/>
        用户姓名：<input type="text" name="user.uname"/><br/>
        用户年龄：<input type="text" name="user.age"/><br/>
        <input type="submit" value="提交"/>
    </form>
</body>
```

```java
@RequestMapping("/param")
@Controller
public class ParamController {
    @RequestMapping("/saveAccount")
    public String saveAccount(Account account) {
        System.out.println("执行了");
        System.out.println(account);
        return "success";
    }
```

注意：

- 实体类的属性必须要有get set方法，才能将参数注入到实体类
- param.jsp只有放到webapp目录下才可以被直接访问：http://localhost:8080/param.jsp

### 接收集合参数list、map

```jsp
    <form action="param/saveAccount2" method="post">
        姓名：<input type="text" name="username"/><br/>
        密码：<input type="text" name="password"/><br/>
        金额：<input type="text" name="money"/><br/>
        用户姓名：<input type="text" name="list[0].uname"/><br/>
        用户年龄：<input type="text" name="list[0].age"/><br/>
        用户姓名：<input type="text" name="map['one'].uname"/><br/>
        用户年龄：<input type="text" name="map['one'].age"/><br/>
        <input type="submit" value="提交"/>
    </form>
```

### 获取servlet原生的api

```java
    @RequestMapping("/testServlet")
    public String testServlet(HttpServletRequest request, HttpServletResponse response){
        Cookie[] cookies = request.getCookies();
        HttpSession session = request.getSession();
        ServletContext servletContext = session.getServletContext();
        return "success";
    }
```

### @RequestParam注解

value为前端传的形参

```java
@Controller
@RequestMapping("/anno")
public class AnnoController {
    @RequestMapping("/testRequestParam")
    public String testRequestParam(@RequestParam(value = "name",required = true) String username){
        System.out.println(username);
        return null;
    }
}
```

### @RequestBody：获取整个请求体的内容

获取post提交的json string

```jsp
<form action="/anno/testRequestBody" method="post">
    姓名：<input type="text" name="username"/><br/>
    生日：<input type="text" name="birth"/><br/>
    <input type="submit" value="提交"/>
</form>
```

```java
    @RequestMapping("/testRequestBody")
    public String testRequestBody(@RequestBody String requestBody){
        System.out.println(requestBody);
        return null;
    }
```



### @PathVariable



## 配置自定义类型转换器

目的：使得用户输入的"yyyy-mm-dd"可以自动转换为date类型传给后台

### 自定义类型转换器

```java
public class StringToDateConverter implements Converter<String, Date> {
    @Override
    public Date convert(String source) {
        if (source == null) {
            throw new RuntimeException("请传入数据");
        }
        SimpleDateFormat format = new SimpleDateFormat("yyyy-mm-dd");
        Date date = null;
        try {
            date = format.parse(source);
        } catch (ParseException e) {
            throw new RuntimeException("数据类型转换错误");
        }
        return date;
    }
}
```

### springmvc配置文件配置，使得组件生效

```xml
    <!--    开启springmvc框架的注解支持-->
    <mvc:annotation-driven conversion-service="conversionService"></mvc:annotation-driven>
    <!--    配置自定义类型转换器-->
    <bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
        <property name="converters">
            <set>
                <bean class="StringToDateConverter"></bean>
            </set>
        </property>
    </bean>
```



## 文件上传

## 拦截器

