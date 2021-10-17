---
layout: post
title: Spring-SpringMVC
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



## 请求

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

```jsp
<a href="/anno/testPathVariable/1">PathVariable</a><br/>
```

```java
    @RequestMapping("/testPathVariable/{sid}")
    public String testPathVariable(@PathVariable(name="sid") String id){
        System.out.println(id);
        return "success";
    }
```

### @sessionAttributes：用于控制器多次执行方法间的参数共享（只能作用于类上）

spring mvc提供了Model：类似于servlet原生api：HttpServletRequest.setAttribute()

```jsp
<a href="/anno/testSessionAttributes">putSessionAttributes</a><br/>
<a href="/anno/getSessionAttributes">getSessionAttributes</a><br/>
<a href="/anno/delSessionAttributes">delSessionAttributes</a><br/>
```

```java
@Controller
@RequestMapping("/anno")
@SessionAttributes(value = {"msg"})
public class AnnoController {
  
    @RequestMapping("/testSessionAttributes")
    public String testSessionAttributes(Model model){
        //底层存储到request域对象
        model.addAttribute("msg","美美");
        return "success";
    }
    @RequestMapping("/getSessionAttributes")
    public String getSessionAttributes(ModelMap modelMap){
        System.out.println(modelMap.get("msg"));
        return "success";
    }
    @RequestMapping("/delSessionAttributes")
    public String delSessionAttributes(SessionStatus sessionStatus){
        sessionStatus.setComplete();
        return "success";
    }
```

页面可以回显设置的attribute

```jsp
<body>
${requestScope.msg}
${sessionScope}
</body>
```

## 响应数据和结果视图

### 返回值类型：字符串

```jsp
<a href="/user/testString">testString</a><br/>
```

```java
@RequestMapping("/user")
@Controller
public class UserController {
    @RequestMapping("/testString")
    public String testString(Model model){
        model.addAttribute("user",new User("uname","18"));
        return "responseSuccess";
    }
```

```jsp
<body>
${user.uname}
${user.age}
</body>
```

### 返回值类型：空

可以servlet的方式转发/重定向、或直接输出字符串回显

```java
    @RequestMapping("/testVoid")
    public void testVoid(HttpServletRequest request, HttpServletResponse response) throws Exception {
        request.getRequestDispatcher("WEB-INF/pages/success.jsp").forward(request, response);
        response.sendRedirect(request.getContextPath() + "/index.jsp");
        response.setCharacterEncoding("UTF-8");
        response.setContentType("text/html;charset=UTF-8");
        response.getWriter().print("hello");
        return;
    }
```

### springmvc方式转发/重定向

```java
    @RequestMapping("/testForwardAndRedirect")
    public String testForwardAndRedirect(HttpServletRequest request, HttpServletResponse response) throws Exception {
//        return "forward:/WEB-INF/pages/success.jsp";
//        return "redirect:/WEB-INF/pages/success.jsp";
        return "redirect:index.jsp";
    }
```

### 返回ModelAndView

```java
    @RequestMapping("/testModelAndView")
    public ModelAndView testModelAndView() throws Exception {
        ModelAndView modelAndView = new ModelAndView();
        //相当于model.addAttribute("user", new User("uname", "18"));
        //把user对象存储到request中，底层存储到request域对象
        modelAndView.addObject("user", new User("modelAndView", "18"));
        modelAndView.setViewName("responseSuccess");
        return modelAndView;
    }
```

### 接收ajax请求响应json数据

```jsp
    <script type="text/javascript"src="http://code.jquery.com/jquery-1.4.1.min.js"></script>
    <script>
        $(function () {
            $("#btn").click(function () {
                // alert("hello btn");
                $.ajax({
                    url:"user/testAjax",
                    contentType:"application/json;charset=UTF-8",
                    data:'{"username":"hehe","password":"123","age":"30"}',
                    dataType:"json",
                    type:"post",
                    success:function (data) {
                        alert(data);
                        alert(data.uname);
                        
                    }
                })
            })
        })
    </script>
```

```java
    @RequestMapping("/testAjax")
    public @ResponseBody User testAjax(@RequestBody User user) {
        user.setAge("40");
        user.setUname("tom");
        System.out.println(user);
        return user;
    }
```

报错

```java
//前端
Failed to load resource: the server responded with a status of 415 ()
//后端
springframework.web.servlet.mvc.support.DefaultHandlerExceptionResolver.handleHttpMessageNotReadable Failed to read HTTP message: org.springframework.http.converter.HttpMessageNotReadableException: Required request body is missing: public entity.User controller.UserController.testAjax(entity.User)

```

导入依赖

```xml
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.9.0</version>
    </dependency>
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-core</artifactId>
      <version>2.9.0</version>
    </dependency>
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-annotations</artifactId>
      <version>2.9.0</version>
    </dependency>
```



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

pom依赖：

```xml
    <dependency>
      <groupId>commons-fileupload</groupId>
      <artifactId>commons-fileupload</artifactId>
    </dependency>
    <dependency>
      <groupId>commons-io</groupId>
      <artifactId>commons-io</artifactId>
    </dependency>
```



## 拦截器

