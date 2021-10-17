---
layout: post
title: Java并发编程-completableFuture异步回调
catalog: true
tags:
    - Java并发编程



---

## completableFuture

### completableFuture有无返回值的异步回调

```java
public class CompletableFutureDemo {
    public static void main(String[] args) throws Exception {
        //无返回值的异步回调CompletableFuture.runAsync
        CompletableFuture<Void> completableFuture = CompletableFuture.runAsync(() -> {
            System.out.println(Thread.currentThread().getName() + "没有返回值，update mysql ok");
        });
        completableFuture.get();

        //有返回值的异步回调CompletableFuture.supplyAsync
        CompletableFuture<Integer> completableFuture2 = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread().getName() + "有返回值，insert mysql ok");
            int age = 10 / 0;
            return 1024;
        });
        //t为返回值，u为异常信息
        completableFuture2.whenComplete((t,u)->{
            System.out.println("*****t:"+t);
            System.out.println("*****u:"+u);
        }).exceptionally(f->{
            System.out.println("*****exception:"+f.getMessage());
            return 4444;
        }).get();
    }
```

--------

### completableFuture创建异步操作

![image-20210801161803155](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210801161803155.png)

![image-20210801161844085](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210801161844085.png)

#### demo1：将异步任务放到自定义线程池，没有返回值

![image-20210801162251588](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210801162251588.png)

#### demo2：将异步任务放到自定义线程池，有返回值（future.get获取返回值

![image-20210801162545579](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210801162545579.png)

### completableFuture计算完成时的回调方法

![image-20210801163037911](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210801163037911.png)

#### whenComplete（res,exception

![image-20210801163819080](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210801163819080.png)

#### exceptionally出现异常详细处理（指定异常后的返回数据

![image-20210801164042088](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210801164042088.png)

#### handle(res,exception 可以指定返回值

![image-20210801164634623](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210801164634623.png)

### completableFuture线程之间的串行化

![image-20210801164922043](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210801164922043.png)

线程AB按顺序串行化执行，可使用以下api

#### then apply（B需要接收A的返回值，且调用者需要获取B的返回值

![image-20210801170802293](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210801170802293.png)

#### then accept（B需要接收A的返回值，且调用者不需要获取B的返回值

![image-20210801170628596](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210801170628596.png)

#### then run（B不需要接收A的返回值，且调用者不需要获取B的返回值

![image-20210801170654584](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210801170654584.png)

### 两任务AB都必须完成，触发任务C

![image-20210801193256092](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210801193256092.png)

#### runAfterBoth（任务1和2结束之后执行任务3（任务3不接收1和2的返回值，任务3无返回值

![image-20210801193324636](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210801193324636.png)

#### thenAcceptBoth（任务1和2结束之后执行任务3（任务3接收1和2的返回值，任务3无返回值

![image-20210801192942340](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210801192942340.png)

#### thenCombine（任务1和2结束之后执行任务3（任务3接收1和2的返回值，任务3有返回值

![image-20210801193131451](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210801193131451.png)

### 两任务AB只要完成一个，即触发任务C

![image-20210801193444240](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210801193444240.png)

#### 举例：applyToEither（任务1和2结束之后执行任务3（任务3接收1或2的返回值，任务3有返回值

![image-20210801193623008](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210801193623008.png)

### completableFuture多任务组合

![image-20210801193743078](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210801193743078.png)

#### 阻塞式等待的写法（效率低

![image-20210801194005855](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210801194005855.png)

#### allof（效率高

![image-20210801194054576](https://gitee.com/chrisxyq/picgo/raw/master/https://gitee.com/chrisxyq/image-20210801194054576.png)