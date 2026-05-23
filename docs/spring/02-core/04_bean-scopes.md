---
title: Bean Scopes
parent: Core
nav_order: 4
---

# Bean Scopes in Spring

A **Bean Scope** defines **how many instances** of a bean Spring creates
and **how long** those instances live inside the Spring container.

Understanding bean scopes is important because:
- It affects memory usage
- It affects concurrency
- It affects lifecycle and garbage collection
- Wrong scope choice causes serious bugs

---

## What Is Bean Scope?

Bean scope answers two questions:

1. **How many instances of a bean are created?**
2. **Who manages the bean lifecycle?**

---

## Default Scope

> **Singleton is the default scope in Spring.**

If you don’t specify a scope:
```java
@Component
public class BranchService {
}
```
Spring creates ONE instance for the entire application.    

## Overview of Bean Scopes

Spring supports five main scopes:

| Scope       | Description                     |
| ----------- | ------------------------------- |
| Singleton   | One instance per container      |
| Prototype   | New instance per request        |
| Request     | One instance per HTTP request   |
| Session     | One instance per HTTP session   |
| Application | One instance per ServletContext |

---

### 1️⃣ Singleton Scope (DEFAULT)

**What It Means**

- Only one instance per Spring container
- Shared across all requests and threads

``` java 
@Service
@Scope("singleton")
public class BranchService {
}
```

**Characteristics:**

✔ Created at startup (eager by default)   
✔ Destroyed on application shutdown   
✔ Spring manages full lifecycle   

**Important Note (Concurrency)**

Singleton beans:

- Are shared across threads
- Must be thread-safe
- Should NOT store request-specific state
- Stateless design is recommended.

**Common Use Cases**

- Services
- Repositories
- Controllers
- Utility beans

---

## 2️⃣ Prototype Scope

**What It Means**

New instance every time the bean is requested

```java 
@Component
@Scope("prototype")
public class ReportGenerator {
}
```

**Characteristics**    

✔ Created on demand    
✔ Dependency injection happens    
❌ Spring does NOT destroy it    

**Lifecycle Reminder (Important)**

- Spring creates the bean
- Hands it to the caller
- Caller manages cleanup
- JVM GC handles memory

**Common Use Cases**

- Stateful objects
- Temporary objects
- Objects with short life

**Caution ⚠️**

Overusing prototype beans can cause:

- Memory leaks
- Object explosion
- Hard-to-debug issues
---

## 3️⃣ Request Scope (Web Only)

**What It Means**

- One instance per HTTP request

```java
@Component
@Scope(value = WebApplicationContext.SCOPE_REQUEST)
public class RequestContext {
}
```

**Characteristics**

✔ Created at request start   
✔ Destroyed after request completion   
✔ Safe for request-specific data   

**Common Use Cases**

- Request metadata
- Correlation IDs
- Request-scoped context objects

---

## 4️⃣ Session Scope (Web Only)

**What It Means**

- One instance per HTTP session

```java
@Component
@Scope(value = WebApplicationContext.SCOPE_SESSION)
public class UserSession {
}
```

**Characteristics**

✔ Shared across multiple requests   
✔ Destroyed when session expires   
❌ Can increase memory usage    

**Common Use Cases**

- Logged-in user details
- Session preferences

---

## 5️⃣ Application Scope (Web Only)

**What It Means**

- One instance per ServletContext
- Shared across the whole web application

``` java
@Component
@Scope(value = WebApplicationContext.SCOPE_APPLICATION)
public class AppConfigCache {
}
```

**Characteristics**

✔ Similar to singleton   
✔ Servlet container–managed   

---

## Singleton vs Prototype (VERY IMPORTANT)

| Aspect                      | Singleton | Prototype        |
| --------------------------- | --------- | ---------------- |
| Instances                   | One       | Many             |
| Thread safety               | Required  | Not required     |
| Lifecycle managed by Spring | Full      | Partial          |
| GC handled by               | JVM       | JVM              |
| Typical usage               | Services  | Stateful objects |

---

## Bean Scopes in Banking / Enterprise Systems

### Common patterns:

- Singleton → Services, repositories
- Request → Request metadata, tracing
- Prototype → Rare, controlled usage
- Session → Minimal (stateless preferred)

> Banking systems prefer stateless + singleton design.
 
> Bean scope defines the lifecycle and visibility of a bean instance. Singleton is the default scope, prototype creates a new instance per request, and web scopes like request and session are used in web applications.

