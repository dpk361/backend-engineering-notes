---
title: Exception & Error Handling
parent: REST
nav_order: 6
---

# Exception Handling in Spring Boot

# 1. Why Exception Handling Is Important

In real REST APIs, exception handling is not just about avoiding crashes. It is about:

* keeping controllers clean
* returning correct HTTP status codes
* sending consistent error responses
* protecting internal details
* improving debugging and observability
* making APIs predictable for clients

A production REST API should never rely on scattered `try-catch` blocks inside controllers.

---

# 2. What is @ControllerAdvice?

`@ControllerAdvice` is a Spring annotation used to define a **global cross-cutting component** for controllers.

It can be used for:

* global exception handling
* global model attributes
* global binder configuration
* request/response preprocessing

In REST APIs, it is most commonly used with `@ExceptionHandler`.

---

# 3. What is @RestControllerAdvice?

`@RestControllerAdvice` is a combination of:

* `@ControllerAdvice`
* `@ResponseBody`

It is used when you want global exception handlers to return JSON/XML response bodies directly.

For REST APIs, this is usually the preferred annotation.

---

# 4. Difference Between @ControllerAdvice and @RestControllerAdvice

## @ControllerAdvice

Use when:

* you want to apply logic globally
* you may return views in MVC applications
* you may use `@ResponseBody` manually if needed

## @RestControllerAdvice

Use when:

* you are building REST APIs
* you want error responses returned as JSON automatically
* you want cleaner code without repeating `@ResponseBody`

### Rule of Thumb

For Spring Boot REST services, use:

```
@RestControllerAdvice
```

---

# 5. Basic Structure of Global Exception Handling

Recommended structure:

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidationException(
            MethodArgumentNotValidException ex) {
       // ...
    }

    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleUserNotFound(
            UserNotFoundException ex) {
        //...
    }

    @ExceptionHandler(InsufficientBalanceException.class)
    public ResponseEntity<ErrorResponse> handleInsufficientBalance(
            InsufficientBalanceException ex) {
        //...
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleGenericException(
            Exception ex) {
        //...
    }
}
```

---

# 6. Recommended Error Response DTO

A standard error response should be consistent across all APIs.

Example:

```java
public record ErrorResponse(
        Instant timestamp,
        int status,
        String error,
        String code,
        String message,
        String path,
        String traceId
) {}
```

### Why this is useful

* `status` helps clients understand HTTP outcome
* `code` gives machine-readable business meaning
* `message` gives readable detail
* `path` identifies the failing endpoint
* `traceId` helps production debugging

---

# 7. Exception Handling Flow

Typical flow:

```text
Request comes in
    ↓
Controller or Service detects a problem
    ↓
Exception is thrown
    ↓
@ControllerAdvice / @RestControllerAdvice catches it
    ↓
Matching @ExceptionHandler method runs
    ↓
Standard error response is returned
```

---

# 8. Best Practice: Throw Exceptions from the Service Layer

Do not handle business errors inside controllers.

### Good

```java
public UserResponse getUser(Long id) {
    User user = repository.findById(id)
            .orElseThrow(() -> new UserNotFoundException(id));
    return mapper.toResponse(user);
}
```

### Bad

```java
@GetMapping("/{id}")
public ResponseEntity<?> getUser(@PathVariable Long id) {
    try {
       // ...
    } catch (Exception ex) {
       // ...
    }
}
```

### Why the service layer is better

* keeps controllers thin
* separates business logic from HTTP logic
* improves reuse
* makes testing easier

---

# 9. Most Common Exceptions to Handle

These are the most frequently used exceptions in enterprise REST APIs.

---

## 9.1 MethodArgumentNotValidException

This occurs when validation fails on a request body annotated with `@Valid`.

Example:

```java
public class CreateUserRequest {

    @NotBlank
    private String name;

