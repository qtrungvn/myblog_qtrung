---
title: ""
date: 2025-12-23T00:00:00Z
draft: false
categories: ["Java"]
tags: ["Java", "Multithreading", "Concurrency", "Performance"]
summary: "Khám phá Java Multithreading với Thread, Runnable, synchronized, và các công cụ concurrency hiện đại."
---

Multithreading cho phép chương trình thực hiện nhiều tác vụ đồng thời, tăng hiệu suất và responsiveness. Java cung cấp các API mạnh mẽ để làm việc với threads.

## Cơ bản về Thread

Thread là đơn vị thực thi nhỏ nhất trong một process. Java cung cấp hai cách tạo thread:

### 1. Extend Thread class

```java
public class MyThread extends Thread {

    @Override
    public void run() {
        System.out.println("Thread đang chạy: " + Thread.currentThread().getName());
        for (int i = 0; i < 5; i++) {
            System.out.println("Count: " + i);
            try {
                Thread.sleep(1000); // Ngủ 1 giây
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

public class ThreadExample {
    public static void main(String[] args) {
        MyThread thread1 = new MyThread();
        MyThread thread2 = new MyThread();

        thread1.start(); // Khởi động thread
        thread2.start();

        System.out.println("Main thread kết thúc");
    }
}
```

### 2. Implement Runnable interface (khuyến khích)

```java
public class MyRunnable implements Runnable {

    @Override
    public void run() {
        System.out.println("Runnable đang chạy: " + Thread.currentThread().getName());
        for (int i = 0; i < 5; i++) {
            System.out.println("Task " + i + " completed");
            try {
                Thread.sleep(500);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt(); // Khôi phục trạng thái interrupt
            }
        }
    }
}

public class RunnableExample {
    public static void main(String[] args) {
        Thread thread1 = new Thread(new MyRunnable(), "Thread-1");
        Thread thread2 = new Thread(new MyRunnable(), "Thread-2");

        thread1.start();
        thread2.start();

        // Chờ thread kết thúc
        try {
            thread1.join();
            thread2.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println("Tất cả threads đã kết thúc");
    }
}
```

## Thread Lifecycle

Một thread có các trạng thái:

1. **NEW**: Thread được tạo nhưng chưa start
2. **RUNNABLE**: Thread đang chạy hoặc sẵn sàng chạy
3. **BLOCKED**: Thread đang chờ lock
4. **WAITING**: Thread đang chờ một thread khác
5. **TIMED_WAITING**: Thread đang chờ với timeout
6. **TERMINATED**: Thread đã kết thúc

```java
public class ThreadLifecycleDemo {
    public static void main(String[] args) {
        Thread thread = new Thread(() -> {
            System.out.println("Thread state: " + Thread.currentThread().getState());
            try {
                Thread.sleep(2000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });

        System.out.println("Before start: " + thread.getState()); // NEW
        thread.start();
        System.out.println("After start: " + thread.getState()); // RUNNABLE

        try {
            Thread.sleep(100);
            System.out.println("During sleep: " + thread.getState()); // TIMED_WAITING
            thread.join();
            System.out.println("After join: " + thread.getState()); // TERMINATED
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

## Synchronization

Synchronization đảm bảo chỉ một thread có thể truy cập vào một tài nguyên tại một thời điểm.

### synchronized methods

```java
public class Counter {
    private int count = 0;

    public synchronized void increment() {
        count++;
    }

    public synchronized int getCount() {
        return count;
    }
}

public class SynchronizationExample {
    public static void main(String[] args) throws InterruptedException {
        Counter counter = new Counter();

        Runnable task = () -> {
            for (int i = 0; i < 1000; i++) {
                counter.increment();
            }
        };

        Thread thread1 = new Thread(task);
        Thread thread2 = new Thread(task);

        thread1.start();
        thread2.start();

        thread1.join();
        thread2.join();

        System.out.println("Final count: " + counter.getCount()); // 2000
    }
}
```

### synchronized blocks

```java
public class SynchronizedBlockExample {
    private int count = 0;
    private final Object lock = new Object();

    public void increment() {
        synchronized (lock) {
            count++;
        }
    }

    public int getCount() {
        synchronized (lock) {
            return count;
        }
    }
}
```

## Thread Communication

### wait(), notify(), notifyAll()

```java
public class ProducerConsumer {
    private final Object lock = new Object();
    private boolean itemAvailable = false;

