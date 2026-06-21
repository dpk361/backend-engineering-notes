---
title: Pattern Matching (instanceof & switch)
parent: Java-17
nav_order: 3
---

# Pattern Matching in Java 17

Pattern Matching improves type checking and reduces boilerplate code.

Java 17 includes:

- Pattern Matching for `instanceof` (Finalized)
- Pattern Matching for `switch` (Preview in Java 17)

It makes type-based logic cleaner and safer.

---

## Pattern Matching for instanceof

### Before Java 16

```
Object obj = "Hello";

if (obj instanceof String) {
    String s = (String) obj;
    System.out.println(s.length());
}
```

Problems:

- Repeated type
- Manual casting
- Verbose

### After (Java 17)

```
Object obj = "Hello";

if (obj instanceof String s) {
System.out.println(s.length());
}
```

#### What changed?

✔ Automatic type casting     
✔ New variable `s` declared      
✔ Cleaner and safer       

**How It Works**    

```
if (obj instanceof String s)
```

Means:

- Check if `obj` is String
- If true → bind to variable `s`
- `s` is available inside the block

**Scope Rules:**    

```
if (obj instanceof String s && s.length() > 3) {
System.out.println(s);
}
```

✔ `s` is available in condition      
✔ `s` available inside block       

Outside block → not accessible.

---

## Pattern Matching for switch (Preview in Java 17)

Switch can now match types directly.

---

### Traditional Switch (Old Style)

```
if (obj instanceof String s) {

System.out.println(s.length());

} else if (obj instanceof Integer i) {

System.out.println(i * 2);

}
```

### Pattern Matching Switch

```
switch (obj) {

case String s -> System.out.println(s.length());

case Integer i -> System.out.println(i * 2);

default -> System.out.println("Unknown type");

}
```

Cleaner. More readable.

#### Limitations in Java 17

- Pattern matching for switch was preview in 17
- Must enable preview features during compilation

Compile with:

> --enable-preview

---

> Pattern matching in Java 17 simplifies type checking by combining instanceof with variable binding and enabling type-based switch expressions.      
> It reduces casting, improves readability, and works well with sealed classes for safer domain modeling.