    @Email
    private String email;
}
```

Controller:

```java
@PostMapping
public ResponseEntity<?> createUser(@Valid @RequestBody CreateUserRequest request) {
    return ResponseEntity.ok().build();
}
```

If validation fails, Spring throws:

```
MethodArgumentNotValidException
```

### Recommended response

Return `400 Bad Request`.

### Example handler

```java
@ExceptionHandler(MethodArgumentNotValidException.class)
public ResponseEntity<ErrorResponse> handleValidation(
        MethodArgumentNotValidException ex,
        HttpServletRequest request) {

    Map<String, String> fieldErrors = new LinkedHashMap<>();
    ex.getBindingResult()
      .getFieldErrors()
      .forEach(err -> fieldErrors.put(err.getField(), err.getDefaultMessage()));

    ErrorResponse error = new ErrorResponse(
            Instant.now(),
            HttpStatus.BAD_REQUEST.value(),
            "Bad Request",
            "VALIDATION_ERROR",
            "Request validation failed",
            request.getRequestURI(),
            null
    );

    return ResponseEntity.badRequest().body(error);
}
```

### Industry note

Many teams return detailed field errors separately inside `errors`.

Example:

```json
{
  "code": "VALIDATION_ERROR",
  "message": "Request validation failed",
  "errors": {
    "email": "must be a valid email",
    "name": "must not be blank"
  }
}
```

---

## 9.2 ConstraintViolationException

This often occurs when validation is applied to:

* query parameters
* path variables
* method parameters

Example:

```java
@GetMapping("/users")
public ResponseEntity<?> getUsers(@RequestParam @Min(1) int page) {
 //   ...
}
```

If invalid input is passed, Spring may throw:

```
ConstraintViolationException
```

### Recommended response

Return `400 Bad Request`.

### Typical use case

* `page < 1`
* invalid numeric range
* invalid request parameter value

---

## 9.3 UserNotFoundException

Very common business exception.

### Use case

* requested resource does not exist

Example:

```java
public class UserNotFoundException extends RuntimeException {
    public UserNotFoundException(Long id) {
        super("User not found: " + id);
    }
}
```

### Recommended HTTP status

`404 Not Found`

### Example handler

```java
@ExceptionHandler(UserNotFoundException.class)
public ResponseEntity<ErrorResponse> handleUserNotFound(
        UserNotFoundException ex,
        HttpServletRequest request) {

    ErrorResponse error = new ErrorResponse(
            Instant.now(),
            HttpStatus.NOT_FOUND.value(),
            "Not Found",
            "USER_NOT_FOUND",
            ex.getMessage(),
            request.getRequestURI(),
            null
    );

    return ResponseEntity.status(HttpStatus.NOT_FOUND).body(error);
}
```

---

## 9.4 InsufficientBalanceException

Common in finance, wallet, ordering, or inventory systems.

### Use case

* account balance is not enough
* wallet has insufficient funds
* stock quantity is not enough

### Recommended HTTP status

Usually `409 Conflict` or sometimes `422 Unprocessable Entity`

### Practical industry preference

`409 Conflict` is often used when business state prevents the operation.

### Example

```java
@ExceptionHandler(InsufficientBalanceException.class)
public ResponseEntity<ErrorResponse> handleInsufficientBalance(
        InsufficientBalanceException ex,
        HttpServletRequest request) {

    ErrorResponse error = new ErrorResponse(
            Instant.now(),
            HttpStatus.CONFLICT.value(),
            "Conflict",
            "INSUFFICIENT_BALANCE",
            ex.getMessage(),
            request.getRequestURI(),
            null
    );

    return ResponseEntity.status(HttpStatus.CONFLICT).body(error);
}
```

---

## 9.5 DuplicateResourceException / ConflictException

Very common when creating duplicate users, orders, emails, usernames, or references.

### Use cases

* email already exists
* username already exists
* duplicate order number
* version conflict

### Recommended HTTP status

`409 Conflict`

### Example

```java
public class DuplicateUserException extends RuntimeException {
    public DuplicateUserException(String email) {
        super("User already exists with email: " + email);
    }
}
```

---

## 9.6 HttpMessageNotReadableException

Occurs when Spring cannot parse the request body.

### Common causes

* malformed JSON
* invalid date format
* wrong JSON structure
* sending string where number is expected

### Example

```json
{
  "name": "Mohan",
  "age": "abc"
}
```

### Recommended HTTP status

`400 Bad Request`

### Example handler

```java
@ExceptionHandler(HttpMessageNotReadableException.class)
public ResponseEntity<ErrorResponse> handleUnreadableJson(
        HttpMessageNotReadableException ex,
        HttpServletRequest request) {

    ErrorResponse error = new ErrorResponse(
            Instant.now(),
            HttpStatus.BAD_REQUEST.value(),
            "Bad Request",
            "INVALID_JSON",
            "Request body is malformed or unreadable",
            request.getRequestURI(),
            null
    );

    return ResponseEntity.badRequest().body(error);
}
```

---

## 9.7. MissingServletRequestParameterException

It Occurs when a required query parameter is missing.

### Example

```java
@GetMapping("/users")
public List<UserResponse> getUsers(@RequestParam String status) {
    // ...
}
```

If `status` is not provided, Spring can throw this exception.

### Recommended HTTP status

`400 Bad Request`

---

## 9.8 MissingPathVariableException

It Occurs when a required path variable is missing or request mapping is incorrect.

### Recommended HTTP status

`400 Bad Request`

This is less common than validation or not-found handling, but still useful.

---

## 9.9 AccessDeniedException

It Occurs when a user is authenticated but not allowed to access the resource.

### Example use case

* normal user trying to access admin endpoint
* role-based access denied

### Recommended HTTP status

`403 Forbidden`

---

## 9.10 Authentication Exceptions

Common cases:

* invalid token
* expired token
* missing token

### Recommended HTTP status

`401 Unauthorized`

In Spring Security, many authentication failures are handled by security filters rather than normal controller advice, but understanding the status mapping is important.

---

## 9.11 IllegalArgumentException

This is commonly used when service-layer inputs are invalid.

### Example

* negative quantity
* invalid state transition
* invalid enum value from internal logic

### Recommended HTTP status

Usually `400 Bad Request`

### Example

```java
@ExceptionHandler(IllegalArgumentException.class)
public ResponseEntity<ErrorResponse> handleIllegalArgument(
        IllegalArgumentException ex,
        HttpServletRequest request) {

    ErrorResponse error = new ErrorResponse(
            Instant.now(),
            HttpStatus.BAD_REQUEST.value(),
            "Bad Request",
            "INVALID_ARGUMENT",
            ex.getMessage(),
            request.getRequestURI(),
            null
    );

    return ResponseEntity.badRequest().body(error);
}
```

---

## 9.12 DataAccessException / JpaSystemException

Used when database access fails.

### Common causes

* DB connection failure
* constraint issues
* query failure
* transaction problems

### Recommended HTTP status

Usually `500 Internal Server Error`
Sometimes `503 Service Unavailable` if the DB/service is temporarily unavailable

### Industry note

Do not expose raw SQL or database internals to clients.

---

## 9.13 NullPointerException

This should not be your normal business exception, but a generic fallback handler should catch it.

### Recommended status

`500 Internal Server Error`

### Industry note

If this appears often, it indicates a bug in code quality, null handling, or mapping logic.

---

# 10. Generic Exception Handler

Always keep a fallback handler.

```java
@ExceptionHandler(Exception.class)
public ResponseEntity<ErrorResponse> handleGenericException(
        Exception ex,
        HttpServletRequest request) {

    log.error("Unhandled exception occurred", ex);

    ErrorResponse error = new ErrorResponse(
            Instant.now(),
            HttpStatus.INTERNAL_SERVER_ERROR.value(),
            "Internal Server Error",
            "INTERNAL_ERROR",
            "Something went wrong",
            request.getRequestURI(),
            null
    );

    return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(error);
}
```

### Why this is important

* It avoids leaking stack traces
* protects internal details
* gives a stable API response
* acts as the last safety net

---

# 11. Recommended Exception Handler Order

Spring chooses the **most specific match first**.

Example:

```
@ExceptionHandler(MethodArgumentNotValidException.class)
@ExceptionHandler(UserNotFoundException.class)
@ExceptionHandler(InsufficientBalanceException.class)
@ExceptionHandler(Exception.class)
```

The last one is only a fallback.

### Rule

* specific handlers first in design
* generic handler last as a safety net

Spring resolves based on type specificity, not file order alone.

---

# 12. Should You Return Different Error Structures for Different Exceptions?

Usually no.

The Industry's best practice is to keep the response shape consistent.

### Good

```json
{
  "code": "USER_NOT_FOUND",
  "message": "User not found: 10",
  "status": 404
}
```

```json
{
  "code": "VALIDATION_ERROR",
  "message": "Request validation failed",
  "status": 400
}
```

### Bad

* one exception returns string
* another return map
* another return raw stack trace
* another return entity

Consistency matters more than variety.

---

# 13. Best Practices for Exception Handling

## Do

* use `@RestControllerAdvice`
* keep controllers thin
* throw business exceptions from the service layer
* return consistent error DTOs
* log exceptions internally
* include trace/correlation IDs
* map exceptions to correct HTTP status codes
* have a generic fallback handler

## Do Not

* put try-catch everywhere
* return 200 for failures
* expose stack traces
* mix success and error logic in controllers
* return different response structures randomly
* swallow exceptions silently

---

# 14. Industry-Standard Pattern

```text
Controller
    ↓
