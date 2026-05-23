---
title: IoC vs DI
parent: Core
nav_order: 1
---


# IoC (Inversion of Control) vs DI (Dependency Injection)

---

## What is IoC?

**Inversion of Control (IoC)** is a design principle where the control of object creation and lifecycle is transferred from the application code to the framework (Spring).

### Key Idea:
> Instead of creating objects manually, the framework manages them.

### Without IoC:

```java
PaymentService paymentService = new PaymentService();
```

### With IoC:

```java
@Service
class PaymentService {}
```

- Spring creates and manages the object
- Lifecycle is handled by the container

----

## What is DI?

**Dependency Injection (DI)** is a design pattern used to implement IoC.

It provides the required dependencies to a class from outside instead of the class creating them.

```java
@Service
class OrderService {

    private final PaymentService paymentService;

    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

- Dependency is injected by Spring
- No direct object creation

### Types of Dependency Injection

#### 1. Constructor Injection (Recommended)

```java
public OrderService(PaymentService paymentService) { }
```

- Immutable
- Mandatory dependencies
- Easy to test

#### 2. Setter Injection

```java
@Autowired
public void setPaymentService(PaymentService paymentService) { }
```

- Optional dependencies
- Can be modified later

#### 3. Field Injection (Not Recommended)

```java
@Autowired
private PaymentService paymentService;
```

- Hard to test
- Hidden dependencies

----

## Relationship Between IoC and DI

1. Spring IoC container starts
2. It creates beans
3. It resolves dependencies
4. It injects dependencies using DI

👉 IoC = Concept      
👉 DI = Implementation       


---


## summary

**IoC:**

> Inversion of Control is a principle where object creation and lifecycle are managed by the Spring container instead of the application.

**DI:**

> Dependency Injection is a pattern where dependencies are provided to a class by the container rather than being created by the class itself.


