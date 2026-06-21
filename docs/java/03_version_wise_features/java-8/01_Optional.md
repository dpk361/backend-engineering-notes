---
title: Optional
parent: Java-8
nav_order: 1
---

# Java Optional

---

# 1. Why Optional Exists

`Optional<T>` is introduced to represent **a value that may or may not be present**.

It helps:
- Avoid accidental `NullPointerException`
- Make absence explicit
- Improve API readability
- Enable functional-style transformations

It is **not** a replacement for null everywhere.

---

# 2. Where To Use Optional

## Recommended Usage
- Method return types
- Repository/service layer
- Stream pipelines
- Value transformation chains

Example:
```java
Optional<Employee> findById(int id);
```

## Avoid Using In

- Entity fields
- DTO fields
- Method parameters
- Serialization models
- JPA entities

Optional is for API boundaries, not internal model design.

---

# 3. Creating Optional

---

## 3.1 Optional.of(value)
   
```java
Optional<Employee> emp = Optional.of(employee);
```
- Does NOT allow null.
- If value(employee) is null → throws NullPointerException.
- Use only when 100% sure value is non-null.

#### Example:

```text
Optional.of(null); // ❌ Throws NullPointerException
```

## 3.2 Optional.ofNullable(value)

```java
Optional<Employee> emp = Optional.ofNullable(employee);
```

- Allows null.
- If value is null → returns Optional.empty().
- If value is not null → wraps inside Optional.

#### Example:

```text
Optional.ofNullable(null);
// Result → Optional.empty
```

## 3.3 Optional.empty()

```java
Optional<Employee> empty = Optional.empty();
```

- Represents absence of value.
- isPresent() → false
- toString() → "Optional.empty"

#### Example:

```
Optional.empty().isPresent();
// false
```

---

# 4. Checking Value Presence

---

## 4.1 isPresent()

> optional.isPresent();

- Returns true if value exists.
- Returns false if empty.
- Not preferred (leads to imperative style).

## 4.2 ifPresent(Consumer)

> optional.ifPresent(emp -> System.out.println(emp.getName()));

- Executes code only if value exists.
- If empty → does nothing.

## 4.3 ifPresentOrElse() (Java 9+)

```text
optional.ifPresentOrElse(emp -> System.out.println(emp.getName()),
() -> System.out.println("Not found")
);
```

- Executes first block if present.
- Executes second block if empty.

---

# 5. Transforming Optional

---

## 5.1 map(Function)


```java
Optional<String> dept = optionalEmployee.map(Employee::getDepartment);
```

- Applies function if value exists.
- If empty → remains empty.
- If mapper returns null → result becomes Optional.empty().

Example:

> Optional.of("ABC").map(String::toLowerCase);     
> // Optional[abc]


## 5.2 flatMap(Function)

Used when function already returns Optional.

```java
Optional<String> phone = optionalEmployee.flatMap(emp ->
Optional.ofNullable(emp.getPhoneNumber()));
```

- Prevents nested Optional.
- If empty → remains empty.

Without flatMap:        

`Optional<Optional<String>>`

With flatMap:      

`Optional<String>` 


## 5.3 filter(Predicate)

> optionalEmployee.filter(Employee::isActive);

- Keeps value if condition true.
- If condition false → becomes empty.
- If already empty → stays empty.

Example:

> Optional.of(10).filter(n -> n > 20);       
> // Optional.empty

---

# 6. Retrieving Values

---

## 6.1 get()

> optional.get();

- Returns value if present.
- If empty → throws NoSuchElementException.
- Not recommended unless absolutely certain.

#### Example:

> Optional.empty().get();    
> // ❌ NoSuchElementException


## 6.2 orElse(T other)

> optional.orElse(defaultValue);

- Returns value if present.
- If empty → returns defaultValue.
- ⚠ Always evaluates defaultValue (even if not needed).

####cExample:

> Optional.of("ABC").orElse(expensiveMethod());     
> // expensiveMethod() still executes

## 6.3 orElseGet(Supplier)

> optional.orElseGet(() -> createDefault());

- Lazy evaluation.
- Supplier runs only if empty.
- Preferred over orElse() when default creation is expensive.

## 6.4 orElseThrow()

> optional.orElseThrow(() -> new RuntimeException("Not found"));

- Returns value if present.
- If empty → throws provided exception.
- Best practice in service layer.

---

# 7. Response Behavior Summary

| Expression                                  | Result                   |
| ------------------------------------------- | ------------------------ |
| `Optional.of(null)`                         | ❌ NullPointerException   |
| `Optional.ofNullable(null)`                 | Optional.empty           |
| `Optional.empty().isPresent()`              | false                    |
| `Optional.empty().get()`                    | ❌ NoSuchElementException |
| `Optional.of("A").map(String::toLowerCase)` | Optional[a]              |
| `Optional.of(10).filter(n -> n > 20)`       | Optional.empty           |
| `Optional.of("A").orElse("B")`              | A                        |
| `Optional.empty().orElse("B")`              | B                        |

---

# 8. Real Service Example

```java

  public String getEmployeeDepartment(int id) {
       return employeeRepository.findById(id)
       .filter(Employee::isActive)
       .map(Employee::getDepartment)
       .orElseThrow(() ->
       new IllegalArgumentException("Active employee not found"));
   }
```

Pipeline Flow:

- Find employee
- Check active
- Extract department
- Throw if missing

No null checks required.

---

## 9. Best Practices

| Scenario               | Use Optional?     |
| ---------------------- | ----------------- |
| Repository return type | ✅ Yes             |
| Service return type    | ✅ Yes             |
| Entity field           | ❌ No              |
| DTO field              | ❌ No              |
| Method parameter       | ❌ No              |
| Stream transformation  | ✅ Yes             |
| Throw if missing       | ✅ Use orElseThrow |

---


