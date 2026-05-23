---
title: DTO Mapping Strategies
parent: REST
nav_order: 7
---

# DTO Mapping Strategies in Spring Boot 

# 1. What is DTO Mapping?

DTO mapping is the process of converting:

```
Entity ↔ DTO
```

or

```
Request DTO → Domain Model
Domain Model → Response DTO
```

---

# 2. Why DTO Mapping Is Critical

Many beginners think DTO mapping is:

```
just copying fields
```

But in enterprise systems it is actually about:

* API contract isolation
* security
* backward compatibility
* data hiding
* performance
* versioning
* service boundaries

---

# 3. Real Banking Example

Suppose bank database entity:

```java
@Entity
public class BankAccount {

    private Long id;

    private String accountNumber;

    private String customerName;

    private BigDecimal balance;

    private String internalAuditCode;

    private String encryptedPin;

    private LocalDateTime createdAt;
}
```

---

# Problem

If you return entity directly:

```
return account;
```

API may expose:

❌ internalAuditCode
❌ encryptedPin
❌ internal DB structure
❌ unwanted sensitive fields

Huge banking security risk.

---

# 4. Industry Standard Approach

Always return Response DTOs.

---

# Response DTO

```java
public record AccountResponse(

        String accountNumber,

        String customerName,

        BigDecimal balance

) {}
```

---

# API Response

```json
{
  "accountNumber": "1234567890",
  "customerName": "Mohan",
  "balance": 50000
}
```

Safe and clean.

---

# 5. Request DTO vs Response DTO

This is extremely important.

---

# Request DTO

Used for:

```
incoming client input
```

Example:

```java
public record CreateAccountRequest(

        String customerName,

        String mobile,

        String aadhaarNumber

) {}
```

---

# Response DTO

Used for:

```text 
outgoing API response
```

Example:

```java
public record AccountResponse(

        String accountNumber,

        String customerName,

        BigDecimal balance

) {}
```

---

# Why Separate Them?

Because:

```text
input contract != output contract
```

Crucial in banking systems.

---

# 6. Real Banking Use Case — Money Transfer

---

# Transfer Request DTO

```java
public record TransferRequest(

        String fromAccount,

        String toAccount,

        BigDecimal amount,

        String remarks

) {}
```

---

# Transfer Response DTO

```java
public record TransferResponse(

        String transactionId,

        String status,

        Instant completedAt

) {}
```

---

# Why Not Return Entity?

Transaction entity may contain:

* internal ledger IDs
* settlement metadata
* reconciliation info
* fraud flags

These should NEVER leak externally.

---

# 7. Common Enterprise DTO Layers

Large banking systems often use:

| Layer        | Purpose               |
| ------------ | --------------------- |
| Request DTO  | API input             |
| Response DTO | API output            |
| Internal DTO | service communication |
| Entity       | database persistence  |
| Event DTO    | Kafka/event payloads  |

---

# 8. Industry Mapping Flow

```text
Client JSON
    ↓
Request DTO
    ↓
Mapper
    ↓
Entity / Domain Model
    ↓
Service Logic
    ↓
Mapper
    ↓
Response DTO
    ↓
JSON Response
```

---

# 9. Manual Mapping (Most Common Initially)

---

# Example

```java
public AccountResponse toResponse(BankAccount account) {

    return new AccountResponse(
            account.getAccountNumber(),
            account.getCustomerName(),
            account.getBalance()
    );
}
```

---

# Advantages

✅ explicit
✅ easy to debug
✅ simple

---

# Problems

❌ repetitive
❌ boilerplate
❌ difficult for huge objects

---

# 10. MapStruct (Industry Standard)

Most enterprise Spring Boot systems use:

```text
MapStruct
```

for DTO mapping.

---

# Why MapStruct?

Because it:

* generates compile-time mapping code
* avoids reflection
* high performance
* less boilerplate
* type-safe

---

# Example

```java
@Mapper(componentModel = "spring")
public interface AccountMapper {

    AccountResponse toResponse(
            BankAccount account);

    BankAccount toEntity(
            CreateAccountRequest request);
}
```

Spring injects it automatically.

---

# Generated Code

MapStruct generates:

```
account.getBalance()
```

style code at compile time.

Very fast.

---

# 11. Why Banks Prefer MapStruct

Large banking systems may have:

* 500+ DTOs
* 1000+ mappings
* massive API layers

Manual mapping becomes: `maintenance nightmare`


---

# 12. Nested Mapping Example

Real-world banking object:

---

# Entity

```java 
public class Transaction {

    private BankAccount sender;

    private BankAccount receiver;

    private BigDecimal amount;
}
```

---

# Response DTO

```java 
public record TransactionResponse(

        String senderAccount,

        String receiverAccount,

        BigDecimal amount

) {}
```

---

# MapStruct Nested Mapping

```java
@Mapper(componentModel = "spring")
public interface TransactionMapper {

    @Mapping(
            source = "sender.accountNumber",
            target = "senderAccount"
    )
    @Mapping(
            source = "receiver.accountNumber",
            target = "receiverAccount"
    )
    TransactionResponse toResponse(
            Transaction tx);
}
```

