---
title: Collection Factory Methods
parent: Java-9
nav_order: 3
---

# Collection Factory Methods

Java 9 added convenient factory methods to create small unmodifiable collections.

Main methods:

- `List.of(...)`
- `Set.of(...)`
- `Map.of(...)`
- `Map.ofEntries(...)`

---

## Why Were They Introduced?

Before Java 9, creating small fixed collections was verbose.

### Before Java 9

```
List<String> roles = new ArrayList<>();
roles.add("ADMIN");
roles.add("USER");
roles = Collections.unmodifiableList(roles);
```

Or:

```java
List<String> roles = Arrays.asList("ADMIN", "USER");
```

Problems:

- `Arrays.asList` returns a fixed-size list, not a truly unmodifiable list.
- You cannot add/remove, but you can replace elements.
- `Collections.unmodifiableList` is verbose.
- The underlying list can still be modified if someone keeps a reference.

Java 9 made this simple.

---

## List.of

```java
List<String> roles = List.of("ADMIN", "USER", "AUDITOR");
```

This list is unmodifiable.

```
roles.add("MANAGER"); // UnsupportedOperationException
```

---

## Set.of

```java
Set<String> permissions = Set.of("READ", "WRITE", "DELETE");
```

Duplicate values are not allowed.

```java
Set<String> permissions = Set.of("READ", "READ");
// IllegalArgumentException
```

---

## Map.of

Good for small maps.

```java
Map<Integer, String> statusNames = Map.of(
    200, "OK",
    400, "BAD_REQUEST",
    500, "SERVER_ERROR"
);
```

`Map.of` supports up to 10 key-value pairs.

For more entries, use `Map.ofEntries`.

```java
Map<String, String> countryCodes = Map.ofEntries(
    Map.entry("IN", "India"),
    Map.entry("US", "United States"),
    Map.entry("GB", "United Kingdom")
);
```

---

## Nulls Are Not Allowed

```
List.of("A", null);          // NullPointerException
Set.of("A", null);           // NullPointerException
Map.of("key", null);         // NullPointerException
Map.of(null, "value");       // NullPointerException
```

This is intentional.

It avoids collections where null handling becomes ambiguous.

---

## Daily Coding Use Cases

### 1. Constant Values

```java
private static final Set<String> ALLOWED_STATUSES =
    Set.of("PENDING", "APPROVED", "REJECTED");
```

### 2. Simple Lookup Map

```java
private static final Map<String, Integer> RETRY_LIMITS = Map.of(
    "PAYMENT", 3,
    "NOTIFICATION", 5,
    "REPORT", 1
);
```

### 3. Test Data

```java
List<String> input = List.of("java", "spring", "kafka");
```

### 4. Validation

```java
private static final Set<String> SUPPORTED_CURRENCIES =
    Set.of("INR", "USD", "EUR");

public void validateCurrency(String currency) {
    if (!SUPPORTED_CURRENCIES.contains(currency)) {
        throw new IllegalArgumentException("Unsupported currency: " + currency);
    }
}
```

---

## List.of vs Arrays.asList

| Feature            | `List.of` | `Arrays.asList` |
|--------------------|-----------|-----------------|
| Added in           | Java 9    | Java 1.2        |
| Allows null        | No        | Yes             |
| Add/remove         | No        | No              |
| Replace element    | No        | Yes             |
| Truly unmodifiable | Yes       | No              |

Example:

```
List<String> names = Arrays.asList("A", "B");
names.set(0, "X"); // allowed
```

```
List<String> names = List.of("A", "B");
names.set(0, "X"); // UnsupportedOperationException
```

---

## Best Practices

- Use `List.of`, `Set.of`, and `Map.of` for small fixed data.
- Use them for constants, test input, configuration-like values, and validation sets.
- Do not use them when the collection must be modified later.
- Avoid passing null values.
- Use `Map.ofEntries` when you have more than 10 entries.
- Do not depend on iteration order of `Set.of` or `Map.of`.

---

## Common Mistakes

### Mistake 1: Trying to mutate the collection

```
List<String> names = List.of("A", "B");
names.add("C"); // Runtime exception
```

If you need mutation:

```
List<String> names = new ArrayList<>(List.of("A", "B"));
names.add("C");
```

### Mistake 2: Passing duplicate keys to Map.of

```
Map.of("A", 1, "A", 2); // IllegalArgumentException
```

### Mistake 3: Passing null values

```
List.of("A", null); // NullPointerException
```

---

## Quick Summary

Java 9 collection factory methods create small unmodifiable collections with clean syntax. They are excellent for constants, test data, and lookup values, but they reject nulls and cannot be mutated.


