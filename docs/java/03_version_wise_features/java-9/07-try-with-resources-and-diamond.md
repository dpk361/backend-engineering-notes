---
title: Try-With-Resources and Diamond
parent: Java-9
nav_order: 7
---

# Try-With-Resources and Diamond Enhancements

Java 9 included small language improvements from **Project Coin**.

Important daily-use improvements:

- Try-with-resources can use effectively final variables.
- Diamond operator can be used with anonymous inner classes.
- `_` became a reserved keyword and can no longer be used as an identifier.

---

## 1. Try-With-Resources Enhancement

### Why Was It Introduced?

Java 7 introduced try-with-resources:

```
try (BufferedReader reader = Files.newBufferedReader(path)) {
    return reader.readLine();
}
```

But if you already had a resource variable, Java 7/8 forced you to redeclare it:

```
BufferedReader reader = Files.newBufferedReader(path);

try (BufferedReader r = reader) {
    return r.readLine();
}
```

This was unnecessary.

---

## Java 9 Improvement

Java 9 allows an existing final or effectively final variable in try-with-resources.

```
BufferedReader reader = Files.newBufferedReader(path);

try (reader) {
    return reader.readLine();
}
```

The resource will be closed automatically.

---

## Effectively Final Rule

This works:

```
BufferedReader reader = Files.newBufferedReader(path);

try (reader) {
    System.out.println(reader.readLine());
}
```

This does not work:

```
BufferedReader reader = Files.newBufferedReader(path);
reader = Files.newBufferedReader(otherPath);

try (reader) {
    System.out.println(reader.readLine());
}
```

The variable must not be reassigned.

---

## Daily Coding Example

```java
public String readFirstLine(Path path) throws IOException {
    BufferedReader reader = Files.newBufferedReader(path);

    try (reader) {
        return reader.readLine();
    }
}
```

Useful when resource creation is separated from usage.

---

## 2. Diamond Operator With Anonymous Classes

### Before Java 9

The diamond operator was not allowed with anonymous inner classes.

```java
Comparator<String> comparator = new Comparator<String>() {
    @Override
    public int compare(String a, String b) {
        return a.length() - b.length();
    }
};
```

### Java 9

```java
Comparator<String> comparator = new Comparator<>() {
    @Override
    public int compare(String a, String b) {
        return a.length() - b.length();
    }
};
```

In modern Java, this is often replaced with lambdas:

```java
Comparator<String> comparator = Comparator.comparingInt(String::length);
```

But the Java 9 improvement helps when an anonymous class is still needed.

---

## When Anonymous Classes Are Still Useful

Use an anonymous class instead of lambda when:

- You need to override multiple methods.
- You need a distinct class body.
- You need different `this` behavior.

Example:

```java
abstract class Handler<T> {
    abstract void handle(T value);

    void log(T value) {
        System.out.println("Handling " + value);
    }
}

Handler<String> handler = new Handler<>() {
    @Override
    void handle(String value) {
        log(value);
    }
};
```

---

## 3. Underscore Is Reserved

Before Java 9, `_` could be used as a variable name, though it was discouraged.

```java
String _ = "temporary";
```

In Java 9, this is an error.

Use meaningful names instead:

```java
String ignoredValue = "temporary";
```

---

## Best Practices

- Use enhanced try-with-resources when the resource variable already exists.
- Keep resource variables effectively final.
- Prefer lambdas to anonymous classes for functional interfaces.
- Use diamond with anonymous classes only when anonymous classes are truly needed.
- Do not use `_` as an identifier.

---

## Quick Summary

Java 9 made small language improvements that reduce boilerplate: try-with-resources can use effectively final variables, anonymous classes can use diamond where type inference is safe, and `_` is no longer allowed as an identifier.

