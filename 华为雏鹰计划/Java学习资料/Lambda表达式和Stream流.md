## 1、一个Lambda表达式例子

不用Lambda

```java
interface Printer {
    void print(String str);
}

public static void printSomeThing(String s, Printer printer) {
    printer.print(s);
}

public static void main(String[] args) {
    String s = "aabhifdadh";

    Printer printer = new Printer() {
        @Override
        public void print(String str) {
            System.out.println(s);
        }
    };

    printSomeThing(s, printer);

}
```

使用Lambda表达式

```java
interface Printer {
    void print(String str);
}

public static void printSomeThing(String s, Printer printer) {
    printer.print(s);
}

public static void main(String[] args) {
    String s = "aabhifdadh";

    //lambda支持只有一个方法
    Printer printer = (String val) -> {
        System.out.println(val);
    };

    printSomeThing(s, printer);

}
```

## 2、Stream代替for循环

数组转化成Stream

```java
String[] strings = new String[5];
Stream.of(strings)
    .filter()
    .map();
```

列表转化成Stream

```java
List<String> list = Arrays.asList("kobe", "james", "ad", "hhh", "dada");

List<String> sort = list.stream()
    .filter(s -> s.startsWith("k"))
    .map(String::toUpperCase)
    .sorted()
    .collect(Collectors.toList());
```

#### Filter()：Predicate，过滤一些判断的东西

实体类

```java
class Student {
    int age;
    String sex;
    static Predicate<Student> ageGreaterThan80 = x -> x.getAge() > 80;
    static Predicate<Student> sexM = x -> x.getSex().equals("M");
}
```

测试

```java
Student student1 = new Student(10, "F");
Student student2 = new Student(20, "F");
Student student3 = new Student(30, "F");
Student student4 = new Student(40, "F");
Student student5 = new Student(50, "F");
Student student6 = new Student(60, "M");
Student student7 = new Student(70, "M");
Student student8 = new Student(80, "M");
Student student9 = new Student(90, "M");
Student student10 = new Student(100, "M");

List<Student> list = Arrays.asList(student1, student2, student3, student4, student5, student6, student7, student8, student9, student10);

List<Student> res = list.stream()
    .filter(x -> x.getAge() > 80 && x.getSex().equals("M")) //也可以使用or或negate
    .collect(Collectors.toList());

List<Student> res2 = list.stream()
    .filter(Student.ageGreaterThan80.and(Student.sexM))
    .collect(Collectors.toList());
```

#### Map()：数据转化

数据类型转换

```java
List<String> list2 = Arrays.asList("dioahd", "uifdagf", "fkahf");
List<String> res2 = list2.stream()
    .map(String::toUpperCase)
    //.map(s -> s.toUpperCase())  //也可以用一般的lambda表达式
    .collect(Collectors.toList());


res2.stream().forEach(System.out::println);
```

数据格式转化：

```java
Stream.of("daohda","fghuiagf","fvhnuiaof")
    .mapToInt(x -> x.length())
    .forEach(System.out::println);
```

将上面的信息年龄+1，且变性

```java
List<Student> res = list.stream()
    .map(x -> {  //使用peek的话就不用return x
        x.setAge(x.getAge() + 1);
        x.setSex(x.getSex().equals("M") ? "male" : "female");
        return x;
    })
    .collect(Collectors.toList());
```

#### FlatMap()：多维数据转化

对比两个结果，mapsplit后对应的是数组，打印出来就是数组，而使用flatMap则可以打印出来多维的数组

```java
List<String> list1 = Arrays.asList("hello", "world");

list1.stream()
    .map(x -> x.split(""))
    .forEach(System.out::println);

list1.stream()
    .flatMap(x -> Arrays.stream(x.split("")))
    .forEach(System.out::println);
```

## 3、Stream有状态操作

**状态一般代表一种公共的数据。filter、map和flatmap这些都是无状态操作，有输入就有结果。而distinct、limit、skip和sorted则是由状态操作，必须通过多个数据的对比和交互才能得到结果。**

几个有状态的操作

```java
List<String> list1 = Arrays.asList("hello", "world", "hello", "hahahaha");

List<String> res1 = list1.stream()
    .limit(2)
    .collect(Collectors.toList());

List<String> res2 = list1.stream()
    .skip(2)
    .collect(Collectors.toList());

List<String> res3 = list1.stream()
    .distinct()
    .collect(Collectors.toList());

List<String> res4 = list1.stream()
    .sorted()
    .collect(Collectors.toList());
```

