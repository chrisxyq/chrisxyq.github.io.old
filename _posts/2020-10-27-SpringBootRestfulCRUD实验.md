---
layout: post
title: SpringBootRestfulCRUD实验
catalog: true
tags:
    - Springboot
---
thymeleaf语法：

- 超链接的url用@：其中/就代表静态文件的根目录
- 国际化的消息表达式用：#{}

# 1.RestfulCRUD实验

## 1.1.dao伪造数据库

在dao类上使用@repository注解，则dao类被spring托管，则可以使用@autowired注解

![1603699350082](https://gitee.com/chrisxyq/picgo/raw/master/img/1603699350082.png)

网页html放到templates下，以得到模板引擎的解析

静态资源放到static下

## 1.2.访问首页：添加viewcontroller视图映射

classpath/templates下的页面只能通过controller跳转

方式1：扩展springmvc，添加首页视图控制

添加viewcontroller视图映射，直接把请求映射到controller

![1592380688396](https://gitee.com/chrisxyq/picgo/raw/master/img/1592380688396.png)

方式2：写controller的形式：

![1592380935281](https://gitee.com/chrisxyq/picgo/raw/master/img/1592380935281.png)

可以将index改名成login

## 1.3加载静态资源：将网页修改成thymeleaf风格的

将网页修改成thymeleaf风格的，以成功加载静态资源。即使用thymeleaf接管静态资源，注意加thymeleaf的命名空间

例：html中的超链接加载css，要修改成thymeleaf风格，可修改如下：

注：超链接的url用@：其中/就代表静态文件的根目录

![1592381743314](https://gitee.com/chrisxyq/picgo/raw/master/img/1592381743314.png)

注意：

- 在application.properties关闭模板引擎的缓存

![1592381913432](https://gitee.com/chrisxyq/picgo/raw/master/img/1592381913432.png)

- 若静态资源文件夹下也有index.html首页，则默认打开的是该首页，而不是templates文件夹下的首页。

![1592295474584](https://gitee.com/chrisxyq/picgo/raw/master/img/1592295474584.png)

## 1.4国际化：LocaleResolver：thymeleaf的message用#{}取值

首先保证settings-editor-file encodings编码是UTF-8，以防止乱码

### step1 编写国际化配置文件，抽取页面的国际化信息

可视化配置，进行编写国际化配置文件

![1591634836921](https://gitee.com/chrisxyq/picgo/raw/master/img/1591634836921.png)

### step2 在application.properties配置配置文件的路径信息：设置国际化资源文件的基础名的路径

![1603701169819](https://gitee.com/chrisxyq/picgo/raw/master/img/1603701169819.png)

### step3：修改html：在需要抽取的信息处，新增th:text标签

![1592296416449](https://gitee.com/chrisxyq/picgo/raw/master/img/1592296416449.png)





### 功能实现：点击按钮切换语言：编写一个LocaleResolver添加在容器中

#### step1修改login.html，实现点击链接发送带参数的请求

**thymeleaf使用括号实现向后端传递参数**

![1592389512484](https://gitee.com/chrisxyq/picgo/raw/master/img/1592389512484.png)



#### step2:在config文件夹下，写一个区域信息解析器，实现LocaleResolver接口：接收前端传递的l参数

![1592395879011](https://gitee.com/chrisxyq/picgo/raw/master/img/1592395879011.png)



#### step3:在容器中注入写的区域信息解析器，使得区域信息解析器生效

![1603702313188](https://gitee.com/chrisxyq/picgo/raw/master/img/1603702313188.png)





## 1.5登录功能实现

### 1.为表单的input标签添加name属性，使得提交时可以传值到后端controller

![1603702837275](https://gitee.com/chrisxyq/picgo/raw/master/img/1603702837275.png)

### 2.controller

![1603702957841](https://gitee.com/chrisxyq/picgo/raw/master/img/1603702957841.png)

关于@responsebody的说明：

若使用@responsebody，则无法跳到视图，而是返回一个字符串，因此上图需要把@responsebody注解去掉

@ResponseBody：使用在控制层（controller）的方法上，作用：将方法的返回值，以特定的格式写入到response的body区域，进而将数据返回给客户端。如果返回值是字符串，那么直接将字符串写到客户端；如果是一个对象，会将对象转化为json串，然后写到客户端。

### **3.增加登陆失败提示：修改login.html--增加p标签实现消息回显**

![1592408162623](https://gitee.com/chrisxyq/picgo/raw/master/img/1592408162623.png)

### 4.解决登陆后的域名问题

![1592408521900](https://gitee.com/chrisxyq/picgo/raw/master/img/1592408521900.png)

#### step1：修改MyMvcConfig文件，增加视图映射

将main请求映射到登陆后的dashboard页面，使得登陆后的url显示为main

![1592408730178](https://gitee.com/chrisxyq/picgo/raw/master/img/1592408730178.png)

#### step2：修改logincontroller：重定向

![1603703836191](https://gitee.com/chrisxyq/picgo/raw/master/img/1603703836191.png)

其实根本不存在main.html这个文件，只是将其作为一个请求名映射到主页

效果

![1592408984547](https://gitee.com/chrisxyq/picgo/raw/master/img/1592408984547.png)

## 1.6登陆拦截器

为了解决登不登录均能进入主页的问题

#### step1：修改LoginController，在login方法中传入session对象，使得登陆成功之后，session里面存在loginUser

在方法中传入session参数，在登陆成功后，使用session.setAttribute来使得session的loginUser有值

![1592447964251](https://gitee.com/chrisxyq/picgo/raw/master/img/1592447964251.png)

如果session中存在loginUser，则说明已经登录

#### step2：在config文件夹下写一个拦截器LoginHandlerInterceptor，实现HandlerInterceptor接口

拦截器重写preHandle方法（目标函数执行之前的方法），在方法中接收session中的loginUser，进行业务逻辑判断

如果loginUser为空，说明没有登陆

![1592449083204](https://gitee.com/chrisxyq/picgo/raw/master/img/1592449083204.png)

注意（对上图的请求转发的修改：

```java
请求转发,注意是转发到"/index.html"，而非"/login.html"（参考MyMvcConfig的视图映射）
request.getRequestDispatcher("/index.html").forward(request,response);
```

注：MyMvcConfig的视图映射

![1592450580902](https://gitee.com/chrisxyq/picgo/raw/master/img/1592450580902.png)



#### step3：将拦截器配置到bean里面注册--修改MyMvcConfig：重写addinterceptors方法

至此，MyMvcConfig配置了页面路由映射、区域信息解析器、拦截器

![1592450957735](https://gitee.com/chrisxyq/picgo/raw/master/img/1592450957735.png)



## 1.7thymeleaf：抽取公共页面：使用th:fragments

### step1：在dashboard为顶部导航栏和侧边栏加上th:fragments

![1592489093157](https://gitee.com/chrisxyq/picgo/raw/master/img/1592489093157.png)

### step2：在list.html使用th:insert引用dashboard的顶部导航栏和侧边栏

![1592489228632](https://gitee.com/chrisxyq/picgo/raw/master/img/1592489228632.png)

#### step3：将dashboard的顶部导航栏和侧边栏移动到commons.html，使用th:replace标签，在dashboard和index中引用commons.html的顶部导航栏和侧边栏

类似第二步

![1592490991507](https://gitee.com/chrisxyq/picgo/raw/master/img/1592490991507.png)

## 1.8thymeleaf：侧边栏切换高亮显示：（）传参与active

dashboard和list.html通过（）传参数给commons.html，由commons.html判断我们点了哪个标签，就active高亮哪个标签

![1592494334337](https://gitee.com/chrisxyq/picgo/raw/master/img/1592494334337.png)

![1592494786820](https://gitee.com/chrisxyq/picgo/raw/master/img/1592494786820.png)

修改commons.html

![1592494662220](https://gitee.com/chrisxyq/picgo/raw/master/img/1592494662220.png)













## 1.9员工管理系统--展示员工列表

### 1登陆页面dashborad显示用户名--从session中取名字

修改dashboard.html

![1592451309046](https://gitee.com/chrisxyq/picgo/raw/master/img/1592451309046.png)

ctrl+F9编译一下即可

### 2实现员工管理的页面跳转

![1592475614956](https://gitee.com/chrisxyq/picgo/raw/master/img/1592475614956.png)

![1592738622407](https://gitee.com/chrisxyq/picgo/raw/master/img/1592738622407.png)

前端list.html回显数据

![1592492764102](https://gitee.com/chrisxyq/picgo/raw/master/img/1592492764102.png)

### 3增加操作表格的按钮、性别01改成男女、日期格式

目标：性别01改成男女、增加操作按钮、日期格式

![1592493475756](https://gitee.com/chrisxyq/picgo/raw/master/img/1592493475756.png)

结果

![1592493504577](https://gitee.com/chrisxyq/picgo/raw/master/img/1592493504577.png)

## 1.7添加员工

### 添加员工页面

添加员工的页面的表单的每个input标签都应有name属性，且name需要与员工类的字段一致，只有这样点击添加才能提交上去

![1603707505900](https://gitee.com/chrisxyq/picgo/raw/master/img/1603707505900.png)

注意到添加员工页面的12345是不合理的，因此在跳转到该页面的时候需要查出所有的部门信息显示

修改后的

注：由于dao的方法需要的是id，因此选中时th:value需要传出id

![1603707972093](https://gitee.com/chrisxyq/picgo/raw/master/img/1603707972093.png)

点击添加按钮，发送post请求

![1603708301962](https://gitee.com/chrisxyq/picgo/raw/master/img/1603708301962.png)

### 跳转到添加员工页面的controller

在跳转到添加员工页面时，查出所有部门信息带到前端

![1603707834338](https://gitee.com/chrisxyq/picgo/raw/master/img/1603707834338.png)

### 处理添加按钮post请求的controller

![1603763764199](https://gitee.com/chrisxyq/picgo/raw/master/img/1603763764199.png)

### 400错误

检查add.html

![1592743644608](https://gitee.com/chrisxyq/picgo/raw/master/img/1592743644608.png)

为option赋值，value才是真正的值，text只是显示。

发现错误，select标签的name不应该是department，而是department.id，即传出的参数为id属性。

### 由于输入时间格式导致的400错误

spring默认支持时间格式为/，若在输入框输入-格式的时间，则提交表单将发生400错误

如果希望前端可以传入-格式的时间，则需要做如下配置：

![1592747112521](https://gitee.com/chrisxyq/picgo/raw/master/img/1592747112521.png)

## 1.8修改员工

修改员工与增加员工的区别在于：修改需要携带员工的信息

### 修改员工按钮

![1592751575915](https://gitee.com/chrisxyq/picgo/raw/master/img/1592751575915.png)

### 跳转到员工修改页面的controller

controller方法中需要查出原来的数据，显示在修改页面中

方法接收rest风格的请求

controller查出当前id的员工，以及所有的部门信息。

![1592753091658](https://gitee.com/chrisxyq/picgo/raw/master/img/1592753091658.png)



### 员工修改页面：把员工信息携带到修改页面：回显数据

复制add到update.html

**div、p标签是th:text**、**input标签是th:value**

修改update.html：

![1592752977720](https://gitee.com/chrisxyq/picgo/raw/master/img/1592752977720.png)

将所有的部门信息添加到部门下拉框中，然后将下拉框定位到要编辑的员工所属的部门

![1592753933687](https://gitee.com/chrisxyq/picgo/raw/master/img/1592753933687.png)

### 日期格式设置

![1592754629114](https://gitee.com/chrisxyq/picgo/raw/master/img/1592754629114.png)



### 修改按钮提交

首先修改update.html

![1592754372319](https://gitee.com/chrisxyq/picgo/raw/master/img/1592754372319.png)

注意修改员工的sql是根据员工的id修改的，因此提交表单需要提交员工的id，需要把员工id放到隐藏域里面提交，如下图所示：

![1592754883807](https://gitee.com/chrisxyq/picgo/raw/master/img/1592754883807.png)

## 1.9删除员工

### 为删除按钮写跳转请求链接

注意要把button改成a标签

![1592836147434](https://gitee.com/chrisxyq/picgo/raw/master/img/1592836147434.png)



### 写controller方法

![1592835992865](https://gitee.com/chrisxyq/picgo/raw/master/img/1592835992865.png)

## 1.10自定义404页面：在templates的error文件夹下建立404.html

## 1.11注销

![1600677595251](https://gitee.com/chrisxyq/picgo/raw/master/img/1600677595251.png)

![1600677534662](https://gitee.com/chrisxyq/picgo/raw/master/img/1600677534662.png)

