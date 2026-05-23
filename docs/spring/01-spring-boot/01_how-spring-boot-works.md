---
title: How Spring Boot Works (End-to-End)
parent: Spring Boot
nav_order: 1
---

# How Spring Boot Works (End-to-End)

This document explains **how Spring Boot works internally** using a
simple real-world example: a **BRANCH service** with:

- 1 Controller
- 1 Service
- 1 Repository
- 1 Entity
- 1 DTO
- Default configurations

The goal is to understand **what happens when the application starts**
and **how a request flows through the system**.

---

## Example Application: BRANCH Service

Business requirement:

> Expose an API to fetch branch details by branch code.

---

## Project Structure (Minimal & Clean)

```
branch-service
│
├── BranchApplication.java
│
├── controller
│ └── BranchController.java
│
├── service
│ └── BranchService.java
│
├── repository
│ └── BranchRepository.java
│
├── entity
│ └── Branch.java
│
├── dto
│ └── BranchDto.java
│
└── resources
└── application.properties
```


---

## 1️⃣ Application Entry Point

```java
@SpringBootApplication
public class BranchApplication {

    public static void main(String[] args) {
        SpringApplication.run(BranchApplication.class, args);
    }
}
```

What **@SpringBootApplication** Does

It is a combination of:   

```
@Configuration   
@EnableAutoConfiguration   
@ComponentScan  
```

#### Meaning:  

- Marks this as configuration class
- Enables auto-configuration
- Scans components from this package downward

---

## 2️⃣ What Happens When Application Starts

Step-by-Step Internally

1. JVM starts `main()` method

2. `SpringApplication.run()` is called

3. Spring Boot:

    - Creates ApplicationContext
    - Loads default configurations
    - Scans components
    - Creates beans
    - Starts embedded server (Tomcat)

4. Application becomes ready to accept requests

---

## 3️⃣ Default Auto Configuration

Spring Boot automatically configures:

- Embedded Tomcat
- Jackson (JSON serialization)
- Spring MVC
- Spring Data JPA (if on classpath)
- Transaction management
- Exception handling

All of this happens because of:

```
 spring-boot-starter-*
```

> Classpath decides behavior — not configuration files.

## 4️⃣ Entity Layer

``` java
@Entity
public class Branch {

    @Id
    private Long id;

    private String branchCode;
    private String branchName;
    private String city;
}
```

Purpose:

- Represents database table
- Managed by JPA/Hibernate
- Used only inside backend

---

## 5️⃣ Repository Layer

```java
@Repository
public interface BranchRepository extends JpaRepository<Branch, Long> {

    Optional<Branch> findByBranchCode(String branchCode);
}
```

What Spring Does:

- Detects interface
- Creates proxy implementation
- Registers it as a bean
- Injects EntityManager internally

> You never write implementation — Spring generates it.

---

## 6️⃣ DTO Layer

```java

public class BranchDto {

    private String branchCode;
    private String branchName;
    private String city;
}
```


Purpose:

- API contract
- Decouples entity from API
- Protects database structure

---

## 7️⃣ Service Layer

```java

@Service
public class BranchService {

    private final BranchRepository repository;

    public BranchService(BranchRepository repository) {
        this.repository = repository;
    }

    public BranchDto getBranch(String branchCode) {
        Branch branch = repository.findByBranchCode(branchCode)
                .orElseThrow();

        return mapToDto(branch);
    }

    private BranchDto mapToDto(Branch branch) {
        BranchDto dto = new BranchDto();
        dto.setBranchCode(branch.getBranchCode());
        dto.setBranchName(branch.getBranchName());
        dto.setCity(branch.getCity());
        return dto;
    }
}

```

Responsibilities:

- Business logic
- Transaction boundary (usually)
- Entity → DTO mapping

---

## 8️⃣ Controller Layer
```java 

@RestController
@RequestMapping("/branches")
public class BranchController {

    private final BranchService service;

    public BranchController(BranchService service) {
        this.service = service;
    }

    @GetMapping("/{code}")
    public BranchDto getBranch(@PathVariable String code) {
        return service.getBranch(code);
    }
}
```

**Responsibilities:**  

- Handle HTTP request
- Validate input
- Call service
- Return response

---

## 9️⃣ Request Flow
```
HTTP Request
↓
DispatcherServlet
↓
BranchController
↓
BranchService
↓
BranchRepository
↓
Database
```

Response flows back in reverse order.

---

## 1️⃣0️⃣ How Beans Are Created (High-Level)

Spring Boot:

- Scans classes annotated with:

    - @Controller
    - @Service
    - @Repository
    - @Component

- Creates objects (beans)
- Stores them in ApplicationContext
- Injects dependencies automatically

This is Dependency Injection + IoC.

---

## 1️⃣1️⃣ application.properties (Default Config)

```properties
server.port=8080
spring.datasource.url=jdbc:h2:mem:TEST_DB
spring.jpa.show-sql=true
```

Spring Boot:

- Loads defaults
- Overrides with these values
- Applies configuration automatically

---

## 1️⃣2️⃣ Why Spring Boot Is Powerful

Without Spring Boot you would:

- Configure Tomcat manually
- Configure MVC manually
- Configure JPA manually
- Write lots of boilerplate

Spring Boot:

- Provides sensible defaults
- Reduces configuration
- Focuses on business logic
---

### Common Beginner Mistakes (Avoid)

- ❌ Putting business logic in controller
- ❌ Returning entities directly
- ❌ Ignoring service layer
- ❌ Mixing responsibilities

---

> Spring Boot works by AUTO CONFIGURING components based on the classpath, creating and managing beans using IoC, starting an embedded server, and routing requests through controllers, services, and repositories.

### Summary

- Spring Boot is convention over configuration
- Auto-configuration is classpath-driven
- Beans are managed by Spring container
- Controllers handle HTTP
- Services handle business logic
- Repositories handle data access


> Spring Boot lets you focus on business logic by handling infrastructure, configuration, and lifecycle for you.





