---
title: Strategy Design Pattern
parent: Behavior Patterns
nav_order: 1
---

# Strategy Design Pattern

---

# What is Strategy Pattern?

Strategy Pattern means:

```text
Same task
Different ways to perform it
Choose behavior at runtime
```

---

# Real Banking Example

A bank supports multiple payment methods:

* UPI
* Credit Card
* Net Banking
* Wallet

All do the same thing:

```text
Process Payment
```

But implementation is different for each.

---

# Problem Without Strategy Pattern

```java
class PaymentService {

    void pay(String type) {

        if(type.equals("UPI")) {
            System.out.println("UPI payment");
        }
        else if(type.equals("CARD")) {
            System.out.println("Card payment");
        }
        else if(type.equals("NETBANKING")) {
            System.out.println("Net banking payment");
        }
    }
}
```

---

# Problems

* ❌ Huge if-else blocks
* ❌ Violates Open/Closed Principle
* ❌ Risky for production changes

---

# Strategy Pattern Solution

Move each payment logic into separate classes.

---

# Step 1: Create Strategy Interface

```java
public interface PaymentStrategy {

    void pay(double amount);

}
```

This says:

```text
Every payment type must implement pay()
```

---

# Step 2: Create Different Strategies

---

## UPI Payment

```java
public class UpiPayment implements PaymentStrategy {

    @Override
    public void pay(double amount) {
        System.out.println("Paid using UPI: " + amount);
    }
}
```

---

## Card Payment

```java
public class CardPayment implements PaymentStrategy {

    @Override
    public void pay(double amount) {
        System.out.println("Paid using Card: " + amount);
    }
}
```

---

## Net Banking Payment

```java
public class NetBankingPayment implements PaymentStrategy {

    @Override
    public void pay(double amount) {
        System.out.println("Paid using Net Banking: " + amount);
    }
}
```

---

# Step 3: Context Class

This class uses strategy.

```java
public class PaymentService {

    private PaymentStrategy paymentStrategy;

    public PaymentService(PaymentStrategy paymentStrategy) {
        this.paymentStrategy = paymentStrategy;
    }

    public void processPayment(double amount) {
        paymentStrategy.pay(amount);
    }
}
```

---

# Step 4: Usage

---

## UPI Example

```
PaymentStrategy strategy = new UpiPayment();

PaymentService service =
        new PaymentService(strategy);

service.processPayment(1000);
```

Output:

```text
Paid using UPI: 1000
```

---

## Card Example

```
PaymentStrategy strategy = new CardPayment();

PaymentService service =
        new PaymentService(strategy);

service.processPayment(2000);
```

Output:

```text
Paid using Card: 2000
```

---

# Why Strategy Pattern is Useful

* ✅ Removes huge if-else blocks
* ✅ Easy to add new behavior
* ✅ Easy testing
* ✅ Clean code
* ✅ Runtime flexibility
* ✅ Follows SOLID principles

---

# Open/Closed Principle Relation

---

# Open/Closed Principle Says

```text
Open for extension
Closed for modification
```

Meaning:

* ✅ Add new functionality
* ❌ Avoid changing old stable code

---

# Example

Suppose a Bank adds:

```text
Wallet Payment
```

Without Strategy Pattern:

* modify existing PaymentService
* risky change

With Strategy Pattern:

```java
class WalletPayment implements PaymentStrategy {

    public void pay(double amount) {
        System.out.println("Wallet payment");
    }
}
```

Done.

No existing code changed.

---

# Important Point

```text
Open/Closed Principle = WHY
Strategy Pattern = HOW
```

---

# Simple Diagram

```text
           PaymentStrategy
                  ↑
    --------------------------------
    ↑              ↑              ↑
UPIPayment   CardPayment   NetBankingPayment

                  ↑
          PaymentService
```

---

# Real Banking Use Cases

| Use Case        | Strategy                 |
| --------------- | ------------------------ |
| Payments        | UPI/Card/Wallet          |
| Loan Interest   | Home/Car/Personal        |
| Fraud Detection | Different algorithms     |
| Notifications   | SMS/Email/Push           |
| Tax Calculation | Country-specific logic   |
| Kafka Retry     | Different retry policies |

---

# Spring Boot Real Usage

```java
@Autowired
private PaymentStrategy paymentStrategy;
```

Spring injects different implementations dynamically.

---

# Easy Memory Trick

## Strategy Pattern Means

```text
One task
Multiple ways
Choose dynamically
```

---

# One-Line Summary

```text
Strategy Pattern = Multiple interchangeable ways to perform one action.
```
