---
title: Local Variable Type Inference - var
parent: Java-10
nav_order: 1
---

# Local Variable Type Inference - var

Java 10 introduced `var` for local variables.

`var` means:

> Let the compiler find the type from the right side.

It does **not** mean dynamic typing.

Java is still strongly typed. The type is decided at compile time.

---

## Why Was It Introduced?

Before Java 10, some local variable declarations were repetitive.

```java
ArrayList<String> names = new ArrayList<String>();
```

Even with diamond operator:

```java
ArrayList<String> names = new ArrayList<>();
```

The developer can already see the type from the right side.

Java 10 allows:

```java
var names = new ArrayList<String>();
```

This reduces repeated code, especially in local method logic.

---

## What Does It Do?

The compiler looks at the initializer and decides the type.

```java
var name = "Mohan";                // String
var age = 30;                      // int
var amount = BigDecimal.TEN;       // BigDecimal
var names = new ArrayList<String>(); // ArrayList<String>
```

After the type is decided, it cannot change.

```
var count = 10; // int
count = 20;     // valid
count = "20";   // compile-time error
```

---

## Where Can We Use var?

### 1. Local Variables Inside Methods

```java
public void processOrder() {
    var orderId = "ORD-101";
    var totalAmount = BigDecimal.valueOf(1500);
}
```

### 2. For Loop Variables

```
for (var i = 0; i < 5; i++) {
    System.out.println(i);
}
```

### 3. Enhanced For Loop Variables

```
List<String> names = List.of("A", "B", "C");

for (var name : names) {
    System.out.println(name.toUpperCase());
}
```

### 4. Try-With-Resources Local Variable

```
try (var reader = Files.newBufferedReader(path)) {
    System.out.println(reader.readLine());
}
```

---

## Where We Cannot Use var

### 1. Fields

```java
class User {
    var name = "Mohan"; // compile-time error
}
```

Use explicit type:

```java
class User {
    private String name = "Mohan";
}
```

### 2. Method Parameters

```java
public void save(var user) { // compile-time error
}
```

Use explicit type:

```java
public void save(User user) {
}
```

### 3. Method Return Type

```java
public var getName() { // compile-time error
    return "Mohan";
}
```

Use explicit type:

```java
public String getName() {
    return "Mohan";
}
```

### 4. Variables Without Initial Value

```java
var name; // compile-time error
```

The compiler cannot guess the type.

### 5. Direct null Assignment

```java
var value = null; // compile-time error
```

The compiler cannot know the intended type.

Use explicit type:

```java
String value = null;
```

---

## Important Edge Cases

### Edge Case 1: var With Empty Diamond

```java
var list = new ArrayList<>();
```

This may infer:

```
ArrayList<Object>
```

That may not be what you wanted.

Better:

```java
var names = new ArrayList<String>();
```

Or:

```java
List<String> names = new ArrayList<>();
```

Use explicit type when generic type is important.

---

### Edge Case 2: var Uses Concrete Type

```java
var users = new ArrayList<User>();
```

The type is:

```
ArrayList<User>
```

Not:

```
List<User>
```

If you want to program to interface, write:

```java
List<User> users = new ArrayList<>();
```

---

### Edge Case 3: Numeric Type Can Surprise You

```java
var id = 100;
```

Type is `int`.

If you need `long`:

```java
var id = 100L;
```

Example:

```
var amount = 10;  // int
amount = 10.5;    // compile-time error
```

---

### Edge Case 4: Lambda Needs Explicit Target Type

This does not work:

```java
var task = () -> System.out.println("Hello"); // compile-time error
```

Reason:

A lambda needs a target functional interface.

Use:

```java
Runnable task = () -> System.out.println("Hello");
```

---

### Edge Case 5: Method Reference Needs Explicit Target Type

This does not work:

```java
var printer = System.out::println; // compile-time error
```

Use:

```java
Consumer<String> printer = System.out::println;
```

---

### Edge Case 6: Array Initializer Needs Explicit Type

This does not work:

```java
var numbers = {1, 2, 3}; // compile-time error
```

Use:

```java
var numbers = new int[] {1, 2, 3};
```

Or:

```java
int[] numbers = {1, 2, 3};
```

---

### Edge Case 7: Multiple Variables Are Not Allowed

```java
var a = 10, b = 20; // compile-time error
```

Use separate lines:

```java
var a = 10;
var b = 20;
```

---

## Daily Coding Examples

### 1. Good Use - Type Is Clear

```java
var user = new User("Mohan", "mohan@test.com");
var amount = BigDecimal.valueOf(5000);
var createdAt = LocalDateTime.now();
```

The right side clearly shows the type.

---

### 2. Good Use - Long Generic Type

```java
var usersByDepartment = new HashMap<String, List<User>>();
```

This avoids noisy generic declarations.

---

### 3. Avoid - Right Side Is Not Clear

```java
var result = service.process(input);
```

What is `result`?

- `User`?
- `OrderResponse`?
- `boolean`?
- `List<Order>`?

Better:

```java
OrderResponse result = service.process(input);
```

---

### 4. Avoid - Weak Variable Name

```java
var data = repository.findAll();
```

Better:

```java
var users = repository.findAll();
```

Or:

```java
List<User> users = repository.findAll();
```

---

## var Is Not JavaScript var

In JavaScript, `var` is dynamic and flexible.

In Java:

```
var name = "Mohan"; // compiler decides String
name = 100;         // compile-time error
```

Java `var` is only a shortcut for local variable type writing.

---

## Best Practices

- Use `var` when the right side clearly shows the type.
- Use clear variable names.
- Avoid `var` when the method name hides the return type.
- Use explicit type when programming to an interface matters.
- Use explicit type for `null`, lambdas, method references, and array initializers.
- Be careful with empty generic constructors like `new ArrayList<>()`.
- Do not use `var` just to make code shorter if it makes code harder to read.

---

## Quick Summary

`var` reduces local variable boilerplate. It is compile-time type inference, not dynamic typing. Use it when it improves readability, and avoid it when it hides important type information.

