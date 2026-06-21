---
title: Optional orElseThrow
parent: Java-10
nav_order: 3
---

# Optional orElseThrow

Java 10 added a no-argument version of:

```
Optional.orElseThrow()
```

It returns the value if present.

If value is missing, it throws:

```text
NoSuchElementException
```

---

## Why Was It Introduced?

Before Java 10, many developers used:

```
optional.get()
```

Problem:

`get()` throws `NoSuchElementException` when Optional is empty, but the method name does not clearly show that risk.

Java 10 made this clearer:

```
optional.orElseThrow()
```

This reads better:

> Give me the value, or throw if it is missing.

---

## Basic Example

```
Optional<String> name = Optional.of("Mohan");

String value = name.orElseThrow();

System.out.println(value); // Mohan
```

Empty Optional:

```java
Optional<String> name = Optional.empty();

String value = name.orElseThrow(); // NoSuchElementException
```

---

## Before Java 10

```java
String value = optional.orElseThrow(NoSuchElementException::new);
```

Java 10:

```java
String value = optional.orElseThrow();
```

---

## Daily Coding Example

```java
public User findRequiredUser(Long userId) {
    return userRepository.findById(userId)
        .orElseThrow();
}
```

This is okay when missing user means a generic programming error.

But in real backend code, a custom exception is often better.

```java
public User findRequiredUser(Long userId) {
    return userRepository.findById(userId)
        .orElseThrow(() -> new UserNotFoundException(userId));
}
```

Why?

Because `UserNotFoundException` gives business meaning.

---

## orElseThrow vs get

| Method                | Empty Optional Behavior         | Developer Readability    |
|-----------------------|---------------------------------|--------------------------|
| `get()`               | Throws `NoSuchElementException` | Risk is hidden           |
| `orElseThrow()`       | Throws `NoSuchElementException` | Risk is clear            |
| `orElseThrow(custom)` | Throws custom exception         | Best for business errors |

Prefer:

```
optional.orElseThrow()
```

over:

```
optional.get()
```

---

## Edge Cases

### Edge Case 1: Empty Optional

```
Optional<String> value = Optional.empty();

value.orElseThrow();
```

Throws:

```text
NoSuchElementException
```

---

### Edge Case 2: Custom Exception Supplier Is Null

This uses the older overloaded method:

```
Optional<String> value = Optional.empty();

value.orElseThrow(null);
```

Throws:

```text
NullPointerException
```

because Optional is empty and Java tries to call the exception supplier.

---

### Edge Case 3: Do Not Use For Normal Optional Flow

Bad:

```
try {
    String email = user.getEmail().orElseThrow();
    sendEmail(email);
} catch (NoSuchElementException e) {
    // ignore
}
```

Better:

```
user.getEmail().ifPresent(this::sendEmail);
```

Use exception only when missing value is really exceptional.

---

## Daily Use Cases

### 1. Required Configuration

```java
String apiKey = Optional.ofNullable(System.getenv("PAYMENT_API_KEY"))
    .orElseThrow(() -> new IllegalStateException("PAYMENT_API_KEY is missing"));
```

Custom exception message is better here.

---

### 2. Required Entity

```java
Order order = orderRepository.findById(orderId)
    .orElseThrow(() -> new OrderNotFoundException(orderId));
```

---

### 3. Internal Logic Where Absence Means Bug

```java
User admin = users.stream()
    .filter(User::isAdmin)
    .findFirst()
    .orElseThrow();
```

This is fine when earlier validation guarantees an admin exists.

---

## Best Practices

- Prefer `orElseThrow()` over `get()`.
- Use custom exception when the error has business meaning.
- Use `orElse`, `orElseGet`, or `ifPresent` when absence is normal.
- Do not catch `NoSuchElementException` just to continue normal flow.
- Add useful messages for production errors.

---

## Quick Summary

Java 10 added no-argument `Optional.orElseThrow()`. It is clearer than `get()`, but for backend business errors, the custom exception version is usually better.

