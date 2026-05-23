---
title: Scheduled 
parent: Annotations
nav_order: 190
---

# @Scheduled 

----

## 1. What is @Scheduled?

`@Scheduled` is used in Spring to execute tasks automatically at fixed intervals or specific times.

> Used for background jobs like cron tasks inside applications.

---

## 2. Enable Scheduling

```java
@EnableScheduling
@SpringBootApplication
public class App { }
```

---

## 3. Basic Example

```java
@Component
public class MyScheduler {

    @Scheduled(fixedRate = 5000)
    public void runTask() {
        System.out.println("Running every 5 seconds");
    }
}
```

---

## 4. Scheduling Types

### 1. fixedRate

Runs every 5 seconds (from start time)
- Doesn’t wait for previous execution to finish
- Can overlap ⚠️

```
@Scheduled(fixedRate = 5000)
```

---

### 2. fixedDelay

- Runs 5 seconds AFTER previous execution completes
- No overlap

```
@Scheduled(fixedDelay = 5000)
```

---

### 3. initialDelay

- Delay before first execution

```
@Scheduled(initialDelay = 10000, fixedRate = 5000)
```

---

### 4. cron (Important)
- Runs based on cron expression

```
@Scheduled(cron = "0 0 10 * * ?")
```

---

## 5. Cron Format

```
sec min hour day month day-of-week
```

### Common Examples:
| Use Case | Expression |
|--------|-----------|
| Every 10 sec | 0/10 * * * * ? |
| Every 5 min | 0 */5 * * * ? |
| Daily 10 AM | 0 0 10 * * ? |

---

## 6. Internal Working

1. Application starts  
2. @EnableScheduling activates scheduling  
3. ScheduledAnnotationBeanPostProcessor scans beans  
4. Finds @Scheduled methods  
5. Registers with TaskScheduler  
6. Executes using threads  

---

## 7. Key Internal Components

- ScheduledAnnotationBeanPostProcessor  
- TaskScheduler  
- ThreadPoolTaskScheduler  
- CronTrigger  
- ScheduledTaskRegistrar  

---

## 8. Thread Behavior

### Default:
- Single thread
- Tasks may block each other

### Custom Thread Pool:

```java
@Bean
public TaskScheduler taskScheduler() {
    ThreadPoolTaskScheduler scheduler = new ThreadPoolTaskScheduler();
    scheduler.setPoolSize(5);
    return scheduler;
}
```

---

## 9. Limitations

- No retry mechanism  (If task fails → Spring won’t retry automatically)
- No persistence  
- Not distributed-safe  
- Multiple instances → duplicate execution  (If you run 3 instances → task runs 3 times ⚠️)

---

## 10. Production Usage

### Safe to use when:
- Single instance  
- Non-critical jobs  
- Duplicate execution is acceptable  

### Not safe when:
- Distributed systems  
- Critical operations (payments, transactions)  

---

## 11. Solutions for Distributed Systems

- DB locking  
- Redis distributed lock  
- Leader election  
- Quartz Scheduler  
- Kubernetes CronJobs  

---

## 12. Real Use Cases

- Cache refresh  
- Report generation  
- Cleanup jobs  
- API polling  
- Retry failed operations  

---

## 13. Best Practices

- Prefer fixedDelay for long tasks  
- Use cron for business schedules  
- Handle exceptions properly  
- Avoid heavy logic  
- Use thread pool for multiple tasks  

---

## Summary

**Basic:**
@Scheduled is used to run tasks periodically using fixed intervals or cron expressions.

**Advanced:**
It works using ScheduledAnnotationBeanPostProcessor and TaskScheduler internally.

---

