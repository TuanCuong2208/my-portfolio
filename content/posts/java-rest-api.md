---
title: "Xây dựng RESTful API với Java Spring Boot và Networking"
date: 2024-12-12
draft: false
---

## REST API là gì?

REST (Representational State Transfer) là một kiến trúc phần mềm cho việc xây dựng web services. RESTful API sử dụng HTTP methods (GET, POST, PUT, DELETE) để thực hiện các thao tác CRUD.

## Các nguyên tắc của REST

1. **Stateless**: Mỗi request độc lập, không lưu trạng thái
2. **Client-Server**: Tách biệt giao diện người dùng và lưu trữ dữ liệu
3. **Cacheable**: Response có thể được cache
4. **Uniform Interface**: Giao diện thống nhất
5. **Layered System**: Hệ thống phân tầng

## Xây dựng REST API với Spring Boot

### Bước 1: Tạo Entity

```java
import javax.persistence.*;

@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String name;
    private String email;
    
    // Constructors, Getters, Setters
    public User() {}
    
    public User(String name, String email) {
        this.name = name;
        this.email = email;
    }
    
    // Getters and Setters...
}
```

### Bước 2: Tạo Repository

```java
import org.springframework.data.jpa.repository.JpaRepository;

public interface UserRepository extends JpaRepository {
    User findByEmail(String email);
}
```

### Bước 3: Tạo REST Controller

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/api/users")
public class UserController {
    
    @Autowired
    private UserRepository userRepository;
    
    // GET: Lấy tất cả users
    @GetMapping
    public List getAllUsers() {
        return userRepository.findAll();
    }
    
    // GET: Lấy user theo ID
    @GetMapping("/{id}")
    public ResponseEntity getUserById(@PathVariable Long id) {
        User user = userRepository.findById(id)
            .orElseThrow(() -> new ResourceNotFoundException("User not found"));
        return ResponseEntity.ok(user);
    }
    
    // POST: Tạo user mới
    @PostMapping
    public User createUser(@RequestBody User user) {
        return userRepository.save(user);
    }
    
    // PUT: Cập nhật user
    @PutMapping("/{id}")
    public ResponseEntity updateUser(
        @PathVariable Long id, 
        @RequestBody User userDetails
    ) {
        User user = userRepository.findById(id)
            .orElseThrow(() -> new ResourceNotFoundException("User not found"));
        
        user.setName(userDetails.getName());
        user.setEmail(userDetails.getEmail());
        
        User updatedUser = userRepository.save(user);
        return ResponseEntity.ok(updatedUser);
    }
    
    // DELETE: Xóa user
    @DeleteMapping("/{id}")
    public ResponseEntity deleteUser(@PathVariable Long id) {
        User user = userRepository.findById(id)
            .orElseThrow(() -> new ResourceNotFoundException("User not found"));
        
        userRepository.delete(user);
        return ResponseEntity.ok().build();
    }
}
```

## Test API với cURL

```bash
# GET tất cả users
curl http://localhost:8080/api/users

# POST tạo user mới
curl -X POST http://localhost:8080/api/users \
  -H "Content-Type: application/json" \
  -d '{"name":"John Doe","email":"john@example.com"}'

# GET user theo ID
curl http://localhost:8080/api/users/1

# PUT cập nhật user
curl -X PUT http://localhost:8080/api/users/1 \
  -H "Content-Type: application/json" \
  -d '{"name":"Jane Doe","email":"jane@example.com"}'

# DELETE xóa user
curl -X DELETE http://localhost:8080/api/users/1
```

## Kết luận

Spring Boot giúp xây dựng REST API nhanh chóng và dễ dàng. Việc hiểu rõ các HTTP methods và RESTful principles là nền tảng quan trọng trong lập trình mạng hiện đại.
