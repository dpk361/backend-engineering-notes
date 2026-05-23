---
title: Repositories
parent: Spring Data JPA
nav_order: 2
---

# Repository Interfaces in Spring Data JPA

Repository interfaces are the **core abstraction** provided by Spring Data JPA to handle database operations.

They allow developers to perform CRUD and query operations **without writing boilerplate DAO code**, while still keeping full control when needed.

---

## What is a Repository?

A repository is an **interface** that represents a collection like access to entities.

Instead of:
- Writing DAO classes
- Injecting `EntityManager`
- Writing repetitive CRUD code

You define an interface, and **Spring generates the implementation automatically at runtime**.

> Think of a repository as a **bridge** between your domain model and the database.

---

## Why Do We Need Repository Interfaces?

Without repositories:
- Every entity needs a DAO class
- CRUD logic is repeated
- Code becomes verbose and error-prone
- Maintenance becomes difficult

Repository interfaces:
- Reduce boilerplate
- Improve readability
- Encourage clean layering (Controller → Service → Repository)
- Let developers focus on business logic

---

## Where Repositories Fit in the Application

Typical backend flow:
>Controller → Service → Repository → Hibernate → Database


Important rules:
- Controllers should **never** talk directly to repositories
- Business logic belongs in the service layer
- Repositories should focus only on data access

---

## Repository Hierarchy 

Spring Data JPA provides a **hierarchy of repository interfaces**.

### Repository (Marker Interface)

```java
public interface Repository<T, ID> {
}
```
- Base marker interface
- Does not provide any methods
- Rarely used directly

### CrudRepository

```java

public interface CrudRepository<T, ID> extends Repository<T, ID> {
}
```

Provides basic **CRUD** operations:

- save()
- findById()
- findAll()
- deleteById()

Use when:

- Very simple CRUD requirements
- Minimal functionality needed

---

### PagingAndSortingRepository

```java

public interface PagingAndSortingRepository<T, ID> extends CrudRepository<T, ID> {
}
```
Adds:

- Pagination
- Sorting

Use when:

- Large result sets
- APIs need pageable responses

---

### JpaRepository (Most Common)

```java 
public interface JpaRepository<T, ID> extends PagingAndSortingRepository<T, ID> {
}
```
Adds:

- flush() - Flushes all pending changes to the database.
- saveAndFlush() - Saves an entity and flushes changes instantly.
- Batch operations
- JPA-specific behavior

> JpaRepository is the most commonly used interface in real projects.
 
> Use JpaRepository unless you have a strong reason not to.

---

### Creating a Repository (Example)

```java
@Repository
public interface CustomerRepository extends JpaRepository<Customer, Long> {
}
```
That’s it.

Spring automatically:
- Detects the interface
- Generates an implementation
- Registers it as a Spring bean

---

### How Spring Creates Repository Implementations
At application startup:

1. Spring scans for repository interfaces
2. Creates proxy implementations
3. Wires them into the application context
4. Delegates calls to Hibernate internally

You never see the actual implementation class.


#### Repositories support:

- Derived query methods
- JPQL queries
- Native SQL queries

Example:
```java
List<Customer> findByStatus(String status);
```

---

### Notes:

- Repository implementations are generated at runtime
- JpaRepository is preferred in most cases
- Repositories should not contain business logic
- Pagination is critical for performance
- Repositories are Spring-managed beans
