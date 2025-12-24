---
title: ""
date: 2025-12-22T00:00:00Z
draft: false
categories: ["Java"]
tags: ["Spring Boot", "Java", "Web Development", "Framework"]
summary: "Hướng dẫn tạo ứng dụng Spring Boot đầu tiên với REST API, Dependency Injection và Auto-configuration."
---



Spring Boot là một framework giúp phát triển ứng dụng Spring một cách nhanh chóng và dễ dàng. Nó loại bỏ hầu hết các cấu hình phức tạp của Spring truyền thống.

## Tại sao Spring Boot?

Spring Boot cung cấp:

- **Auto-configuration**: Tự động cấu hình dựa trên dependencies
- **Standalone applications**: Không cần external application server
- **Production-ready features**: Metrics, health checks, externalized configuration
- **No code generation**: Không cần generate code
- **Opinionated defaults**: Cấu hình mặc định hợp lý

## Tạo dự án Spring Boot đầu tiên

### Sử dụng Spring Initializr

Truy cập [https://start.spring.io/](https://start.spring.io/) và cấu hình:

- **Project**: Maven Project
- **Language**: Java
- **Spring Boot**: 3.2.0 (hoặc phiên bản mới nhất)
- **Group**: com.example
- **Artifact**: demo
- **Name**: demo
- **Description**: Demo project for Spring Boot
- **Package name**: com.example.demo
- **Packaging**: Jar
- **Java**: 17

**Dependencies**:

- Spring Web
- Spring Boot DevTools

### Cấu trúc dự án

```
src/
├── main/
│   ├── java/
│   │   └── com/
│   │       └── example/
│   │           └── demo/
│   │               ├── DemoApplication.java
│   │               └── controller/
│   │                   └── HelloController.java
│   └── resources/
│       ├── application.properties
│       ├── static/
│       └── templates/
└── test/
    └── java/
        └── com/
            └── example/
                └── demo/
                    └── DemoApplicationTests.java
```

## Main Application Class

```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }

}
```

`@SpringBootApplication` là một composite annotation bao gồm:

- `@Configuration`: Đánh dấu class là source của bean definitions
- `@EnableAutoConfiguration`: Cho phép auto-configuration
- `@ComponentScan`: Quét các components trong package

## Tạo REST Controller

```java
package com.example.demo.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    @GetMapping("/")
    public String home() {
        return "Hello World!";
    }

    @GetMapping("/hello/{name}")
    public String hello(@PathVariable String name) {
        return "Hello " + name + "!";
    }

    @GetMapping("/greeting")
    public String greeting(@RequestParam(value = "name", defaultValue = "World") String name) {
        return "Hello " + name + "!";
    }
}
```

## Dependency Injection

Spring Boot sử dụng Dependency Injection để quản lý dependencies.

```java
package com.example.demo.service;

import org.springframework.stereotype.Service;

@Service
public class GreetingService {

    public String getGreeting(String name) {
        return "Hello, " + name + "!";
    }
}
```

```java
package com.example.demo.controller;

import com.example.demo.service.GreetingService;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class GreetingController {

    private final GreetingService greetingService;

    // Constructor injection (khuyến khích)
    public GreetingController(GreetingService greetingService) {
        this.greetingService = greetingService;
    }

    @GetMapping("/greet/{name}")
    public String greet(@PathVariable String name) {
        return greetingService.getGreeting(name);
    }
}
```

## Configuration Properties

Spring Boot cho phép externalize configuration.

```java
package com.example.demo.config;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@Component
@ConfigurationProperties(prefix = "app")
public class AppProperties {

    private String name = "Demo App";
    private String version = "1.0.0";

    // Getters and setters
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }

    public String getVersion() { return version; }
    public void setVersion(String version) { this.version = version; }
}
```

Trong `application.properties`:

```properties
app.name=My Awesome App
app.version=2.0.0
```

## Data Access với Spring Data JPA

Thêm dependencies:

- Spring Data JPA
- H2 Database (cho demo)

```java
package com.example.demo.entity;

import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;

@Entity
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private String email;

    // Constructors, getters, setters
    public User() {}

    public User(String name, String email) {
        this.name = name;
        this.email = email;
    }

    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }

    public String getName() { return name; }
    public void setName(String name) { this.name = name; }

    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }
}
```

```java
package com.example.demo.repository;

import com.example.demo.entity.User;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    // Spring Data JPA sẽ tự động implement các methods cơ bản
}
```

```java
package com.example.demo.controller;

import com.example.demo.entity.User;
import com.example.demo.repository.UserRepository;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/api/users")
public class UserController {

    private final UserRepository userRepository;

    public UserController(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    @GetMapping
    public List<User> getAllUsers() {
        return userRepository.findAll();
    }

    @GetMapping("/{id}")
    public User getUserById(@PathVariable Long id) {
        return userRepository.findById(id).orElseThrow();
    }

    @PostMapping
    public User createUser(@RequestBody User user) {
        return userRepository.save(user);
    }

    @PutMapping("/{id}")
    public User updateUser(@PathVariable Long id, @RequestBody User user) {
        User existingUser = userRepository.findById(id).orElseThrow();
        existingUser.setName(user.getName());
        existingUser.setEmail(user.getEmail());
        return userRepository.save(existingUser);
    }

    @DeleteMapping("/{id}")
    public void deleteUser(@PathVariable Long id) {
        userRepository.deleteById(id);
    }
}
```

## Exception Handling

```java
package com.example.demo.exception;

public class UserNotFoundException extends RuntimeException {

    public UserNotFoundException(Long id) {
        super("User not found with id: " + id);
    }
}
```

```java
package com.example.demo.controller;

import com.example.demo.exception.UserNotFoundException;
import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.ResponseStatus;
import org.springframework.web.bind.annotation.RestControllerAdvice;

@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(UserNotFoundException.class)
    @ResponseStatus(HttpStatus.NOT_FOUND)
    public ErrorResponse handleUserNotFoundException(UserNotFoundException ex) {
        return new ErrorResponse(ex.getMessage());
    }

    @ExceptionHandler(Exception.class)
    @ResponseStatus(HttpStatus.INTERNAL_SERVER_ERROR)
    public ErrorResponse handleGenericException(Exception ex) {
        return new ErrorResponse("An unexpected error occurred");
    }
}

class ErrorResponse {
    private String message;

    public ErrorResponse(String message) {
        this.message = message;
    }

    public String getMessage() { return message; }
    public void setMessage(String message) { this.message = message; }
}
```

Cập nhật UserController:

```java
@GetMapping("/{id}")
public User getUserById(@PathVariable Long id) {
    return userRepository.findById(id)
            .orElseThrow(() -> new UserNotFoundException(id));
}
```

## Validation

Thêm dependency: Spring Boot Starter Validation

```java
package com.example.demo.entity;

import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import jakarta.validation.constraints.Email;
import jakarta.validation.constraints.NotBlank;

@Entity
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @NotBlank(message = "Name is required")
    private String name;

    @NotBlank(message = "Email is required")
    @Email(message = "Email should be valid")
    private String email;

    // Constructors, getters, setters
}
```

Cập nhật Controller:

```java
@PostMapping
public User createUser(@Valid @RequestBody User user) {
    return userRepository.save(user);
}
```

## Testing

```java
package com.example.demo;

import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class DemoApplicationTests {

    @Test
    void contextLoads() {
    }

}
```

```java
package com.example.demo.controller;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.test.web.servlet.MockMvc;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@WebMvcTest(HelloController.class)
public class HelloControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    public void testHome() throws Exception {
        mockMvc.perform(get("/"))
                .andExpect(status().isOk())
                .andExpect(content().string("Hello World!"));
    }
}
```

## Application Properties

```properties
# Server configuration
server.port=8080

# Database configuration (H2)
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=password
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect

# JPA/Hibernate properties
spring.jpa.hibernate.ddl-auto=create-drop
spring.jpa.show-sql=true

# H2 Console (for development)
spring.h2.console.enabled=true

# Logging
logging.level.com.example.demo=DEBUG

# Application specific
app.name=Spring Boot Demo
app.version=1.0.0
```

## Chạy ứng dụng

```bash
# Chạy từ command line
./mvnw spring-boot:run

# Hoặc với Maven
mvn spring-boot:run

# Hoặc chạy JAR file
java -jar target/demo-0.0.1-SNAPSHOT.jar
```

## Production Deployment

Spring Boot có thể chạy như một standalone JAR:

```bash
# Build JAR
./mvnw clean package

# Chạy JAR
java -jar target/demo-0.0.1-SNAPSHOT.jar
```

## Actuator cho Monitoring

Thêm dependency: Spring Boot Actuator

```properties
# Actuator endpoints
management.endpoints.web.exposure.include=health,info,metrics
management.endpoint.health.show-details=when-authorized
```

Truy cập:

- Health: http://localhost:8080/actuator/health
- Info: http://localhost:8080/actuator/info
- Metrics: http://localhost:8080/actuator/metrics

## Kết luận

Spring Boot giúp phát triển ứng dụng Java web một cách nhanh chóng với ít cấu hình. Nó cung cấp auto-configuration, embedded servers và production-ready features. Đây là nền tảng tuyệt vời để xây dựng microservices và ứng dụng doanh nghiệp.
