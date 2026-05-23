---
title: Spring Boot Actuator
parent: Observability
nav_order: 1
---

# Spring Boot Actuator

**Spring Boot Actuator** provides **production-ready features** to help
monitor, manage, and observe a Spring Boot application.

It exposes **operational endpoints** that give insight into:
- Application health
- Metrics
- Environment
- Beans
- Configuration
- Thread state

---

## Why Spring Actuator Is Needed

In real applications (especially banking / enterprise):
- You must know if the app is **healthy**
- You must monitor **performance**
- You must diagnose issues **without restarting**
- You must expose **safe operational data**

Actuator solves this **without writing custom code**.

---

## What Actuator Is (Simple Definition)

> Actuator is a set of **management endpoints** that expose
internal application information over HTTP or JMX.

---

## Enabling Actuator in Spring boot application (Branch Service)

### 1️⃣ Add Dependency

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Once added:

- Actuator auto-configures itself
- No extra code required

### Enabling Actuator Endpoints

Default Behavior

- Only `health` and `info` are exposed over HTTP
- Others are disabled for security

### Enable All Endpoints (Dev Only)

```properties
management.endpoints.web.exposure.include=*

```
⚠️ Never do this in production.

### Enable Selected Endpoints (Recommended)

```properties
management.endpoints.web.exposure.include=health,info,metrics,beans,env
```

### Default Actuator Endpoint

By Default:

```
/actuator
```

Example:

> GET http://localhost:8083/actuator

This returns links to enabled endpoints.

```json
{
  "_links": {
    "self": {
      "href": "http://localhost:8083/actuator",
      "templated": false
    },
    "beans": {
      "href": "http://localhost:8083/actuator/beans",
      "templated": false
    },
    "health": {
      "href": "http://localhost:8083/actuator/health",
      "templated": false
    },
    "health-path": {
      "href": "http://localhost:8083/actuator/health/{*path}",
      "templated": true
    },
    "info": {
      "href": "http://localhost:8083/actuator/info",
      "templated": false
    },
    "env": {
      "href": "http://localhost:8083/actuator/env",
      "templated": false
    },
    "env-toMatch": {
      "href": "http://localhost:8083/actuator/env/{toMatch}",
      "templated": true
    },
    "metrics-requiredMetricName": {
      "href": "http://localhost:8083/actuator/metrics/{requiredMetricName}",
      "templated": true
    },
    "metrics": {
      "href": "http://localhost:8083/actuator/metrics",
      "templated": false
    }
  }
}
```

---

## Important Actuator Endpoints

### 1️⃣ /actuator/health

**Purpose**

- Shows application health
- Used by load balancers & Kubernetes

> GET /actuator/health

Sample Response

```json
{
"status": "UP"
}
```

**Detailed Health Info**

> management.endpoint.health.show-details=always

```json
{
"status": "UP",
"components": {
"db": { "status": "UP" },
"diskSpace": { "status": "UP" }
}
}
```

> When multiple services are deployed on the same server/port,   
> each service exposes its own health endpoint using a unique context path.    

Health is checked per application, not per port.

### Scenario

- One Tomcat / Managed Server
- One port (e.g. 8080)
- Multiple applications deployed

Example:

> http://server:8080/branch-service
> http://server:8080/account-service
> http://server:8080/customer-service

Here:

- Port = shared
- JVM = shared
- Server = shared
- Applications are separate

### How Health Check Works in This Case

Each application exposes its own actuator endpoint under its context path.

#### Example Health URLs

> http://server:8080/branch-service/actuator/health   
> http://server:8080/account-service/actuator/health    
> http://server:8080/customer-service/actuator/health    

### What Happens If You Have 5 Services and Only 3 Enable Actuator

> All services SHOULD enable actuator.
> If some services don’t, they become “invisible” to monitoring systems.

Example Setup

```pgsql

server:8080
├── branch-service        ✅ actuator
├── account-service       ✅ actuator
├── customer-service      ❌ no actuator
├── payment-service       ❌ no actuator
├── notification-service  ✅ actuator

```

###  What Monitoring Systems See

Monitoring tool checks:

```text
/branch-service/actuator/health        → UP
/account-service/actuator/health       → UP
/customer-service/actuator/health      → ❌ 404 / Not Found
/payment-service/actuator/health       → ❌ 404 / Not Found
/notification-service/actuator/health  → UP
```

Now monitoring system cannot answer:

- Is customer-service healthy?
- Is payment-service down?
- Is it slow or broken?

---

### 2️⃣ /actuator/info

**Purpose:** 

- Exposes application metadata

```properties
info.app.name=Branch Service
info.app.version=1.0.0
info.app.owner=Banking Team
```

> GET /actuator/info

### 3️⃣ /actuator/metrics

- Application performance metrics

> GET /actuator/metrics

#### Example metrics:

- JVM memory
- CPU usage
- HTTP requests
- Thread count

Example
> GET /actuator/metrics/http.server.requests

**Used heavily with:**

- Prometheus
- Grafana

### 4️⃣ /actuator/beans

- Lists all Spring beans in application

Useful for:   

- Debugging
- Verifying bean creation
- Understanding auto-configuration   

### 5️⃣ /actuator/env

- Shows environment properties

> GET /actuator/env

Helps debug:

- Profile issues
- Property resolution
- Configuration overrides   

### 6️⃣ /actuator/mappings

- Shows all request mappings

Very useful when:

- API is not reachable
- Conflicts exist


### 7️⃣ /actuator/loggers

- View & change log levels at runtime

> GET /actuator/loggers


Change log level:

```http
POST /actuator/loggers/com.bank.branch
{
"configuredLevel": "DEBUG"
}
```

✔ No restart needed   
✔ Very useful in production   

### 8️⃣ /actuator/threaddump

- Shows JVM thread state

Used for:

- Debugging deadlocks
- High CPU issues

### 9️⃣ /actuator/heapdump

- Dumps JVM heap
- Used for memory leak analysis

⚠️ Heavy operation      
⚠️ Restricted in production   

---

## Custom Health Indicator (Branch Example)

```java
@Component
public class BranchHealthIndicator implements HealthIndicator {

    @Override
    public Health health() {
        return Health.up()
            .withDetail("branch-service", "Available")
            .build();
    }
}
```


Now:

> GET /actuator/health

includes custom health info.   

---

## Security of Actuator

**NEVER expose actuator openly in prod**

Best practices:

- Secure with Spring Security
- Expose only required endpoints
- Use separate management port

---

## Separate Management Port (Recommended)

> management.server.port=9090

Now:

- App runs on 8080
- Actuator runs on 9090

---

### Actuator in Banking / Enterprise Systems

Used for:

- Health checks
- SLA monitoring
- Production debugging
- Auto-scaling decisions
- Incident response

Actuator endpoints are often consumed by:

- Kubernetes
- Prometheus
- ELK
- Monitoring dashboards     

---

> Spring Boot Actuator provides production-ready endpoints to monitor and manage applications. I’ve used it for health checks, metrics, logging configuration, and operational debugging.


