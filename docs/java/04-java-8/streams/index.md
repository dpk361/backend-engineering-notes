---
title: Stream API
parent: Java-8
nav_order: 5
---

# Java Streams (Java 8+)

Java Streams were introduced in **Java 8** to simplify **data processing on collections**
by providing a **declarative, functional-style API**.

> Streams are **not data structures** —  
> they are **operations performed on data**. 

---

## Why Streams Were Introduced

Before Java 8:
- Data processing required loops
- Code was verbose and error-prone
- Parallel processing was complex

Streams solve this by providing:
- Clean and readable code
- Internal iteration
- Built-in parallelism
- Functional programming style

---

## What Is a Stream?

> A Stream is a **sequence of elements from a source**
> that supports **data processing operations**.

### Important Clarification
- Streams **do not store data**
- Streams **operate on data from a source**
- Source can be:
    - Collection
    - Array
    - I/O channel

---

## Characteristics of Streams 

- Streams do **not modify the source**
- Streams are **not reusable**
- Streams support **lazy evaluation**
- Streams process elements **only when needed**
- Streams support **parallel execution**

---

## Internal Iteration

Collections:

> for (String s : list) { }


Streams:

> list.stream().filter(...).map(...);

✔ Iteration handled internally    
✔ Less boilerplate     
✔ Safer parallel execution    

---

## Stream Pipeline Structure


### A stream pipeline consists of:

- Source     
- Intermediate operation(s)      
- Terminal operation       

> Source → Intermediate Ops → Terminal Op      

Without a terminal operation:

- Nothing executes.

-------------------------

##  Intermediate Operations (return Stream)

| Method       | Input                  | Output    | Use Case                   | Example                                   |
|--------------|------------------------|-----------|----------------------------|-------------------------------------------|
| `filter`     | Predicate              | Stream<T> | Select elements            | `p -> p.getAge() > 30`                    |
| `map`        | Function               | Stream<R> | Transform data             | `Player::getName`                         |
| `flatMap` 🚨 | Function<T, Stream<R>> | Stream<R> | Flatten nested collections | `team -> team.getPlayers().stream()`      |
| `sorted`     | Comparator             | Stream<T> | Sort elements              | `Comparator.comparingInt(Player::getAge)` |
| `distinct`   | —                      | Stream<T> | Remove duplicates          | `.distinct()`                             |
| `limit`      | long                   | Stream<T> | Get first N elements       | `.limit(3)`                               |
| `skip`       | long                   | Stream<T> | Skip first N               | `.skip(2)`                                |
| `peek` ⚠️    | Consumer               | Stream<T> | Debugging                  | `.peek(System.out::println)`              |


## Terminal Operations (end the stream)

| Method       | Output        | Use Case                 | Example                           |
|--------------|---------------|--------------------------|-----------------------------------|
| `toList()`   | List<T>       | Collect results          | `.toList()`                       |
| `collect()`  | Any           | Custom collection/result | `Collectors.groupingBy(...)`      |
| `forEach` ⚠️ | void          | Perform action           | `.forEach(System.out::println)`   |
| `count`      | long          | Count elements           | `.count()`                        |
| `anyMatch`   | boolean       | Any match condition      | `.anyMatch(p -> p.getAge() > 40)` |
| `allMatch`   | boolean       | All match condition      | `.allMatch(...)`                  |
| `noneMatch`  | boolean       | No match                 | `.noneMatch(...)`                 |
| `findFirst`  | Optional<T>   | First element            | `.findFirst()`                    |
| `findAny`    | Optional<T>   | Any element              | `.findAny()`                      |
| `max`        | Optional<T>   | Maximum element          | `.max(Comparator...)`             |
| `min`        | Optional<T>   | Minimum element          | `.min(Comparator...)`             |
| `reduce` 🚨  | Optional<T>/T | Combine elements         | `.reduce(Integer::sum)`           |


