---
title: Records (Java 16+)
parent: Java-17
nav_order: 1
---

# Records (From Java 16, Enterprise Usage in 17)

Records are a special type of class designed to model **immutable data carriers**.

They reduce boilerplate code and make intent explicit:
> This class is only for holding data.

---

## Why Records Were Introduced

Before records, creating a simple data class required:

- Fields
- Constructor
- Getters
- equals()
- hashCode()
- toString()

Example (Traditional Class):

```java
public class User {
    private final String name;
    private final int age;

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() { return name; }
    public int getAge() { return age; }

    @Override
    public boolean equals(Object o) { /* boilerplate */ }

    @Override
    public int hashCode() { /* boilerplate */ }

    @Override
    public String toString() { /* boilerplate */ }
}
```

## Record Syntax

```java
public record User(String name, int age) { }
```

That’s it.

Java automatically generates:

- Private final fields
- Public constructor
- Getter methods (name(), age())
- equals()
- hashCode()
- toString()

---

## Records Are Immutable

Fields are:

- final
- Cannot be modified

```
User user = new User("Mohan", 30);

user.name = "New";  // ❌ Compilation error

```

Immutability provides:    

✔ Thread safety    
✔ Safer design     
✔ Predictable behavior     

---

## Custom Constructor (Validation)

You can add validation logic.

```java
public record Account(String accountNumber, double balance) {

    public Account {
        if (balance < 0) {
            throw new IllegalArgumentException("Balance cannot be negative");
        }
    }
}
```

This is called a **compact constructor.**

---

## Custom Methods Allowed

Records can have methods.

```java
public record Account(String accountNumber, double balance) {

    public boolean isActive() {
        return balance > 0;
    }
}
```

But records:

❌ Cannot have instance fields (other than components)      
❌ Cannot extend other classes      
✔ Can implement interfaces      

---

## Records with Collections

```java
public record Order(String orderId,List<String> items) { }
```

Important:

- The reference is immutable,
- but the List itself can still be modified.
- For full immutability:
    > List.copyOf(items);

---

## When to Use Records

✔ DTOs      
✔ API responses     
✔ Event payloads     
✔ Value objects     
✔ Immutable configuration objects     


---

> Records are immutable data carrier classes introduced in Java 16.
> They automatically generate constructor, getters, equals(), hashCode(), and toString(), reducing boilerplate and improving code readability.
