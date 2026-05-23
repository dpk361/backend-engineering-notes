---
title: How Transactional works internally
parent: Core
nav_order: 11
---

# How `@Transactional` works internally

```text
1. Spring Boot Application Starts
        ↓
2. Transaction Management Enabled
        ↓
3. Spring Scans Beans for @Transactional
        ↓
4. Spring Creates Proxy for Transactional Beans
        ↓
5. Client Calls Service Method
        ↓
6. Proxy Intercepts Method Call
        ↓
7. TransactionInterceptor Reads @Transactional Metadata
        ↓
8. TransactionManager Starts Transaction
        ↓
9. Method Executes (DB operations happen)
        ↓
10. Exception Check
        ↓
11A. No Exception → Commit
11B. Exception → Rollback
        ↓
12. Connection Released
```


## 1. Spring Boot Application Starts

```java
@SpringBootApplication
public class BankingApplication {
    public static void main(String[] args) {
        SpringApplication.run(BankingApplication.class);
    }
}
```

During startup Spring Boot performs:

```text
Component Scan
Auto Configuration
Bean Creation
AOP Setup
Transaction Infrastructure Setup
```

Spring Boot automatically enables transaction management through auto-configuration.

## 2. Transaction Management Gets Enabled

Spring registers transaction infrastructure via:

> @EnableTransactionManagement

Spring Boot usually enables this automatically through auto configuration.

This registers key beans:

```
TransactionInterceptor
TransactionAttributeSource
PlatformTransactionManager
BeanFactoryTransactionAttributeSourceAdvisor
```

These are responsible for transaction handling.

## 3. Spring Scans Beans for @Transactional

During bean creation Spring checks:

> Does this bean contain @Transactional?

Example:

```java
@Service
public class TransferService {

    @Transactional
    public void transferMoney() { }
}
```

Spring detects the annotation using:

> AnnotationTransactionAttributeSource

## 4. Spring Creates a Proxy

Spring does NOT modify your class directly.

Instead it creates a proxy wrapper around the bean.

```
Original Bean
TransferService
```

Spring creates:

```
TransferServiceProxy
```

Architecture becomes:

```
Controller
↓
Proxy
↓
Actual Service Bean
```

**Proxy types used:**       

| Proxy Type        | When Used              |
| ----------------- | ---------------------- |
| JDK Dynamic Proxy | If class has interface |
| CGLIB Proxy       | If no interface        |


## 5. Client Calls Service Method

Example call:

> Controller → transferService.transferMoney()

Actual call becomes:

> Controller → Proxy → Service

The **proxy intercepts the method call.**

## 6. Proxy Intercepts Method

The proxy delegates to:

> TransactionInterceptor

This class is responsible for transaction logic.

Pseudo flow:

> invokeWithinTransaction()

## 7. Read @Transactional Metadata

TransactionInterceptor reads annotation properties:

Example:

```java
@Transactional(
propagation = REQUIRED,
isolation = READ_COMMITTED,
rollbackFor = Exception.class
)
public void transferMoney() { }
```

Metadata is obtained from:

> TransactionAttributeSource

## 8. Transaction Manager Starts Transaction

Spring gets the configured transaction manager.

Common one in Spring Boot:

> JpaTransactionManager

All transaction managers implement:

> PlatformTransactionManager

Now Spring calls:

> getTransaction()

Internally:

```
Connection connection = datasource.getConnection();
connection.setAutoCommit(false);
```

Transaction is now started.

## 9. Transaction Stored in ThreadLocal

Spring binds the connection to the current thread using:

TransactionSynchronizationManager

Internally:

> ThreadLocal

This ensures:

> Same transaction across all repository calls

Example:

- save()
- update()
- delete()

All use the same connection.

## 10. Actual Method Executes

Now the real method runs:

```java
@Transactional
public void transferMoney() {

    debitAccount();
    creditAccount();

}
```

All database operations execute within the same transaction.

## 11. Method Completes → Decision Point

Spring checks:

> Did an exception occur?

### Case A — Success → Commit

Spring calls:

> transactionManager.commit()

Internally:

> connection.commit()

Database permanently saves changes.

### Case B — Exception → Rollback

Spring calls:

> transactionManager.rollback()

Internally:

> connection.rollback()

All changes are undone.

Default rollback conditions:

- RuntimeException
- Error

**Checked exceptions do not rollback by default.**


## 12. Transaction Cleanup

Finally Spring performs cleanup:

- Connection released
- ThreadLocal cleared
- Resources closed


- TransactionSynchronizationManager.clear()

Transaction lifecycle ends.

---


# Transactions work only when the method is called through the proxy.

This will NOT start transaction:
> this.transferMoney();

Because proxy is bypassed.

