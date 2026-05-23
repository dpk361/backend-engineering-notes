---
title: Hibernate Cache
parent: Spring Data JPA
nav_order: 11
---

# Hibernate Cache

Hibernate cache is a mechanism used to **reduce database access** by storing
data in memory. It improves performance by avoiding repeated database queries
for the same data.

Hibernate provides **multiple cache levels**, each serving a different purpose.

---

> **Important:**  
> Cache is a performance optimization, **not a source of truth**.

## Cache Levels in Hibernate

Hibernate supports **three cache levels**:

1. First-Level Cache
2. Second-Level Cache
3. Query Cache

---

## 1️⃣ First-Level Cache (Default)

### What It Is
- Also called **Persistence Context**
- Exists per **Session / EntityManager**

You **cannot disable** it.


### How It Works

```java
Customer c1 = entityManager.find(Customer.class, 1L);
Customer c2 = entityManager.find(Customer.class, 1L);
```

Only one SQL query is executed.

#### Why?

- First-level cache stores the entity
- Same entity ID → same object instance

> All JPA repository methods automatically use the First-Level Cache.  
> Ex: customerRepository.findById(1L);


### Characteristics

- Scope: Transaction / Session
- Lifetime: Until transaction ends
- Stores: Managed entities only
- Acts as identity map

> **Rule:**  
>Within one transaction, Hibernate never hits DB twice for same entity ID.


---

## 2️⃣ Second-Level Cache

### What It Is

- Shared across sessions
- Application-level cache
- Requires explicit configuration

### Used for:

- Read-heavy data
- Reference data
- Rarely changing data

#### Example Use Cases

- Branch details
- Product configurations
- Country / currency lists

- ❌ Not for transactional data.

### How It Works

```java
Customer c1 = session1.find(Customer.class, 1L); // DB hit
Customer c2 = session2.find(Customer.class, 1L); // Cache hit

```

### Enable Second-Level Cache 

#### Requires cache provider:

- Ehcache
- Caffeine
- Hazelcast

Please refer implementation of Caffeine : [Cache Notes](https://mmedaram.github.io/backend-engineering-notes/docs/spring-boot/cache.html)

---

## 3️⃣ Query Cache (RARELY USED)

### What It Is

- Caches query results
- Depends on second-level cache
- Disabled by default

> query.setCacheable(true);

### Why Query Cache Is Rare

- Hard to keep consistent
- Sensitive to data changes
- Risk of stale results

Most teams avoid it.

---

