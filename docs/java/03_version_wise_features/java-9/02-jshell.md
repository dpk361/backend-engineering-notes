---
title: JShell
parent: Java-9
nav_order: 2
---

# JShell

JShell is Java's official REPL.

REPL means:

> Read - Evaluate - Print - Loop

It allows you to quickly run Java expressions, statements, methods, and classes without creating a full Java file.

---

## Why Was It Introduced?

Before Java 9, testing a small Java idea required too much ceremony:

```java
public class Main {
    public static void main(String[] args) {
        System.out.println("Hello");
    }
}
```

For learning, API exploration, and quick experiments, this was slow.

JShell solves this by allowing direct execution:

```
jshell> "hello".toUpperCase()
$1 ==> "HELLO"
```

---

## Starting JShell

```bash
jshell
```

Exit:

```text
/exit
```

Help:

```text
/help
```

---

## Basic Examples

### Expression

```
jshell> 10 + 20
$1 ==> 30
```

### Variable

```
jshell> String name = "Mohan"
name ==> "Mohan"

jshell> name.length()
$2 ==> 5
```

### Method

```
jshell> int square(int n) { return n * n; }
|  created method square(int)

jshell> square(6)
$3 ==> 36
```

### Class

```
jshell> class User {
   ...>     String name;
   ...>     User(String name) { this.name = name; }
   ...> }
|  created class User

jshell> new User("Ravi").name
$4 ==> "Ravi"
```

---

## Useful JShell Commands

| Command          | Use                           |
|------------------|-------------------------------|
| `/vars`          | Show variables                |
| `/methods`       | Show methods                  |
| `/types`         | Show classes/interfaces/enums |
| `/list`          | Show entered snippets         |
| `/edit`          | Edit snippets                 |
| `/save file.jsh` | Save session                  |
| `/open file.jsh` | Load saved snippets           |
| `/reset`         | Reset session                 |
| `/exit`          | Exit JShell                   |

---

## Daily Coding Use Cases

### 1. Quickly Check API Behavior

```
jshell> List.of("A", "B").add("C")
|  Exception java.lang.UnsupportedOperationException
```

This quickly confirms that `List.of` creates an unmodifiable list.

### 2. Test Date-Time Formatting

```
jshell> import java.time.*
jshell> import java.time.format.*

jshell> LocalDate.now().format(DateTimeFormatter.ISO_DATE)
$1 ==> "2026-06-18"
```

### 3. Test Stream Logic

```
jshell> import java.util.stream.*
jshell> Stream.of(1, 2, 3, 4).filter(n -> n % 2 == 0).collect(Collectors.toList())
$1 ==> [2, 4]
```

---

## Best Practices

- Use JShell for learning and small experiments.
- Use it to verify API behavior before writing production code.
- Use `/save` when an experiment becomes useful.
- Do not treat JShell as a replacement for unit tests.
- Make sure your JShell version matches the Java feature you are testing.

---

## Common Mistakes

### Mistake 1: Testing on a newer JShell and assuming Java 9 supports it

If you run JShell from Java 21, it supports Java 21 APIs.

Always check:

```bash
jshell --version
```

### Mistake 2: Forgetting imports

JShell automatically imports some common packages, but not everything.

Use:

```text
/imports
```

---

## Quick Summary

JShell makes Java easier to learn and explore. It is useful for quick experiments, API checks, stream logic, and examples, but production behavior should still be verified with tests.

---
