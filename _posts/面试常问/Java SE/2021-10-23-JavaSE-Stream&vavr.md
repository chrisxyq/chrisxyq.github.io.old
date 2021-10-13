---
layout: post
title: Stream&vavr
catalog: true
tags:
    - Java SE

---

## Stream

### 排序

#### 员工按属性排序

```java
    @Test
    public void sortTest() {
        List<Person> personList = new ArrayList<Person>();

        personList.add(new Person("Sherry", 9000, 24, "female", "New York"));
        personList.add(new Person("Tom", 8900, 22, "male", "Washington"));
        personList.add(new Person("Jack", 9000, 25, "male", "Washington"));
        personList.add(new Person("Lily", 8800, 26, "male", "New York"));
        personList.add(new Person("Alisa", 9000, 26, "female", "New York"));

        // 按工资升序排序（自然排序）
        List<String> newList = personList.stream().sorted(Comparator.comparing(Person::getSalary)).map(Person::getName)
                .collect(Collectors.toList());
        // 按工资倒序排序
        List<String> newList2 = personList.stream().sorted(Comparator.comparing(Person::getSalary).reversed())
                .map(Person::getName).collect(Collectors.toList());
        // 先按工资再按年龄升序排序
        List<String> newList3 = personList.stream()
                .sorted(Comparator.comparing(Person::getSalary).thenComparing(Person::getAge)).map(Person::getName)
                .collect(Collectors.toList());
        // 先按工资再按年龄自定义排序（降序）
        List<String> newList4 = personList.stream().sorted((p1, p2) -> {
            if (p1.getSalary() == p2.getSalary()) {
                return p2.getAge() - p1.getAge();
            } else {
                return p2.getSalary() - p1.getSalary();
            }
        }).map(Person::getName).collect(Collectors.toList());

        System.out.println("按工资升序排序：" + newList);
        System.out.println("按工资降序排序：" + newList2);
        System.out.println("先按工资再按年龄升序排序：" + newList3);
        System.out.println("先按工资再按年龄自定义降序排序：" + newList4);
    }
//按工资升序排序：[Lily, Tom, Sherry, Jack, Alisa]
//按工资降序排序：[Sherry, Jack, Alisa, Tom, Lily]
//先按工资再按年龄升序排序：[Lily, Tom, Sherry, Jack, Alisa]
//先按工资再按年龄自定义降序排序：[Alisa, Jack, Sherry, Tom, Lily]
```

### 筛选：Stream的遍历

#### Integer集合的筛选和判断

```java
    @Test
    public void findTest() {
        List<Integer> list = Arrays.asList(7, 6, 9, 3, 8, 2, 1);

        // 遍历输出符合条件的元素
        list.stream().filter(x -> x > 6).forEach(System.out::println);
        // 匹配第一个
        Optional<Integer> findFirst = list.stream().filter(x -> x > 6).findFirst();
        // 是否包含符合特定条件的元素
        boolean anyMatch = list.stream().anyMatch(x -> x < 6);
        System.out.println("匹配第一个值：" + findFirst.get());
        System.out.println("是否存在大于6的值：" + anyMatch);
        long count = list.stream().filter(x -> x > 6).count();
        System.out.println("list中大于6的元素个数：" + count);
    }
```

#### 筛选：获取String集合中最长的元素

```java
@Test
public void filterTest3() {
    List<String> list = Arrays.asList("adnm", "admmt", "pot", "xbangd", "weoujgsd");

    Optional<String> max = list.stream().max(Comparator.comparing(String::length));
    System.out.println("最长的字符串：" + max.get());
}
```

#### 筛选：返回年龄为偶数，工资大于7000的最后一名员工大写的年龄

