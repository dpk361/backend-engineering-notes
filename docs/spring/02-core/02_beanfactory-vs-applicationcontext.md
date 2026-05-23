---
title: ApplicationContext vs BeanFactory
parent: Core
nav_order: 2
---


# ApplicationContext vs BeanFactory (Spring)

-----

## What is BeanFactory?

**BeanFactory** is the core IoC container in Spring Framework.

### Key Idea:

> Provides basic dependency injection and bean management.

### Responsibilities:

- Creates beans  
- Injects dependencies  
- Manages bean lifecycle  

### Characteristics:
- Lazy Initialization (default)  
- Lightweight  
- Minimal features  

---

## What is ApplicationContext?

**ApplicationContext** is an advanced container built on top of BeanFactory.

### Key Idea:
> Provides all BeanFactory features + enterprise-level capabilities.

### Characteristics:
- Eager Initialization (default)  
- Feature-rich  
- Used in almost all Spring Boot applications  

---

## Features of ApplicationContext

- Internationalization (i18n)  
- Event publishing  
- AOP support  
- Environment & property handling  
- Annotation-based configuration  
- BeanPostProcessor support  

---

## Key Differences

| Feature | BeanFactory | ApplicationContext |
|--------|-------------|--------------------|
| Type | Basic container | Advanced container |
| Initialization | Lazy | Eager |
| Features | Basic DI | Enterprise features |
| AOP Support | Limited | Full |
| Event Handling | No | Yes |
| Use Case | Rare | Standard |

---

## Relationship

ApplicationContext extends BeanFactory.

---

## Real-world (Spring Boot)

SpringApplication.run() internally creates ApplicationContext and manages:
- Bean creation
- Dependency injection
- Application startup

---

## When to Use

- BeanFactory → Rare, low-level usage  
- ApplicationContext → Almost always  

---

##  Summary

**BeanFactory:**  
- Basic IoC container for managing beans.
- BeanFactory = Core engine

**ApplicationContext:**  

- Advanced container with additional enterprise features.
- ApplicationContext = Full-featured container built on top
