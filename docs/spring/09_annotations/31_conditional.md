---
title: Conditional 
parent: Annotations
nav_order: 31
---

# @Conditional

`@Conditional` is a Spring annotation used to register a bean **only if a specific condition is satisfied**.

It allows conditional bean creation based on custom logic during application startup.

In simple terms, it tells Spring:

> Create this bean only when this condition returns true.

---

## Why It Is Needed

In real-world applications, different environments require different configurations.

Examples:
- Enable a mock service in Dev but not in Prod
- Create a bean only if a specific property exists
- Register a feature bean only if a feature flag is enabled
- Use different implementations based on OS or profile

`@Conditional` provides this flexibility.

---

## Basic Syntax

```java
import org.springframework.context.annotation.Conditional;
import org.springframework.context.annotation.Configuration;

@Configuration
@Conditional(MyCondition.class)
public class MyConfig {
}
```

The MyCondition class must implement: Condition Functional Interface 

```java
import org.springframework.context.annotation.Condition;
import org.springframework.context.annotation.ConditionContext;
import org.springframework.core.type.AnnotatedTypeMetadata;

public class MyCondition implements Condition {

    @Override
    public boolean matches(ConditionContext context,
                           AnnotatedTypeMetadata metadata) {

        return true; // return true to register bean
    }
}

```

- If matches() returns true, the bean is created.
- If it returns false, the bean is skipped.

---

## Real-Time Example – Enable Bean Based on Property

application.yml

```yaml
features:
  payment:
    enabled: true
```

```java
public class PaymentFeatureCondition implements Condition {

    @Override
    public boolean matches(ConditionContext context,
                           AnnotatedTypeMetadata metadata) {

        String value = context.getEnvironment()
                .getProperty("features.payment.enabled");

        return "true".equalsIgnoreCase(value);
    }
}
```

```java
@Configuration
public class PaymentConfig {

    @Bean
    @Conditional(PaymentFeatureCondition.class)
    public PaymentService paymentService() {
        return new PaymentService();
    }
}

```

- If property is true → bean created
- If false → bean not registered

---

## Applying @Conditional on Different Levels

It can be used on:

- @Configuration classes
- @Bean methods
- @Component classes

Example:

```java
@Component
@Conditional(MyCondition.class)
public class DevOnlyService {
}

```

---

## Spring Boot Built-in Conditional Annotations

Spring Boot provides commonly used condition annotations built on top of @Conditional:

- @ConditionalOnProperty
- @ConditionalOnMissingBean
- @ConditionalOnClass
- @ConditionalOnBean
- @ConditionalOnExpression

Example using built-in:

```java
@Bean
@ConditionalOnProperty(name = "features.payment.enabled", havingValue = "true")
public PaymentService paymentService() {
return new PaymentService();
}
```

This is cleaner than writing a custom condition.

---

## When to Use

Use @Conditional when:

- You need dynamic bean creation
- Environment-specific configuration is required
- Implementing auto-configuration
- Building reusable libraries
- Feature toggling

#### Avoid it when:

- A simple @Profile is enough
- There is no conditional logic required

---

## How It Works Internally

During application startup:

1. Spring scans configuration classes.
2. If @Conditional is present,
3. Spring executes the matches() method.
4. Based on result:
    - true → bean is registered
    - false → bean is ignored

This happens before the bean is instantiated.

---

## Summary

> @Conditional provides conditional bean registration in Spring.
It enables flexible, environment-aware configuration and is heavily used in Spring Boot auto-configuration.

> It is a powerful mechanism for building scalable, configurable enterprise applications.