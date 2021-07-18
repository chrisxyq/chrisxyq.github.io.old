---
layout: post
title: SpringMVC使用
catalog: true
tags:
    - SpringMVC
---



## 在单元方法里面获取请求参数的方法

#### 1.紧耦合

![1599142063183](https://gitee.com/chrisxyq/picgo/raw/master/img/1599142063183.png)

#### 2.解耦合方式（形参名和请求数据键名一致

![1599142406725](https://gitee.com/chrisxyq/picgo/raw/master/img/1599142406725.png)

#### 3.解耦合方式（形参名和请求数据键名不一致

提交的域名称和处理方法参数不一致

![1599143315510](https://gitee.com/chrisxyq/picgo/raw/master/img/1599143315510.png)

![1599143334472](https://gitee.com/chrisxyq/picgo/raw/master/img/1599143334472.png)

#### 4.解耦合方式（使用实体类接收请求参数

![1599145061723](https://gitee.com/chrisxyq/picgo/raw/master/img/1599145061723.png)

## SpringMVC处理restful风格的请求

### restful传递参数@pathVariable

![1603444602406](https://gitee.com/chrisxyq/picgo/raw/master/img/1603444602406.png)



![1593962427637](https://gitee.com/chrisxyq/picgo/raw/master/img/1593962427637.png)

![1599147373919](https://gitee.com/chrisxyq/picgo/raw/master/img/1599147373919.png)

### 指定请求方式的两种方法

方法1：@requestmapping+请求方式

![1603445172014](https://gitee.com/chrisxyq/picgo/raw/master/img/1603445172014.png)

方法2：组合注解

![1603445057442](https://gitee.com/chrisxyq/picgo/raw/master/img/1603445057442.png)

![1603445068222](https://gitee.com/chrisxyq/picgo/raw/master/img/1603445068222.png)

## springmvc实现转发和重定向

### 方法1：不使用视图解析器

请求转发

![1603446047804](https://gitee.com/chrisxyq/picgo/raw/master/img/1603446047804.png)

重定向

![1603446093579](https://gitee.com/chrisxyq/picgo/raw/master/img/1603446093579.png)

### 方法2：有视图解析器

![1603446122934](https://gitee.com/chrisxyq/picgo/raw/master/img/1603446122934.png)



## controller返回json数据：jackson、fastjson

把对象以json字符串的形式输出



### @RestController@ResponseBody

使用以上注解的controller，不会被视图解析器解析，返回的就是字符串。

传json格式就使用该注解。

由于controller注解返回的字符串默认会被视图解析器解析，因此如果要返回json格式的话，需要使用@RestController注解，从而取消视图解析器解析。

### jackon

传递单个对象

![1600245321329](https://gitee.com/chrisxyq/picgo/raw/master/img/1600245321329.png)

传递arraylist

![1600245555755](https://gitee.com/chrisxyq/picgo/raw/master/img/1600245555755.png)



