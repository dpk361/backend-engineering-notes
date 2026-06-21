---
title: Sealed Classes (Java 17)
parent: Java-17
nav_order: 2
---

# Sealed Classes (Finalized in Java 17)

Sealed classes allow you to **restrict which classes can extend or implement them**.

They provide **controlled inheritance**, making your domain model safer and more predictable.

---

## Why Sealed Classes Were Introduced

Before Java 17, inheritance control options were limited:

- `final` → no one can extend
- `public` → anyone can extend

There was no middle ground.

Sealed classes introduce controlled extensibility.

---

## Basic Syntax

```java
public sealed class Shape permits Circle, Rectangle {
}
```

Subclasses must declare one of:

- final
- sealed
- non-sealed

### Example:

```java
public final class Circle extends Shape {
}

public final class Rectangle extends Shape {
}

```

---

## What Does permits Mean?

- **permits** explicitly lists allowed subclasses.
- Only the listed classes can extend the sealed class.

If another class tries:

```java
public class Triangle extends Shape { } // ❌ Compilation error
```

It will fail.

---

## Subclass Rules

A permitted subclass must declare one of:

#### 1. final

```java 
public final class Circle extends Shape { }
```

No further inheritance allowed.

### 2. sealed

```java 
public sealed class Rectangle extends Shape permits Square {
}
```

Continues controlled inheritance.

### 3. non-sealed

```java   
public non-sealed class Circle extends Shape { }
```

Inheritance becomes unrestricted from here.

---

## Rules & Restrictions

✔ Sealed class and permitted subclasses must be in the same module    
✔ Or in the same package (if not using modules)        
✔ Cannot extend outside permitted list
✔ Works with classes and interfaces

---

## Sealed Interfaces Example

```java

public sealed interface Payment permits UpiPayment, CardPayment {
}

public final class UpiPayment implements Payment { }

public final class CardPayment implements Payment { }

```
---

## Sealed vs final vs abstract

| Feature            | final   | abstract | sealed       |
| ------------------ | ------- | -------- | ------------ |
| Can extend?        | ❌ No    | ✔ Yes    | ✔ Restricted |
| Control subclasses | No      | No       | Yes          |
| Domain modeling    | Limited | Flexible | Controlled   |


---

> Sealed classes, introduced in Java 17, allow controlled inheritance by explicitly defining which classes can extend them. 



