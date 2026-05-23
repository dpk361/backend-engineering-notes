---
title: Entity Relationships
parent: Spring Data JPA
nav_order: 6
---

# Entity Relationships in JPA

Entity relationships define **how tables are connected** in the database and
**how objects reference each other** in Java.

Correctly modeling relationships is **one of the most important and most misused**
parts of JPA. Many performance issues and bugs originate here.

---

## Why Entity Relationships Matter

In real applications:
- Data is never isolated
- Entities depend on other entities
- Tables are connected using foreign keys

If relationships are designed incorrectly:
- Too many SQL queries are executed
- Lazy loading fails
- Pagination breaks
- Performance degrades badly

> **Rule:**  
> Most JPA performance problems are caused by **incorrect relationship mapping**, not queries.

---

## Relationship Types in JPA

JPA supports four main relationship types:

1. One-to-One
2. One-to-Many
3. Many-to-One
4. Many-to-Many

Each relationship has **ownership rules**, **fetch behavior**, and **performance impact**.

---

## 1️⃣ One-to-One Relationship

### Use Case
- User ↔ Profile
- Account ↔ AccountDetails

### Example
```java
@Entity
public class User {

    @Id
    private Long id;

    @OneToOne
    @JoinColumn(name = "profile_id")
    private Profile profile;
}
```
**Key Points**

- One row maps to one row
- Foreign key exists on one side
- Use only when truly one-to-one

---

## 2️⃣ Many-to-One Relationship 

### Use Case

- Many Orders → One Customer
- Many Transactions → One Account

#### Example

```java

@Entity
public class Order {

    @Id
    private Long id;

    @ManyToOne
    @JoinColumn(name = "customer_id")
    private Customer customer;
}

```

**Key Points**

- Foreign key is on the many side
- Default fetch type: EAGER (important!)
- Very common in real systems

>Best Practice:
> 
>Many-to-One is the safest and most frequently used relationship.

---

## 3️⃣ One-to-Many Relationship

### Use Case

- Customer → Orders
- Account → Transactions

```java

@Entity
public class Customer {

    @Id
    private Long id;

    @OneToMany(mappedBy = "customer")
    private List<Order> orders;
}

```

#### Key Points

- Usually the inverse side
- No foreign key column here
- Controlled by mappedBy
- Default fetch type: LAZY

- ⚠️ This relationship is dangerous if misused.

### Owning Side vs Inverse Side

#### Owning Side

- Controls the foreign key
- Changes are persisted from this side

#### Inverse Side

- Uses mappedBy
- Does NOT control the relationship

```java
@OneToMany(mappedBy = "customer")
private List<Order> orders;
```

##### Here:

- Order.customer → owning side
- Customer.orders → inverse side

> Rule:
> Only the owning side updates the database.

---

## 4️⃣ Many-to-Many Relationship

### Use Case

- Users ↔ Roles
- Students ↔ Courses

#### Example

```java
@Entity
public class User {

    @ManyToMany
    @JoinTable(
        name = "user_roles",
        joinColumns = @JoinColumn(name = "user_id"),
        inverseJoinColumns = @JoinColumn(name = "role_id")
    )
    private Set<Role> roles;
}

```

#### Key Points

- Uses join table
- Hard to control
- Poor performance at scale

❌ Avoid Many-to-Many in banking systems

>Best Practice:
>
>Replace many-to-many with two many-to-one relationships using a join entity.

---

#### Fetch Types

| Relationship | Default Fetch |
|--------------|---------------|
| OneToOne	    | EAGER         |
| ManyToOne	   | EAGER         |
| OneToMany	   | LAZY          |
|  ManyToMany	 | LAZY          | 

---

### Banking / Enterprise Best Practices

In banking systems:

- Prefer Many-to-One
- Keep relationships unidirectional
- Avoid large collections
- Avoid Many-to-Many
- Always think about fetch behavior

#### Example:

- Transaction → Account (Many-to-One)
- Not Account → Transactions (One-to-Many in API layer)

---

### How Relationships Affect Pagination

#### Very important rule:

- Pagination + One-to-Many = risk

#### Why:

- Join multiplies rows
- Page size becomes incorrect
- Duplicate root entities appear

#### Solution:

- Paginate root entity only
- Load child collections separately
- Use DTO projections