```java
    @Test
    public void filterTest1() {
        List<Person> personList = new ArrayList<Person>();
        personList.add(new Person("Tom", 8900, 23, "male", "New York"));
        personList.add(new Person("Jack", 7000, 25, "male", "Washington"));
        personList.add(new Person("Lily", 7800, 21, "female", "Washington"));
        personList.add(new Person("Anni", 8200, 24, "female", "New York"));
        personList.add(new Person("Owen", 9500, 25, "male", "New York"));
        personList.add(new Person("Alisa", 7900, 26, "female", "New York"));

        Stream<String> stringStream = personList.stream().filter(u -> u.getAge() % 2 == 0)
                .filter(u -> u.getSalary() > 7000)
                .map(m -> m.getName().toUpperCase())
                .sorted((Comparator.reverseOrder()))
                .limit(1);
        stringStream.forEach(System.out::println);
    }
```

#### 筛选：获取员工工资最高的人

```java
    @Test
    public void filterTest5() {
        List<Person> personList = new ArrayList<Person>();
        personList.add(new Person("Tom", 8900, 23, "male", "New York"));
        personList.add(new Person("Jack", 7000, 25, "male", "Washington"));
        personList.add(new Person("Lily", 7800, 21, "female", "Washington"));
        personList.add(new Person("Anni", 8200, 24, "female", "New York"));
        personList.add(new Person("Owen", 9500, 25, "male", "New York"));
        personList.add(new Person("Alisa", 7900, 26, "female", "New York"));

        Optional<Person> max = personList.stream().max(Comparator.comparingInt(Person::getSalary));
        System.out.println("员工工资最大值：" + max.get().getSalary());
        System.out.println("求最高工资：" + personList.stream().map(Person::getSalary).collect(Collectors.maxBy(Integer::compare)));
        System.out.println("求工资之和：" + personList.stream().collect(Collectors.summingInt(Person::getSalary)));
        System.out.println("员工工资最大值：" + personList.stream().map(Person::getSalary).collect(Collectors.maxBy(Integer::compare)));
    }
```

### 分组

```java
    /**
     * 分区：将stream按条件分为两个Map，比如员工按薪资是否高于8000分为两部分。
     * 分组：将集合分为多个Map，比如员工按性别分组。有单级分组和多级分组。
     * 将员工按薪资是否高于8000分为两部分；将员工按性别和地区分组
     * partitioningBy:函数的返回值只能将数据分为两组也就是true和false两组数据。
     
     员工按薪资是否大于8000分组情况：{false=[Person(name=Jack, salary=7000, age=0, sex=male, area=Washington),
     Person(name=Lily, salary=7800, age=0, sex=female, area=Washington),
     Person(name=Alisa, salary=7900, age=0, sex=female, area=New York)],
     
     true=[Person(name=Tom, salary=8900, age=0, sex=male, area=New York),
     Person(name=Anni, salary=8200, age=0, sex=female, area=New York),
     Person(name=Owen, salary=9500, age=0, sex=male, area=New York)]}
     
员工按性别分组情况：{female=[Person(name=Lily, salary=7800, age=0, sex=female, area=Washington),
Person(name=Anni, salary=8200, age=0, sex=female, area=New York),
Person(name=Alisa, salary=7900, age=0, sex=female, area=New York)],

male=[Person(name=Tom, salary=8900, age=0, sex=male, area=New York),
Person(name=Jack, salary=7000, age=0, sex=male, area=Washington),
Person(name=Owen, salary=9500, age=0, sex=male, area=New York)]}

员工按性别、地区：{female={New York=[Person(name=Anni, salary=8200, age=0, sex=female, area=New York),
Person(name=Alisa, salary=7900, age=0, sex=female, area=New York)],
Washington=[Person(name=Lily, salary=7800, age=0, sex=female, area=Washington)]},
male={New York=[Person(name=Tom, salary=8900, age=0, sex=male, area=New York),
Person(name=Owen, salary=9500, age=0, sex=male, area=New York)],
Washington=[Person(name=Jack, salary=7000, age=0, sex=male, area=Washington)]}}

     */
    @Test
    public void groupTest() {
        List<Person> personList = new ArrayList<Person>();
        personList.add(new Person("Tom", 8900, "male", "New York"));
        personList.add(new Person("Jack", 7000, "male", "Washington"));
        personList.add(new Person("Lily", 7800, "female", "Washington"));
        personList.add(new Person("Anni", 8200, "female", "New York"));
        personList.add(new Person("Owen", 9500, "male", "New York"));
        personList.add(new Person("Alisa", 7900, "female", "New York"));

        // 将员工按薪资是否高于8000分组
        Map<Boolean, List<Person>> part = personList.stream().collect(Collectors.partitioningBy(x -> x.getSalary() > 8000));
        // 将员工按性别分组
        Map<String, List<Person>> group = personList.stream().collect(Collectors.groupingBy(Person::getSex));
        // 将员工先按性别分组，再按地区分组
        Map<String, Map<String, List<Person>>> group2 = personList.stream().collect(Collectors.groupingBy(Person::getSex, Collectors.groupingBy(Person::getArea)));
        System.out.println("员工按薪资是否大于8000分组情况：" + part);
        System.out.println("员工按性别分组情况：" + group);
        System.out.println("员工按性别、地区：" + group2);
    }

```

