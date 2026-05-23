---
title: ConfigurationProperties 
parent: Annotations
nav_order: 30
---

# @ConfigurationProperties

`@ConfigurationProperties` is a Spring Boot annotation used to bind external configuration properties (from `application.yml`, `application.properties`, environment variables, or Config Server) to a strongly-typed Java class.

It allows grouping related configuration values into a structured, type-safe object.

---

## 1. Why It Is Needed

In real-world applications:

- Applications have multiple configuration values (DB, Kafka, security, feature flags).
- Using `@Value` for each property becomes messy and scattered.
- We need centralized, structured, and maintainable configuration handling.

`@ConfigurationProperties` solves this by binding properties into a single class.

---

## 2. Basic Example

### application.yml

```yaml
bank:
  datasource:
    url: jdbc:postgresql://localhost:5432/bankdb
    username: bank_user
    password: secret
    maxPoolSize: 20
```

### Configuration Class

```java
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@Component
@ConfigurationProperties(prefix = "bank.datasource")
public class DataSourceProperties {

    private String url;
    private String username;
    private String password;
    private int maxPoolSize;

    // getters and setters
}

```

Spring automatically maps:

```text
bank.datasource.url  → url
bank.datasource.username → username
```
---

## 3. How It Works

Flow:

```text
application.yml / Config Server
        ↓
Spring Environment
        ↓
@ConfigurationProperties binding
        ↓
Strongly-typed Java object

```

It binds values at application startup.

---

## 4. Advantages

- Type-safe binding
- Centralized configuration
- Cleaner than multiple @Value annotations
- Supports validation
- Supports nested properties
- Works with Spring Cloud Config

---

## 5. Validation Support

```java
import jakarta.validation.constraints.Min;
import jakarta.validation.constraints.NotBlank;
import org.springframework.validation.annotation.Validated;

@Validated
@Component
@ConfigurationProperties(prefix = "bank.kafka")
public class KafkaProperties {

    @NotBlank
    private String bootstrapServers;

    @Min(1)
    private int retryCount;

    // getters and setters
}

```

If invalid values are provided, the application fails at startup (fail-fast behavior).

---

## 6.When to Use

Use `@ConfigurationProperties` when:

- You have multiple related properties.
- You need structured configuration.
- You are working in microservices.
- You use external Config Server.
- You need validation.

**Avoid it for:**

- Single small property (use @Value instead).

---

| Situation                            | Need `@EnableConfigurationProperties`? |
| ------------------------------------ | -------------------------------------- |
| Class has `@Component`               | ❌ No                                   |
| No `@Component`                      | ✅ Yes                                  |
| Using `@ConfigurationPropertiesScan` | ❌ No                                   |


---

## Summary

`@ConfigurationProperties` is a clean and production-ready way to bind external configuration into structured Java objects.

It improves maintainability, readability, and reliability in enterprise Spring Boot applications.