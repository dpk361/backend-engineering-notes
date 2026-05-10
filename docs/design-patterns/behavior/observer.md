---
title: Observer Design Pattern
parent: Behavior Patterns
nav_order: 2
---

# Observer Design Pattern

---

# What is Observer Pattern?

Observer Pattern means:

```text
One object changes
Multiple other objects automatically get notified
```

---

# Real-Life Banking Example

Suppose:

```text
A customer deposits money into bank account
```

After deposit:

* SMS service should notify
* Email service should notify
* Fraud monitoring system should check
* Transaction logger should save logs
* Cashback service may trigger reward points

One event:

```text
Money Deposited
```

Many systems react automatically.

This is `Observer Pattern.`

---

# Easy Understanding

```text
One publisher
Many subscribers
```

Like YouTube:

```text
YouTube Channel uploads video
Subscribers get notification
```

---

# Banking Version

```text
Bank Account = Publisher
SMS/Email/Fraud systems = Observers
```

---

# Why We Need Observer Pattern

Without observer pattern:

```java
class BankAccount {

    void deposit() {

        smsService.send();

        emailService.send();

        fraudService.check();

        cashbackService.process();
    }
}
```

---

# Problems

- ❌ Tight coupling
- ❌ Hard to add new features
- ❌ BankAccount becomes huge
- ❌ All new service changes existing code
- ❌ Violates Open/Closed Principle

---

# Observer Pattern Solution

Separate all listeners/subscribers.

Publisher only sends notification.

Observers decide what to do.

---

# Main Components

| Component | Meaning                       |
|-----------|-------------------------------|
| Subject   | Main object being watched     |
| Observer  | Objects listening for updates |
| Notify    | Inform all observers          |
| Subscribe | Register observer             |

---

# Banking Example Structure

```text
BankAccount
    ↓
Notify Observers
    ↓
---------------------------------
↓         ↓          ↓
SMS     Email     FraudCheck
```

---

# Step 1: Create Observer Interface

```java
public interface Observer {

    void update(String message);

}
```

Meaning:

```text
Every observer must implement update()
```

---

# Step 2: Create Different Observers

---

## SMS Observer

```java
public class SmsService implements Observer {

    @Override
    public void update(String message) {
        System.out.println("SMS Sent: " + message);
    }
}
```

---

## Email Observer

```java
public class EmailService implements Observer {

    @Override
    public void update(String message) {
        System.out.println("Email Sent: " + message);
    }
}
```

---

## Fraud Detection Observer

```java
public class FraudDetectionService implements Observer {

    @Override
    public void update(String message) {
        System.out.println("Fraud Check Started: " + message);
    }
}
```

---

# Step 3: Create Subject (Publisher)

```java
import java.util.ArrayList;
import java.util.List;

public class BankAccount {

    private List<Observer> observers = new ArrayList<>();

    public void subscribe(Observer observer) {
        observers.add(observer);
    }

    public void notifyObservers(String message) {

        for(Observer observer : observers) {
            observer.update(message);
        }
    }

    public void deposit(double amount) {

        System.out.println("Amount Deposited: " + amount);

        notifyObservers("Deposited Amount: " + amount);
    }
}
```

---

# Step 4: Usage

```java
public class Main {

    public static void main(String[] args) {

        BankAccount account = new BankAccount();

        account.subscribe(new SmsService());

        account.subscribe(new EmailService());

        account.subscribe(new FraudDetectionService());

        account.deposit(10000);
    }
}
```

---

# Output

```text
Amount Deposited: 10000

SMS Sent: Deposited Amount: 10000

Email Sent: Deposited Amount: 10000

Fraud Check Started: Deposited Amount: 10000
```

---

# Crucial Understanding

When deposit happens:

```text
ONE event occurs
MULTIPLE systems react automatically
```

This is an Observer Pattern.

---

# Real Banking Use Cases

| Banking Event       | Observers                   |
|---------------------|-----------------------------|
| Deposit             | SMS, Email, Fraud           |
| Loan Approved       | Notification, Audit, CRM    |
| Credit Card Payment | Rewards, Ledger, Analytics  |
| Large Transaction   | Compliance, Fraud Detection |
| Account Created     | Welcome Email, KYC, CRM     |
| Failed Login        | Security Alert, Audit Logs  |

---

# Real Spring Boot Microservices Example

Suppose:

```text
Payment Service publishes event
```

Other services listen:

* Notification Service
* Fraud Service
* Analytics Service
* Reward Service

---

# Kafka Version of Observer Pattern

```text
Payment Service
      ↓
Publishes Kafka Event
      ↓
--------------------------------
↓          ↓           ↓
SMS      Fraud      Analytics
Consumer  Consumer   Consumer
```

This is a distributed Observer Pattern.

---

# Observer Pattern vs Strategy Pattern

| Strategy Pattern           | Observer Pattern         |
|----------------------------|--------------------------|
| Choose ONE behavior        | Notify MANY listeners    |
| Runtime behavior selection | Event notification       |
| One action different ways  | One event many reactions |

---

# Easy Difference

---

## Strategy Pattern

```text
How should payment happen?
```

Choose:

* UPI
* Card
* Wallet

ONLY ONE strategy used.

---

## Observer Pattern

```text
Payment completed
Who should react?
```

Many systems react:

* SMS
* Fraud
* Cashback
* Analytics

MULTIPLE observers react.

---

# Open/Closed Principle Relation

Observer Pattern also supports OCP.

Suppose a bank adds:

```text
WhatsApp Notification Service
```

Create new observer:

```java
class WhatsappService implements Observer{
    
}
```

No existing code changes.

---

# Advantages

* ✅ Loose coupling
* ✅ Easy extension
* ✅ Event-driven design
* ✅ Supports microservices
* ✅ Good for async systems
* ✅ Easy to add/remove listeners

---

# Disadvantages

* ❌ Too many observers may affect performance
* ❌ Debugging becomes harder
* ❌ Event chains can become complex

---

# Real Enterprise Usage

Observer Pattern is heavily used in:

* Kafka consumers
* Spring Events
* RabbitMQ
* Event-driven architecture
* Notification systems
* Audit logging
* Monitoring systems

---

# Spring Boot Example

Spring internally supports an observer pattern.

```java
@EventListener
public void handleEvent(OrderCreatedEvent event) {
}
```

Publisher:

```
applicationEventPublisher.publishEvent(event);
```

---

# Simple Mental Model

```text
One event happens
Many systems listen and react
```

---

# Final Diagram

```text
                 BankAccount
                      ↓
               notifyObservers()
                      ↓
------------------------------------------------
↓                  ↓                    ↓
SMS Service    Email Service    Fraud Service
```

---

# Easy Memory Trick

```text
Observer Pattern =
Publish → Subscribe → Notify
```

---

# One-Line Summary

```text
Observer Pattern = One event automatically notifies multiple interested objects.
```
