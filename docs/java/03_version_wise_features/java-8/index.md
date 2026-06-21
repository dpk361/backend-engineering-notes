---
title: Java-8
parent: Java Versions
nav_order: 8
---

# Java 8 - Features / Enhancements

---

## Java 8 - Language & Core Library Enhancements

### Lambda Expressions
- Syntax → `(x, y) -> x + y`
- Enables functional programming style.
- Replaces anonymous classes.
- Target typing support.
- Captures only effectively final variables.
- Compiled using `invokedynamic` (not inner classes).

### Functional Interfaces
- `@FunctionalInterface`
- Exactly one abstract method (SAM).
- Built-in interfaces:
    - `Predicate`
    - `Function`
    - `Consumer`
    - `Supplier`
    - `UnaryOperator`
    - `BinaryOperator`
- Supports default and static methods.

### Default & Static Methods in Interfaces
- Backward compatibility for interface evolution.
- Diamond problem resolution using explicit override.
- Static methods belong to interface (not inherited).

### Method References
- `Class::staticMethod`
- `object::instanceMethod`
- `Class::instanceMethod`
- `Class::new` (constructor reference)

### Repeating Annotations
- Same annotation can be applied multiple times.
- Uses container annotation internally.

### Type Annotations
- Can annotate any use of a type.
- Useful for static analysis tools (e.g., null safety).

### Optional
- Avoids `NullPointerException`.
- Methods:
    - `map`
    - `flatMap`
    - `filter`
    - `orElse`
    - `orElseGet`
    - `orElseThrow`
- Not recommended for fields or serialization.

---

## Java 8 - Collections & Concurrency

### Streams API
- Functional-style collection processing.
- Pipeline model (Source → Intermediate → Terminal).
- Lazy evaluation.
- Stateless vs Stateful operations.
- Uses `Spliterator` internally.
- Common operations:
    - `filter`
    - `map`
    - `flatMap`
    - `reduce`
    - `collect`

### Parallel Streams
- Uses `ForkJoinPool.commonPool()`.
- Automatic workload splitting.
- Good for CPU-bound tasks.
- Avoid for:
    - I/O-heavy tasks
    - Small datasets
    - Shared mutable state

### Collectors API
- `Collectors.toList()`
- `toSet()`
- `toMap()`
- `groupingBy()`
- `partitioningBy()`
- Downstream collectors.
- Custom collector support.

### New Map Methods
- `forEach`
- `computeIfAbsent`
- `computeIfPresent`
- `compute`
- `merge`
- `replaceAll`

### ConcurrentHashMap Enhancements
- `forEach`
- `reduce`
- `search`
- Better parallelism and scalability.

### HashMap Improvement
- Bucket converts from LinkedList → Red-Black Tree.
- Treeification threshold = 8 entries.
- Improves worst-case:
    - O(n) → O(log n)

---

## Java 8 - Date & Time API (java.time)

### Core Classes
- `LocalDate`
- `LocalTime`
- `LocalDateTime`
- `ZonedDateTime`
- `Instant`

### Additional Classes
- `Duration` (time-based)
- `Period` (date-based)
- `DateTimeFormatter`

### Key Improvements
- Immutable
- Thread-safe
- Clear timezone handling
- Replaces `java.util.Date` and `Calendar`

---

## Java 8 - JVM & Language Runtime

### Metaspace
- Replaces PermGen.
- Uses native memory.
- Eliminates `OutOfMemoryError: PermGen space`.

### Nashorn JavaScript Engine
- Run JavaScript inside JVM.
- Removed in Java 15.

### Compact Profiles
- Subset JREs for embedded systems.

---

## Java 8 - Concurrency Enhancements

### CompletableFuture
- Asynchronous programming.
- Non-blocking pipelines.
- `thenApply`
- `thenCompose`
- `thenCombine`
- Exception handling support.
- Supports async and sync execution.

### StampedLock
- Supports:
    - Optimistic read
    - Read lock
    - Write lock
- Better performance than traditional read-write locks in some cases.

---

## Java 8 - New Utilities & APIs

### Base64 API
- `java.util.Base64`
- Basic, URL, and MIME encoders.

### Unsigned Arithmetic
- `Integer.divideUnsigned()`
- `compareUnsigned()`
- `toUnsignedLong()`

### StringJoiner
- Custom delimiter.
- Optional prefix and suffix.

### Files & IO Enhancements
- `Files.lines(Path)` → Stream of lines.
- `Files.walk()` → Recursive traversal.
- `Files.find()` → Search with predicate.
- Stream-based file processing.

### Arrays Enhancements
- `Arrays.parallelSort()`

### String Enhancements
- `String.join()`
- `String.chars()` → `IntStream`
- `String.codePoints()`

### Comparator Enhancements
- `thenComparing()`
- `naturalOrder()`
- `reverseOrder()`
- Comparator chaining.

---