## Collectors Cheat Sheet

### Basic Collectors

| Method      | Output  | Use Case          | Example               |
|-------------|---------|-------------------|-----------------------|
| `toList()`  | List<T> | Collect to list   | `Collectors.toList()` |
| `toSet()`   | Set<T>  | Remove duplicates | `Collectors.toSet()`  |
| `joining()` | String  | Join strings      | `joining(", ")`       |


### Grouping & Aggregation

| Method                    | Output              | Use Case               | Example                              |
|---------------------------|---------------------|------------------------|--------------------------------------|
| `groupingBy`              | Map<K, List<T>>     | Group data             | `groupingBy(Player::getRole)`        |
| `groupingBy + counting`   | Map<K, Long>        | Count per group        | `groupingBy(role, counting())`       |
| `groupingBy + mapping` 🚨 | Map<K, List<R>>     | Transform inside group | `mapping(Player::getName, toList())` |
| `groupingBy + maxBy`      | Map<K, Optional<T>> | Max per group          | `maxBy(comparator)`                  |


### Advanced Collectors

| Method                 | Output                | Use Case                  | Example                   |
|------------------------|-----------------------|---------------------------|---------------------------|
| `partitioningBy`       | Map<Boolean, List<T>> | Split into 2 groups       | `p -> p.getAge() > 35`    |
| `collectingAndThen` 🚨 | Any                   | Post-process result       | `Optional::get`           |
| `mapping` 🚨           | Collector             | Transform inside grouping | `mapping(name, toList())` |
| `counting`             | Long                  | Count elements            | `counting()`              |
| `maxBy / minBy`        | Optional<T>           | Find max/min              | `maxBy(comparator)`       |


----------------

## Key Patterns

| Problem Type   | Pattern                   |
|----------------|---------------------------|
| Nested list    | `flatMap`                 |
| Filter data    | `filter`                  |
| Transform data | `map`                     |
| Group data     | `groupingBy`              |
| Count          | `count()` or `counting()` |
| Top element    | `max()`                   |
| Boolean check  | `anyMatch()`              |
| Top per group  | `groupingBy + maxBy`      |



------------------------

## Can a Stream be used twice?

No. A Java Stream can be consumed only once.

```java
public static void main(String[] args) {
    
    List<String> names = List.of("A", "B", "C");

    Stream<String> stream = names.stream();

    stream.forEach(System.out::println);

// Reusing the same stream
    stream.count(); // Exception
}
```

Output:

```text
java.lang.IllegalStateException: stream has already been operated upon or closed
```

---

## Why?

A Stream is not a collection that stores data.

It is a pipeline of operations that processes data and gets consumed when a terminal operation is executed.

Once a terminal operation is executed, the stream is closed.

---

## Example

```java
Stream<Integer> stream = Stream.of(1, 2, 3);

long count = stream.count();   // OK

long count2 = stream.count();  // IllegalStateException
```

---

## Correct Approach

Create a new stream whenever needed.

```java
List<Integer> numbers = List.of(1, 2, 3, 4);

long total = numbers.stream().count();

long evenCount =
        numbers.stream()
               .filter(n -> n % 2 == 0)
               .count();
```

---

## Using Supplier (Interview Favorite)

```java
public static void main(String[] args) {

    Supplier<Stream<Integer>> supplier =
            () -> Stream.of(1, 2, 3);

    supplier.get().count();

    supplier.get().forEach(System.out::println);
}
```

Each call to `get()` creates a fresh stream.

---

## Multiple Operations on Same Result

Instead of reusing a stream, collect the result first.

```java
public static void main(String[] args) {

    List<Integer> result =
            numbers.stream()
                    .filter(n -> n > 2)
                    .toList();

    System.out.println(result.size());
    System.out.println(result.contains(4));

}
```


## Golden Rule

"A Stream is a one-time-use pipeline. Once a terminal operation executes, the stream is closed and cannot be reused."


