---
title: "Java Streams API - Xử lý dữ liệu hiệu quả"
date: 2024-12-20T00:00:00Z
draft: false
categories: ["Java"]
tags: ["Java", "Streams", "Functional Programming"]
summary: "Khám phá Java Streams API với các phép toán trung gian và cuối cùng để xử lý dữ liệu một cách khai báo."
---

# Java Streams API - Xử lý dữ liệu hiệu quả

Java Streams API, được giới thiệu từ Java 8, cung cấp một cách khai báo để xử lý dữ liệu. Streams cho phép thực hiện các phép toán phức tạp trên collections một cách dễ đọc và hiệu quả.

## Giới thiệu về Streams

Stream là một sequence của các phần tử hỗ trợ các phép toán tổng hợp tuần tự và song song.

```java
import java.util.Arrays;
import java.util.List;
import java.util.stream.Stream;

public class StreamBasics {
    public static void main(String[] args) {
        // Tạo Stream từ Collection
        List<String> names = Arrays.asList("Alice", "Bob", "Charlie", "David");
        Stream<String> nameStream = names.stream();

        // Tạo Stream từ Array
        String[] array = {"Java", "Python", "JavaScript"};
        Stream<String> arrayStream = Arrays.stream(array);

        // Tạo Stream từ các giá trị
        Stream<String> valueStream = Stream.of("A", "B", "C");

        // Tạo Stream vô hạn
        Stream<Integer> infiniteStream = Stream.iterate(1, n -> n + 1);
    }
}
```

## Các phép toán trung gian (Intermediate Operations)

Các phép toán trung gian trả về một Stream mới và có thể được chain lại với nhau.

### filter() - Lọc phần tử

```java
import java.util.Arrays;
import java.util.List;

public class FilterExample {
    public static void main(String[] args) {
        List<String> languages = Arrays.asList("Java", "Python", "JavaScript", "C++", "Ruby");

        languages.stream()
                .filter(lang -> lang.length() > 4)
                .forEach(System.out::println);
        // Output: Python, JavaScript
    }
}
```

### map() - Biến đổi phần tử

```java
import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

public class MapExample {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("alice", "bob", "charlie");

        List<String> upperCaseNames = names.stream()
                .map(String::toUpperCase)
                .collect(Collectors.toList());

        System.out.println(upperCaseNames); // [ALICE, BOB, CHARLIE]
    }
}
```

### sorted() - Sắp xếp

```java
import java.util.Arrays;
import java.util.List;

public class SortedExample {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(3, 1, 4, 1, 5, 9, 2, 6);

        numbers.stream()
                .sorted()
                .forEach(System.out::println);
        // Output: 1, 1, 2, 3, 4, 5, 6, 9
    }
}
```

### distinct() - Loại bỏ trùng lặp

```java
import java.util.Arrays;
import java.util.List;

public class DistinctExample {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 2, 3, 3, 3, 4);

        numbers.stream()
                .distinct()
                .forEach(System.out::println);
        // Output: 1, 2, 3, 4
    }
}
```

## Các phép toán cuối cùng (Terminal Operations)

Các phép toán cuối cùng trả về kết quả không phải là Stream.

### collect() - Thu thập kết quả

```java
import java.util.Arrays;
import java.util.List;
import java.util.Set;
import java.util.stream.Collectors;

public class CollectExample {
    public static void main(String[] args) {
        List<String> languages = Arrays.asList("Java", "Python", "Java", "C++", "Python");

        // Thu thập thành List
        List<String> distinctLanguages = languages.stream()
                .distinct()
                .collect(Collectors.toList());

        // Thu thập thành Set
        Set<String> languageSet = languages.stream()
                .collect(Collectors.toSet());

        // Thu thập thành String
        String joined = languages.stream()
                .distinct()
                .collect(Collectors.joining(", "));

        System.out.println(joined); // Java, Python, C++
    }
}
```

### forEach() - Duyệt qua các phần tử

```java
import java.util.Arrays;
import java.util.List;

public class ForEachExample {
    public static void main(String[] args) {
        List<String> fruits = Arrays.asList("Apple", "Banana", "Orange");

        fruits.stream()
                .map(String::toUpperCase)
                .forEach(System.out::println);
    }
}
```

