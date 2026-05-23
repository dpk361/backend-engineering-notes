---
title: Stereotype Annotations 
parent: Annotations
nav_order: 191
---


# Spring Stereotype Annotations

## 1. Overview

In Spring Framework, the following annotations are used to define beans and represent different layers:

- @Component
- @Service
- @Repository
- @Controller
- @RestController

All are specializations of **@Component.**

---

## 2. @Component

Generic annotation to declare a Spring-managed bean.

### Usage:

Used when class does not belong to a specific layer.

```java
@Component
public class UtilityClass { }
```

### Key Points:
- Base annotation
- Enables component scanning
- No special behavior

---

## 3. @Service

Represents business logic layer.

```java
@Service
public class PaymentService { }
```

### Key Points:
- Used for business logic
- Supports AOP (transactions, etc.)
- Improves readability

---

## 4. @Repository

Represents persistence layer (database interaction).

```java
@Repository
public class UserRepository { }
```

### Key Points:
- Used with JPA/JDBC
- Provides exception translation

### Exception Translation:
- Converts SQL exceptions into Spring exceptions
- Example: SQLException → DataAccessException

---

## 5. @Controller

Used in Spring MVC to handle HTTP requests.

```java
@Controller
public class UserController {

    @GetMapping("/users")
    public String getUsers() {
        return "users";
    }
}
```

### Key Points:
- Returns view (HTML/JSP)
- Used in web layer

---

## 6. @RestController

Combination of @Controller and @ResponseBody.

```java
@RestController
public class UserController {

    @GetMapping("/users")
    public List<User> getUsers() {
        return users;
    }
}
```

### Key Points:
- Returns JSON response
- Used in REST APIs

---

## 7. Internal Hierarchy

@Component
 ├── @Service
 ├── @Repository
 └── @Controller

---

## 8. Key Differences

| Annotation | Layer | Purpose | Special Feature |
|-----------|------|--------|----------------|
| @Component | Generic | Any bean | No special behavior |
| @Service | Business | Business logic | AOP support |
| @Repository | Persistence | DB access | Exception translation |
| @Controller | Web | HTTP handling | MVC support |
| @RestController | Web API | REST APIs | JSON response |

---

## 9. Real-World Flow

Controller → Service → Repository → Database

---

## 10. Why Not Use Only @Component?

### Problems:
- No clear architecture
- Hard to maintain
- Lose special features

---

## 11. Best Practices

- Use layer-specific annotations
- Keep separation of concerns
- Apply transactions in service layer
- Use repository only for DB operations

---

## Summary

All annotations are specializations of @Component used to represent different layers in an application.

- @Component → Generic bean
- @Service → Business logic
- @Repository → Database layer
- @Controller → Web layer
- @RestController → REST APIs

----