    public void produce() throws InterruptedException {
        synchronized (lock) {
            while (itemAvailable) {
                lock.wait(); // Chờ consumer tiêu thụ
            }
            System.out.println("Sản xuất item");
            itemAvailable = true;
            lock.notify(); // Thông báo cho consumer
        }
    }

    public void consume() throws InterruptedException {
        synchronized (lock) {
            while (!itemAvailable) {
                lock.wait(); // Chờ producer sản xuất
            }
            System.out.println("Tiêu thụ item");
            itemAvailable = false;
            lock.notify(); // Thông báo cho producer
        }
    }
}

public class ProducerConsumerDemo {
    public static void main(String[] args) {
        ProducerConsumer pc = new ProducerConsumer();

        Thread producer = new Thread(() -> {
            try {
                for (int i = 0; i < 5; i++) {
                    pc.produce();
                    Thread.sleep(1000);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });

        Thread consumer = new Thread(() -> {
            try {
                for (int i = 0; i < 5; i++) {
                    pc.consume();
                    Thread.sleep(1500);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });

        producer.start();
        consumer.start();
    }
}
```

## Volatile Keyword

Volatile đảm bảo visibility của biến giữa các threads.

```java
public class VolatileExample {
    private volatile boolean running = true;

    public void stop() {
        running = false;
    }

    public void run() {
        while (running) {
            // Do some work
            System.out.println("Running...");
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }
        System.out.println("Stopped");
    }

    public static void main(String[] args) throws InterruptedException {
        VolatileExample example = new VolatileExample();

        Thread worker = new Thread(example::run);
        worker.start();

        Thread.sleep(3000);
        example.stop();
        worker.join();
    }
}
```

## Thread Pool với ExecutorService

Thread pool quản lý lifecycle của threads hiệu quả hơn.

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

public class ThreadPoolExample {
    public static void main(String[] args) {
        // Tạo thread pool với 3 threads
        ExecutorService executor = Executors.newFixedThreadPool(3);

        for (int i = 0; i < 5; i++) {
            final int taskId = i;
            executor.submit(() -> {
                System.out.println("Task " + taskId + " executed by " + Thread.currentThread().getName());
                try {
                    Thread.sleep(2000);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            });
        }

        // Shutdown executor
        executor.shutdown();
        try {
            // Chờ tất cả tasks hoàn thành hoặc timeout
            if (!executor.awaitTermination(10, TimeUnit.SECONDS)) {
                executor.shutdownNow();
            }
        } catch (InterruptedException e) {
            executor.shutdownNow();
        }
    }
}
```

## Callable và Future

Callable trả về kết quả, Future để lấy kết quả.

```java
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

public class CallableFutureExample {
    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(2);

        Callable<Integer> task = () -> {
            Thread.sleep(2000);
            return 42;
        };

        Future<Integer> future = executor.submit(task);

        System.out.println("Task submitted, doing other work...");

        try {
            // Lấy kết quả (blocking)
            Integer result = future.get();
            System.out.println("Result: " + result);
        } catch (InterruptedException | ExecutionException e) {
            e.printStackTrace();
        }

        executor.shutdown();
    }
}
```

## Atomic Variables

Atomic classes cung cấp thread-safe operations mà không cần synchronization.

```java
import java.util.concurrent.atomic.AtomicInteger;

public class AtomicExample {
    private static AtomicInteger counter = new AtomicInteger(0);

    public static void main(String[] args) throws InterruptedException {
        Runnable task = () -> {
            for (int i = 0; i < 1000; i++) {
                counter.incrementAndGet();
            }
        };

        Thread thread1 = new Thread(task);
        Thread thread2 = new Thread(task);

        thread1.start();
        thread2.start();

        thread1.join();
        thread2.join();

        System.out.println("Final counter: " + counter.get()); // 2000
    }
}
```

## Concurrent Collections

Java cung cấp các collection thread-safe.

```java
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.ConcurrentLinkedQueue;

public class ConcurrentCollectionsExample {
    public static void main(String[] args) throws InterruptedException {
        // ConcurrentHashMap - thread-safe HashMap
        ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();

        // ConcurrentLinkedQueue - thread-safe Queue
        ConcurrentLinkedQueue<String> queue = new ConcurrentLinkedQueue<>();

        Runnable producer = () -> {
            for (int i = 0; i < 10; i++) {
                String item = "Item-" + i;
                queue.offer(item);
                map.put(item, i);
                System.out.println("Produced: " + item);
            }
        };

        Runnable consumer = () -> {
            for (int i = 0; i < 10; i++) {
                String item = queue.poll();
                if (item != null) {
                    System.out.println("Consumed: " + item + " -> " + map.get(item));
                }
            }
        };

        Thread producerThread = new Thread(producer);
        Thread consumerThread = new Thread(consumer);

        producerThread.start();
        consumerThread.start();

        producerThread.join();
        consumerThread.join();
    }
}
```

## Deadlock

Deadlock xảy ra khi hai hoặc nhiều threads chờ lẫn nhau.

```java
public class DeadlockExample {
    private static final Object lock1 = new Object();
    private static final Object lock2 = new Object();

    public static void main(String[] args) {
        Thread thread1 = new Thread(() -> {
            synchronized (lock1) {
                System.out.println("Thread 1: Holding lock 1");
                try { Thread.sleep(100); } catch (InterruptedException e) {}

                System.out.println("Thread 1: Waiting for lock 2");
                synchronized (lock2) {
                    System.out.println("Thread 1: Holding lock 1 & 2");
                }
            }
        });

        Thread thread2 = new Thread(() -> {
            synchronized (lock2) {
                System.out.println("Thread 2: Holding lock 2");
                try { Thread.sleep(100); } catch (InterruptedException e) {}

                System.out.println("Thread 2: Waiting for lock 1");
                synchronized (lock1) {
                    System.out.println("Thread 2: Holding lock 1 & 2");
                }
            }
        });

        thread1.start();
        thread2.start();
    }
}
```

## Best Practices

1. **Tránh nested locks**: Giảm nguy cơ deadlock
2. **Sử dụng high-level concurrency utilities**: ExecutorService, concurrent collections
3. **Hiểu rõ happens-before relationship**: Đảm bảo memory visibility
4. **Sử dụng immutable objects**: Khi có thể
5. **Test concurrency code**: Sử dụng stress testing
6. **Document thread-safety**: Rõ ràng về thread-safety guarantees

## Ví dụ thực tế: Web Crawler

```java
import java.util.concurrent.*;
import java.util.Set;
import java.util.concurrent.atomic.AtomicInteger;

public class WebCrawler {
    private final ExecutorService executor;
    private final ConcurrentHashMap<String, Boolean> visited;
    private final BlockingQueue<String> queue;
    private final AtomicInteger activeTasks;

    public WebCrawler(int numThreads) {
        this.executor = Executors.newFixedThreadPool(numThreads);
        this.visited = new ConcurrentHashMap<>();
        this.queue = new LinkedBlockingQueue<>();
        this.activeTasks = new AtomicInteger(0);
    }

    public void crawl(String startUrl) {
        queue.offer(startUrl);

        for (int i = 0; i < 10; i++) {
            executor.submit(this::crawlTask);
        }
    }

    private void crawlTask() {
        activeTasks.incrementAndGet();

        try {
            while (!queue.isEmpty() || activeTasks.get() > 1) {
                String url = queue.poll(1, TimeUnit.SECONDS);

                if (url == null) continue;

                if (visited.putIfAbsent(url, true) != null) continue;

                System.out.println("Crawling: " + url);

                // Simulate crawling
                Thread.sleep(1000);

                // Add more URLs (simplified)
                if (visited.size() < 50) {
                    queue.offer(url + "/page" + visited.size());
                }
            }
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        } finally {
            activeTasks.decrementAndGet();
        }
    }

    public void shutdown() {
        executor.shutdown();
        try {
            executor.awaitTermination(10, TimeUnit.SECONDS);
        } catch (InterruptedException e) {
            executor.shutdownNow();
        }
    }

    public static void main(String[] args) {
        WebCrawler crawler = new WebCrawler(5);
        crawler.crawl("http://example.com");
        crawler.shutdown();
    }
}
```

## Kết luận

Multithreading là một chủ đề phức tạp nhưng mạnh mẽ trong Java. Việc hiểu rõ các khái niệm cơ bản như synchronization, thread communication, và các công cụ concurrency sẽ giúp bạn viết code đa luồng hiệu quả và an toàn.
