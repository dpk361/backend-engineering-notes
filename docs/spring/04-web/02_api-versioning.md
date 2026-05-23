---
title: API Versioning
parent: Web
nav_order: 2
---

# API Versioning

**API Versioning** is the practice of managing changes to APIs in a way that
**does not break existing clients**.

It allows backend services to evolve while maintaining **backward compatibility**
with consumers such as frontend apps, mobile apps, or other microservices.

---

## Why API Versioning Is Required

APIs inevitably change due to:
- New features
- Business rule changes
- Data model evolution
- Security requirements
- Performance improvements

Without versioning:
- Existing clients break ❌
- Production incidents occur ❌
- Rollbacks become risky ❌

> **Core principle:**  
> Never break an existing API contract used by clients.

---

## What Is an API Contract?

An API contract defines:
- Endpoints (URLs)
- HTTP methods
- Request structure
- Response structure
- Validation rules
- Error formats
- Field semantics (meaning)

Once published, an API contract becomes a **promise** to clients.

---

## When Should You Create a New API Version?

Create a new version when you introduce **breaking changes**, such as:
- Removing or renaming fields
- Changing field types
- Changing validation rules
- Changing business behavior
- Changing response semantics

### What Does NOT Require a New Version
- Adding optional fields
- Internal refactoring
- Performance improvements
- Bug fixes that preserve behavior

---

## Common API Versioning Strategies

There are **four commonly used approaches**.

---

## 1️⃣ URI-Based Versioning (Most Common)

### Example
```http
GET /api/v1/accounts/123
GET /api/v2/accounts/123
```

#### Spring Boot Example

```java
@RestController
@RequestMapping("/api/v1/accounts")
class AccountV1Controller { }
```

```java
@RestController
@RequestMapping("/api/v2/accounts")
class AccountV2Controller { }
```

#### Pros

✔ Very clear and explicit  
✔ Easy to debug and route  
✔ Most widely adopted  

#### Cons

❌ URL changes between versions  
❌ Controller duplication   

---

## 2️⃣ Request Header Versioning

### Example
```http
GET /accounts/123  
X-API-VERSION: 2  
```

#### Spring Boot Example

```
@GetMapping(value = "/accounts/{id}", headers = "X-API-VERSION=2")
```

#### Pros

✔ Clean URLs  
✔ Explicit version control  

Cons

❌ Harder to test  
❌ Less visible  
❌ Not browser-friendly  

---

## 3️⃣ Media Type (Accept Header) Versioning

### Example

```
Accept: application/vnd.bank.account.v2+json
```

### Spring Boot Example

```
@GetMapping(
value = "/accounts/{id}",
produces = "application/vnd.bank.account.v2+json")
```

#### Pros

✔ REST-compliant  
✔ Clean URLs  

#### Cons

❌ Complex to implement  
❌ Hard to understand  
❌ Rarely used in practice  

---

## 4️⃣ Query Parameter Versioning (Least Preferred)
### Example

```
GET /accounts/123?version=2
```

### Pros

✔ Easy to implement  

### Cons

❌ Easy to misuse  
❌ Poor governance  
❌ Generally discouraged  
---

## How Versioning Is Implemented in Real Projects

Where Versioning Lives

- Controller layer
- DTOs (request/response models)

What Usually Does NOT Change

- Service layer (mostly reused)
- Repository layer
- Database schema (often)

---

## Example: API Evolution (Banking Context)

V1 Response

```json
{
"accountId": "123",
"balance": 5000
}
```

V2 Response (Breaking Change)

```json
{
"accountId": "123",
"balance": 5000,
"currency": "INR"
}
```

✔ V1 clients continue working  
✔ V2 clients get enhanced contract  

---

## Contract Sharing & Communication (VERY IMPORTANT)

API versioning is incomplete without contract communication.

Common ways to share contracts:

- OpenAPI / Swagger specs
- API documentation portals
- GitHub Pages
- Release notes & change logs
- Versioning is both a technical and communication process.

---

## Deprecation Strategy

Old versions should not be removed immediately.

Recommended approach:

1. Release new version
2. Mark old version as deprecated
3. Inform clients
4. Monitor usage
5. Remove after migration window

#### Example:

```
@Deprecated
@RequestMapping("/api/v1/accounts")
```

---

## Microservices Perspective

In microservices:

- Services are also clients
- Contract stability is critical
- Versioning prevents cascading failures
- Backward compatibility is preferred over version explosion

---

## Banking / Enterprise Best Practices

- URI-based versioning is most common
- Long deprecation cycles
- Strict backward compatibility
- Contracts reviewed before release
- Old versions removed carefully

---


> API versioning allows APIs to evolve without breaking existing clients.
> I’ve used URI-based versioning with separate controllers per version, 
> shared the updated contract with clients, and followed a proper deprecation strategy.



