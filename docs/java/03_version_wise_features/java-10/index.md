---
title: Java-10
parent: Java Versions
nav_order: 10
---

# Java 10 - Features / Enhancements

Java 10 was released on **20 March 2018**.

Java 10 was a **short-term release**, not an LTS release. It was smaller than Java 9, but it added one very important developer feature:

> `var` for local variables.

It also improved **unmodifiable collections, Optional, JVM startup, garbage collection, release versioning, certificates, and some tooling**.

---


1. [Local Variable Type Inference - var](01-local-variable-type-inference-var.md)
2. [Unmodifiable Collections](02-unmodifiable-collections.md)
3. [Optional orElseThrow](03-optional-orelsethrow.md)
4. [JVM Performance Improvements](04-jvm-performance-improvements.md)
5. [Release Versioning and Migration Notes](05-release-versioning-and-migration-notes.md)

---

## Java 10 Feature Map

| Area        | Feature                                                                   | Developer Meaning                                          |
|-------------|---------------------------------------------------------------------------|------------------------------------------------------------|
| Language    | `var` for local variables                                                 | Less repeated type writing, but only inside methods/blocks |
| Collections | `List.copyOf`, `Set.copyOf`, `Map.copyOf`                                 | Make unmodifiable copies from existing collections         |
| Streams     | `Collectors.toUnmodifiableList`, `toUnmodifiableSet`, `toUnmodifiableMap` | Collect stream result into unmodifiable collections        |
| Optional    | `orElseThrow()`                                                           | Shorter replacement for risky `get()`                      |
| JVM         | Application Class-Data Sharing                                            | Faster startup and lower memory in some apps               |
| JVM         | Parallel Full GC for G1                                                   | Better worst-case G1 full GC behavior                      |
| JVM         | Thread-Local Handshakes                                                   | JVM can pause selected threads for some operations         |
| JVM         | Experimental Java-Based JIT Compiler                                      | Graal could be tested as an experimental JIT on Linux/x64  |
| Runtime     | Time-Based Release Versioning                                             | Java moved to predictable six-month feature releases       |
| Security    | Root Certificates                                                         | OpenJDK builds got default CA certificates                 |
| Tools       | Removed `javah`                                                           | Use `javac -h` for JNI headers                             |
| Internal    | GC Interface, alternate memory heap allocation                            | Mostly JVM/JDK engineering improvements                    |

---

## What Java 10 Means For Backend Developers

For daily backend coding, focus on:

- `var` usage rules and readability.
- Unmodifiable copy methods.
- Unmodifiable stream collectors.
- `Optional.orElseThrow()`.

For platform understanding, know:

- Java 10 was the first release in the newer faster release rhythm.
- It was not LTS.
- Java 11 came next as an LTS release.
- JVM changes improved startup, GC, and runtime behavior without requiring normal code changes.

---

## Quick Summary

Java 10 introduced local variable type inference using `var`. It also added unmodifiable collection copy methods, unmodifiable stream collectors, no-argument `Optional.orElseThrow()`, Application Class-Data Sharing, parallel full GC for G1, thread-local handshakes, experimental Graal JIT support, root certificates, time-based release versioning, and removed the old `javah` tool.

