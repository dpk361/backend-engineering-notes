---
title: Java-9
parent: Java Versions
nav_order: 10
---

# Java 9 - Features / Enhancements

Java 9 was released on **21 September 2017**.

The biggest theme of Java 9 was **modularization**. Java moved from one large runtime image to a modular platform, and applications could also be organized as modules.

Java 9 also added many daily-use improvements in collections, streams, optional, process handling, concurrency, tooling, JVM logging, security, and packaging.

---

1. [Module System](01-module-system.md)
2. [JShell](02-jshell.md)
3. [Collection Factory Methods](03-collection-factory-methods.md)
4. [Stream API Enhancements](04-stream-api-enhancements.md)
5. [Optional Enhancements](05-optional-enhancements.md)
6. [Private Interface Methods](06-private-interface-methods.md)
7. [Try-With-Resources and Diamond Operator Enhancements](07-try-with-resources-and-diamond.md)
8.  [JVM and Runtime Improvements](08-jvm-runtime-improvements.md)

---

## Java 9 Feature Map

| Area           | Feature                                                    | Why It Matters                                                  |
|----------------|------------------------------------------------------------|-----------------------------------------------------------------|
| Platform       | Java Platform Module System                                | Stronger encapsulation, reliable dependencies, smaller runtimes |
| Tooling        | JShell                                                     | Quickly test Java expressions and APIs                          |
| Collections    | `List.of`, `Set.of`, `Map.of`                              | Easy immutable collections                                      |
| Streams        | `takeWhile`, `dropWhile`, `ofNullable`, enhanced `iterate` | Cleaner stream pipelines                                        |
| Optional       | `ifPresentOrElse`, `or`, `stream`                          | Better null-safe control flow                                   |
| Interfaces     | Private methods                                            | Reuse logic inside default/static interface methods             |
| Language       | Improved try-with-resources                                | Use effectively final resources directly                        |
| Language       | Diamond with anonymous classes                             | Less repeated generic type code                                 |
| Concurrency    | CompletableFuture updates                                  | Timeouts, delays, failed futures                                |
| Concurrency    | Flow API                                                   | Reactive Streams interfaces in JDK                              |
| JVM            | G1 default GC                                              | Better default latency behavior for many applications           |
| JVM            | Compact Strings                                            | Reduced memory for many String-heavy applications               |
| Logging        | Unified JVM logging                                        | One consistent logging style for JVM internals                  |

---

## What Java 9 Means For Backend Developers

For daily backend coding, the most useful Java 9 features are:

- Immutable collection factory methods.
- Stream and Optional enhancements.
- Better process handling for tools and infrastructure code.
- CompletableFuture timeout and delay support.
- Serialization filtering for safer legacy Java serialization.
- Module-system awareness, even if your application still runs on the classpath.

---

## Quick Summary

Java 9 introduced the **Java Platform Module System** as its major feature. It also added immutable collection factory methods, JShell, stream and optional improvements, private interface methods, Process API updates, Flow API, CompletableFuture enhancements, compact strings, G1 as default GC, unified JVM logging, and serialization filters.
