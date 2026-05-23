---
title: Validations
parent: Web
nav_order: 1
---

# Validation in Spring Boot

Validation in Spring Boot ensures that **incoming data is correct, safe, and meaningful** before it is processed by business logic.

Spring Boot provides built-in support for validation using **Jakarta Bean Validation** (formerly `javax.validation`) and integrates seamlessly with Spring through annotations like `@Valid` and `@Validated`.

---

## What is Validation?

Validation is the process of **checking input data against predefined rules** such as:
- Mandatory fields
- Length restrictions
- Format constraints
- Numeric ranges
- Date boundaries

The goal is to **fail fast** and prevent invalid data from entering the system.

---

## Why Validation is Important

Without proper validation:
- Invalid data reaches business logic
- Bugs surface late in processing
- Data integrity is compromised
- Error handling becomes complex

Validation helps:
- Enforce business rules early
- Protect downstream systems (DB, integrations)
- Provide clear and structured error responses
- Improve overall system reliability

---

## Where Validation is Used in Spring Boot

Validation is commonly applied at:
- API boundaries (request DTOs)
- Service layer (method-level validation)
- Nested objects and collections

Typical flow:
>Client → Controller (@Valid) → Service (@Validated) → Business Logic


---

## Core Validation Annotations (Jakarta Bean Validation)

### Basic Constraints
- `@NotNull` – Field must not be null
- `@NotEmpty` – Must not be null and must contain at least one element
- `@NotBlank` – Must contain non-whitespace characters
- `@Size(min, max)` – String or collection size limits
- `@Min` / `@Max` – Numeric range validation
- `@Positive` / `@Negative`
- `@Email` – Valid email format
- `@Pattern` – Regex-based validation
- `@Past` / `@Future` – Date validations

### Advanced Constraints
- `@Digits(integer, fraction)` – Numeric precision control
- `@DecimalMin` / `@DecimalMax`
- `@AssertTrue` / `@AssertFalse`

---

## Applying Validation in DTOs

Validation is typically applied at the **DTO level**, not on entities.

```java
public class CustomerDTO {

    @NotNull(message = "Customer name cannot be null")
    @Size(min = 3, max = 50, message = "Customer name must be between 3 and 50 characters")
    private String name;

    @NotBlank(message = "Email cannot be blank")
    @Email(message = "Invalid email format")
    private String email;

    @Pattern(regexp = "\\d{10}", message = "Phone number must be 10 digits")
    private String phoneNumber;

    @Min(value = 18, message = "Age must be at least 18")
    private int age;

    @Past(message = "Date of birth must be in the past")
    private LocalDate dob;
}
```

## Validating DTOs in Controllers

Use **@Valid** to trigger validation on request payloads.

```java
@RestController
@RequestMapping("/customers")
public class CustomerController {

    @PostMapping
    public ResponseEntity<String> createCustomer(
            @Valid @RequestBody CustomerDTO customerDTO) {
        return ResponseEntity.ok("Customer created successfully");
    }
}
```
If validation fails, Spring throws **MethodArgumentNotValidException**.

## Handling Validation Errors Globally

Validation errors should be handled centrally using **@ControllerAdvice**.

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<Map<String, String>> handleValidationExceptions(
            MethodArgumentNotValidException ex) {

        Map<String, String> errors = new HashMap<>();
        ex.getBindingResult().getFieldErrors().forEach(error ->
                errors.put(error.getField(), error.getDefaultMessage())
        );

        return ResponseEntity.badRequest().body(errors);
    }
}

```
This produces a clean, field-level error response.

---

## Custom Validations

When built-in constraints are insufficient, custom validators can be created.

### Step 1: Create a Custom Annotation
```java
@Constraint(validatedBy = PanCardValidator.class)
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
public @interface ValidPan {
    String message() default "Invalid PAN number";
    Class<?>[] groups() default {};
    Class<? extends Payload>[] payload() default {};
}

```

### Step 2: Implement Validation Logic

```java 
public class PanCardValidator
implements ConstraintValidator<ValidPan, String> {

    private static final String PAN_REGEX = "[A-Z]{5}[0-9]{4}[A-Z]{1}";

    @Override
    public boolean isValid(String value, ConstraintValidatorContext context) {
        return value != null && value.matches(PAN_REGEX);
    }
}
```

### Step 3: Use in DTO
```java 

public class UserDTO {

    @ValidPan
    private String panNumber;
}

```

---
## Validating Nested Objects and Collections

Use @Valid on nested objects or collections.

```java
public class BankDTO {

    @NotBlank
    private String bankName;

    @Valid
    @NotEmpty
    private List<@Valid BranchDTO> branches;
}

```
---

## Method-Level Validation Using @Validated

Use **@Validated** at the service layer for method parameter validation.

```java
@Service
@Validated
public class BankService {

    public void updateBankName(@NotBlank String name) {
        // Business logic
    }
}
```
---

## Enabling Validation in Spring Boot

Validation is auto-enabled when the following dependency is present:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```
---

### Alternatives and Design Considerations

- Manual validation (if/else checks) – not scalable
- Database constraints – last line of defense

Bean Validation remains the cleanest and most maintainable approach.

---

## Summery

- Use **@Valid** in controllers for request validation

- Use **@Validated** for method-level validation

- DTOs are preferred over entities for validation

- Always handle validation errors globally

- Custom validators are used for domain-specific rules