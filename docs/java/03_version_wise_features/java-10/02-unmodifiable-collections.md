---
title: Unmodifiable Collections
parent: Java-10
nav_order: 2
---

# Unmodifiable Collections

Java 10 improved unmodifiable collections.

Java 9 gave us:

```
List.of("A", "B")
Set.of("READ", "WRITE")
Map.of("IN", "India")
```

Java 10 added:

- `List.copyOf(...)`
- `Set.copyOf(...)`
- `Map.copyOf(...)`
- `Collectors.toUnmodifiableList()`
- `Collectors.toUnmodifiableSet()`
- `Collectors.toUnmodifiableMap()`

---

## Why Were They Introduced?

Java 9 made it easy to create fixed values.

But developers also need to create unmodifiable collections from:

- Existing mutable collections.
- Stream results.
- Method arguments.
- Repository or API results.

Before Java 10:

```java
List<String> copy = Collections.unmodifiableList(new ArrayList<>(names));
```

Java 10 made this cleaner:

```java
List<String> copy = List.copyOf(names);
```

---

## What Does Unmodifiable Mean?

You cannot add, remove, or replace elements in the returned collection.

```
List<String> names = List.copyOf(List.of("A", "B"));

names.add("C"); // UnsupportedOperationException
```

Important:

Unmodifiable does not mean deep immutable.

If the objects inside the collection are mutable, their internal state can still change.

```
class User {
    String name;
}

User user = new User();
user.name = "A";

List<User> users = List.copyOf(List.of(user));

user.name = "B"; // users still points to same User object
```

---

## List.copyOf

```
List<String> mutableNames = new ArrayList<>();
mutableNames.add("Mohan");
mutableNames.add("Ravi");

List<String> safeNames = List.copyOf(mutableNames);
```

Now:

```
safeNames.add("John"); // UnsupportedOperationException
```

If original list changes:

```
mutableNames.add("Anil");
```

`safeNames` does not change.

---

## Set.copyOf

```
List<String> roles = List.of("ADMIN", "USER", "ADMIN");

Set<String> uniqueRoles = Set.copyOf(roles);

System.out.println(uniqueRoles); // contains ADMIN and USER
```

If input has duplicates, only one duplicate value is kept.

The kept element is arbitrary when duplicates are equal.

---

## Map.copyOf

```
Map<String, Integer> retryCount = new HashMap<>();
retryCount.put("PAYMENT", 3);
retryCount.put("EMAIL", 5);

Map<String, Integer> safeRetryCount = Map.copyOf(retryCount);
```

Now:

```
safeRetryCount.put("SMS", 2); // UnsupportedOperationException
```

---

## Nulls Are Not Allowed

These methods reject null.

```
List.copyOf(Arrays.asList("A", null)); // NullPointerException
Set.copyOf(Arrays.asList("A", null));  // NullPointerException
```

For maps:

```
Map<String, String> map = new HashMap<>();
map.put("A", null);

Map.copyOf(map); // NullPointerException
```

Null key also fails:

```
map.put(null, "value");
Map.copyOf(map); // NullPointerException
```

---

## Stream Collectors

### toUnmodifiableList

```java
List<String> activeUsers = users.stream()
    .filter(User::isActive)
    .map(User::getName)
    .collect(Collectors.toUnmodifiableList());
```

The result cannot be changed.

```
activeUsers.add("New User"); // UnsupportedOperationException
```

---

### toUnmodifiableSet

```java
Set<String> departments = users.stream()
    .map(User::getDepartment)
    .collect(Collectors.toUnmodifiableSet());
```

If duplicate values appear, the set keeps one value.

Set order is not guaranteed.

---

### toUnmodifiableMap

```java
Map<Long, String> userNamesById = users.stream()
    .collect(Collectors.toUnmodifiableMap(
        User::getId,
        User::getName
    ));
```

If duplicate keys appear:

```
users.stream()
    .collect(Collectors.toUnmodifiableMap(
        User::getDepartment,
        User::getName
    ));
```

This throws:

```text
IllegalStateException
```

because multiple users can belong to the same department.

Use merge function:

```java
Map<String, String> oneUserByDepartment = users.stream()
    .collect(Collectors.toUnmodifiableMap(
        User::getDepartment,
        User::getName,
        (first, second) -> first
    ));
```

---

## Daily Coding Use Cases

### 1. Defensive Copy In Constructor

```java
public class Order {
    private final List<String> items;

    public Order(List<String> items) {
        this.items = List.copyOf(items);
    }

    public List<String> getItems() {
        return items;
    }
}
```

This protects `Order` from outside changes to the original list.

---

### 2. Safe Return From Service

```java
public List<String> supportedCurrencies() {
    return List.copyOf(currencies);
}
```

The caller cannot modify your internal collection.

---

### 3. Stream Result That Should Not Change

```java
public Set<String> activeUserEmails() {
    return users.stream()
        .filter(User::isActive)
        .map(User::getEmail)
        .collect(Collectors.toUnmodifiableSet());
}
```

---

## List.copyOf vs Collections.unmodifiableList

```java
public static void main(String[] args) {

    List<String> original = new ArrayList<>();
    original.add("A");

    List<String> view = Collections.unmodifiableList(original);
    List<String> copy = List.copyOf(original);

    original.add("B");

    System.out.println(view); // [A, B]
    System.out.println(copy); // [A]
}
```

`Collections.unmodifiableList` is a read-only view of the original list.

`List.copyOf` creates an unmodifiable copy.

---

## Best Practices

- Use `copyOf` for defensive copies.
- Use unmodifiable collectors when stream result should not be changed.
- Do not pass null values.
- Do not expect `Set` or `Map` iteration order unless a specific implementation guarantees it.
- Do not use these methods if the caller is expected to add or remove items.
- Remember: collection is unmodifiable, but objects inside may still be mutable.
- Do not rely on object identity of returned collections.

---

## Common Exceptions

| Case                                                        | Exception                       |
|-------------------------------------------------------------|---------------------------------|
| Modify returned list/set/map                                | `UnsupportedOperationException` |
| Null element in `List.copyOf`                               | `NullPointerException`          |
| Null element in `Set.copyOf`                                | `NullPointerException`          |
| Null key/value in `Map.copyOf`                              | `NullPointerException`          |
| Duplicate key in `toUnmodifiableMap` without merge function | `IllegalStateException`         |
| Null key/value from `toUnmodifiableMap` mapping function    | `NullPointerException`          |

---

## Quick Summary

Java 10 made unmodifiable collection creation easier. Use `copyOf` to protect existing collections and `Collectors.toUnmodifiable...` when stream results should not be changed.

