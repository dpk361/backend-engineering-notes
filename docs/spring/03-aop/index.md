---
title: AOP
parent: Spring
nav_order: 3
---

# Spring AOP

-----

In a real application, your code has two types of logic:

#### 1. Business Logic
- Payment processing
- Order creation
- Account transfer

#### 2. Cross-Cutting Concerns
- Logging
- Security
- Transactions
- Monitoring

---

### Without AOP

```java
public void pay() {
    log.info("Start");
    securityCheck();

    // business logic

    log.info("End");
}
```

#### Problems:

- Same logging/security everywhere
- Code duplication
- Hard to maintain
- If logging logic changes → update 100 places

---

## What AOP Does

```java
@Service
class PaymentService {

    public void pay() {
        // Only business logic
    }
}
```

```java
@Aspect
@Component
class LoggingAspect {

    @Before("execution(* com.app.service.*.*(..))")
    public void log() {
        System.out.println("Logging...");
    }
}
```

**AOP says:**
>Keep your business logic clean, and move common logic (logging, security, etc.) outside

👉 Business code remains clean     
👉 Common logic is applied externally           

So instead of writing logging everywhere, you define it once, and Spring applies it automatically where needed

---

# Core Concepts

---

### Aspect

A class that contains cross-cutting logic.

```java
@Aspect
@Component
class LoggingAspect {}
```

---

### Advice

The actual logic that runs.

```java
@Before("execution(...)")
public void log() {
    System.out.println("Logging...");
}
```

---

### Join Point

A point where AOP can be applied.

👉 In Spring: only method execution

---

### Pointcut

Defines where AOP should apply.

```
execution(* com.app.service.*.*(..))
```

---

### Target Object

The actual business class.

```java
@Service
class PaymentService {}
```

---

### Proxy

Spring creates a proxy instead of calling the object directly.

```text
Client → Proxy → Actual Method
```

---

## Internal Flow

1. Spring starts
2. Detects @Aspect
3. Creates proxy for beans
4. Intercepts method calls
5. Executes advice
6. Calls actual method

---

### Flow:

```text
Client → Proxy → Advice → Target Method
```

---

## Why AOP is Powerful

- Clean code
- No duplication
- Centralized logic
- Easy maintenance

---

## Common Mistakes

- Ignoring proxy behavior
- Wrong pointcut expressions
- Self-invocation issue

---


> AOP = Proxy + Interception + External Logic

---

## Summary

- Separates cross-cutting concerns
- Uses proxy mechanism
- Works on Spring beans
- Improves maintainability  

---