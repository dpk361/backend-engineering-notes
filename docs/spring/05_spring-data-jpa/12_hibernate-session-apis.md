---
title: Hibernate Session APIs
parent: Spring Data JPA
nav_order: 12
---

# Hibernate Session APIs (save, persist, merge, update, get, load)

Hibernate (and JPA) provide multiple APIs that **look similar but behave very differently**.
Misunderstanding these APIs leads to:
- duplicate records
- lost updates
- exceptions in production
- confusion during interviews

This document clarifies **what each API does, when to use it, and when NOT to use it**.

---

## JPA vs Hibernate APIs 

| Category | APIs |
|-------|----|
| JPA (Standard) | `persist`, `merge`, `find` |
| Hibernate (Specific) | `save`, `update`, `load` |

Spring Data JPA internally uses **JPA APIs**, but Hibernate APIs still matter
for understanding behavior and legacy code.

---

## persist() vs save()

### persist() (JPA)

```
entityManager.persist(customer);
```

### Behavior

- Makes entity 'managed' 
- Does NOT return ID
- Insert happens at flush/commit
- Fails if entity already has ID

✔ Preferred in JPA  
✔ Safe and standard

### save() (Hibernate)

```
session.save(customer);
```

### Behavior

- Makes entity managed
- Returns generated ID
- Insert may happen immediately

❌ Hibernate-specific  
❌ Avoid in new JPA code

### persist vs save (Summary)
| Aspect	     | persist	 | save      |
|-------------|----------|-----------|
|Standard	| JPA      |	Hibernate|
|Returns ID	|❌ No	|✅ Yes|
|Portable|	✅ Yes|	❌ No|
|Recommended|	✅	|❌|

---

## merge() vs update() 

### merge() (JPA)

> Customer managed = entityManager.merge(detachedCustomer);

#### Behavior

- Copies state from detached entity
- Returns a new managed instance
- Original object remains detached

✔ Safe  
✔ Handles detached entities  
✔ Common in REST APIs  

#### Important Rule

> Always use the object returned by merge()

### update() (Hibernate)

> session.update(detachedCustomer);

#### Behavior

- Attaches detached entity directly
- Fails if entity already exists in persistence context

❌ Can throw NonUniqueObjectException  
❌ Unsafe in complex flows

#### merge vs update (Summary)
| Aspect                  | 	merge  | 	update  |
|-------------------------|---------|----------|
| Standard                | 	JPA    |	Hibernate |
| Handles detached safely | 	✅	     | ❌ |
| Returns managed entity  | 	✅      | 	❌     |
| Recommended             | 	✅	     | ❌      |      

---

## find() vs get() vs load()

### find() (JPA)

> Customer c = entityManager.find(Customer.class, 1L);

#### Behavior

- Hits DB immediately (if not cached)
- Returns null if not found
- Uses first-level cache

✔ Safe  
✔ Predictable  
✔ Preferred  

### get() (Hibernate)
> Customer c = session.get(Customer.class, 1L);

#### Behavior

- Same as find()
- Hibernate-specific

- ❌ Prefer find() in JPA code

### load() (Hibernate) ⚠️

> Customer c = session.load(Customer.class, 1L);

#### Behavior

- Returns a proxy
- Does NOT hit DB immediately
- Throws exception if entity not found
- DB hit happens on first access

❌ Dangerous  
❌ Causes LazyInitializationException  
❌ Avoid unless you really know why   

### find vs load (Summary)

|Aspect	| find	     | load   |
|-----|-----------|--------|
|DB hit	| Immediate |	Deferred|
|Returns null if missing	| ✅         |	❌|
|Proxy	| ❌	        | ✅|
|Safe| 	✅        | 	❌        |


## saveOrUpdate() (Hibernate – Legacy)
>session.saveOrUpdate(entity);

#### Behavior

- Saves or updates based on ID
- Legacy method
- Ambiguous behavior

❌ Avoid  
✔ Use persist or merge instead

---

#### Banking / Enterprise Best Practices

In banking systems:

- Prefer JPA APIs (persist, merge, find)
- Avoid Hibernate-specific APIs
- Treat incoming data as detached
- Never rely on implicit behavior

---

### Summary

- persist → new entity
- merge → detached entity
- update → unsafe for detached
- find → safe, returns null
- load → proxy, risky
- saveOrUpdate → legacy

> **Final Takeaway:**    
> Use JPA APIs for safety and portability.  
> Hibernate APIs exist, but most production bugs come from misusing them.
