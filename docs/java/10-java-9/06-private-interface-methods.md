---
title: Private Interface Methods
parent: Java-9
nav_order: 6
---

# Private Interface Methods

Java 9 allows private methods inside interfaces.

These methods help reuse logic between default methods and static methods.

---

## Why Were They Introduced?

Java 8 introduced:

- Default methods in interfaces.
- Static methods in interfaces.

This was useful, but it created a problem:

```java
interface PaymentValidator {

    default boolean isValidCardPayment(String cardNumber) {
        if (cardNumber == null || cardNumber.trim().isEmpty()) {
            return false;
        }
        return cardNumber.length() == 16;
    }

    default boolean isValidSavedCardPayment(String token) {
        if (token == null || token.trim().isEmpty()) {
            return false;
        }
        return token.startsWith("tok_");
    }
}
```

Common validation logic had to be duplicated.

Java 9 allows shared private methods.

---

## Java 9 Example

```java
interface PaymentValidator {

    default boolean isValidCardPayment(String cardNumber) {
        return hasText(cardNumber) && cardNumber.length() == 16;
    }

    default boolean isValidSavedCardPayment(String token) {
        return hasText(token) && token.startsWith("tok_");
    }

    private boolean hasText(String value) {
        return value != null && !value.trim().isEmpty();
    }
}
```

---

## Private Static Methods

Interfaces can also have private static methods.

```java
interface IdGenerator {

    static String orderId(long id) {
        return prefix("ORD", id);
    }

    static String paymentId(long id) {
        return prefix("PAY", id);
    }

    private static String prefix(String type, long id) {
        return type + "-" + id;
    }
}
```

Private static methods are used by static interface methods.

---

## Rules

- Private interface methods must have a body.
- They cannot be abstract.
- They are not inherited by implementing classes.
- They cannot be called from outside the interface.
- Private instance methods can be used by default methods.
- Private static methods can be used by static methods.
- Private static methods can also be used by default methods if called with normal static syntax.

---

## Daily Coding Use Cases

### 1. Avoid Duplicate Logic In Default Methods

```java
interface AuditFormatter {

    default String createdMessage(String entity, String id) {
        return format("CREATED", entity, id);
    }

    default String deletedMessage(String entity, String id) {
        return format("DELETED", entity, id);
    }

    private String format(String action, String entity, String id) {
        return action + " " + entity + " with id " + id;
    }
}
```

### 2. Keep Helper Methods Hidden

Before Java 9, helper methods often had to be public by accident.

```java
interface ReportExporter {
    default void exportCsv() {
        validate();
        System.out.println("Exporting CSV");
    }

    private void validate() {
        System.out.println("Validation complete");
    }
}
```

`validate` is an implementation detail and should not be part of the public contract.

---

## Best Practices

- Use private interface methods only to remove duplication inside the interface.
- Keep them small and implementation-focused.
- Do not put heavy business logic in interfaces.
- Use abstract classes if shared state or complex implementation is needed.
- Do not expose helper methods as default methods just because private methods were not available before.

---

## Common Mistakes

### Mistake 1: Treating private interface methods as inherited methods

```java
class MyValidator implements PaymentValidator {
    void test() {
        // hasText("abc"); // Not accessible
    }
}
```

Private interface methods are private to the interface.

### Mistake 2: Creating large utility interfaces

If all methods are static helpers, prefer a final utility class.

```java
final class PaymentIds {
    private PaymentIds() {
    }
}
```

---

## Quick Summary

Private interface methods let default and static methods share internal logic without exposing helper methods as part of the interface API.

