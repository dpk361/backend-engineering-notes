---
title: Module System
parent: Java-9
nav_order: 1
---

# Java 9 Module System

The Java Platform Module System (JPMS) is the most important feature in Java 9.

It allows Java applications and the JDK itself to be split into **modules** with explicit dependencies and controlled visibility.

---

## Why Was It Introduced?

Before Java 9, Java applications mainly used the **classpath**.

Classpath problems:

- No clear dependency declaration.
- Any public class could be accessed from anywhere.
- Large applications became hard to understand.
- The JDK runtime was one large image with `rt.jar` and many APIs bundled together.

Java 9 introduced modules to solve these problems:

- Reliable dependencies.
- Strong encapsulation.
- Smaller custom runtimes.
- Better platform maintainability.

---

## What Is A Module?

A module is a named group of packages, resources, and a module descriptor.

The descriptor file is:

```text
module-info.java
```

Example:

```java
module com.example.orders {
    requires java.sql;
    requires com.example.payments;

    exports com.example.orders.api;
}
```

Meaning:

- `com.example.orders` is the module name.
- It depends on `java.sql`.
- It depends on `com.example.payments`.
- It exposes only `com.example.orders.api` to other modules.
- Other packages remain internal.

---

## Classpath vs Module Path

### Classpath

```bash
java -cp app.jar;lib/* com.example.Main
```

Classpath behavior:

- JARs are loaded as a flat set.
- No strong dependency graph.
- Public classes are globally accessible.

### Module Path

```bash
java --module-path mods --module com.example.orders/com.example.orders.Main
```

Module path behavior:

- Modules are resolved by name.
- Dependencies must be declared.
- Only exported packages are visible.

---

## Basic Module Keywords

### requires

Declares dependency on another module.

```java
module com.example.orders {
    requires java.sql;
}
```

### exports

Makes a package visible to other modules.

```java
module com.example.orders {
    exports com.example.orders.api;
}
```

Without `exports`, even public classes are not accessible outside the module.

### exports to

Exports a package only to selected modules.

```java
module com.example.orders {
    exports com.example.orders.internal.api to com.example.billing;
}
```

### opens

Allows deep reflection at runtime.

Useful for frameworks such as Jackson, Hibernate, Spring, and testing libraries.

```java
module com.example.orders {
    opens com.example.orders.entity to com.fasterxml.jackson.databind;
}
```

### opens module

Allows reflection on all packages in the module.

```java
open module com.example.orders {
    requires java.sql;
}
```

Use this carefully. It gives up strong reflection encapsulation.

### uses and provides

Used with `ServiceLoader`.

```java
module com.example.notification {
    uses com.example.notification.spi.MessageSender;
}
```

```java
module com.example.email {
    requires com.example.notification;

    provides com.example.notification.spi.MessageSender
        with com.example.email.EmailSender;
}
```

---

## Daily Coding Example

Suppose an order service has API classes, internal service classes, and JPA entities.

```text
src/
  com.example.orders/
    module-info.java
    com/example/orders/api/OrderController.java
    com/example/orders/service/OrderService.java
    com/example/orders/entity/OrderEntity.java
```

`module-info.java`:

```java
module com.example.orders {
    requires java.sql;
    requires com.fasterxml.jackson.databind;

    exports com.example.orders.api;

    opens com.example.orders.entity to com.fasterxml.jackson.databind;
}
```

This design says:

- Other modules can call the API package.
- The service package stays internal.
- Jackson can use reflection on entity classes.

---

## Automatic Modules

Many older JARs do not have `module-info.java`.

When such a JAR is placed on the module path, Java treats it as an **automatic module**.

Example:

```bash
java --module-path libs --module com.example.app/com.example.Main
```

If `libs/legacy-utils.jar` has no module descriptor, Java derives a module name from the JAR name.

Best practice:

- Do not depend long-term on generated automatic module names.
- Prefer libraries that define `Automatic-Module-Name` in their manifest or provide real module descriptors.

---

## Unnamed Module

Code on the classpath belongs to the **unnamed module**.

This helps migration:

- Existing classpath applications can still run.
- You can migrate gradually.
- You do not need to modularize everything on day one.

---

## Common Migration Commands

### Find JDK Internal API Usage

```bash
jdeps --jdk-internals app.jar
```

This helps identify code that uses internal APIs such as `sun.*`.

### Temporarily Export An Internal Package

```bash
java --add-exports java.base/sun.nio.ch=ALL-UNNAMED -jar app.jar
```

This is a temporary migration workaround, not a clean solution.

### Temporarily Open A Package For Reflection

```bash
java --add-opens java.base/java.lang=ALL-UNNAMED -jar app.jar
```

This is commonly seen when old reflection-heavy libraries run on newer Java versions.

---

## Best Practices

- Keep module names stable and globally unique, like package names.
- Export only API packages.
- Keep implementation packages unexported.
- Use `opens` only for packages that truly need reflection.
- Avoid `open module` unless the whole module is framework-driven.
- Use `jdeps` before migrating a large application.
- Do not depend on JDK internal APIs.
- Migrate incrementally: classpath first, then modules where useful.

---

## Common Mistakes

### Mistake 1: Thinking public means globally visible

In JPMS, this class is public:

```java
public class InternalHelper { }
```

But it is not visible outside the module unless its package is exported.

### Mistake 2: Using exports for reflection

`exports` is for compile-time and normal runtime access.

Reflection frameworks usually need `opens`.

### Mistake 3: Modularizing a Spring Boot app too early

Many backend applications still run perfectly on the classpath.

Learn modules because Java and the JDK are modular, but do not force JPMS into every application unless it solves a real problem.

---

## Quick Summary

The Java 9 module system makes dependencies explicit and hides internal packages by default. It improves maintainability, security, and runtime packaging, but large existing backend systems should usually migrate gradually.