#### 重点：为什么要区分有状态和无状态操作？？

这里有一段代码：他代表并行处理该数据流

```java
List<String> list1 = Arrays.asList("hello", "world", "hello", "hahahaha");
List<String> res1 = list1.stream()
    .parallel()
    .limit(2)
    .collect(Collectors.toList());
```

- 对于无状态操作，串行和并行是一样的。而对于有状态操作，并行结果可能跟串行不一样。

## 4、像SQL一样排序

将一个list进行排序

```java
Student student1 = new Student(10, "F");
Student student2 = new Student(20, "F");
Student student3 = new Student(30, "F");
Student student4 = new Student(40, "F");
Student student5 = new Student(50, "F");
Student student6 = new Student(60, "M");
Student student7 = new Student(70, "M");
Student student8 = new Student(80, "M");
Student student9 = new Student(90, "M");
Student student10 = new Student(100, "M");

List<Student> list = Arrays.asList(student1, student2, student3, student4, student5, student6, student7, student8, student9, student10);

list.sort(Comparator.comparing(Student::getAge).reversed());

list.forEach(System.out::println);
```

说明：两个变量排序时reversed说明。

- 都是正序，不加reversed
- 都是倒序，最后面加一个reversed
- 先是倒序（加reversed），然后正序
- 先是正序（加reversed），再是倒序（加reversed）

## 5、函数式接口

- 函数式接口表示**只有一个抽象函数的接口**，lambda表达式只适用于函数式接口
- 允许定义静态非抽象方法
- 允许定义默认default非抽象方法
- 允许java.lang.Object中的public方法，比如equals
- FuncationInterface注解不是必须的

**java8之后有个新特性：原来是一改接口就需要改所有的实现类，而现在把方法定义成default方法，就可以有默认实现的方法。**

使用函数式接口进行排序

```java
Student student1 = new Student(10, "F");
Student student2 = new Student(20, "F");
Student student3 = new Student(30, "F");
Student student4 = new Student(40, "F");
Student student5 = new Student(50, "F");
Student student6 = new Student(60, "M");
Student student7 = new Student(70, "M");
Student student8 = new Student(80, "M");
Student student9 = new Student(90, "M");
Student student10 = new Student(100, "M");

List<Student> list = Arrays.asList(student1, student2, student3, student4, student5, student6, student7, student8, student9, student10);


list.sort(new Comparator<Student>() {
    @Override
    public int compare(Student o1, Student o2) {
        if (o1.getAge() == o2.getAge()) {
            return 0;
        }
        return o1.getAge() - o2.getAge() > 0 ? -1 : 1;
    }
});

list.forEach(System.out::println);
```

## 6、Stream其他一些API

- 任意一个条件满足

  ```java
  //是否存在任意一个员工年龄大于70
  boolean isAnyGreaterThan70 = list.stream().anyMatch(x -> x.getAge() > 70);
  ```

- 判断是否全部满足

  ```java
  //判断是否全部满足
  boolean isAllGreaterThan9 = list.stream().allMatch(x -> x.getAge() > 9);
  ```

- 判断是否全部不满足

  ```java
  //判断是否全部不满足
  boolean isNoneGreatrThan18 = list.stream().noneMatch(x -> x.getAge() < 18);
  ```

- 查找特定的元素：返回一个Optional对象（存在则使用get获取，不存在用get则抛出异常）

  ```java
  //返回特定过滤条件的Optional对象
  Optional<Student> res1 = list.stream().filter(x -> x.getAge() < 50).findFirst();
  System.out.println(res1.get());
  //使用.isPresent()返回boolean类型对象，表示是否存在
  System.out.println(res1.isPresent());
  ```

## 7、集合规约

规约表示将集合内多个值经过计算最终得到一个值的过程

简单累加器

```java
//简单累加器
Integer res2 = list1.stream().reduce(0, (subtotal, element) -> subtotal + element);
Integer res3 = list1.stream().reduce(0, Integer::sum);

System.out.println(res2);
System.out.println(res3);
```

年龄累加

```java
//得到学生的年龄之和
Integer sum = list.stream().map(Student::getAge).reduce(0, (subtotal, element) -> subtotal + element);
System.out.println(sum);
```

使用并行流

```java
//第一个参数初始值，第二个参数为累加器，第三个参数为合并器
Integer sum = list.parallelStream().map(Student::getAge).reduce(0, Integer::sum, Integer::sum);
System.out.println(sum);
```







