---
title: Transactions
parent: Core
nav_order: 10
---

# Transactions in Spring Data JPA

A **transaction** represents a unit of work that must be executed **completely or not at all**.

Spring Data JPA relies on Spring’s transaction management to ensure **data consistency, integrity,
and reliability**.

Understanding transactions is critical because:
- JPA entity state depends on transactions
- Persistence context lifecycle depends on transactions
- Most data bugs occur due to incorrect transactional boundaries

---

## What is a Transaction?

A transaction is a sequence of operations that follows **ACID** properties:

- **Atomicity** – All or nothing
- **Consistency** – Data remains valid
- **Isolation** – Concurrent transactions don’t interfere incorrectly
- **Durability** – Committed data is permanent

In JPA, **SQL execution and entity synchronization happen within a transaction**.

---

## Why Transactions Are Important

Without transactions:
- Partial updates may occur
- Data becomes inconsistent
- Concurrent users overwrite data
- Entity changes may not be saved

In enterprise and banking systems, transactions are **mandatory**.

> **Rule:**  
> Any operation that modifies data must be transactional.

---

## Where Transactions Belong 

### Correct Layer
✅ **Service layer**

### Incorrect Layers
❌ Controller  
❌ Repository (business transactions)

---

## Typical Flow

```
Controller
↓
@Service (@Transactional)
↓
Repository
↓
Database
```

> **Best Practice:**  
> Keep transaction boundaries at the **service layer**.

---

## @Transactional Annotation

Spring manages transactions using `@Transactional`.

### Basic Usage

```java
@Transactional
public void transferMoney(Long from, Long to, BigDecimal amount) {
    // business logic
}
```

#### Spring:

- Opens transaction before method execution
- Commits if method succeeds
- Rolls back if method fails

---

## Transaction Commit & Flush

- **Flush** → Synchronizes persistence context with DB
- **Commit** → Makes changes permanent

#### Hibernate:

- Automatically flushes before commit
- May flush before queries

```
repository.save(entity); // not immediately written to DB
```

Actual SQL execution happens:
- At flush
- Or at transaction commit

---

## Rollback Rules 

#### Default Behavior

Spring rolls back only on **unchecked** exceptions:

- **RuntimeException**
- **Error**

❌ Does NOT rollback on **checked exceptions** by default.

---
 
## Custom Rollback Rules

```java

@Transactional(rollbackFor = Exception.class)
public void process() throws Exception {
}

```

#### Use when:

- Checked exceptions represent business failure

---

## Read-Only Transactions

```java 
@Transactional(readOnly = true)
public List<Customer> getCustomers() {
}
```

**Benefits:**

- Optimized performance
- Prevents accidental updates
- Clear intent

> Use read-only for query-only methods.

---

## Transaction Propagation 

Propagation defines how transactions behave when one transactional method calls another.

---

### REQUIRED (Default)

```
@Transactional
```
- Uses existing transaction
- Creates new one if none exists

✔ Most common
✔ Safe default

---

### REQUIRES_NEW

``` 
@Transactional(propagation = Propagation.REQUIRES_NEW)
```

- Suspends existing transaction
- Starts a new one

#### Use cases:

- Audit logging
- Notifications
- Independent operations

---

### SUPPORTS

- Joins transaction if present
- Runs non-transactional otherwise

---

### NOT_SUPPORTED

- Suspends existing transaction
- Runs without transaction

> Used rarely.

---

### Transaction Isolation Levels (High-Level)

Isolation controls how concurrent transactions see data.

#### Common levels:

- READ_COMMITTED (default)
- REPEATABLE_READ
- SERIALIZABLE

In most systems:

- Default isolation is sufficient
- Higher isolation = lower concurrency

---

#### Transactions & Entity Lifecycle (Connection)

- Entities are managed only inside a transaction
- After transaction ends → entities become detached
- Lazy loading works only within transaction

This explains:

- LazyInitializationException
- Missing updates
- Detached entity behavior

---

#### Common Transaction Mistakes

- ❌ Placing @Transactional on controller
- ❌ Forgetting transactions for write operations
- ❌ Long-running transactions
- ❌ Using REQUIRES_NEW blindly
- ❌ Assuming save() commits immediately

---

## Summary

- @Transactional manages commit & rollback

- Rollback happens on RuntimeException by default

- Service layer owns transactions

- Entities are managed within transactions

- REQUIRES_NEW creates independent transaction

- readOnly improves performance