---

# 13. Partial Update Mapping (PATCH)

Very important.

---

# Problem

PATCH updates only provided fields.

Need to avoid:

```text
overwriting existing values with null
```

---

# Example

```json
{
  "customerName": "Mohan"
}
```

Should NOT nullify:

* mobile
* email
* address

---

# MapStruct Solution

```
@BeanMapping(
    nullValuePropertyMappingStrategy =
        NullValuePropertyMappingStrategy.IGNORE
)
```

---

# Example

```java
@Mapper(componentModel = "spring")
public interface AccountMapper {

    @BeanMapping(
        nullValuePropertyMappingStrategy =
            NullValuePropertyMappingStrategy.IGNORE
    )
    void updateAccount(
            UpdateAccountRequest request,
            @MappingTarget BankAccount account
    );
}
```

---

# 14. DTO Versioning

Crucial in enterprise APIs.

---

# V1 Response

```java
public record AccountResponseV1(
        String accountNumber
) {}
```

---

# V2 Response

```java
public record AccountResponseV2(
        String accountNumber,
        String branchCode
) {}
```

---

# Why DTOs Matter Here

DTOs isolate:

```
API contracts from database changes
```

---

# 15. Security Benefits of DTOs

Huge in banking systems.

---

# Prevents Exposure Of

- ❌ passwords
- ❌ account PINs
- ❌ audit flags
- ❌ internal notes
- ❌ fraud markers
- ❌ compliance metadata

---

# Example Attack

If an entity is exposed:

```json
{
  "role": "ADMIN"
}
```

attacker may manipulate fields.

DTOs prevent:

```text 
mass assignment vulnerabilities
```

---

# 16. Performance Considerations

Returning entities directly can trigger:

- ❌ lazy loading
- ❌ huge object graphs
- ❌ recursive serialization
- ❌ memory issues

---

# Example Problem

```text
Account
    ↓
Transactions
    ↓
Account
    ↓
Transactions
```

Can cause:

```text 
infinite recursion
```

or huge payloads.

---

# DTOs Solve This

Expose only:

```text 
minimal required fields
```

---

# 17. DTO Naming Conventions (Industry)

Very common conventions:

| Type         | Example               |
| ------------ | --------------------- |
| Request DTO  | CreateUserRequest     |
| Response DTO | UserResponse          |
| Update DTO   | UpdateUserRequest     |
| Internal DTO | UserDetailsDto        |
| Event DTO    | PaymentProcessedEvent |

---

# 18. Real Banking Example — Loan API

---

# Request

```java
public record LoanApplicationRequest(

        BigDecimal amount,

        Integer tenureMonths,

        BigDecimal annualIncome

) {}
```

---

# Internal Entity

```java
public class LoanApplication {

    private String riskScore;

    private String internalReviewNotes;

    private String fraudCheckStatus;
}
```

---

# Response DTO

```java
public record LoanApplicationResponse(

        String applicationId,

        String status

) {}
```

Internal fields hidden safely.

---

# 19. Common DTO Mapping Anti-Patterns

---

# ❌ Returning Entities Directly

Biggest beginner mistake.

---

# ❌ Reusing Same DTO Everywhere

Bad:

```text 
One DTO for create/update/response
```

Creates:

* coupling
* confusion
* validation issues

---

# ❌ Business Logic Inside Mapper

Mapper should:

```text 
transform data only
```

NOT:

* validate
* call DB
* execute business logic

---

# ❌ Massive DTOs

Huge DTOs create:

* maintenance issues
* bandwidth waste
* serialization overhead

---

# 20. Best Enterprise Practices

## Use DTOs Always

Never expose entities externally.

---

# Separate DTO Types

Use:

* request DTO
* response DTO
* update DTO

---

# Prefer Immutable DTOs

Use:

```
record
```

when possible.

---

# Use MapStruct

For:

* compile-time mapping
* performance
* maintainability

---

# Keep Mappers Pure

Only:

```text
data transformation
```

---

# 21. Recommended Enterprise Package Structure

```text 
dto/
    request/
    response/

mapper/

entity/

service/

controller/
```

---

# 22. Real Enterprise Mapping Flow

```text 
Request JSON
    ↓
Request DTO
    ↓
Validation
    ↓
Mapper
    ↓
Entity
    ↓
Service
    ↓
Mapper
    ↓
Response DTO
    ↓
Response JSON
```

---

# 23. Senior Engineering Insight

DTO mapping is NOT:

```text
just copying fields
```

It is:

* API contract engineering
* security engineering
* scalability engineering
* compatibility engineering

---

# 24. Final Recommendation

## For Enterprise Spring Boot Systems

Use:

* Request DTOs
* Response DTOs
* MapStruct
* Immutable DTOs
* Separate API contracts from entities

Avoid:

* entity exposure
* generic DTO reuse
* business logic in mappers

This is the standard approach used in:

* banking systems
* fintech platforms
* payment gateways
* enterprise microservices



