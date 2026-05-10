---
title: REST Controllers in Spring Boot
parent: REST
nav_order: 3
---

# REST Controllers in Spring Boot

## 1. What is a REST Controller?

A REST Controller is responsible for:

* Handling HTTP requests
* Mapping requests to business logic
* Returning HTTP responses (usually JSON)

In Spring Boot:

```java
@RestController
@RequestMapping("/users")
public class UserController {
}
```

---

## 2. @RestController vs @Controller

### @RestController

* Combines:

    * `@Controller`
    * `@ResponseBody`
* Return JSON directly

### @Controller

* Used for MVC (views, JSP, Thymeleaf)
* Requires `@ResponseBody` for APIs

👉 Always use `@RestController` for REST APIs

---

## 3. Request Mapping Basics

### Class Level Mapping

```
@RequestMapping("/users")
```

### Method Level Mapping

```
@GetMapping("/{id}")
@PostMapping
@PutMapping("/{id}")
@DeleteMapping("/{id}")
```

---

## 4. Handling Request Inputs

### 4.1 Path Variables

```java
@GetMapping("/{id}")
public User getUser(@PathVariable Long id) {
    return userService.getUser(id);
}
```

---

### 4.2 Query Parameters

```java
@GetMapping
public List<User> getUsers(@RequestParam int page,
                           @RequestParam int size) {
    return userService.getUsers(page, size);
}
```

---

### 4.3 Request Body

```java
@PostMapping
public User createUser(@RequestBody UserRequest request) {
    return userService.createUser(request);
}
```

---

### 4.4 Validation

```java
@PostMapping
public User createUser(@Valid @RequestBody UserRequest request) {
    return userService.createUser(request);
}
```

---

## 5. Response Handling

### 5.1 Returning Objects

```java
@GetMapping("/{id}")
public User getUser(@PathVariable Long id) {
    return userService.getUser(id);
}
```

👉 Automatically converted to JSON

---

### 5.2 Using ResponseEntity (Recommended)

```java
@GetMapping("/{id}")
public ResponseEntity<User> getUser(@PathVariable Long id) {
    return ResponseEntity.ok(userService.getUser(id));
}
```

---

### 5.3 Custom Status Codes

```java
@PostMapping
public ResponseEntity<User> createUser(@RequestBody UserRequest request) {
    User user = userService.createUser(request);
    return ResponseEntity
            .status(HttpStatus.CREATED)
            .body(user);
}
```

---

## 6. Controller Layer Responsibilities

### ✅ Should Do

* Handle HTTP layer
* Validate input
* Call service layer
* Return response

###  Should NOT Do

* Business logic
* Database access
* Complex transformations

---

## 7. Thin Controller Pattern

###  Bad (Fat Controller)

```java
@PostMapping
public User createUser(@RequestBody UserRequest request) {
    // validation logic
    // business logic
    // DB calls
    return user;
}
```

---

### ✅ Good (Thin Controller)

```java
@PostMapping
public ResponseEntity<UserResponse> createUser(@Valid @RequestBody UserRequest request) {
    return ResponseEntity.ok(userService.createUser(request));
}
```

👉 Move logic to **Service layer**

---

## 8. DTO Pattern (Critical)

### Why NOT expose entities?

* Security risks
* Tight coupling
* Unwanted fields exposed

---

### Example

#### Request DTO

```java
public class UserRequest {
    private String name;
    private String email;
}
```

#### Response DTO

```java
public class UserResponse {
    private Long id;
    private String name;
}
```

---

## 9. API Design in Controllers

### Naming

* Use nouns
* Keep endpoints predictable

```http
GET    /users
GET    /users/{id}
POST   /users
DELETE /users/{id}
```

---

### Versioning

```
@RequestMapping("/api/v1/users")
```

---

## 10. Exception Handling (Integration)

Controllers should NOT handle exceptions directly.

 Avoid:

```
try {
    ...
} catch(Exception e) {
    return ResponseEntity.status(500).body("error");
}
```

✅ Use:

* `@ControllerAdvice` (global handling)

---

## 11. Content Negotiation

Spring automatically handles:

* JSON (default via Jackson)

You can control:

```
@GetMapping(produces = MediaType.APPLICATION_JSON_VALUE)
```

---

## 12. Common Mistakes

*  Putting business logic in controllers
*  Returning entities directly
*  Not using DTOs
*  Not using ResponseEntity when needed
*  Handling exceptions inside controllers
*  Overloading controllers with too many responsibilities

---


## 13. Real-World Structure

```
controller/
    UserController.java
service/
    UserService.java
dto/
    UserRequest.java
    UserResponse.java
entity/
    User.java
repository/
    UserRepository.java
```

---

## 14. Summary

* Controllers = HTTP layer only
* Keep them thin
* Use DTOs always
* Delegate logic to services
* Use proper request/response handling

---

## 15. Key Takeaway

* 👉 A good controller is **boring and simple**
* 👉 Complexity belongs in the service layer

---
