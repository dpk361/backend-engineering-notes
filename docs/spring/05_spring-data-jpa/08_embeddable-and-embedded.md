---
title: Embeddable and Embedded
parent: Spring Data JPA
nav_order: 8
---

# Embeddable and Embedded in JPA

`@Embeddable` and `@Embedded` are used to **model value objects** in JPA.
They allow you to group related fields together **without creating a separate table**.

This is a clean way to represent **conceptual objects** like Address, AuditInfo,
or Money inside an entity.

---

## What is @Embeddable?

An **embeddable** is a class whose fields are:
- Stored as columns in the **owning entity’s table**
- Do NOT have their own table
- Do NOT have their own identity (no primary key)

It represents a **value**, not an entity.

---

## What is @Embedded?

`@Embedded` is used in an entity to:
- Include an embeddable object
- Flatten its fields into the entity table

---

## Why Embeddables Are Needed

Without embeddables:
- Entities become large and cluttered
- Repeated fields appear in multiple entities
- Poor readability and duplication

Embeddables help:
- Group related fields logically
- Improve readability
- Reuse common structures
- Follow SRP (Single Responsibility Principle)

---

## Simple Example: Address (Very Common)

### Embeddable Class

```java
@Embeddable
public class Address {

    private String street;
    private String city;
    private String state;
    private String pincode;
}
```

---

## Entity Using Embedded

```java
@Entity
public class Customer {

    @Id
    private Long id;

    private String name;

    @Embedded
    private Address address;
}

```

Database Table Structure

```
customer
--------
id
name
street
city
state
pincode

```

- 👉 No separate address table
- 👉 Address fields are flattened into customer table

---

## Important Characteristics 

### Embeddables:

- ❌ Cannot be queried independently
- ❌ Cannot be shared as entities
- ❌ Do not have lifecycle

### Embeddables:

- ✔ Are part of the owning entity
- ✔ Are persisted automatically
- ✔ Follow the entity’s lifecycle

---

## Using Multiple Embedded Objects

```java

@Entity
public class Branch {

    @Embedded
    private Address officeAddress;

    @Embedded
    @AttributeOverrides({
        @AttributeOverride(name = "street", column = @Column(name = "billing_street")),
        @AttributeOverride(name = "city", column = @Column(name = "billing_city")),
        @AttributeOverride(name = "state", column = @Column(name = "billing_state")),
        @AttributeOverride(name = "pincode", column = @Column(name = "billing_pincode"))
    })
    private Address billingAddress;
}

```

Use **AttributeOverrides**

#### When:

- Same embeddable is used multiple times
- Column names clash

---

### Embeddable vs Entity 

| Aspect   | 	Embeddable	   | Entity    |
|----------|----------------|-----------|
|Identity	|❌ No ID	|✔ Has ID|
|Table	| ❌ No table |	✔ Own table |
|Lifecycle	|❌ No |	✔ Yes |
|Queryable	|❌ No |	✔ Yes |
|Use case	|Value object |	Business object |

---

### When NOT to Use Embeddable

Do NOT use embeddable if:

- Object has its own lifecycle
- Object needs separate table
- Object is shared across many entities
- Object needs independent queries

In such cases → use an entity.

---

### Summary


- Embeddables are value objects
- Stored in the same table as entity
- No identity, no lifecycle
- Use @AttributeOverrides for column conflicts
- Good for grouping related fields


