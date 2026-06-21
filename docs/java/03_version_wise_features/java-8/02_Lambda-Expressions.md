---
title: Lambda Expressions
parent: Java-8
nav_order: 2
---

# Lambda Expressions in Java 

## 1. Introduction

A **Lambda Expression** is a concise way to represent an **anonymous function** (a function without a name) that can be passed as an argument.

Introduced in **Java 8** to support **functional programming**.

---

## 2. Basic Syntax


> (parameters) -> expression

or

> (parameters) -> { statements }

Examples:

> (int a, int b) -> a + b

> () -> System.out.println("Hello")

> x -> x * x

----

## 3. Functional Interface

A Functional Interface is an interface with only one abstract method.

Example:

```java
@FunctionalInterface
interface MyFunc {
int add(int a, int b);
}
```

Lambda works only with functional interfaces.

## 4. Simple Example

### Without Lambda:

```java
Runnable r = new Runnable() {
public void run() {
System.out.println("Hello");
}
};
```

### With Lambda:

```java
Runnable r = () -> System.out.println("Hello");
```

----

## 5. Common Functional Interfaces

| Interface     | Method    | Description            |
| ------------- | --------- | ---------------------- |
| Runnable      | run()     | No input, no output    |
| Comparator    | compare() | Compare objects        |
| Callable      | call()    | Returns result         |
| Predicate<T>  | test()    | Returns boolean        |
| Function<T,R> | apply()   | Transform input        |
| Consumer<T>   | accept()  | Takes input, no return |
| Supplier<T>   | get()     | Returns value          |


---

## 6. Variable Capture

Lambdas can access variables from outer scope.

> int a = 10;
> Runnable r = () -> System.out.println(a);

Rule:
- Variables must be final or effectively final

------

## 7. Lambda vs Anonymous Class

| Feature        | Lambda                | Anonymous Class       |
| -------------- | --------------------- | --------------------- |
| Syntax         | Short                 | Verbose               |
| `this` keyword | Refers to outer class | Refers to inner class |
| Performance    | Better (optimized)    | Slightly heavier      |
| Compilation    | invokedynamic         | Separate class file   |


## 8. Internal Working

- Uses invokedynamic
- JVM creates function objects dynamically
- No separate .class file like anonymous class

## 9. Lambda with Collections (Streams)
    
```
list.stream()
    .filter(x -> x > 10)
    .forEach(x -> System.out.println(x));
```

## 10. Method References (Shortcut)

Instead of:

> x -> System.out.println(x)

Use:

> System.out::println

## 11. Types of Method References

| Type        | Example             |
| ----------- | ------------------- |
| Static      | Class::staticMethod |
| Instance    | obj::method         |
| Constructor | Class::new          |


## 12. Benefits of Lambda
    
- Reduces boilerplate code
- Improves readability
- Enables functional programming
- Works well with Streams API
- Easier parallel processing

## 13. Performance Insights
   
- Lambdas are not always heap objects
- JVM uses Escape Analysis:
    - May eliminate object creation
    - May inline code
- Non-escaping lambdas → very efficient

## 15. Important Rules
    
- Can only be used with functional interfaces
- Cannot modify captured variables
- Supports type inference
- Parentheses optional for single parameter

