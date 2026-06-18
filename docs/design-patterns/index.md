---
title: Design Patterns
parent: Backend Engineering Notes
nav_order: 5
---

# Design Patterns

**Design patterns** are reusable solutions to common software design problems.

There are three core problem areas in object-oriented design:

1. Object creation
2. Object structure / composition 
3. Object behavior / interaction

That’s why we have:
- **Creational Patterns**
- **Structural Patterns**
- **Behavioral Patterns**

> Design patterns are grouped / categorized based on what problem they primarily solve in software design.

----------

### 1️⃣ Creational Patterns:
> Who creates the object, when, and how?

Directly creating objects using **new**:

- Couples code tightly
- Makes changes difficult
- Makes testing harder

Creational patterns:
- Control object creation
- Hide creation logic
- Improve flexibility

**Examples:**
- **Singleton**
- **Factory**
- **Abstract Factory**
- **Builder**
- **Prototype**

---

### 2️⃣ Structural Patterns

> 👉 “How objects are connected or composed”

As systems grow:
- Classes increase
- Dependencies become complex
- Code becomes hard to manage

Structural patterns:

- Define relationships between classes
- Simplify object composition
- Improve readability and reuse

**Examples:**
- **Adapter**
- **Decorator**
- **Facade**
- **Proxy**
- **Composite**

---

### 3️⃣ Behavioral Patterns
> 👉 “How objects communicate and behave”


In real applications:
- Many objects talk to each other
- Logic gets scattered
- Changes become risky

Behavioral patterns:
- Define clear communication rules
- Separate responsibilities
- Reduce tight coupling between behaviors

**Examples:**
- **Strategy**
- **Observer**
- **Command**
- **Template Method**
- **State**
- **Chain of Responsibility**