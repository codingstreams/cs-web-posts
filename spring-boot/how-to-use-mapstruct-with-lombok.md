MapStruct is an open-source, compile-time code generator that simplifies the implementation of mappings between Java bean types. Unlike reflection-based mapping libraries that operate at runtime, MapStruct generates plain Java method implementations during the build process, ensuring high performance, type safety, and early error reporting. By using a convention-over-configuration approach, it allows developers to define mapping interfaces with simple annotations, significantly reducing the boilerplate code required to convert data between different layers of an application, such as shifting data from a database Entity to a shallow Data Transfer Object (DTO).

When using MapStruct and Lombok together, the most important thing is the **order of execution**. MapStruct needs the getters, setters, and builders that Lombok generates to create the mapping code. If MapStruct runs first, it won't see those methods and will fail.

## 1. Project Dependencies (Maven)

In your `pom.xml`, you need to ensure the `lombok-mapstruct-binding` is included. This bridge ensures the two processors communicate correctly.

```xml
<properties>
    <org.mapstruct.version>1.6.3</org.mapstruct.version>
    <org.projectlombok.version>1.18.30</org.projectlombok.version>
    <lombok-mapstruct-binding.version>0.2.0</lombok-mapstruct-binding.version>
</properties>
```

```xml
<dependencies>
    <dependency>
        <groupId>org.mapstruct</groupId>
        <artifactId>mapstruct</artifactId>
        <version>${org.mapstruct.version}</version>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>${org.projectlombok.version}</version>
        <scope>provided</scope>
    </dependency>
</dependencies>
```

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.11.0</version>
            <configuration>
                <annotationProcessorPaths>
                    <path>
                        <groupId>org.projectlombok</groupId>
                        <artifactId>lombok</artifactId>
                        <version>${org.projectlombok.version}</version>
                    </path>
                    <path>
                        <groupId>org.projectlombok</groupId>
                        <artifactId>lombok-mapstruct-binding</artifactId>
                        <version>${lombok-mapstruct-binding.version}</version>
                    </path>
                    <path>
                        <groupId>org.mapstruct</groupId>
                        <artifactId>mapstruct-processor</artifactId>
                        <version>${org.mapstruct.version}</version>
                    </path>
                </annotationProcessorPaths>
            </configuration>
        </plugin>
    </plugins>
</build>
```

## 2. Define Your Models (Using Lombok)

Use Lombok's `@Data`, `@Getter`, or `@Builder`. MapStruct is smart enough to detect Lombok's `@Builder` automatically and use it for object creation.

```java
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class UserEntity {
    private Long id;
    private String firstName;
    private String lastName;
    private String email;
}

@Data
public class UserDto {
    private String fullName;
    private String email;
}
```

## 3. Create the Mapper (MapStruct)

Now, create an interface for your mapper. MapStruct will generate the implementation at compile time.

```java
import org.mapstruct.Mapper;
import org.mapstruct.Mapping;
import org.mapstruct.factory.Mappers;

@Mapper(componentModel = "spring") // Use "spring" if using Spring Boot - Can be injected as dependency
public interface UserMapper {
    
    UserMapper INSTANCE = Mappers.getMapper(UserMapper.class);

    @Mapping(target = "fullName", expression = "java(user.getFirstName() + \" \" + user.getLastName())")
    UserDto toDto(UserEntity user);
}
```

## 4. How it works

The magic happens in the **Annotation Processing Tool (APT)**. 

1. **Lombok** intercepts the compilation and injects the bytecode for getters/setters.
2. The **Binding** ensures the AST (Abstract Syntax Tree) is updated.
3. **MapStruct** scans the updated classes, finds the new getters/setters, and generates the `UserMapperImpl.class`.

## Important Points

* **Builder Conflicts:** If you use `@Builder` on your DTOs, MapStruct will try to use the builder instead of the constructor. If you have specific logic in your constructor, you might need to tell MapStruct to ignore the builder using `@Mapper(builder = @Builder(disableBuilder = true))`.

* **Compilation Error (Can't find property):** This almost always means the `annotationProcessorPaths` in your `pom.xml` are in the wrong order or the `lombok-mapstruct-binding` is missing.

* **IDE Support:** If you are using IntelliJ IDEA, make sure you have the **MapStruct Support** plugin installed and "Annotation Processing" enabled in your settings.

By combining MapStruct and Lombok, you effectively eliminate the most tedious parts of Java development: writing repetitive boilerplate for POJOs and manual "getter-to-setter" mapping logic. This setup not only keeps your codebase clean and readable but also leverages compile-time validation to catch mapping errors before your application even starts.
