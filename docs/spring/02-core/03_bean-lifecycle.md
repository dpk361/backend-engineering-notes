---
title: Bean Lifecycle
parent: Core
nav_order: 3
---

# Spring Bean Lifecycle

The **Spring Bean Lifecycle** describes the complete journey of a bean from the moment Spring creates it until it is destroyed when the
application shuts down.

Understanding the lifecycle is important because:
- Spring controls object creation
- Spring injects dependencies automatically
- Spring provides hooks at each stage
- Many Spring features depend on lifecycle phases

---

## What Is a Spring Bean?

A **Spring Bean** is any Java object that is:
- Created
- Managed
- Injected
- Destroyed

by the **Spring IoC Container**.

> If Spring creates and manages an object → it is a Bean.

---

## Why Bean Lifecycle Matters

Knowing the lifecycle helps you:
- Initialize resources correctly
- Clean up resources safely
- Avoid startup bugs
- Understand AOP, transactions, and proxies

---

## High-Level Lifecycle Flow (Remember This)

1. Bean Instantiation
2. Dependency Injection
3. Aware Interfaces
4. BeanPostProcessor (Before Init)
5. Initialization
6. BeanPostProcessor (After Init)
7. Bean Ready for Use
8. Destruction

---

## 1️⃣ Bean Instantiation

Spring creates the bean object using:
- Constructor
- Factory method

Example:   

```java
    @Component
    public class BranchService {
    }
```

At this stage:

- Bean object exists
- Dependencies are NOT injected yet

⚠️ Avoid heavy logic in constructors

## 2️⃣ Dependency Injection (Populate Properties)

Spring injects dependencies using:

- Constructor injection
- @Autowired
- @Value

```java 
@Autowired
private BranchRepository repository;
```

Now:

- Bean has all required dependencies
- Bean is fully wired

## 3️⃣ Aware Interfaces (Optional but Important)

If a bean implements certain interfaces,
Spring injects internal container objects.

Common Aware interfaces:

- BeanNameAware
- BeanFactoryAware
- ApplicationContextAware

Example:

```java 
    @Component
    public class MyBean implements BeanNameAware {

    public void setBeanName(String name) {
    System.out.println(name);
    }
    
}
```

✔ Mostly used internally by Spring   
✔ Rare in application-level code   

## 4️⃣ BeanPostProcessor (Before Initialization)

```
    postProcessBeforeInitialization()
```

Used by Spring to:

- Process annotations (@Autowired, @PostConstruct)
- Prepare beans before initialization

> Developers rarely implement this directly, but **Spring uses it heavily internally**.

## 5️⃣ Initialization Phase (Very Important)

This is where custom initialization logic runs.   
Spring supports three mechanisms, executed in order:   

#### a) @PostConstruct (Recommended)

```java
@PostConstruct
public void init() {
// initialization logic
}
```   

✔ Clean   
✔ Annotation-based   
✔ Most commonly used   

#### b) InitializingBean

```java 
public class MyBean implements InitializingBean {
    public void afterPropertiesSet() {
    }
}
```

❌ Spring-specific   
❌ Tightly coupled   

#### c) init-method

```java 
@Bean(initMethod = "init")
public MyBean myBean() {
return new MyBean();
}
```

✔ Useful when you cannot modify the class   

## 6️⃣ BeanPostProcessor (After Initialization)

Spring calls:

```
postProcessAfterInitialization()
```

This stage is critical because:   

- AOP proxies are created here
- Transactional proxies are applied
- Security proxies are applied    
> The object you inject is often a proxy, not the original bean.

## 7️⃣ Bean Ready for Use ✅

At this point:

- Bean is fully initialized
- Dependencies injected
- Proxies applied
- Ready to handle requests

This is the bean used by:

- Controllers
- Services
- Other beans

## 8️⃣ Bean Destruction (On Shutdown)

Triggered when:
- Application context closes
- Application shuts down

### Destruction Callbacks (Order)

#### a) @PreDestroy (Recommended)

```java 
@PreDestroy
public void cleanup() {
}
```

✔ Clean    
✔ Annotation-based    
✔ Most common   

#### b) DisposableBean

```java
public class MyBean implements DisposableBean {
    public void destroy() {
    }
}
```

❌ Spring-coupled    

#### c) destroy-method

```
@Bean(destroyMethod = "cleanup")
```

---

## Singleton vs Prototype Lifecycle (VERY IMPORTANT)

| Phase          | Singleton | Prototype |
| -------------- | --------- | --------- |
| Instantiation  | ✅         | ✅         |
| Initialization | ✅         | ✅         |
| Destruction    | ✅         | ❌         |

> Spring does not manage destruction of prototype beans.

---

### Where Lifecycle Methods Are Used (Real Projects)

**Initialization:**    
- Loading reference data
- Initializing caches
- Setting up connections
- Validating configuration

**Destruction:**

- Closing connections
- Flushing logs
- Releasing resources

---


> Spring bean lifecycle describes the phases a bean goes through from instantiation, dependency injection, initialization callbacks, post-processing, usage, and finally destruction when the application shuts down.

#### Key Points to Remember

- Spring controls bean creation
- Dependency injection happens before init
- @PostConstruct and @PreDestroy are preferred
- BeanPostProcessor enables AOP & transactions
- Prototype beans are not destroyed by Spring

> Bean lifecycle hooks are meant for resource management, not for business logic.
