---
title: Functional Interfaces
parent: Java-8
nav_order: 3
---

# Functional Interfaces in Java

## 1. What is a Functional Interface?

A **Functional Interface** is an interface that contains:

> ✅ Exactly **one abstract method**

It can have:
- Multiple **default methods**
- Multiple **static methods**

### Example:
```java
@FunctionalInterface
interface MyFunc {
    int add(int a, int b);
}
```

## 2. Why Functional Interfaces?

They enable:

- Lambda expressions
- Method references
- Functional programming in Java

## 3. Before Java 8 vs After Java 8

### Before Java 8

- No lambda expressions
- Used anonymous classes
- Interfaces were mainly:
  - Marker interfaces
  - Callback-style interfaces

#### Example interfaces:

| Interface  | Method      | Purpose               |
| ---------- | ----------- | --------------------- |
| Runnable   | run()       | Thread execution      |
| Callable   | call()      | Task returning result |
| Comparator | compare()   | Sorting               |
| Comparable | compareTo() | Natural ordering      |


👉 Problems:

- Verbose code
- Hard to read
- Boilerplate-heavy

### After Java 8

Introduced:
- Lambda expressions
- Streams API
- Functional interfaces (java.util.function)

👉 Goal:

- Write concise, expressive code


----

## 4. Built-in Functional Interfaces (java.util.function)

--------------------

### 1. Predicate<T>

> boolean test(T t);

#### Purpose:
Test a condition (returns boolean)

#### Example:

> Predicate<Integer> isEven = x -> x % 2 == 0;

--------

### 2. Function<T, R>
> R apply(T t);

#### Purpose:

Transform input → output

#### Example:

> Function<Integer, Integer> square = x -> x * x;

---------------------

### 3. Consumer<T>

> void accept(T t);

#### Purpose:

Takes input, no return

#### Example:

> Consumer<String> print = x -> System.out.println(x);

-----------------------

### 4. Supplier<T>

> T get();

#### Purpose:

Returns value, no input

#### Example:

> Supplier<Double> random = () -> Math.random();

-----------------

### 5. Bi-Functional Interfaces

| Interface         | Method      | Purpose            |
| ----------------- | ----------- | ------------------ |
| BiPredicate<T,U>  | test(T,U)   | Boolean condition  |
| BiFunction<T,U,R> | apply(T,U)  | Transform 2 inputs |
| BiConsumer<T,U>   | accept(T,U) | Consume 2 inputs   |

-----

### 6. Unary & Binary Operators

#### UnaryOperator<T>

> T apply(T t);

Same input & output type

#### BinaryOperator<T>

> T apply(T t1, T t2);

##### Example:

> BinaryOperator<Integer> add = (a, b) -> a + b;

-----

### 7. Primitive Functional Interfaces

👉 Avoid boxing/unboxing overhead

| Interface      | Method      |
| -------------- | ----------- |
| IntPredicate   | test(int)   |
| IntFunction<R> | apply(int)  |
| IntConsumer    | accept(int) |
| IntSupplier    | getAsInt()  |

✔ Same for:

- Long
- Double

----------------------

## 5. Custom Functional Interface

```java
@FunctionalInterface
interface MyInterface {
void doSomething();
}
```

✔ Annotation is optional but recommended


#### ✅ Must have only ONE abstract method

```java
interface A {
    void m1();
    void m2(); // ❌ Not functional
}
```

#### ✅ Can have default & static methods

```java
interface A {
void m1();

    default void m2() {}
    static void m3() {}
}
```


#### ✅ Can extend another interface (with care)

```java
interface A {
void m1();
}

interface B extends A {
// still functional
}
```

------------------

## Summary

- Functional interface = 1 abstract method
- Introduced to support lambdas
- Package: java.util.function
- Examples:
  - Predicate → condition
  - Function → transform
  - Consumer → consume
  - Supplier → provide

- Supports:
  - Method references
  - Streams API


| Type      | Input | Output  | Use       |
| --------- | ----- | ------- | --------- |
| Predicate | 1     | boolean | Filter    |
| Function  | 1     | 1       | Map       |
| Consumer  | 1     | void    | Print/use |
| Supplier  | 0     | 1       | Generate  |



