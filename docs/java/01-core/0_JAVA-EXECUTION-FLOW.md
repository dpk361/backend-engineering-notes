---
title: JAVA EXECUTION FLOW
parent: Core Concepts
nav_order: 0
---

# Java Execution Flow (Inheritance)

When both Parent (Super Class) and Child Class have:

- Static variables
- Static blocks
- Instance variables
- Instance initializer blocks
- Constructors

### Child c = new Child();

the execution order is always:

# Phase 1: Class Loading (Runs Only Once)

When the JVM loads the classes:

1. Parent Static Variables Initialization
2. Parent Static Blocks (Top → Bottom)
3. Child Static Variables Initialization
4. Child Static Blocks (Top → Bottom)

### Rule

```text
Parent Static → Child Static
```

---

# Phase 2: Object Creation (Runs For Every Object)

## Parent Initialization

1. Parent Instance Variables Initialization
2. Parent Instance Initializer Blocks (Top → Bottom)
3. Parent Constructor

## Child Initialization

1. Child Instance Variables Initialization
2. Child Instance Initializer Blocks (Top → Bottom)
3. Child Constructor

### Rule

```text
Parent Instance → Parent Constructor
                 ↓
Child Instance  → Child Constructor
```

---

# Complete Execution Order

```text
Parent Static Variables
Parent Static Blocks

Child Static Variables
Child Static Blocks

Parent Instance Variables
Parent Instance Blocks
Parent Constructor

Child Instance Variables
Child Instance Blocks
Child Constructor
```

---

# Interview Shortcut

```text
PS → CS → PI → PC → CI → CC
```

Where:

| Short Form   | Meaning              |
|--------------|----------------------|
| PS           | Parent Static        |
| CS           | Child Static         |
| PI           | Parent Instance      |
| PC           | Parent Constructor   |
| CI           | Child Instance       |
| CC           | Child Constructor    |

---

# Important Notes

### Static Members

* Static variables execute before static blocks if declared above them.
* Static blocks execute only once per class loading.
* Multiple static blocks execute in source-code order.

### Instance Members

* Instance variables execute before instance blocks.
* Instance blocks execute before constructors.
* Multiple instance blocks execute in source-code order.

### Constructors

* Parent constructor always executes before child constructor.
* Every constructor implicitly calls:

```
super();
```

unless another constructor is explicitly specified.

---

# Memory Trick

```text
CLASS LOADING

Parent Static
    ↓
Child Static


OBJECT CREATION

Parent Instance
    ↓
Parent Constructor
    ↓
Child Instance
    ↓
Child Constructor
```

---

# One-Line Rule

```text
Parent Static
→ Child Static
→ Parent Instance
→ Parent Constructor
→ Child Instance
→ Child Constructor
```

Remember:

```text
PS → CS → PI → PC → CI → CC
```

