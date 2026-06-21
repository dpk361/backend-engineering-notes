---
title: Default & Static Methods in Java Interfaces
parent: Java-8
nav_order: 4
---

# Default & Static Methods in Java Interfaces

## 1. Introduction

Before Java 8, interfaces could only have:
- Abstract methods
- No method implementations

Java 8 introduced:
- **Default methods**
- **Static methods**

---

## 2. Problem Before Java 8

Interfaces could not be modified without breaking existing implementations.

### Example:
```java
interface List {
    void add(Object o);
}
```

If we add a new method:

> void sort();

❌ All existing classes (ArrayList, LinkedList, etc.) break.


## 3. Default Methods
   
A default method is a method inside an interface with a body.

```java
interface List {
    default void sort() {
        System.out.println("Default sorting");
    }
}
```

### Why Default Methods?

#### ✅ 1. Backward Compatibility
Add new methods without breaking old code

#### ✅ 2. API Evolution
Modify interfaces like:
- Collection
- List
- Iterable

#### ✅ 3. Provide Common Behavior

Share logic across implementations

Example:

```java
interface Vehicle {
    default void start() {
        System.out.println("Vehicle starting");
    }
}

```

----------


## 4. Static Methods in Interfaces

Static methods belong to the interface itself.

```java
interface MathUtil {
    static int add(int a, int b) {
        return a + b;   
    }
}
```

### Usage:

> MathUtil.add(10, 20);

### Why Static Methods?

**1. Utility Methods in Same Place**

Keep related logic inside interface

**2. Better Design (Cohesion)**
Avoid separate utility classes

------------------------

| Feature     | Default Method    | Static Method      |
| ----------- | ----------------- | ------------------ |
| Belongs to  | Object (instance) | Interface          |
| Overridable | Yes               | No                 |
| Access      | obj.method()      | Interface.method() |
| Purpose     | Extend behavior   | Utility/helper     |


------

## 6. Diamond Problem

If multiple interfaces define same default method:

```java
interface A {
    default void show() {}
}

interface B {
    default void show() {}
}

class C implements A, B {
    @override
    public void show() {
        A.super.show(); // calls A's show()
        B.super.show(); // calls B's show()
        System.out.println("Resolved");
    }
}

public class Main {
    public static void main(String[] args) {
        C obj = new C();
        obj.show();
    }
}

```

✔ Must override to resolve ambiguity


## Summary
- Default methods allow adding new functionality safely
- Static methods provide helper utilities inside interfaces
- Both improved flexibility and API design in Java

> Default methods allow interfaces to have method implementations, while static methods provide utility functions within interfaces.

> Static methods in interfaces can be called directly using the interface name, but default methods require an implementing class instance and cannot be called directly.

