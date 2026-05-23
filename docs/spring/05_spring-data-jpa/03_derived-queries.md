---
title: Derived Query Methods
parent: Spring Data JPA
nav_order: 3
---

# Derived Query Methods in Spring Data JPA

Derived query methods allow developers to **create database queries by simply
naming repository methods**, without writing JPQL or SQL explicitly.

Spring Data JPA **parses the method name**, understands the intent, and
automatically generates the query at runtime.

---

## What are Derived Queries?

Derived queries are repository methods whose **names describe the query logic**.

Example:
```java
List<Customer> findByStatus(String status);
```
Here:
- 
- findBy → indicates a query
- Status → entity field name

Spring Data JPA automatically generates:
```sql
SELECT * FROM customer WHERE status = ?
```
### Why Derived Queries Exist

Before derived queries:

- Developers wrote JPQL for simple queries
- Repeated boilerplate for basic filters
- Reduced readability

Derived queries solve this by:
  
- Eliminating simple query boilerplate
- Making intent obvious from method name
- Improving readability and speed of development

#### Key idea:
> If the query is simple, the method name itself should describe it.

---
### Where Derived Queries Are Best Used

Derived queries are ideal for:

- Simple filtering conditions
- CRUD-style queries
- Read-heavy operations
- Clear, predictable query logic

They are commonly used in:

- Service-layer reads
- Search endpoints
- Reference data lookups
--- 

## Basic Naming Rules
A derived query method generally follows this pattern:

```php-template
findBy<Field><Condition>
```
#### Examples:
```java
findByName(String name);
findByStatus(String status);
findByEmail(String email);
```
#### Rules:

- Field names must match entity field names, not column names
- Method names are case-sensitive for field parts
- CamelCase is mandatory

---

## Common Keywords in Derived Queries

### Comparison Keywords

> - findByAgeGreaterThan(int age)
> - findByAgeLessThan(int age)
> - findByAgeBetween(int min, int max)

### String Matching

> - findByNameLike(String pattern)
> - findByNameContaining(String text)
> - findByNameStartingWith(String prefix)
> - findByNameEndingWith(String suffix)

### Multiple Conditions

> - findByStatusAndType(String status, String type)
> - findByStatusOrType(String status, String type)

### Boolean Conditions

> - findByActiveTrue()
> - findByActiveFalse()

### Null Checks

> - findByEmailIsNull()
> - findByEmailIsNotNull()

### Sorting with Derived Queries

```java
List<Customer> findByStatusOrderByCreatedDateDesc(String status);
```

Or handled dynamically using Sort:
```java
List<Customer> findByStatus(String status, Sort sort);
```
> Dynamic sorting is preferred for flexibility.

### Pagination with Derived Queries

```java
Page<Customer> findByStatus(String status, Pageable pageable);
```
This is critical for:

- Large datasets
- APIs returning lists
- Performance optimization

---

## Return Types Supported

Derived queries support multiple return types:
```
- List<T>
- Optional<T>
- Page<T>
- Single entity
```
#### Example
```java
Optional<Customer> findByEmail(String email); 
```

---

## How Spring Processes Derived Queries (Internally)

At startup:

- Spring scans repository interfaces
- Parses method names
- Builds query metadata
- Generates JPQL dynamically
- Executes via Hibernate at runtime 

You don’t see SQL, but it exists under the hood.

---

## Limitations of Derived Queries

Derived queries are not suitable when:

- Query logic is complex
- Multiple joins are required
- Subqueries are involved
- Performance tuning is critical

In such cases:

- JPQL
- Native queries
- Specifications

are better options.

---

## Common Mistakes

- ❌ Using database column names instead of entity fields
- ❌ Overly long method names
- ❌ Complex logic in method names
- ❌ Ignoring pagination for large result sets

> If the method name becomes hard to read, stop and use JPQL.

---

## Summary Notes

- Derived queries are parsed from method names
- Field names must match entity attributes
- Best for simple queries
- Not suitable for complex joins
- Pagination support is built-in


> Derived queries improve readability and speed of development, but should be used only when query logic remains simple and clear.