---
title: Method Overriding - Rules
parent: Core Concepts
nav_order: 3
---

# Method Overriding—Rules

**Method overriding** allows a subclass to provide a **specific implementation**
of a method that is already defined in its superclass.

It supports **runtime polymorphism**, which is a core concept of OOP.

---

## What Is Method Overriding?

> Method overriding occurs when a subclass defines a method with:
- Same method name
- Same parameter list
- Compatible return type
- Same or broader access level

as a method in its parent class.

---

## Simple Example

```java
class Account {
    void calculateInterest() {
        System.out.println("Generic interest");
    }
}

class SavingsAccount extends Account {
    @Override
    void calculateInterest() {
        System.out.println("Savings account interest");
    }
}
```

At runtime:

``` java
Account acc = new SavingsAccount();
acc.calculateInterest(); // Savings account interest
```

✔ Method call decided at runtime      

---

### Why Method Overriding Is Needed

- To change behavior of inherited methods
- To provide specialized implementation
- To achieve runtime polymorphism
- To support extensibility (Open/Closed Principle)

---

### Key Rules of Method Overriding

---

#### 1️⃣ Method Signature Must Match

- Same method name
- Same parameter types and order

❌ Changing parameters → method overloading, not overriding


#### 2️⃣ Return Type Rules (Covariant Return Type)

Return type can be:

- Same
- Subclass of original return type (covariant)

```java
class Parent {
Number getValue() { return 10; }
}

class Child extends Parent {
Integer getValue() { return 20; }
}

```

✔ Valid overriding     

#### 3️⃣ Access Modifier Rules

> You cannot reduce visibility while overriding.

| Parent    | Child                        |
| --------- | ---------------------------- |
| public    | public                       |
| protected | protected / public           |
| default   | default / protected / public |
| private   | ❌ cannot override            |


#### 4️⃣ Static Methods Cannot Be Overridden

Static methods are:

- Class-level
- Resolved at compile time

``` java
class Parent {
static void show() {}
}

class Child extends Parent {
static void show() {}
}
```

This is method hiding, not overriding.

#### 5️⃣ Final Methods Cannot Be Overridden

> final void process() {}

❌ Overriding not allowed       
✔ Ensures behavior cannot be changed       

#### 6️⃣ Private Methods Cannot Be Overridden

- Private methods are not visible to subclasses
- They belong only to the defining class

---

## Exception Rules in Method Overriding

### Checked Exceptions Rule

Overriding method:

- Can throw same checked exception
- Can throw subclass of checked exception
- ❌ Cannot throw broader checked exception   

### Unchecked Exceptions Rule

Overriding method:

- Can throw any unchecked exception
- No restriction

### If Parent Method Throws NO Exception

Child method:

- ❌ Cannot throw checked exception
- ✔ Can throw unchecked exception


### Summary of Exception Rules

| Parent Method              | Child Method                  |
| -------------------------- | ----------------------------- |
| Throws checked exception   | Same or subclass only         |
| Throws unchecked exception | Any unchecked                 |
| Throws no exception        | No checked, unchecked allowed |


