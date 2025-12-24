---
title: "Java Collections Framework - Hướng dẫn toàn diện"
date: 2024-12-19T00:00:00Z
draft: false
categories: ["Java"]
tags: ["Java", "Collections", "Data Structures"]
summary: "Khám phá Java Collections Framework với ArrayList, HashMap, HashSet và các cấu trúc dữ liệu quan trọng khác."
---

# Java Collections Framework - Hướng dẫn toàn diện

Java Collections Framework là một trong những phần quan trọng nhất của Java, cung cấp các cấu trúc dữ liệu và thuật toán để lưu trữ và thao tác với dữ liệu một cách hiệu quả.

## ArrayList - Danh sách động

ArrayList là một implementation của List interface, cho phép lưu trữ các phần tử có thể thay đổi kích thước.

```java
import java.util.ArrayList;
import java.util.List;

public class ArrayListExample {
    public static void main(String[] args) {
        List<String> fruits = new ArrayList<>();

        // Thêm phần tử
        fruits.add("Apple");
        fruits.add("Banana");
        fruits.add("Orange");

        // Truy cập phần tử
        System.out.println(fruits.get(0)); // Apple

        // Duyệt qua danh sách
        for (String fruit : fruits) {
            System.out.println(fruit);
        }
    }
}
```

## HashMap - Bảng băm

HashMap lưu trữ dữ liệu dưới dạng key-value pairs, cho phép truy cập nhanh chóng theo key.

```java
import java.util.HashMap;
import java.util.Map;

public class HashMapExample {
    public static void main(String[] args) {
        Map<String, Integer> studentGrades = new HashMap<>();

        // Thêm dữ liệu
        studentGrades.put("Alice", 95);
        studentGrades.put("Bob", 87);
        studentGrades.put("Charlie", 92);

        // Truy cập dữ liệu
        System.out.println("Alice's grade: " + studentGrades.get("Alice"));

        // Duyệt qua HashMap
        for (Map.Entry<String, Integer> entry : studentGrades.entrySet()) {
            System.out.println(entry.getKey() + ": " + entry.getValue());
        }
    }
}
```

## HashSet - Tập hợp không trùng lặp

HashSet lưu trữ các phần tử duy nhất, không cho phép phần tử trùng lặp.

```java
import java.util.HashSet;
import java.util.Set;

public class HashSetExample {
    public static void main(String[] args) {
        Set<String> uniqueWords = new HashSet<>();

        uniqueWords.add("Java");
        uniqueWords.add("Python");
        uniqueWords.add("Java"); // Sẽ bị bỏ qua vì trùng lặp

        System.out.println("Size: " + uniqueWords.size()); // 2
        System.out.println(uniqueWords); // [Java, Python]
    }
}
```

## LinkedList - Danh sách liên kết

LinkedList là implementation của List và Deque interface, phù hợp cho việc thêm/xóa phần tử ở đầu và cuối danh sách.

```java
import java.util.LinkedList;
import java.util.Queue;

public class LinkedListExample {
    public static void main(String[] args) {
        Queue<String> queue = new LinkedList<>();

        // Thêm phần tử vào cuối hàng đợi
        queue.offer("First");
        queue.offer("Second");
        queue.offer("Third");

        // Lấy phần tử đầu tiên
        System.out.println(queue.poll()); // First
        System.out.println(queue.poll()); // Second
    }
}
```

## TreeSet - Tập hợp có thứ tự

TreeSet lưu trữ các phần tử theo thứ tự tự nhiên hoặc theo Comparator được cung cấp.

```java
import java.util.TreeSet;

public class TreeSetExample {
    public static void main(String[] args) {
        TreeSet<Integer> numbers = new TreeSet<>();

        numbers.add(5);
        numbers.add(2);
        numbers.add(8);
        numbers.add(1);

        System.out.println(numbers); // [1, 2, 5, 8]

        // Các phương thức hữu ích
        System.out.println("First: " + numbers.first());
        System.out.println("Last: " + numbers.last());
        System.out.println("Lower than 5: " + numbers.lower(5));
    }
}
```

## Khi nào sử dụng Collection nào?

- **ArrayList**: Khi cần truy cập ngẫu nhiên nhanh và thêm/xóa ở cuối danh sách
- **LinkedList**: Khi cần thêm/xóa ở đầu/cuối danh sách thường xuyên
- **HashMap**: Khi cần ánh xạ key-value với truy cập nhanh
- **HashSet**: Khi cần lưu trữ các phần tử duy nhất không quan trọng thứ tự
- **TreeSet**: Khi cần tập hợp có thứ tự

## Generics trong Collections

Collections Framework sử dụng Generics để đảm bảo type safety:

```java
// Không sử dụng Generics (không khuyến khích)
List list = new ArrayList();
list.add("String");
list.add(123); // Có thể gây lỗi runtime

// Sử dụng Generics (khuyến khích)
List<String> stringList = new ArrayList<>();
stringList.add("String");
// stringList.add(123); // Lỗi compile-time
```

## Kết luận

Java Collections Framework cung cấp các công cụ mạnh mẽ để làm việc với dữ liệu. Việc lựa chọn Collection phù hợp có thể ảnh hưởng đáng kể đến hiệu suất ứng dụng của bạn.
