---
title: Pagination and Sorting
parent: Spring Data JPA
nav_order: 9
---

# Pagination and Sorting in Spring Data JPA

Pagination and sorting are essential techniques for handling **large datasets efficiently**.
Instead of loading all records at once, data is fetched in **small, manageable chunks**
with a defined order.

In real-world systems (especially banking and enterprise applications),
**pagination is mandatory**, not optional.

---

## Why Pagination Is Important

Without pagination:
- Large tables are fully loaded into memory
- APIs become slow
- Database load increases
- Application may crash due to memory issues

Pagination helps:
- Improve performance
- Reduce memory usage
- Control database load
- Provide better API responses

> **Rule:**  
> Any API that can return more than a few hundred records **must use pagination**.

---

## Core Pagination Interfaces

Spring Data JPA provides three main abstractions:

### Pageable
Represents pagination information:
- Page number
- Page size
- Sorting details

### Page
Represents a **page of results** plus metadata.

### Slice
Represents a chunk of results **without total count**.

---

## Basic Pagination Example

### Controller
```java

@GetMapping("/api/customers")
public Page<Customer> getAllCustomers(@RequestParam int page, @RequestParam int size, @RequestParam String sortBy) {

    Pageable pageable =  PageRequest.of(page, size, Sort.by(sortBy).ascending());

    return customerService.getAllCustomers(pageable);
}


@GetMapping("/api/customers/branch/")
public Page<Customer> getAllCustomersByBranchCode(@RequestParam String branchCode, @RequestParam int page, @RequestParam int size) {

    Pageable pageable =  PageRequest.of(page, size);

    return customerService.getAllCustomersByBranchCode(branchCode, pageable);
}

```
Ex: page = 0, size = 10

**What Happens**

- Page number starts from 0
- Page size = 10
- SQL uses LIMIT and OFFSET


### Service

```java
public Page<Customer> getAllCustomersByBranchCode(String branchCode, Pageable pageable ){

    return customerRepository.findByBranchCode(branchCode, pageable);
}


public Page<Customer> getAllCustomers(Pageable pageable){

    return customerRepository.findAll(pageable);
}

```

### Repository

```java 
    Page<Customer> findAll(Pageable pageable);

    Page<Customer> findByBranchCode(String branchCode, Pageable pageable);
```


### Result
```
http://localhost:8084/api/customers/branch/?branchCode=001&page=4&size=10

```

```json

{
  "content": [
    {
      "id": 112,
      "customerNumber": "0010041",
      "name": "Pardeep Kumar",
      "email": "pardeep90@gmail.com",
      "phone": "9000000090",
      "branchCode": "001"
    },
    {
      "id": 113,
      "customerNumber": "0010042",
      "name": "Rituraj Singh",
      "email": "rituraj91@gmail.com",
      "phone": "9000000091",
      "branchCode": "001"
    },
    {
      "id": 114,
      "customerNumber": "0010043",
      "name": "Sahil Gupta",
      "email": "sahil92@gmail.com",
      "phone": "9000000092",
      "branchCode": "001"
    },
    {
      "id": 115,
      "customerNumber": "0010044",
      "name": "Tarsem Lal",
      "email": "tarsem93@gmail.com",
      "phone": "9000000093",
      "branchCode": "001"
    },
    {
      "id": 116,
      "customerNumber": "0010045",
      "name": "Upendra Yadav",
      "email": "upendra94@gmail.com",
      "phone": "9000000094",
      "branchCode": "001"
    },
    {
      "id": 117,
      "customerNumber": "0010046",
      "name": "Vijay Shekhar",
      "email": "vijay95@gmail.com",
      "phone": "9000000095",
      "branchCode": "001"
    },
    {
      "id": 118,
      "customerNumber": "0010047",
      "name": "Waseem Akhtar",
      "email": "waseem96@gmail.com",
      "phone": "9000000096",
      "branchCode": "001"
    },
    {
      "id": 119,
      "customerNumber": "0010048",
      "name": "Yashwant Patil",
      "email": "yashwant97@gmail.com",
      "phone": "9000000097",
      "branchCode": "001"
    },
    {
      "id": 120,
      "customerNumber": "0010049",
      "name": "Zubin Irani",
      "email": "zubin98@gmail.com",
      "phone": "9000000098",
      "branchCode": "001"
    },
    {
      "id": 121,
      "customerNumber": "0010050",
      "name": "Aadil Khan",
      "email": "aadil99@gmail.com",
      "phone": "9000000099",
      "branchCode": "001"
    }
  ],
  "pageable": {
    "pageNumber": 4,
    "pageSize": 10,
    "sort": [],
    "offset": 40,
    "paged": true,
    "unpaged": false
  },
  "last": false,
  "totalPages": 6,
  "totalElements": 51,
  "size": 10,
  "number": 4,
  "sort": [],
  "first": false,
  "numberOfElements": 10,
  "empty": false
}
```