### reduce() - Thu gọn thành một giá trị

```java
import java.util.Arrays;
import java.util.List;
import java.util.Optional;

public class ReduceExample {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

        // Tính tổng
        Optional<Integer> sum = numbers.stream()
                .reduce((a, b) -> a + b);

        sum.ifPresent(System.out::println); // 15

        // Tính tổng với giá trị khởi tạo
        Integer sumWithInitial = numbers.stream()
                .reduce(10, (a, b) -> a + b);

        System.out.println(sumWithInitial); // 25
    }
}
```

### count(), min(), max()

```java
import java.util.Arrays;
import java.util.List;

public class AggregateExample {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

        long count = numbers.stream().count();
        System.out.println("Count: " + count); // 5

        int max = numbers.stream().max(Integer::compare).orElse(0);
        System.out.println("Max: " + max); // 5

        int min = numbers.stream().min(Integer::compare).orElse(0);
        System.out.println("Min: " + min); // 1
    }
}
```

## Ví dụ thực tế: Xử lý dữ liệu người dùng

```java
import java.util.Arrays;
import java.util.List;
import java.util.Map;
import java.util.stream.Collectors;

class User {
    private String name;
    private int age;
    private String department;

    public User(String name, int age, String department) {
        this.name = name;
        this.age = age;
        this.department = department;
    }

    public String getName() { return name; }
    public int getAge() { return age; }
    public String getDepartment() { return department; }

    @Override
    public String toString() {
        return name + " (" + age + ", " + department + ")";
    }
}

public class UserProcessing {
    public static void main(String[] args) {
        List<User> users = Arrays.asList(
            new User("Alice", 25, "IT"),
            new User("Bob", 30, "HR"),
            new User("Charlie", 28, "IT"),
            new User("David", 35, "Finance"),
            new User("Eve", 22, "IT")
        );

        // Lọc người dùng IT có tuổi > 24
        List<User> itUsers = users.stream()
                .filter(user -> "IT".equals(user.getDepartment()))
                .filter(user -> user.getAge() > 24)
                .collect(Collectors.toList());

        System.out.println("IT Users > 24: " + itUsers);

        // Nhóm theo department
        Map<String, List<User>> usersByDept = users.stream()
                .collect(Collectors.groupingBy(User::getDepartment));

        System.out.println("Users by department: " + usersByDept);

        // Tính tuổi trung bình
        double averageAge = users.stream()
                .mapToInt(User::getAge)
                .average()
                .orElse(0.0);

        System.out.println("Average age: " + averageAge);
    }
}
```

## Parallel Streams

Streams có thể được thực thi song song để tận dụng nhiều core CPU:

```java
import java.util.Arrays;
import java.util.List;

public class ParallelStreamExample {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

        // Sequential processing
        long sequentialStart = System.currentTimeMillis();
        int sequentialSum = numbers.stream()
                .map(n -> {
                    try { Thread.sleep(10); } catch (Exception e) {}
                    return n * 2;
                })
                .reduce(0, Integer::sum);
        long sequentialEnd = System.currentTimeMillis();

        // Parallel processing
        long parallelStart = System.currentTimeMillis();
        int parallelSum = numbers.parallelStream()
                .map(n -> {
                    try { Thread.sleep(10); } catch (Exception e) {}
                    return n * 2;
                })
                .reduce(0, Integer::sum);
        long parallelEnd = System.currentTimeMillis();

        System.out.println("Sequential time: " + (sequentialEnd - sequentialStart) + "ms");
        System.out.println("Parallel time: " + (parallelEnd - parallelStart) + "ms");
    }
}
```

## Lưu ý quan trọng

1. **Streams không thể tái sử dụng**: Một Stream chỉ có thể được consume một lần
2. **Lazy evaluation**: Các phép toán trung gian chỉ được thực thi khi gặp phép toán cuối cùng
3. **Side-effects**: Tránh sử dụng side-effects trong lambda expressions
4. **Performance**: Không phải lúc nào parallel streams cũng nhanh hơn sequential

## Kết luận

Java Streams API cung cấp một cách mạnh mẽ và khai báo để xử lý dữ liệu. Việc kết hợp các phép toán trung gian và cuối cùng cho phép viết code ngắn gọn, dễ đọc và dễ bảo trì.
