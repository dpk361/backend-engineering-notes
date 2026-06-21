---
title: JVM Performance Improvements
parent: Java-10
nav_order: 4
---

# JVM Performance Improvements

Java 10 included several JVM improvements.

Most of these do not change normal Java code, but they matter when you work on:

- Backend performance.
- Startup time.
- Memory usage.
- Garbage collection tuning.
- Container or serverless deployments.

---

## 1. Application Class-Data Sharing

Application Class-Data Sharing is also called **AppCDS**.

### Why Was It Introduced?

Large Java applications load many classes at startup.

Loading classes takes:

- Time.
- CPU.
- Memory.

AppCDS allows selected class metadata to be stored in a shared archive file.

Later JVM runs can reuse that archive.

This can improve startup time and reduce memory usage.

---

## Simple Explanation

Think of class loading like preparing books before a class.

Without AppCDS:

- Every JVM prepares the same books again.

With AppCDS:

- The JVM prepares common class information once.
- Later JVMs reuse that prepared data.

---

## AppCDS Basic Flow

### Step 1: Run Application And Record Loaded Classes

```bash
java -Xshare:off -XX:+UseAppCDS -XX:DumpLoadedClassList=app.lst -cp app.jar com.example.Main
```

### Step 2: Create Shared Archive

```bash
java -Xshare:dump -XX:+UseAppCDS \
  -XX:SharedClassListFile=app.lst \
  -XX:SharedArchiveFile=app.jsa \
  -cp app.jar
```

### Step 3: Run With Shared Archive

```bash
java -Xshare:on -XX:+UseAppCDS \
  -XX:SharedArchiveFile=app.jsa \
  -cp app.jar com.example.Main
```

---

## AppCDS Edge Cases

### Classpath Must Match

The classpath used while creating the archive must match the runtime classpath.

If it does not match, JVM can fail to start with archive/classpath mismatch.

Use this to debug:

```bash
-Xlog:class+path=info
```

### Prefer Xshare:auto In Production

```bash
java -Xshare:auto -XX:+UseAppCDS -XX:SharedArchiveFile=app.jsa -cp app.jar com.example.Main
```

With `-Xshare:auto`, if archive sharing cannot be used, the JVM continues without it.

With `-Xshare:on`, failure to use the archive can fail startup.

### Recreate Archive When JARs Change

If application classes or dependencies change, regenerate the archive.

---

## 2. Parallel Full GC For G1

Java 9 made G1 the default garbage collector.

Java 10 improved G1 by making **full GC parallel**.

---

## Why Was It Introduced?

G1 tries to avoid full GC.

But if memory pressure is too high, full GC can still happen.

Before Java 10, G1 full GC had more single-threaded work and could be slow.

Java 10 made this worst-case path use multiple threads.

---

## Developer Meaning

No code change is needed.

But if you see full GC often, that is still a warning sign.

It may mean:

- Heap is too small.
- Application creates too many temporary objects.
- There is a memory leak.
- GC tuning is wrong.

Use GC logs:

```bash
java -Xlog:gc* -jar app.jar
```

The number of GC worker threads can be controlled with:

```bash
-XX:ParallelGCThreads=4
```

Do not tune this blindly. Measure first.

---

## 3. Thread-Local Handshakes

### Simple Explanation

Sometimes the JVM needs to pause threads to do internal work.

Before this improvement, some operations required stopping all Java threads.

Thread-local handshakes allow the JVM to work with selected threads instead of always stopping everyone.

---

## Developer Meaning

You do not call this API directly.

It can help reduce pause impact for some JVM operations.

It matters more to JVM engineers and performance tooling than normal application code.

---

## 4. Experimental Java-Based JIT Compiler

Java 10 allowed Graal to be used as an experimental JIT compiler on Linux/x64.

Enable:

```bash
java -XX:+UnlockExperimentalVMOptions -XX:+UseJVMCICompiler -jar app.jar
```

Developer meaning:

- This was experimental in Java 10.
- It was for testing and evaluation.
- It was not something most backend teams enabled casually in production.

Possible negative side effects:

- Slower startup.
- More heap usage.
- Performance may be better or worse depending on application.

---

## 5. Garbage-Collector Interface

Java 10 introduced a cleaner internal interface for garbage collectors.

Developer meaning:

- Normal application code does not use it.
- It helps JDK developers add or maintain garbage collectors more cleanly.

---

## 6. Heap Allocation On Alternative Memory Devices

Java 10 added support for allocating the Java heap on alternative memory devices.

Developer meaning:

- Mostly relevant to special hardware and JVM tuning.
- Not a daily backend coding feature.
- Do not worry about it unless your infrastructure team uses special memory devices.

---

## Best Practices

- Treat JVM changes as runtime behavior improvements, not coding features.
- Use GC logs before tuning GC options.
- If full GC happens often, investigate memory usage.
- Use AppCDS only after measuring startup/memory benefit.
- Regenerate AppCDS archives when application JARs change.
- Avoid experimental JIT settings in production unless your team has tested them deeply.

---

## Quick Summary

Java 10 improved JVM startup, memory sharing, and GC behavior. For most developers, the main takeaway is: no code change is needed, but these features help when analyzing performance, startup time, and runtime behavior.

