---
title: Optional Enhancements
parent: Java-9
nav_order: 5
---

# Optional Enhancements

Java 9 added three useful methods to `Optional`:

- `ifPresentOrElse`
- `or`
- `stream`

These methods make Optional easier to use in real code.

---

## Why Were They Introduced?

Java 8 introduced `Optional`, but some common patterns still required verbose code.

Common needs:

- Run one action when value is present and another when absent.
- Provide another Optional as fallback.
- Use Optional inside Stream pipelines.

Java 9 improved these scenarios.

---

## ifPresentOrElse

Runs one action when the value exists, and another action when it does not.

```
Optional<String> email = Optional.of("user@test.com");

email.ifPresentOrElse(
    value -> System.out.println("Sending email to " + value),
    () -> System.out.println("Email not found")
);
```

Before Java 9:

```
if (email.isPresent()) {
    System.out.println("Sending email to " + email.get());
} else {
    System.out.println("Email not found");
}
```

---

## or

Provides another Optional if the current Optional is empty.

```
Optional<String> primaryEmail = Optional.empty();
Optional<String> backupEmail = Optional.of("backup@test.com");

Optional<String> selectedEmail = primaryEmail.or(() -> backupEmail);

System.out.println(selectedEmail.get()); // backup@test.com
```

This is useful when your fallback operation also returns an Optional.

---

## stream

Converts Optional into a Stream.

- If value is present: stream with one element.
- If value is absent: empty stream.

```
Optional<String> name = Optional.of("Mohan");

List<String> result = name.stream()
    .map(String::toUpperCase)
    .collect(Collectors.toList());

System.out.println(result); // [MOHAN]
```

---

## Daily Coding Example - Flatten Optional Values

Suppose each user may or may not have an email.

```java
class User {
    private final String email;

    User(String email) {
        this.email = email;
    }

    Optional<String> getEmail() {
        return Optional.ofNullable(email);
    }
}
```

Before Java 9:

```java
List<String> emails = users.stream()
    .map(User::getEmail)
    .filter(Optional::isPresent)
    .map(Optional::get)
    .collect(Collectors.toList());
```

Java 9:

```java
List<String> emails = users.stream()
    .map(User::getEmail)
    .flatMap(Optional::stream)
    .collect(Collectors.toList());
```

Much cleaner.

---

## Daily Coding Example - Fallback Lookup

```java
Optional<User> user = findById(id)
    .or(() -> findByEmail(email))
    .or(() -> findByPhone(phone));
```

Each method returns `Optional<User>`.

This avoids nested `if` checks.

---

## Best Practices

- Use `ifPresentOrElse` when both present and absent cases are meaningful.
- Use `or` when fallback also returns Optional.
- Use `Optional.stream` to flatten Optional values inside stream pipelines.
- Avoid `Optional.get()` unless you have already checked presence.
- Do not use Optional as an entity field in JPA.
- Do not use Optional as a method parameter in normal business APIs.
- Prefer Optional as a return type when a value may be absent.

---

## Common Mistakes

### Mistake 1: Using Optional just to call get

```java
String email = user.getEmail().get(); // risky
```

Better:

```java
String email = user.getEmail()
    .orElseThrow(() -> new IllegalStateException("Email is required"));
```

### Mistake 2: Confusing orElse with or

`orElse` returns a raw value.

```java
String email = primaryEmail.orElse("default@test.com");
```

`or` returns another Optional.

```java
Optional<String> email = primaryEmail.or(() -> backupEmail);
```

---

## Quick Summary

Java 9 made Optional more practical by adding present/absent branching, Optional fallback chaining, and clean Stream integration.

