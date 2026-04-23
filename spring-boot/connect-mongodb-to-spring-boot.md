Learn how to integrate MongoDB with Spring Boot to store and retrieve data easily using Spring Data MongoDB.

---

## 1. Prerequisites

* Java 11 or higher
* Maven or Gradle
* MongoDB installed locally or a MongoDB Atlas cluster
* Basic knowledge of Spring Boot
---

## 2. Add Dependencies

Add the following dependency in your **pom.xml**
:

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-mongodb</artifactId>
</dependency>
```

---

## 3. Configure **application.properties**


For a **local MongoDB** instance:

```
spring.data.mongodb.uri=mongodb://localhost:27017/mydatabase
```

For a **MongoDB Atlas** cluster:

```
spring.data.mongodb.uri=mongodb+srv://<username>:<password>@<cluster-url>/mydatabase?retryWrites=true&w=majority
```

---

## 4. Create Model and Repository

**Model (User.java):**

```java
package com.example.demo.model;

import org.springframework.data.annotation.Id;
import org.springframework.data.mongodb.core.mapping.Document;

@Document(collection = "users")
public class User {
    @Id
    private String id;
    private String name;
    private String email;

    // Getters and setters
    public String getId() { return id; }
    public void setId(String id) { this.id = id; }

    public String getName() { return name; }
    public void setName(String name) { this.name = name; }

    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }
}
```

**Repository (UserRepository.java):**

```java
package com.example.demo.repository;

import com.example.demo.model.User;
import org.springframework.data.mongodb.repository.MongoRepository;

public interface UserRepository extends MongoRepository<User, String> {
    User findByEmail(String email);
}
```

---

## 5. Create a REST Controller

**Controller (UserController.java):**

```java
package com.example.demo.controller;

import com.example.demo.model.User;
import com.example.demo.repository.UserRepository;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/api/users")
public class UserController {

    private final UserRepository repo;

    public UserController(UserRepository repo) {
        this.repo = repo;
    }

    @GetMapping
    public List<User> getAll() {
        return repo.findAll();
    }

    @PostMapping
    public User create(@RequestBody User user) {
        return repo.save(user);
    }

    @GetMapping("/{email}")
    public User getByEmail(@PathVariable String email) {
        return repo.findByEmail(email);
    }
}
```

---

## 6. Run and Test

Start MongoDB locally:

```bash
mongod
```

Then run the Spring Boot app:

```bash
mvn spring-boot:run
```

Test endpoints:

* **GET http://localhost:8080/api/users**

* **POST http://localhost:8080/api/users**
 (with JSON body)

Example body:

```json
{
  "name": "Akshay",
  "email": "akshay@example.com"
}
```

---

## 7. Next Steps

* Add validation using **@Valid**
* Use DTOs for request/response objects
* Deploy with MongoDB Atlas in production
* Secure endpoints using Spring Security

---

**That’s it!** 🎉 You’ve successfully connected MongoDB with Spring Boot using Spring Data MongoDB.