Service
    ↓
Exception Thrown
    ↓
@RestControllerAdvice
    ↓
@ExceptionHandler
    ↓
ErrorResponse DTO
    ↓
Client receives consistent JSON
```

---

# 15. Common Real-World Exception Mapping

| Exception                       | Status | Common Use              |
| ------------------------------- | -----: | ----------------------- |
| MethodArgumentNotValidException |    400 | body validation failure |
| ConstraintViolationException    |    400 | query/path validation   |
| HttpMessageNotReadableException |    400 | malformed JSON          |
| UserNotFoundException           |    404 | missing resource        |
| DuplicateResourceException      |    409 | duplicate data          |
| InsufficientBalanceException    |    409 | state conflict          |
| AccessDeniedException           |    403 | unauthorized permission |
| AuthenticationException         |    401 | login/token issue       |
| IllegalArgumentException        |    400 | bad internal input      |
| Exception                       |    500 | fallback                |

---

# 16. Most Recommended Real-World Setup

A mature REST API usually has:

* one `ErrorResponse` DTO
* one `@RestControllerAdvice`
* several specific `@ExceptionHandler` methods
* one generic fallback handler
* logging with trace IDs
* consistent status code mapping
* service-layer exception throwing

---

# 17. Final Summary

`@ControllerAdvice` and `@RestControllerAdvice` are the foundation of centralized exception handling in Spring Boot.

### Use `@RestControllerAdvice` for REST APIs

because it automatically returns response bodies.

### Handle these frequently:

* validation failures
* not found errors
* business conflicts
* security failures
* malformed JSON
* generic unexpected errors

### Industry best practice

* controllers should stay clean
* services should throw meaningful exceptions
* global handlers should map them to stable JSON error responses

This creates APIs that are:

* predictable
* maintainable
* secure
* easy to debug
* production-friendly

