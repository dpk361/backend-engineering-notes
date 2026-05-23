---
title: static import
parent: Core Concepts
nav_order: 1
---


# static import

---

Normally, to use a static member (method/field), we do:

> Math.sqrt(25);

Here, sqrt() is a static method of Math.

## With static import

```
import static java.lang.Math.sqrt;

sqrt(25);  // no class name needed
```

- You can directly use the **method without the class name.**

- You can also import all static members
```
import static java.lang.Math.*;
sqrt(25);
pow(2, 3);
```

---

## Why does static import exist?

It improves **readability and conciseness** when:

- You frequently use static methods/constants
- The class name adds unnecessary noise

---

## Benefits of static import

### 1. Cleaner & shorter code

```
// Without static import
double result = Math.sqrt(Math.pow(3, 2));

// With static import
double result = sqrt(pow(3, 2));
```

### 2. Very useful in testing frameworks

Example with JUnit:

```
import static org.junit.jupiter.api.Assertions.*;

assertEquals(10, result);
assertTrue(value > 0);
```

**Without static import:**

```
Assertions.assertEquals(10, result);
```

### 3. Useful for constants

```
import static java.lang.Integer.MAX_VALUE;

int max = MAX_VALUE;
```

---

## Problems / Issues with static import

### 1. Readability loss (BIGGEST ISSUE)

> sqrt(25);

**Where is this coming from?**

- Math?
- Some utility class?
- Custom method?

❌ Hard to trace origin

### 2. Naming conflicts

```
import static java.lang.Math.max;
import static java.lang.Integer.max; // hypothetical

max(10, 20); // confusion / compile error
```

### 3. Overuse makes code confusing

```
import static java.lang.Math.*;
import static java.util.Collections.*;
import static com.myapp.Utils.*;
```

**Now:**

```
sort(list);
max(a, b);
```

We don’t know:

- which class each method belongs to

---

## Static import vs normal usage

| Aspect          | Normal Import         | Static Import     |
| --------------- | --------------------- | ----------------- |
| Readability     | Clear (class visible) | Can be unclear    |
| Verbosity       | Slightly longer       | Shorter           |
| Maintainability | Better                | Risky if overused |
| Debugging       | Easy                  | Harder            |
| Best use        | Production code       | Tests / constants |

---

## Best Practices

**Use static import when:**

- In JUnit / testing code 
  > import static org.junit.jupiter.api.Assertions.*;
- For well-known constants 
  > import static java.lang.Math.PI;
- When method meaning is obvious 

**Avoid static import when:**

- In business logic (services, controllers)
- When it reduces clarity
- When importing * from multiple classes


> If removing the class name makes the code unclear → DON’T use static import
