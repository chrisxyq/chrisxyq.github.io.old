---
layout: post
title: Linux指令&JVM命令
catalog: true
tags:
    - JVM

---

### 服务器变慢常用指令

![image-20210728100548330](C:\Users\13692\AppData\Roaming\Typora\typora-user-images\image-20210728100548330.png)

#### 整机：top

load average：1分钟、5分钟、15分钟的负载，三者的均值超过0.6，则说明高负载

![image-20210728100920834](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210728100920834.png)

#### cpu占用：vmstat

![image-20210728101402585](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210728101402585.png)

#### 某个进程的cpu占用：pidstat 进程号

![image-20210728101704812](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210728101704812.png)

#### 查看内存：free -m（单位M

![image-20210728102112530](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210728102112530.png)

#### 磁盘io性能评估：iostat（util：一秒有百分之几的时间用于io操作

![image-20210728102607583](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210728102607583.png)

### 内存飙升如何排查（top,ps,jstack）

#### 定位到我们的java进程号：jps –l或者ps –ef

#### 定位到具体的线程/代码：

ps –mp 进程号 –o thread,tid,time（线程id和耗费时间）

![image-20210728103549246](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210728103549246.png)

#### 将线程id转换成16进制格式

#### jstack 进程id|grep tid（16进制线程id） -A60

![image-20210728103607015](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210728103607015.png)



### JVM命令

#### 查看进程编号jps

jps -l：java的ps 查看当前java进程编号

#### 查看初始值jinfo –flag

##### jinfo –flag printGCDetails 进程编号：查看当前进程是否开启了printGCDetails

##### jinfo –flag MetaSpaceSize 进程编号：查看当前进程的元空间的大小（默认21M）

##### jinfo –flags 进程编号：查看全部参数

#### 查看初始值–XX:+PrintFlagsDetail

##### java –XX:+PrintFlagsDetail查看所有初始参数

#### 设置期望值-XX: KEY=VALUE

##### -XX：MaxTenuringThreshold=15设置15岁升老年代

##### -XX： +PrintGCDetails	开启打印GCDetails

##### -Xms	初始堆内存（默认物理内存的1/64）

##### -Xmx	最大堆内存（默认物理内存的1/4）

##### -Xss	单个线程栈大小（512-1024k）

##### -XX:MetaSpaceSize	元空间（受本地内存限制）（默认21M）

##### -XX:SurvivorRatio	伊甸园区：S0/S1=8

##### -XX:NewRatio	新生代：老年代=1:2



| 堆内存                   | -Xms2G -Xmx5G                                                | 最小最大堆内存                                               |
| ------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 新生代内存               | -XX:NewSize=256m   -XX:MaxNewSize=1024m  -Xmn256m            | 由于 Full GC 的成本远高于   Minor GC，通过“-Xmn”命令调节新生代大小，最大限度降低新对象直接进入老年代的情况。 |
| -XX:NewRatio=1           | 设置新生代（包括Eden和两个Survivor区）与老年代的比值为1      |                                                              |
| 元空间（使用的是本地内存 | -XX:MetaspaceSize=N //设置 Metaspace 的初始（和最小大小）   -XX:MaxMetaspaceSize=N | 设置 Metaspace 的最大大小，如果不指定大小的话，随着更多类的创建，虚拟机会耗尽所有可用的系统内存。 |

**常用jvm参数设置**

| 含义                                     | 参数                                            | 常用值                                                       |
| ---------------------------------------- | ----------------------------------------------- | ------------------------------------------------------------ |
| 每个线程的Stack大小                      | -Xss                                            |                                                              |
| 初始堆大小                               | -Xms                                            | -Xms和-Xmx设为相同值                                         |
| 最大堆大小                               | -Xmx                                            | 默认值为物理内存的1/4                                        |
| 年轻代大小                               | -XX:NewSize                                     |                                                              |
| 年轻代和年老代的比值                     | -XX:NewRatio                                    | 3表示年轻代与年老代比值为1：3                                |
| 年轻代中Eden区与两个Survivor区的比值     | -XX:SurvivorRatio                               | 默认Eden：Survivor=8:1，也就是每次新生代中可用内存空间为整个新生代容量的90%   ( 80%+10% ) |
| 在经历了15次minor gc后是要被移动到老年代 | -XX:MaxTenuringThreshold                        | 15                                                           |
| 大对象的定义                             | -XX:PretenureSizeThreshold                      | 大对象一般直接分配到老年代                                   |
| 开启逃逸分析和标量替换                   | -XX:+DoEscapeAnalysis -XX:+EliminateAllocations | 对象在栈上分配                                               |
| 元空间大小                               | -XX:MaxMetaspaceSize                            |                                                              |
