# [Java 8 新特性介绍](https://github.com/humyna/gitblog/issues/36)

by AI

Java 8 是一个重要的版本，引入了许多新的特性和改进，极大地增强了Java语言和平台的功能。以下是其主要特性的介绍。


#### 1. Lambda 表达式
Lambda表达式是Java 8中最重要的新特性之一，它允许你以更简洁的方式编写匿名函数，从而使代码更加简洁和可读。
```java
// 使用Lambda表达式
List<String> list = Arrays.asList("a", "b", "c");
list.forEach(item -> System.out.println(item));
```

#### 2. Stream API
Stream API 提供了一种高效且易于使用的方法来处理数据集合。它支持链式操作，可以进行过滤、映射、归约等操作。
```java
// 使用Stream API进行数据处理
List<String> filteredList = list.stream()
                                .filter(s -> s.startsWith("a"))
                                .collect(Collectors.toList());
```

#### 3. 默认方法（Default Methods）
接口可以有默认实现方法，这样就可以在不破坏现有实现的情况下向接口添加新方法。
```java
public interface MyInterface {
    default void defaultMethod() {
        System.out.println("This is a default method");
    }
}
```

#### 4. 新的日期和时间API (java.time)
引入了全新的日期和时间API，提供了更好的日期时间操作能力，并解决了旧版 `java.util.Date` 和 `java.util.Calendar` 的诸多问题。
```java
LocalDate today = LocalDate.now();
LocalDate birthday = LocalDate.of(1990, Month.JANUARY, 1);
Period age = Period.between(birthday, today);
System.out.println("Age: " + age.getYears());
```

#### 5. Optional 类
`Optional` 类用于防止出现空指针异常，通过显式地表示值可能为空。
```java
Optional<String> optional = Optional.ofNullable(null);
optional.ifPresent(System.out::println); // 不会打印任何内容，因为值为空
```

#### 6. Nashorn JavaScript 引擎
Nashorn 是一个新的轻量级高性能 JavaScript 引擎，可以在JVM上运行JavaScript代码。
```java
import javax.script.ScriptEngine;
import javax.script.ScriptEngineManager;
import javax.script.ScriptException;

public class NashornExample {
    public static void main(String[] args) throws ScriptException {
        ScriptEngine engine = new ScriptEngineManager().getEngineByName("nashorn");
        engine.eval("print('Hello from Nashorn');");
    }
}
```

### 总结

Java SE 8 引入了许多关键的新特性，如 Lambda 表达式、Stream API、新的日期时间API 等，这些特性极大地增强了开发者使用 Java 编程语言时的效率和便利性。
