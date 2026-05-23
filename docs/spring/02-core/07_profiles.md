---
title: Profiles
parent: Core
nav_order: 7
---

# Spring Boot Profiles

Spring Boot **profiles** are used to **separate configuration and behavior**
for different environments such as **dev, test, qa, and prod**.

Profiles help you run the **same application code** in multiple environments
without changing the code itself.

---

## Why Profiles Are Needed (Very Important)

In real applications, environments differ in:
- Database URLs
- Credentials
- Logging levels
- Caching
- External system endpoints

Without profiles:
- You hardcode values ❌
- You manually change config ❌
- You risk deploying wrong settings ❌

> **Profiles solve environment-specific configuration cleanly and safely.**

---

## What Is a Profile?

A **profile** is a named logical group of configuration.

Examples:
- `dev`
- `test`
- `qa`
- `prod`

Spring activates **one or more profiles** at runtime.

---

## How Profiles Work (High Level)

Spring:
1. Loads default configuration
2. Loads profile-specific configuration
3. Overrides matching properties
4. Creates beans conditionally

> **Profile-specific config overrides default config.**

---

## application.properties vs application-{profile}.properties

### Default Configuration
```properties
application.properties
```

### Used when:

- No profile is active
- Or as base configuration

### Profile-Specific Configuration

```properties
application-dev.properties
application-test.properties
application-prod.properties

```

#### Spring loads:

- application.properties
- PLUS active profile file


### Example

#### application.properties

```properties
server.port=8080
logging.level.root=INFO
```

#### application-dev.properties

```properties
server.port=8081
logging.level.root=DEBUG
spring.datasource.url=jdbc:h2:mem:testdb
```

#### application-prod.properties

```properties
server.port=8080
logging.level.root=ERROR
spring.datasource.url=jdbc:postgresql://prod-db:5432/app
```
---

## How to Activate a Profile

### 1️⃣ Using application.properties

```properties
spring.profiles.active=dev
```

> ❌ Not recommended for prod (hardcoded)

### 2️⃣ Using JVM Argument (MOST COMMON)

```properties
-Dspring.profiles.active=prod
```
✔ Preferred   
✔ Environment controlled

### 3️⃣ Using Command Line

```properties
--spring.profiles.active=dev
```

---

## Using @Profile on Beans

Profiles can control bean creation.

### Example

```java
@Component
@Profile("dev")
public class DevEmailService implements EmailService {
}
```

```java
@Component
@Profile("prod")
public class ProdEmailService implements EmailService {
}
```

Only one bean is created based on active profile.

---

## Multiple Profiles Active

Spring allows multiple active profiles.

> SPRING_PROFILES_ACTIVE=dev,local

### Use cases:

dev + local   
prod + cloud

---

## @Profile with @Configuration

```java
@Configuration
@Profile("test")
public class TestConfig {
}
```

Entire configuration class is profile-specific.

---

## Default Profile

If no profile is active:

- Spring uses default profile
- Name: default

You can also define:

> spring.profiles.default=dev

---

### Profile Groups (Spring Boot Feature)

Profiles can be grouped.

> spring.profiles.group.prod=prod,cloud,monitoring

Activating prod activates all grouped profiles.

---

## Summary

- Profiles separate environment configuration
- application-{profile}.properties overrides defaults
- Activated via env variable or JVM arg
- @Profile controls bean creation
- Multiple profiles can be active
- Default profile exists


