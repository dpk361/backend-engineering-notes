---
title: Entity Lifecycle
parent: Spring Data JPA
nav_order: 5
---

# Entity Lifecycle in JPA

The **entity lifecycle** describes the different states an entity goes through
from creation to removal while interacting with JPA and Hibernate.

Understanding entity lifecycle is **critical** because most real-world JPA
issues (unexpected updates, missing inserts, lazy loading errors, memory leaks)
are caused by misunderstanding these states.

---

## What is an Entity?

An entity is a **Java object** that is:
- Mapped to a database table
- Managed by JPA through the **persistence context**
- Identified by a primary key

Entities are not just POJOs — their behavior depends on **their lifecycle state**.

---

## Why Entity Lifecycle Is Important

If you don’t understand entity lifecycle:
- Updates may not be saved
- Extra SQL queries may be executed
- Lazy loading may fail
- Memory usage may grow unexpectedly
- Transactions may behave incorrectly

In enterprise and banking systems, these issues can cause:
- Data inconsistency
- Performance degradation
- Production defects

---

## The Four Entity States

An entity can be in **one of four states**:

1. Transient
2. Managed (Persistent)
3. Detached
4. Removed

These states define **how JPA treats the entity**.

---

## 1️⃣ Transient State

### What it Means
- The entity is a normal Java object
- It is **not associated** with JPA
- No database row exists for it

### Example
```
Customer customer = new Customer();
customer.setName("Mohan");
```
At this point:

- JPA does not know about this object
- No SQL is generated
- No persistence happens

**Key Point**
> Transient entities are invisible to JPA

---

## 2️⃣ Managed (Persistent) State
### What it Means

- The entity is tracked by JPA
- It is associated with the persistence context
- Any change is automatically synchronized to the database

### How an Entity Becomes Managed

- save() / persist()
- findById()
- JPQL query result

### Example
```
Customer customer = customerRepository.save(new Customer("Mohan"));
customer.setName("Mohan Babu");
```
When the transaction commits:

- Hibernate detects the change
- An UPDATE statement is executed automatically

### Key Point

> Managed entities are automatically synchronized with the database

---
## 3️⃣ Detached State
### What it Means

- The entity was once managed
- It is no longer associated with the persistence context
- Changes are not tracked

### How an Entity Becomes Detached

- Transaction ends
- Persistence context is closed
- Entity is explicitly detached

### Example
```java
@Transactional
public Customer getCustomer(Long id) {
    return customerRepository.findById(id).get();
}
// transaction ends here
```

After the method returns:

- Persistence context is closed
- Entity is DETACHED
Even though the object still exists in memory.

#### 👉 Rule to remember
> End of transaction = entity becomes detached

```
Customer customer = customerRepository.findById(1L).get();
// transaction ends here

customer.setName("Updated Name");
```

#### Result:

- No SQL update happens
- Changes are ignored

### Key Point
> Detached entities look normal but JPA does not track them

---
## 4️⃣ Removed State
### What it Means

- The entity is marked for deletion
- Actual delete happens at transaction commit

### Example
```
customerRepository.delete(customer);
```

### Result:

- Entity becomes removed
- Database row is deleted on commit

---
### Persistence Context

The persistence context is:

- A memory space where JPA tracks entities
- Associated with a transaction (usually)

    #### Rules:

  - One persistence context = one identity per entity
  - Same entity ID → same object instance
  - Acts as a first-level cache

---
## Lifecycle Flow

```arduino 
new Entity()
↓
Transient
↓ persist / save
Managed
↓ transaction ends
Detached
↓ remove
Removed

```

---
### Notes

- Entity lifecycle defines how JPA tracks objects
- Managed entities are auto-synchronized
- Detached entities are not tracked
- Persistence context acts as first-level cache
- Most JPA bugs are lifecycle-related