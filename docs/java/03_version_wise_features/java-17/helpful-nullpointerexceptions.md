---
title: Helpful NullPointerExceptions (Java 14+)
parent: Java-17
nav_order: 6
---

# Java 17 – Helpful NullPointerExceptions (JEP 358)

Java 14 introduced **Helpful NullPointerExceptions** (JEP 358), and this feature is fully available in **Java 17 (LTS)**.

It enhances `NullPointerException` messages by clearly identifying **which variable or expression was null**, making debugging significantly easier—especially in complex backend systems.

---

### Problem in Older Java Versions

In Java versions prior to 14, `NullPointerException` messages were vague.

### Example

```java
String city = user.getAddress().getCity();
```

If getAddress() returned null, the error would be:

```
Exception in thread "main" java.lang.NullPointerException
```

The message does not indicate:

- Which object was null
- Which method invocation failed
- Where the issue occurred in the object chain

This made debugging time-consuming.

### Improvement in Java 17

Using the same example:

```java
String city = user.getAddress().getCity();
```
Now the error message becomes:

```
Exception in thread "main" java.lang.NullPointerException:
Cannot invoke "Address.getCity()" because "user.getAddress()" is null
```

**Key Benefit:**    

The JVM now clearly identifies:

- The exact method call that failed
- The specific expression that evaluated to null

---

## How It Works

The JVM performs bytecode analysis at runtime to determine:

- The full failing expression
- Which intermediate variable was null
- Whether the failure occurred due to:
  - Field access
  - Method invocation
  - Array access

No code changes are required from the developer.

---

## Examples

### 1. Deep Object Graph (Typical in Spring Boot)

```java
String email = order.getCustomer().getProfile().getEmail();
```

Error message:

```text
Cannot invoke "Profile.getEmail()" because "order.getCustomer().getProfile()" is null
```

Now it is immediately clear that:

- order is not null
- customer is not null
- profile is null

### 2. Field Access

```java
int length = user.name.length();
```

If user is null:

```
Cannot read field "name" because "user" is null.
```

If name is null:

```
Cannot invoke "String.length()" because "user.name" is null
```

### 3. Array Access

```java
int size = arr[0].length();
```

If arr is null:

```
Cannot load from object array because "arr" is null
```

If arr[0] is null:

```
Cannot invoke "String.length()" because "arr[0]" is null
```

---

## Is It Enabled by Default?

| Java Version | Default Behavior                                             |
| ------------ | ------------------------------------------------------------ |
| Java 14–15   | Required JVM flag: `-XX:+ShowCodeDetailsInExceptionMessages` |
| Java 16+     | Enabled by default                                           |
| Java 17 LTS  | Enabled by default                                           |


---

## Limitations

- This feature does not prevent NullPointerException.
- It only improves the error message.
- Proper null handling (validation, defensive coding, Optional, etc.) is still required.


---

## Summary

- Introduced in Java 14 (JEP 358)
- Fully supported in Java 17 (LTS)
- Enabled by default in Java 16+
- Improves debugging clarity
- No code changes required
- Highly beneficial in backend and microservice architectures