---

## Page Object – Important Methods

```
Page<Customer> page = ...
```

**Key methods:**

- getContent() → actual records
- getTotalElements() → total rows
- getTotalPages() → total pages
- getNumber() → current page number
- first / last -> current page is first page / last page --> true/false 

> Page always executes a COUNT query.

---

## Sorting Basics

Sorting defines the **order** in which data is returned.

```java
List<Customer> findAll(Sort sort);
```

```java
Sort sort = Sort.by("createdDate").descending();
List<Customer> customers = repository.findAll(sort);
```
### Sorting by Multiple Fields

```java 
Sort sort = Sort.by(
        Sort.Order.desc("createdDate"),
        Sort.Order.asc("name") );

Pageable pageable = PageRequest.of(0, 10, sort);
```

**Meaning:**

- First sort by date (desc)
- If same date → sort by name


---

## Pagination + Sorting Together

```java
Pageable pageable = PageRequest.of(0, 10, Sort.by("createdDate").descending());

Page<Customer> page = repository.findAll(pageable);
```

> This is the standard pattern used in REST APIs.

---

## Slice vs Page

### Page

- Executes COUNT query
- Provides total pages & total elements
- Slightly heavier

### Slice

- No count query
- Faster
- Only tells if next page exists

**Slice Example**

```java
Slice<Customer> findByStatus(String status, Pageable pageable);
```

##### Use Slice when:

- Infinite scrolling
- Performance is critical
- Total count is not required

---

## Pagination with JPQL Queries

```java
@Query("SELECT c FROM Customer c WHERE c.branchCode = :branchCode")
Page<Customer> findAllCustomersByBranchCode(@Param("branchCode") String branchCode, Pageable pageable);
```

**Spring automatically:**
- Applies pagination
- Applies sorting
- Generates count query

---

## Pagination with Native Queries

Native queries require manual count query.

```java
@Query(
  value = "SELECT * FROM customers WHERE c.branchCode = :branchCode",
  countQuery = "SELECT COUNT(*) FROM customers WHERE c.branchCode = :branchCode",
  nativeQuery = true
)
Page<Customer> findAllCustomersByBranchCode( @Param("branchCode") String branchCode, Pageable pageable);

```

> ❗ Missing countQuery → pagination breaks.

---

## Common Mistakes

- ❌ Fetching all records and paginating in memory
- ❌ Forgetting pagination for large tables
- ❌ Using Page when Slice is enough
- ❌ Sorting by non-indexed columns

---

## Performance Considerations

- Large page numbers → high OFFSET cost
- Avoid sorting on text fields without indexes
- Use Slice for better performance where possible

---
### Question :  Is “Get All Records and Paginate in UI” a Good Practice?

- No. It is NOT a good practice.
- In backend systems, it is actually considered dangerous.

**Why it’s bad:**

1. Huge data transfer
   - Backend sends thousands/millions of rows
   - Network becomes the bottleneck


2. High memory usage

   - Backend loads all records into memory
   - UI also stores all data in memory

3. Poor scalability
   - Works for 100 records
   - Fails badly for 1M records

4. Security & stability risk
   - Easy way to overload the system
   - Can cause OutOfMemory errors

👉 This approach might work for small admin screens, but never for production APIs.

---

### ✅ Correct & Professional Approach (Used Everywhere)

> - Backend does pagination
> - UI requests data page by page

each page click fires a new backend request.

### 🔁 How Real Pagination Works (Step-by-Step)
 
#### 1. UI requests first page

> GET /customers?page=0&size=20

#### 2. Backend responds with:

- 20 records
- Metadata (total count, total pages)

```
{ 
"content": [...20 records...],
"totalElements": 1250,
"totalPages": 63,
"pageNumber": 0,
"pageSize": 20
}

```

#### 3. User clicks “Next”

> GET /customers?page=1&size=20

#### 4. Backend executes a new query with OFFSET/LIMIT

> **Then how does UI know total records?** Answer: Backend tells it.

**Using:**

> - Page.getTotalElements()
> - Page.getTotalPages()

That’s why Page exists.

---

## Summary

- Page executes count query, Slice does not
- Pageable starts from page 0
- Sorting can be combined with pagination
- Native queries need explicit count query
- Pagination is essential for performance

