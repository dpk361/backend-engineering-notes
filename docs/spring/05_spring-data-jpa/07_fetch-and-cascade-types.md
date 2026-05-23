---
title: Fetch and Cascade Types
parent: Spring Data JPA
nav_order: 7
---

# Fetch and Cascade Types in JPA

Fetch and Cascade types control **how related entities are loaded** and **how operations propagate**
from one entity to another.

Misusing these two concepts is one of the **top reasons** for:
- N+1 query problem
- Slow APIs
- Accidental deletes
- Data corruption

Understanding them clearly is **mandatory** for real-world systems.

---

## Fetch Type vs Cascade Type

### Fetch Type
👉 **HOW data is loaded**
- When related entities are fetched
- Impacts performance and SQL execution

### Cascade Type
👉 **HOW operations are propagated**
- What happens to child entities when parent is saved/deleted

> **Golden Rule:**  
> Fetch = *loading behavior*  
> Cascade = *operation behavior*

They are **independent concepts**.

---

## Fetch Types in JPA

JPA supports two fetch types:

- `FetchType.EAGER`
- `FetchType.LAZY`

### FetchType.EAGER

#### What It Means
- Related entities are loaded **immediately**
- Happens in the same query or additional queries

#### Example

```java
@ManyToOne(fetch = FetchType.EAGER)
private Customer customer;
```

**Behavior:**

- Whenever Order is fetched → Customer is fetched
- Even if Customer is not needed

**Problems:**

- ❌ Unnecessary data loading
- ❌ Hidden extra SQL queries
- ❌ Performance issues

> Default for **ManyToOne** and **OneToOne** is **EAGER**.


### FetchType.LAZY (RECOMMENDED)

#### What It Means

- Related entity is loaded only when accessed
- Uses proxy objects

#### Example

```java
@ManyToOne(fetch = FetchType.LAZY)
private Customer customer;

```

#### Behavior

- Customer is NOT loaded initially
- Loaded only when getCustomer() is called

#### Benefits

- ✔ Better performance
- ✔ Less memory usage
- ✔ Predictable SQL

> **Best Practice:**
> Always prefer LAZY unless you have a strong reason.


### LazyInitializationException (Very Common)

#### Why It Happens

``` 
Customer customer = service.getCustomer(id);
customer.getOrders(); // ❌ Exception
```

#### Reason:

- Transaction already ended
- Persistence context is closed
- Lazy collection cannot be loaded

#### Solutions (High-Level)

- Fetch data inside transaction
- Use DTO projections
- Use fetch joins (JPQL)
- Avoid exposing entities to controllers

> Always override defaults explicitly.

---

## Cascade Types in JPA

Cascade types define which operations propagate from parent to child.

### Common Cascade Types

- `PERSIST`
- `MERGE`
- `REMOVE`
- `REFRESH`
- `DETACH`
- `ALL`

### CascadeType.PERSIST


> Saving parent also saves child

#### Example

```
@OneToMany(cascade = CascadeType.PERSIST)
private List<Order> orders;
```

```
entityManager.persist(customer);
```
- ✔ Orders are saved automatically

### CascadeType.MERGE

> Merging parent also merges child

#### Used when:

- Updating detached entities

### CascadeType.REMOVE

> Deleting parent deletes child

``` java
@OneToMany(cascade = CascadeType.REMOVE)
```

- ❌ Dangerous in banking systems

#### Example risk:

- Deleting customer → deletes all orders ❌
- Never use REMOVE blindly.

### CascadeType.ALL

> Applies all cascade operations

- ❌ Very risky
- ❌ Often misused


Avoid CascadeType.ALL unless entity lifecycle is tightly bound.

### When Cascade Makes Sense

- ✔ Parent truly owns child
- ✔ Child cannot exist without parent

#### Example:

- Order → OrderItems
- Invoice → InvoiceLines

#### When Cascade Is Dangerous

- ❌ Customer → Orders
- ❌ Account → Transactions

---

## Banking / Enterprise Best Practices

In banking systems:

- Use **LAZY** fetch almost everywhere
- Avoid cascading deletes
- Prefer explicit saves and deletes
- Treat parent and child lifecycles independently

---

## Common Mistakes

- ❌ Using default fetch types
- ❌ Using EAGER everywhere
- ❌ Using CascadeType.ALL blindly
- ❌ Relying on lazy loading in controllers
- ❌ Deleting parent without understanding cascade

---

## summary

- Fetch controls data loading
- Cascade controls operation propagation
- LAZY is preferred over EAGER
- Cascade REMOVE is dangerous
- Defaults are unsafe, override explicitly
- Fetch and cascade are independent

---

> **Key Takeaway:**
> - Fetch types impact performance.
> - Cascade types impact data safety.
> - Misusing either can break production systems.