------------

## vavr

### Match：模式匹配、消灭 if..else

```java
import static io.vavr.API.*;
    /**
     * 匹配一下（这个东西）的结构，是不是下面的情况之一
     * // Match(XXX).Of(
     *
     *   - 结构和 A 一样，做点什么事情
     *   //Case(  $(A),   () -> doSomethingA()   ),
     *
     *   - 结构和 B 一样，做点什么事情
     *   //Case(  $(B),   () -> doSomethingB()   ),
     *   - .....
     *
     *   - 和上面的结构都不一样，也做点事情
     *   //Case(  $(),    () -> doSomethingOthers())
     *   //);
     */
    @Test
    public void bmiFormat() {
        double height = 178;
        double weight = 65;
        double bmi = weight / (height * height);
        final String res = Match(bmi).of(
                // else if (bmi < 18.5)
                Case($(v -> v < 18.5), () -> "有些许晃荡！"),
                // else if (bmi < 25)
                Case($(v -> v < 25), () -> "继续加油哦！"),
                // else if (bmi < 30)
                Case($(v -> v < 30), () -> "你是真的稳！"),
                // else
                Case($(), () -> "难受！")
        );
        System.out.println(res);
    }
```

### Lazy缓存

```java
    /**
     * Lazy 也是一个容器，他可以延迟某个计算，直到该计算被首次调用，初次调用之后该结果会被缓存，
     * 后续调用就可以直接拿到结果。
     * Lazy方法生命周期级别的缓存
     */
    @Test
    public void test(){
        Supplier<User> supplier = ()->new User(1L,"zhangsan",18);
        //final String name = supplier.get().getName();
        Lazy<Double> lazy = Lazy.of(Math::random);
        Lazy<User> lazy2 = Lazy.of(supplier);
        System.out.println(lazy.isEvaluated());
        System.out.println(lazy.get());
        System.out.println(lazy.isEvaluated());
        System.out.println(lazy.get());
    }
```

### Either

```java
    public Either<Exception, String> compute() {
        Lazy<Double> lazy = Lazy.of(Math::random);
        final Double aDouble = lazy.get();
        System.out.println("lazy.get():" + aDouble);
        Either<Exception, String> res = Match(lazy.get()).of(
                Case($(v -> v < 0.5), () -> Either.left(new Exception())),
                // else
                Case($(), () -> Either.right("String"))
        );
        return res;
    }
    @Test
    public void test() {
        Either<Exception, String> either = compute();
        // 异常值
        if (either.isLeft()) {
            Exception exception = either.getLeft();
            throw new RuntimeException(exception);
        }
        // 正确值
        if (either.isRight()) {
            System.out.println(either.get());
        }
    }
```

