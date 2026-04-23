Connecting a **MySQL database** to your **Spring Boot** project is one of the most common and essential tasks for building real-world web applications. In this tutorial, we'll go step-by-step to set up and integrate MySQL into a Spring Boot application.

------------------------------------------------------------------------

## Prerequisites

Before we start, make sure you have the following installed:

-   **Java 17+**
-   **Maven or Gradle**
-   **MySQL Server** (Running locally or remotely)
-   **Spring Boot (v3+)**
-   **IDE** such as IntelliJ IDEA or Eclipse

------------------------------------------------------------------------

## Step 1: Create a Spring Boot Project

You can create a Spring Boot project in two ways:

### Option 1: Using [Spring Initializr](https://start.spring.io/)

1.  Go to <https://start.spring.io/>
2.  Choose:
    -   **Project:** Maven Project
    -   **Language:** Java
    -   **Spring Boot Version:** 3.x
    -   **Dependencies:**
        -   Spring Web
        -   Spring Data JPA
        -   MySQL Driver
3.  Click **Generate**, and extract the downloaded project.

### Option 2: Using IDE (like IntelliJ or Eclipse)

-   Go to **File → New → Project → Spring Initializr**
-   Add the same dependencies mentioned above.

------------------------------------------------------------------------

## Step 2: Configure MySQL Database

Create a new MySQL database:

``` sql
CREATE DATABASE springboot_db;
```

You can use any name for the database.

------------------------------------------------------------------------

## Step 3: Add Database Configuration in **application.properties**


In **src/main/resources/application.properties**
, add:

``` properties
# Database Configuration
spring.datasource.url=jdbc:mysql://localhost:3306/springboot_db
spring.datasource.username=root
spring.datasource.password=your_password

# JPA Configuration
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL8Dialect
```

**Explanation:** - **ddl-auto=update**
: Automatically creates or updates tables based on entity classes. - **show-sql=true**
: Logs SQL queries to the console. - **MySQL8Dialect**
: Optimizes Hibernate for MySQL 8.

------------------------------------------------------------------------

## Step 4: Create a JPA Entity

Inside **src/main/java/com/example/demo/model**
, create a class named
**User.java**
:

``` java {4-7}
package com.example.demo.model;

import jakarta.persistence.*;

@Entity
@Table(name = "users")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private String email;

    // Constructors
    public User() {}
    public User(String name, String email) {
        this.name = name;
        this.email = email;
    }

    // Getters & Setters
    public Long getId() { return id; }
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }
}
```

------------------------------------------------------------------------

## Step 5: Create a Repository Interface

Inside **src/main/java/com/example/demo/repository**
, create
**UserRepository.java**
:

``` java
package com.example.demo.repository;

import com.example.demo.model.User;
import org.springframework.data.jpa.repository.JpaRepository;

public interface UserRepository extends JpaRepository<User, Long> {
}
```

This gives you all CRUD methods (save, findAll, delete, etc.) without writing SQL manually.

------------------------------------------------------------------------

## Step 6: Create a Service and Controller

### **Service Layer**

``` java
package com.example.demo.service;

import com.example.demo.model.User;
import com.example.demo.repository.UserRepository;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class UserService {

    private final UserRepository repository;

    public UserService(UserRepository repository) {
        this.repository = repository;
    }

    public List<User> getAllUsers() {
        return repository.findAll();
    }

    public User addUser(User user) {
        return repository.save(user);
    }
}
```

### Controller Layer

``` java
package com.example.demo.controller;

import com.example.demo.model.User;
import com.example.demo.service.UserService;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/api/users")
public class UserController {

    private final UserService service;

    public UserController(UserService service) {
        this.service = service;
    }

    @GetMapping
    public List<User> getUsers() {
        return service.getAllUsers();
    }

    @PostMapping
    public User createUser(@RequestBody User user) {
        return service.addUser(user);
    }
}
```

------------------------------------------------------------------------

## Step 7: Run the Application

Run the application using:

``` bash
mvn spring-boot:run
```

Or run **DemoApplication.java**
 directly from your IDE.

Once it starts, visit:

-   **GET Users:** <http://localhost:8080/api/users>\
-   **POST Users:** <http://localhost:8080/api/users>

Sample JSON for POST request:

``` json
{
  "name": "Akshay",
  "email": "akshay@example.com"
}
```

------------------------------------------------------------------------

## Step 8: Verify in MySQL

Run this in your MySQL terminal:

``` sql
SELECT * FROM users;
```

You should see your inserted data!

------------------------------------------------------------------------

## Common Troubleshooting Tips

  
  |Problem                         |  Possible Fix |
  |----------------------------------|-----------------------------------------|
  |**Access denied for user 'root'**
  | Check MySQL username/password in  **application.properties**
|
  |**Cannot connect to MySQL**
       |  Ensure MySQL server is running and port is correct|
  |**Table doesn’t exist**
           |  Try **spring.jpa.hibernate.ddl-auto=create**
 for first run|

------------------------------------------------------------------------

## Conclusion

You've successfully integrated **MySQL with Spring Boot**! 🎉 Now your app can store and manage data efficiently with the power of Spring Data JPA.
