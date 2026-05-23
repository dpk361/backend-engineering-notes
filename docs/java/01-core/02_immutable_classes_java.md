---
title: Immutable Classes
parent: Core Concepts
nav_order: 2
---


# Immutable Classes in Java

---

## 1. What is an Immutable Class?
An immutable class is a class whose state cannot be changed after object creation.

👉 Once created:

- No setters
- No field modification
- No internal state changes

---

## 2. Standard Rules to Make a Class Immutable

1. Make class `final` → prevent inheritance
2. Make fields `private` and `final`  
3. No setters      
4. Initialize fields via constructor        
5. Perform defensive copying for mutable fields  
6. Return copies in getters (not original references)

---

## 3. Basic Immutable Class

Example (Only primitives / immutable fields)

```java
public final class Employee {

    private final int id;
    private final String name;

    public Employee(int id, String name) {
        this.id = id;
        this.name = name;
    }

    public int getId() { return id; }
    public String getName() { return name; }
}
```

Why this is immutable:

- int and String are immutable
- No setters
- Fields are final

---

## 4. Immutable Class with Mutable Fields

This is where most people make mistakes.

❌ Problem:

If your class contains mutable objects like:

- Date
- List
- Map

👉 Then immutability can break.

### Solution: Defensive Copying

```java
import java.util.Date;

public final class Employee {

    private final int id;
    private final Date joiningDate;

    public Employee(int id, Date joiningDate) {
        this.id = id;
        this.joiningDate = new Date(joiningDate.getTime());
    }

    public Date getJoiningDate() {
        return new Date(joiningDate.getTime());
    }
}
```

External changes to Date won't affect internal state

---

## 5. Immutable Class with Collections

### Wrong Way:

```
private final List<String> items;

public List<String> getItems() {
return items; // dangerous
}
```

### Correct Way:

```java
import java.util.*;

public final class Order {

    private final List<String> items;

    public Order(List<String> items) {
        this.items = new ArrayList<>(items);
    }

    public List<String> getItems() {
        return Collections.unmodifiableList(items);
    }
    
}
```

---

## 6. Using Unmodifiable Collections

Instead of returning copies every time:

```
return Collections.unmodifiableList(items);
```

- Prevents modification
- More efficient than copying every time

---

## 7. Using Java Records

Introduced in Java 14+

```java
public record Employee(int id, String name) {}
```

✔ Why it's immutable:

- Fields are final
- No setters
- Constructor auto-generated

👉 Best for DTOs

---

## 8. Builder Pattern

Useful when object has many fields.

```java
public final class Employee {

    private final int id;
    private final String name;

    private Employee(Builder builder) {
        this.id = builder.id;
        this.name = builder.name;
    }

    public static class Builder {
        private int id;
        private String name;

        public Builder setId(int id) {
            this.id = id;
            return this;
        }

        public Builder setName(String name) {
            this.name = name;
            return this;
        }

        public Employee build() {
            return new Employee(this);
        }
    }
}
```

---

## 9. Factory Method

Instead of constructors:

```java
public static Employee of(int id, String name) {
    return new Employee(id, name);
}
```

- Helps control object creation
- Can add caching (like Integer.valueOf())

---

## 10. Lombok

If using Lombok:

```java
import lombok.Value;

@Value
public class Employee {
    int id;
    String name;
}
```

✔ Automatically:

- final class
- final fields
- getters
- constructor

---

## 11. Deep Immutability

If object contains nested mutable objects:

👉 You must ensure everything inside is also immutable

Example:

- If Employee has Address
- Then Address must also be immutable

###  11.1. Immutable Address Class

```java
public final class Address {

    private final String city;
    private final String state;

    public Address(String city, String state) {
        this.city = city;
        this.state = state;
    }

    public String getCity() {
        return city;
    }

    public String getState() {
        return state;
    }
}
```
✔ Safe because:

- String is immutable
- No setters
- Class is final

### 11.2 Immutable Employee Class (with Address)

```java
public final class Employee {

    private final int id;
    private final String name;
    private final Address address;

    public Employee(int id, String name, Address address) {
        this.id = id;
        this.name = name;

        // Defensive copy (important mindset)
        this.address = new Address(
            address.getCity(),
            address.getState()
        );
    }

    public int getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public Address getAddress() {
        // Return new object (defensive copy)
        return new Address(
            address.getCity(),
            address.getState()
        );
    }
}
```

### 11.3  What If Address is NOT Immutable? (Critical Case)

❌ Mutable Address (BAD)

```java
public class Address {
    private String city;

    public void setCity(String city) {
        this.city = city;
    }
}
```

### Problem:

```
Address addr = new Address("Hyd");
Employee emp = new Employee(1, "Mohan", addr);

addr.setCity("Bangalore"); // 😱 Employee state changed!
```

### 11.4 Fix for Mutable Address (Mandatory Defensive Copy)

```java
public Employee(int id, String name, Address address) {
    this.address = new Address(address.getCity(), address.getState());
}

public Address getAddress() {
    return new Address(address.getCity(), address.getState());
}
```

> **If your class has reference fields → ensure those objects are also immutable OR defensively copied**

---

## 12. Common Mistakes

❌ Returning mutable reference  
❌ Not copying in constructor  
❌ Not making class final  
❌ Using arrays directly  

---

## 13. Why Immutable Classes Matter
- Thread safety  
- Safe caching  
- Predictable behavior  

---

## 14. Real Examples
- String  
- Integer  
- LocalDate  

---

## 15. Summary

| Approach | Usage |
|----------|------|
| Simple | Primitive/String fields |
| Defensive Copying | Mutable fields |
| Unmodifiable | Collections |
| Record | DTOs |
| Builder | Complex objects |
| Lombok | Boilerplate reduction |

---

## Final Insight
Immutability is about protecting internal state completely.
