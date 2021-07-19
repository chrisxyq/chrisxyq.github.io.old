---
layout: post
title: Collection接口
catalog: true
tags:
    - Java

---

## 集合-Collection接口

### 数组的缺点

![1590910691407](https://gitee.com/chrisxyq/picgo/raw/master/img/1590910691407.png)

### collection接口的常用方法

- add：可添加string、int、date、布尔等元素
- size：集合内元素的个数
- addall：把另一个集合的元素都添过来
- isempty：判断集合是否为空/size是否为0
- clear：清空集合内的元素
- contains：判断集合是否包含某元素、是通过调用该元素的类的equals方法进行比较判断的（object的equals方法默认比较的是引用）而非内容（==）、若对象重写了equals方法，则只要内容一样，即contains为true（如string
- remove（对象）：从集合中删除某对象、若删除成功、则返回true
- 集合1.retainall（集合2）：求集合的交集、直接修改集合1
- 集合1.removeall（集合2）：求集合的差集、直接修改集合1
- 集合1.equals（集合2）：按顺序调用集合元素的equals方法、（集合是有序的
- 集合.toarray()：集合转数组
- arrays.aslist(数组/多个元素)：数组转集合

### 集合遍历

- iterator()：返回一个迭代器iterator接口的对象

  ![image-20210124160506379](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210124160506379.png)

  迭代器提供的remove方法

  ![image-20210124160811569](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210124160811569.png)

- foreach：内部调用迭代器

  ![image-20210124161410364](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210124161410364.png)

注意：普通for循环和增强for循环的区别（增强for循环是重新赋了值给新的变量s、参考string字符串的内存

![image-20210124161636784](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210124161636784.png)

### collection接口的子接口-list：元素有序可重复、动态数组

![1590921368429](https://gitee.com/chrisxyq/picgo/raw/master/img/1590921368429.png)

![image-20210124185654471](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210124185654471.png)

#### arraylist

- jdk7：arraylist默认空参构造器创建长度为10的object[]数组（stringbuffer的空参构造器创建了长度为16的char[]数组
- jdk8：为节省内存、arraylist默认空参构造器不创建数组、当我们第一次add元素时，才创建长度为10的object[]数组

- arraylist默认扩容1.5倍，并将原有数据复制到新数组（stringbuffer默认扩容为两倍加2

![image-20210124163950905](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210124163950905.png)

- 为避免扩容/数组的数据复制，实际开发中，若大概知道数组长度，可使用带参构造器创建arraylist

#### linkedlist

linkedlist查找不方便、元素需要维护指针

arraylist增加删除不方便

linkedlist源码有node这个静态内部类、还有首尾node类型的属性、静态内部类如下：

![1590922846514](https://gitee.com/chrisxyq/picgo/raw/master/img/1590922846514.png)

linkedlist添加一个元素源码：更新lastnode属性

![image-20210124165857503](https://gitee.com/chrisxyq/picgo/raw/master/img/image-20210124165857503.png)



#### 线程安全的arraylist

collections工具类有个方法叫synchronizedlist，把arraylist对象作为参数放进该方法中，将返回线程安全的arraylist



### collection接口的子接口-set：元素无序不可重复

#### hashset

遍历时候的顺序为：元素的哈希值的顺序、而非添加时候的顺序

线程不安全，可以存储null值

![1596289716491](https://gitee.com/chrisxyq/picgo/raw/master/img/1596289716491.png)

#### linkedhashset

是HashSet的子类，遍历时，可以按照添加的顺序遍历

#### treeset

放入的对象需要是同一个类new出来的对象，并根据对象的某些属性排序

![1596290257245](https://gitee.com/chrisxyq/picgo/raw/master/img/1596290257245.png)

自然排序与定制排序

![1596290553807](https://gitee.com/chrisxyq/picgo/raw/master/img/1596290553807.png)

自然排序：则根据放入对象的compareTo方法来排序，即放入对象user需要实现compareTo接口

![1596290881862](https://gitee.com/chrisxyq/picgo/raw/master/img/1596290881862.png)

定制排序示例

![1596290664116](https://gitee.com/chrisxyq/picgo/raw/master/img/1596290664116.png)

### 













