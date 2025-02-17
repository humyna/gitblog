# [Java 8 为什么会引入Lambda 表达式？](https://github.com/humyna/gitblog/issues/37)

Java 8 引入 Lambda 表达式是为了提升 Java 编程语言的表达能力和简洁性，特别是在处理集合、并行计算和事件驱动编程等方面。Lambda 表达式使得代码更加简洁、可读，并且更容易维护。以下是引入 Lambda 表达式的几个主要原因：

### 1. 简化代码

在没有 Lambda 表达式之前，Java 中需要使用匿名内部类来实现函数式接口（如 Runnable, Comparator 等），这通常会导致冗长且难以阅读的代码。Lambda 表达式提供了一种更简洁的方式来表示这些匿名函数。

#### 示例：使用匿名内部类
```java
List<String> list = Arrays.asList("a", "b", "c");
list.forEach(new Consumer<String>() {
    @Override
    public void accept(String s) {
        System.out.println(s);
    }
});
```

#### 使用 Lambda 表达式
```java
list.forEach(s -> System.out.println(s));
```

### 2. 提高可读性

Lambda 表达式使得代码更加直观和易于理解，因为它们直接表达了要执行的操作，而不是通过一大段样板代码来实现。

#### 示例：排序列表

##### 使用匿名内部类
```java
Collections.sort(list, new Comparator<String>() {
    @Override
    public int compare(String s1, String s2) {
        return s1.compareTo(s2);
    }
});
```

##### 使用 Lambda 表达式
```java
Collections.sort(list, (s1, s2) -> s1.compareTo(s2));
```

### 3. 支持函数式编程

Lambda 表达式是函数式编程的重要组成部分，它允许将行为作为参数传递给方法，从而提高了代码的灵活性和重用性。Java 8 引入了 `java.util.function` 包，其中包含了许多常用的函数接口，如 `Function`, `Predicate`, `Consumer` 和 `Supplier` 等。

#### 示例：使用 Predicate 接口进行过滤

##### 没有 Lambda 的情况下：
```java
public static List<String> filter(List<String> list, Predicate<String> predicate) {
    List<String> result = new ArrayList<>();
    for (String str : list) {
        if (predicate.test(str)) {
            result.add(str);
        }
    }
    return result;
}

List<String> filteredList = filter(list, new Predicate<String>() {
    @Override
    public boolean test(String s) {
        return s.startsWith("a");
    }
});
```

##### 使用 Lambda 表达式：
```java
List<String> filteredList = filter(list, s -> s.startsWith("a"));
```

### 4. 并行处理与Stream API

Java 8 引入了 Stream API，它极大地简化了集合数据的处理，包括过滤、映射、归约等操作。Stream API 与 Lambda 表达式紧密结合，使得对集合进行并行处理变得更加简单和高效。

#### 示例：使用 Stream API 和 Lambda 表达式进行并行处理

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
int sum = numbers.parallelStream()
                 .filter(n -> n % 2 == 0)
                 .mapToInt(Integer::intValue)
                 .sum();
System.out.println(sum); // 输出结果为6（即2+4）
```

### 总结

Java 8 引入 Lambda 表达式主要是为了：

- **简化代码**：减少样板代码，使得代码更加简洁。
- **提高可读性**：使得代码更加直观和易于理解。
- **支持函数式编程**：允许将行为作为参数传递，提高灵活性和重用性。
- **增强并行处理能力**：与 Stream API 紧密结合，简化并行数据处理。

通过引入 Lambda 表达式，Java 在保持其面向对象特性的同时，也吸收了一些现代编程语言中的优秀特性，使开发者能够以更高效、更优雅的方式编写代码。这不仅提升了开发效率，还增强了程序的可维护性。

by AI