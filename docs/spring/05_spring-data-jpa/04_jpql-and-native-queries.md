---
title: JPQL and Native Queries
parent: Spring Data JPA
nav_order: 4
---

# JPQL and Native Queries in Spring Data JPA

JPQL and Native Queries are used when **derived query methods are not sufficient**
to express complex query logic or when fine-grained control over the query is required.

They allow developers to write **explicit queries**, while still integrating
with Spring Data JPA repositories.

---

## Why JPQL and Native Queries Are Needed

Derived queries are great for simple use cases, but they have limits.

You need JPQL or Native queries when:
- Queries involve multiple joins
- Query logic becomes complex
- Updates or deletes are required
- Performance tuning is necessary
- Database-specific features are needed

> **Rule:**  
> *Start with derived queries, move to JPQL, and use native queries only when required.*

---

## What is JPQL?

JPQL (Java Persistence Query Language):
- Is **object-oriented**
- Works on **entity names and fields**
- Is independent of the underlying database
- Is translated to SQL by Hibernate

JPQL queries are written in terms of **entities**, not tables.

---

## JPQL Example (Simple)

```java
@Query("SELECT c FROM Customer c WHERE c.status = :status")
List<Customer> findByStatus(@Param("status") String status);
```
Here:

- Customer → entity name
- status → entity field
- No table or column names are used

Hibernate converts this into SQL internally.

---

## JPQL with Joins

```java
@Query("""
       SELECT o
       FROM Order o
       JOIN o.customer c
       WHERE c.id = :customerId
       """)
List<Order> findOrdersByCustomer(@Param("customerId") Long customerId);

```

Why JPQL is preferred here:

- Clean object navigation
- Database-independent
- Easier to maintain than SQL

---

## Update and Delete Queries in JPQL

JPQL supports bulk update and delete operations.

```java
@Modifying
@Query("UPDATE Customer c SET c.status = :status WHERE c.id = :id")
int updateCustomerStatus(@Param("id") Long id,
                          @Param("status") String status);

```
### Important:

- @Modifying is mandatory
    
    > By default, Spring Data JPA assumes queries are SELECT statements.
  @Modifying tells Spring this is an UPDATE or DELETE query.
  Without @Modifying, you'll get an error like: 
    
    > **org.springframework.dao.InvalidDataAccessApiUsageException: Executing an update/delete query**

- These queries bypass the persistence context
- Managed entities may become stale

---

## What is a Native Query?

### Native queries:

- Are written in database-specific SQL
- Work directly on tables and columns
- Bypass JPQL abstraction

Use native queries when:

- Database-specific features are required
- Legacy queries already exist
- Performance tuning demands exact SQL

```java
@Query(
  value = "SELECT * FROM customers WHERE status = :status",
  nativeQuery = true
)
List<Customer> findActiveCustomers(@Param("status") String status);

```

**Here:**

- Table and column names are used
- Hibernate maps result back to entities

---

## Named Queries

Named queries are defined once and reused.

JPA provides two types of Named Queries:

1. @NamedQuery → Uses JPQL (database-independent).
2. @NamedNativeQuery → Uses Raw SQL (database-dependent).

### JPQL Named Query

A Named Query is defined at the entity level and can be called multiple times in different parts of the code.

```java 
@Entity
@NamedQuery(name = "Employee.findByDepartment", query = "SELECT e FROM Employee e WHERE e.department = :dept")
public class Employee {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private String department;
}
```

Using the Named Query in Repository

```java
public interface EmployeeRepository extends JpaRepository<Employee, Long> {

    @Query(name = "Employee.findByDepartment")
    List<Employee> findByDepartment(@Param("dept") String department);

}
```

#### 📌 Note:
- The query name **Employee.findByDepartment** must match the one in **@NamedQuery**.
- @Query(name = "queryName") tells Spring to look for a Named Query instead of writing JPQL inside @Query.

### Native Named Query

If we need raw SQL, we use @NamedNativeQuery.

```java 
@Entity
@NamedNativeQuery(
name = "Employee.findByDepartmentNative",
query = "SELECT * FROM employee WHERE department = :dept",
resultClass = Employee.class
)
public class Employee {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private String department;
}

```
Uses raw SQL but still maps the result to the Employee entity.

Using the Named Native Query in Repository

```java

public interface EmployeeRepository extends JpaRepository<Employee, Long> {

    @Query(name = "Employee.findByDepartmentNative", nativeQuery = true)
    List<Employee> findByDepartmentNative(@Param("dept") String department);

}

```

### 📌 Notes:
- nativeQuery = true → Tell JPA that this is a Native Query.
- resultClass = Employee.class → Maps the raw SQL result to Employee.

> Named queries were introduced to centralize and **pre-validate** frequently used queries at startup, but in modern Spring Data JPA, they are rarely used because @Query provides better readability and locality.


### Summary

- JPQL works on entities, not tables

- Native queries use database-specific SQL
- @Modifying is required for update/delete
- JPQL is preferred over native queries whenever possible

