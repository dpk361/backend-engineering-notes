---
title: JVM and Runtime Improvements
parent: Java-9
nav_order: 14
---

# JVM and Runtime Improvements

Java 9 included important JVM and runtime changes.

Key improvements:

- G1 became the default garbage collector.
- Compact Strings reduced memory usage.
- Unified JVM logging improved diagnostics.
- New version string scheme.
- Runtime image layout changed.
- String concatenation used `invokedynamic`.

---

## 1. G1 Became Default GC

### Why Was It Changed?

Before Java 9, the default GC was usually Parallel GC.

Parallel GC focuses on throughput, but it can have longer stop-the-world pauses.

G1 GC was designed to provide a better balance:

- Good throughput.
- More predictable pause times.
- Better behavior for large heaps.

Java 9 made G1 the default.

---

## What G1 Does

G1 splits the heap into regions.

It collects regions with the most garbage first.

This helps it target pause-time goals.

Example option:

```bash
java -XX:MaxGCPauseMillis=200 -jar app.jar
```

---

## Daily Backend Impact

For many services, Java 9 changed GC behavior even if you did not set any GC option.

If an application depended on Parallel GC behavior, you could explicitly configure it:

```bash
java -XX:+UseParallelGC -jar app.jar
```

Best practice:

Measure before changing GC.

---

## 2. Compact Strings

### Why Was It Introduced?

Many Java applications store huge numbers of strings.

Before Java 9, strings internally used UTF-16 `char[]`, which used 2 bytes per character.

Many strings contain only Latin-1 characters, which can fit in 1 byte.

Java 9 changed String internals to use:

- `byte[]`
- an encoding flag

This can reduce memory usage significantly for Latin-1 strings.

---

## Daily Backend Impact

This helps applications with many strings:

- JSON payloads.
- Logs.
- HTTP headers.
- Database values.
- Cache keys.

No code change is required.

Disable only if testing proves a problem:

```bash
java -XX:-CompactStrings -jar app.jar
```

---

## 3. Unified JVM Logging

Java 9 introduced a common logging system for JVM components.

Old GC logging options were inconsistent.

Java 9 uses:

```bash
-Xlog
```

Examples:

```bash
java -Xlog:gc -jar app.jar
```

```bash
java -Xlog:gc*:file=gc.log:time,uptime,level,tags -jar app.jar
```

Class loading logs:

```bash
java -Xlog:class+load=info -jar app.jar
```

---

## Daily Backend Use Cases

- Debug GC pauses.
- Understand class loading.
- Investigate safepoints.
- Capture JVM diagnostics in production-like environments.

---

## 4. New Version String Scheme

Before Java 9:

```text
1.8.0_202
```

Java 9 changed version strings:

```text
9.0.4
```

Java 9 also added:

```
Runtime.Version version = Runtime.version();

System.out.println(version.major());
System.out.println(version.minor());
System.out.println(version.security());
```

Best practice:

Do not parse `java.version` manually using old assumptions.

---

## 5. Runtime Image Layout Changed

Java 9 removed the old runtime layout based on files like:

- `rt.jar`
- `tools.jar`

The JDK became modular.

Tools that directly depended on old JDK file locations had to change.

---

## 6. String Concatenation With invokedynamic

Java 9 changed how string concatenation is compiled.

Example source:

```java
String message = "User " + userId + " logged in";
```

The compiler can use `invokedynamic` to let the JVM choose efficient concatenation strategies.

No code change is required.

Best practice:

- Continue writing readable string concatenation for simple cases.
- Use `StringBuilder` for complex loops if needed.
- Use logging placeholders for logs.

```
log.info("User {} logged in", userId);
```

---

## Best Practices

- Do not tune GC blindly; collect GC logs first.
- Learn `-Xlog` because old JVM logging flags changed over time.
- Do not depend on internal JDK file layout.
- Do not parse Java version strings using Java 8 assumptions.
- Keep bytecode tools like ASM, ByteBuddy, and Jacoco updated when moving to Java 9+.
- Trust compact strings by default; disable only after measurement.

---

## Quick Summary

Java 9 improved runtime behavior without requiring much application code change. G1 became the default GC, compact strings reduced memory usage, JVM logging became unified, version strings changed, and the runtime image became modular.

