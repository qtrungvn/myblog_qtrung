---
title: "Java Generics - Type Safety và Code Reusability"
date: 2024-12-21T00:00:00Z
draft: false
categories: ["Java"]
tags: ["Java", "Generics", "Type Safety"]
summary: "Khám phá sức mạnh của Java Generics với Generic Classes, Methods và Wildcards để viết code an toàn và tái sử dụng."
---

# Java Generics - Type Safety và Code Reusability

Java Generics, được giới thiệu từ Java 5, cho phép viết code có thể hoạt động với nhiều kiểu dữ liệu khác nhau một cách type-safe. Generics giúp phát hiện lỗi ở compile-time thay vì runtime.

## Tại sao cần Generics?

Trước khi có Generics, chúng ta thường sử dụng Object type cho các cấu trúc dữ liệu tổng quát:

```java
// Không sử dụng Generics - không type-safe
List list = new ArrayList();
list.add("Hello");
list.add(123); // Có thể thêm bất kỳ kiểu nào

String str = (String) list.get(0); // Cần cast
Integer num = (Integer) list.get(1); // ClassCastException tiềm ẩn
```

Với Generics, code trở nên type-safe và dễ đọc hơn:

```java
// Sử dụng Generics - type-safe
List<String> list = new ArrayList<>();
list.add("Hello");
// list.add(123); // Lỗi compile-time

String str = list.get(0); // Không cần cast
```

## Generic Classes

Generic classes cho phép định nghĩa class với type parameters.

```java
public class Box<T> {
    private T value;

    public Box(T value) {
        this.value = value;
    }

    public T getValue() {
        return value;
    }

    public void setValue(T value) {
        this.value = value;
    }
}

// Sử dụng
Box<String> stringBox = new Box<>("Hello World");
Box<Integer> intBox = new Box<>(42);

String str = stringBox.getValue(); // Không cần cast
Integer num = intBox.getValue(); // Không cần cast
```

### Ví dụ thực tế: Pair Class

```java
public class Pair<K, V> {
    private K key;
    private V value;

    public Pair(K key, V value) {
        this.key = key;
        this.value = value;
    }

    public K getKey() { return key; }
    public V getValue() { return value; }

    @Override
    public String toString() {
        return "(" + key + ", " + value + ")";
    }
}

// Sử dụng
Pair<String, Integer> student = new Pair<>("Alice", 95);
Pair<String, String> mapping = new Pair<>("key", "value");
```

## Generic Methods

Generic methods cho phép định nghĩa methods với type parameters riêng biệt.

```java
public class Utility {

    // Generic method
    public static <T> void printArray(T[] array) {
        for (T element : array) {
            System.out.print(element + " ");
        }
        System.out.println();
    }

    // Generic method với multiple type parameters
    public static <K, V> void printMap(Map<K, V> map) {
        for (Map.Entry<K, V> entry : map.entrySet()) {
            System.out.println(entry.getKey() + " -> " + entry.getValue());
        }
    }

    // Generic method với bounded type parameter
    public static <T extends Comparable<T>> T findMax(T[] array) {
        if (array == null || array.length == 0) {
            return null;
        }

        T max = array[0];
        for (int i = 1; i < array.length; i++) {
            if (array[i].compareTo(max) > 0) {
                max = array[i];
            }
        }
        return max;
    }
}

// Sử dụng
Integer[] numbers = {1, 3, 2, 5, 4};
String[] strings = {"Apple", "Banana", "Cherry"};

Utility.printArray(numbers); // 1 3 2 5 4
Utility.printArray(strings); // Apple Banana Cherry

Integer maxNumber = Utility.findMax(numbers);
String maxString = Utility.findMax(strings);

System.out.println("Max number: " + maxNumber); // 5
System.out.println("Max string: " + maxString); // Cherry
```

## Bounded Type Parameters

Bounded type parameters giới hạn các kiểu có thể được sử dụng làm type arguments.

```java
// Upper bounded wildcard
public static <T extends Number> double sum(List<T> numbers) {
    double total = 0.0;
    for (T number : numbers) {
        total += number.doubleValue();
    }
    return total;
}

// Multiple bounds
public interface SerializableComparable<T> extends Serializable, Comparable<T> {}

public static <T extends Serializable & Comparable<T>> T findMax(List<T> list) {
    return list.stream().max(Comparable::compareTo).orElse(null);
}
```

## Wildcards

Wildcards (?) cho phép linh hoạt hơn trong việc sử dụng generics.

### Unbounded Wildcard

```java
public static void printList(List<?> list) {
    for (Object item : list) {
        System.out.print(item + " ");
    }
    System.out.println();
}

// Sử dụng
List<String> strings = Arrays.asList("A", "B", "C");
List<Integer> numbers = Arrays.asList(1, 2, 3);

printList(strings); // A B C
printList(numbers); // 1 2 3
```

### Upper Bounded Wildcard

