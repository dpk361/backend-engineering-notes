---
title: N+1 Query Problem
parent: Spring Data JPA
nav_order: 10
---

# JPA N+1 Problem 

## 1. What is the N+1 Problem


The **N+1 Query Problem** occurs when:

1 query is executed to fetch a list of entities(parent), and  
then **N additional queries** are executed to fetch related entities(child).

Total queries executed:

> 1+ N


This leads to **serious performance issues**, especially when the dataset is large.

Example:

- 1000 records

Queries executed:

- 1 + 1000 = 1001 queries


---

## 2. Real World Example

Assume a banking system.

A **Customer can have multiple Accounts**.

### Tables

#### Customer

| id | name |
|---|---|
| 1 | Mohan |
| 2 | Ramesh |

#### Account

| id | account_number | customer_id |
|---|---|---|
| 101 | ACC1001 | 1 |
| 102 | ACC1002 | 1 |
| 103 | ACC2001 | 2 |

Important: Foreign key exists in Account table


---

## 3. Unidirectional Entity Mapping

In unidirectional mapping we **only keep the relation on the many side**.

Meaning:

> Account → Customer

but not

> Customer → Accounts


---

## Customer Entity

```java
@Entity
public class Customer {

    @Id
    private Long id;

    private String name;

}
```

## Account Entity


```java
@Entity
public class Account {

    @Id
    private Long id;

    private String accountNumber;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "customer_id")
    private Customer customer;

}
```

Important:      
Foreign key: account.customer_id

---

## 4. Repository

```java
public interface AccountRepository extends JpaRepository<Account, Long> {

}
```


## 5. Service Code

Suppose we fetch all accounts.

```
List<Account> accounts = accountRepository.findAll();

for(Account account : accounts) {

    System.out.println(account.getAccountNumber());

    System.out.println(account.getCustomer().getName());
}
```

## 6. What Queries Actually Run

Query 1 (Fetch accounts)

> SELECT * FROM account;

Example result:

```
Account1
Account2
Account3
```

Assume there are 3 accounts.

#### Lazy loading triggers additional queries

When this line executes:

> account.getCustomer().getName();

Hibernate runs:

```sql
SELECT * FROM customer WHERE id = 1;
SELECT * FROM customer WHERE id = 1;
SELECT * FROM customer WHERE id = 2;
```

## Total Queries

```
1 query for accounts
+ 3 queries for customers
-----------------------
4 queries
```

Formula 
>  1 + N

## 7. Why N+1 Happens

N+1 occurs when these two conditions happen together:

1️⃣ Fetch a list of entities       
2️⃣ Access a LAZY relationship inside a loop

**Pattern:**       

```
List<Entity> list = repository.findAll();

for(Entity e : list){
    e.getRelation();   // triggers additional queries
}
```

## 8. How to Detect N+1 Problem

Enable SQL logging.

application.properties

```properties
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
```

If you see repeated queries like:

```
select * from customer where id=?
select * from customer where id=?
select * from customer where id=?
```

You likely have an N+1 problem.

---

## 9. Solution 1 — Fetch Join 

Use JPQL Fetch Join.

**Repository**           

```sql
@Query("""
SELECT a FROM Account a
JOIN FETCH a.customer
""")
List<Account> findAccountsWithCustomer();
```

**Generated SQL**     

```sql
SELECT a.*, c.* FROM account a JOIN customer c ON a.customer_id = c.id;
```

**Queries Executed**

> 1 query only

Now both Account and Customer load in one query.

---

## 10. Solution 2 — EntityGraph

Spring Data JPA provides **EntityGraph.**

**Repository**     

```java
@EntityGraph(attributePaths = {"customer"})
List<Account> findAll();
```

This forces Hibernate to fetch the relationship together.

Result:

> Single optimized query

---

## 11. Solution 3 — DTO Projection (Best for APIs)

Instead of loading entities, fetch only required data.

### DTO

```java
public class AccountDTO {

    private String accountNumber;
    private String customerName;

}
```

### Repository

```java
@Query("""
SELECT new com.example.AccountDTO(
a.accountNumber,
c.name
)
FROM Account a
JOIN a.customer c
""")
List<AccountDTO> fetchAccountDetails();
```

## Generated SQL

```sql
SELECT a.account_number, c.name
FROM account a
JOIN customer c
ON a.customer_id = c.id;
```

### Benefits:

✔ Single query         
✔ Less memory usage         
✔ Faster API response         

## 12. Solution 4 — Batch Fetching

Hibernate can fetch related entities in batches.

application.properties

```properties
spring.jpa.properties.hibernate.default_batch_fetch_size=10
```

Instead of:

```
SELECT * FROM customer WHERE id=1
SELECT * FROM customer WHERE id=2
SELECT * FROM customer WHERE id=3
```

Hibernate runs:

> SELECT * FROM customer WHERE id IN (1,2,3)

---

## 13. Solution Comparison

| Solution       | Performance | Use Case                 |
| -------------- | ----------- | ------------------------ |
| Fetch Join     | Excellent   | Most common fix          |
| EntityGraph    | Excellent   | Cleaner Spring approach  |
| DTO Projection | Excellent   | APIs / read-only queries |
| Batch Fetching | Good        | Large collections        |


---

## 14. Important Best Practice

Many experienced teams follow this rule:

```
1. Keep entity relationships LAZY
2. Never rely on default fetching
3. Control fetching at query level
```

### Example mapping:

```
@ManyToOne(fetch = FetchType.LAZY)
private Customer customer;
```

Then explicitly fetch using:

- JOIN FETCH
- DTO queries
- EntityGraph

