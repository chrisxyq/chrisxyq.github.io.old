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



### **使用迭代器遍历、并且使用迭代器的删除方法(remove()) 删除是正确可行的，也是开发中推荐使用的。**

误区：

如果将上例中的iterator.remove(); 改为list.remove(student);将会报ConcurrentModificationException异常。

这是因为：使用迭代器遍历，却使用集合的方法删除元素的结果。

### **负载因子为什么0.75**

负载因子是0.75的时候，空间利用率比较高，而且避免了相当多的Hash冲突，使得底层的链表或者是红黑树的高度比较低，提升了空间效率。

### **arraylist深浅拷贝**

| 浅拷贝                           | Collections.copy(A,B)   ArrayList.clone()  ArrayList.addAll(list) |
| -------------------------------- | ------------------------------------------------------------ |
| 深拷贝                           | 继承Cloneable接口，同时必须实现clone()方法        @Override       public Student clone() {           return new Student(this.id, this.name);       }  ArrayList<Student> clonedList = new  ArrayList<>();           for (Student student : list) {              clonedList.add(student.clone());           } |
| javabean   继承Serializable 接口 |                                                              |



**list、map、set底层数据结构**

|                                 |                                                              | 数据结构                                                     |
| ------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| List有序,可重复                 | Arraylist                                                    | Object[]数组   默认长度为10，每次扩容1.5倍                   |
| Vector                          | Object[]数组                                                 |                                                              |
| LinkedList                      | 双向链表   默认长度为0，无扩容机制                           |                                                              |
| Map                             | HashMap                                                      | 数组和链表或红黑树   迭代时不是插入顺序  HashMap  默认的初始化大小为 16。之后每次扩充，容量变为原来的 2  倍  当链表长度大于阈值（默认为 8）（将链表转换成红黑树前会判断，如果当前数组的长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树）时，将链表转化为红黑树，以减少搜索时间 |
| LinkedHashMap（继承自 HashMap） | 数组和链表或红黑树+双向链表   维护了一个贯穿所有元素的双向链表，保证按插入顺序迭代 |                                                              |
| Hashtable                       | 数组+链表                                                    |                                                              |
| TreeMap                         | 红黑树（自平衡的排序二叉树）   实现SortMap接口让 TreeMap 有了对集合中的元素根据键排序的能力。默认是按 key 的升序排序  按树的中序遍历就能得到一个按 键-key 大小排序的序列  相比于HashMap来说 TreeMap 主要多了对集合中的元素根据键排序的能力以及对集合内元素的搜索的能力。 |                                                              |
| Set无序，唯一                   | HashSet（无序，唯一）                                        | 基于 HashMap 实现   无序性是指存储的数据在底层数组中并非按照数组索引的顺序添加 ，而是根据数据的哈希值决定的 |
| LinkedHashSet（继承自 HashSet） | 通过 LinkedHashMap 来实现的                                  |                                                              |
| TreeSet（有序，唯一）           | 红黑树(自平衡的排序二叉树)                                   |                                                              |

**HashMap数组长度为何是2的n次方**

HashMap 通过 key 的 hashCode 经过扰动函数处理过后得到 hash 值，Hash 值的范围值大概 40 亿的映射空间，所以这个散列值是不能直接拿来用的。用之前还要先做对数组的长度取模运算，即通过 (n - 1) & hash 判断当前元素存放的位置（这里的 n 指的是数组的长度）

hash%length==hash&(length-1)的前提是 length 是 2 的 n 次方

**HashMap 多线程操作导致死循环问题**

主要原因在于并发下的 Rehash 会造成元素之间会形成一个循环链表。不过，jdk 1.8 后解决了这个问题

重哈希的主要是一个重新计算原HashMap中的元素在新table数组中的位置并进行复制处理的过程