```java
public static double sumOfList(List<? extends Number> list) {
    double sum = 0.0;
    for (Number number : list) {
        sum += number.doubleValue();
    }
    return sum;
}

// Sử dụng
List<Integer> ints = Arrays.asList(1, 2, 3);
List<Double> doubles = Arrays.asList(1.0, 2.0, 3.0);

System.out.println(sumOfList(ints)); // 6.0
System.out.println(sumOfList(doubles)); // 6.0
```

### Lower Bounded Wildcard

```java
public static void addNumbers(List<? super Integer> list) {
    for (int i = 1; i <= 5; i++) {
        list.add(i);
    }
}

// Sử dụng
List<Integer> ints = new ArrayList<>();
List<Number> numbers = new ArrayList<>();
List<Object> objects = new ArrayList<>();

addNumbers(ints);
addNumbers(numbers);
addNumbers(objects);
```

## Generic Interfaces

```java
public interface Processor<T> {
    T process(T input);
}

public class StringProcessor implements Processor<String> {
    @Override
    public String process(String input) {
        return input.toUpperCase();
    }
}

public class NumberProcessor implements Processor<Integer> {
    @Override
    public Integer process(Integer input) {
        return input * 2;
    }
}

// Generic interface với multiple type parameters
public interface Mapper<K, V> {
    V map(K key);
}

public class StringToLengthMapper implements Mapper<String, Integer> {
    @Override
    public Integer map(String key) {
        return key.length();
    }
}
```

## Type Erasure

Java sử dụng type erasure để duy trì backward compatibility. Điều này có nghĩa là generic type information bị remove tại runtime.

```java
public class TypeErasureExample {
    public static void main(String[] args) {
        List<String> strings = new ArrayList<>();
        List<Integer> numbers = new ArrayList<>();

        // Tại runtime, cả hai đều là List
        System.out.println(strings.getClass() == numbers.getClass()); // true

        // Không thể kiểm tra generic type tại runtime
        // if (strings instanceof List<String>) { } // Lỗi compile
        if (strings instanceof List<?>) { // OK
            System.out.println("Is List");
        }
    }
}
```

## Raw Types (không khuyến khích)

Raw types là generic types không có type parameters. Chúng tồn tại để duy trì compatibility với code cũ.

```java
// Raw type - không khuyến khích
List rawList = new ArrayList();
rawList.add("String");
rawList.add(123);

// Generic type - khuyến khích
List<String> genericList = new ArrayList<>();
genericList.add("String");
// genericList.add(123); // Lỗi compile
```

## Best Practices

1. **Luôn sử dụng generics**: Tránh raw types
2. **Đặt tên type parameters có ý nghĩa**: T, E (Element), K (Key), V (Value), etc.
3. **Sử dụng bounded wildcards khi phù hợp**: Giúp API linh hoạt hơn
4. **Tránh heap pollution**: Không mix raw types và generic types
5. **Document generic methods**: Giải thích type parameters

## Ví dụ thực tế: Generic Repository

```java
import java.util.*;

public interface Repository<T, ID> {
    Optional<T> findById(ID id);
    List<T> findAll();
    T save(T entity);
    void deleteById(ID id);
}

public class InMemoryRepository<T, ID> implements Repository<T, ID> {
    private final Map<ID, T> storage = new HashMap<>();
    private final Function<T, ID> idExtractor;

    public InMemoryRepository(Function<T, ID> idExtractor) {
        this.idExtractor = idExtractor;
    }

    @Override
    public Optional<T> findById(ID id) {
        return Optional.ofNullable(storage.get(id));
    }

    @Override
    public List<T> findAll() {
        return new ArrayList<>(storage.values());
    }

    @Override
    public T save(T entity) {
        ID id = idExtractor.apply(entity);
        storage.put(id, entity);
        return entity;
    }

    @Override
    public void deleteById(ID id) {
        storage.remove(id);
    }
}

// Sử dụng
class User {
    private final String id;
    private final String name;

    public User(String id, String name) {
        this.id = id;
        this.name = name;
    }

    public String getId() { return id; }
    public String getName() { return name; }
}

public class RepositoryExample {
    public static void main(String[] args) {
        Repository<User, String> userRepo = new InMemoryRepository<>(User::getId);

        User user1 = new User("1", "Alice");
        User user2 = new User("2", "Bob");

        userRepo.save(user1);
        userRepo.save(user2);

        Optional<User> found = userRepo.findById("1");
        found.ifPresent(user -> System.out.println("Found: " + user.getName()));

        List<User> allUsers = userRepo.findAll();
        System.out.println("Total users: " + allUsers.size());
    }
}
```

## Kết luận

Java Generics là một tính năng mạnh mẽ giúp viết code type-safe, reusable và maintainable. Việc hiểu và sử dụng generics đúng cách sẽ giúp bạn viết code Java chất lượng cao hơn.
