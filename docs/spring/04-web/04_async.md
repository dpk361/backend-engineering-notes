---
title: Async
parent: Web
nav_order: 4
---

# Async in Spring Boot


### What is @Async?

**@Async** allows a method to run asynchronously in a separate thread, so the caller does not wait for it to finish.

Typical use cases:

- Sending emails / notifications
- Writing audit logs
- Calling slow external services
- Background processing (non-blocking)

---
### 1️⃣ Enable Async Support

> We must enable async processing explicitly.

 📌 Without @EnableAsync, @Async will not work.
 
```java

@SpringBootApplication
@EnableAsync
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}

```
---
### 2️⃣ Configure Custom Thread Pool

Configuring a custom thread pool for @Async is not mandatory for functionality, but it is strongly recommended for production systems.
Relying on the default executor can lead to uncontrolled thread creation and performance issues.

```java

@Configuration
public class AsyncConfig {

    @Bean(name = "taskExecutor")
    public Executor taskExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(5);
        executor.setMaxPoolSize(10);
        executor.setQueueCapacity(100);
        executor.setThreadNamePrefix("Async-");
        executor.initialize();
        return executor;
    }
}

```

---

### 3️⃣ Exception Handling in @Async

❌ This will NOT catch exceptions

```
try {
    asyncMethod();
} catch (Exception e) { }

```
✅ Correct way (Async Exception Handler)

```java
@Configuration
public class AsyncExceptionConfig implements AsyncConfigurer {

    @Override
    public AsyncUncaughtExceptionHandler getAsyncUncaughtExceptionHandler() {
        return (ex, method, params) ->
            System.err.println("Async error in " + method.getName());
    }
}

```

---

### 4️⃣ Create an Async Method

```java
@Service
@Slf4j
public class AsyncUseCase {

    @Async
    public void sendEmail(String email) {
        System.out.println("Thread: " + Thread.currentThread().getName());
        // simulate delay
        try {
            Thread.sleep(50000);
            log.info("Testing Async feature");
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
        System.out.println("Email sent to " + email);
    }
}

```

**Rules** for **@Async**

- ✔ Method must be public
- ✔ Must be called from another Spring bean
- ❌ Self-invocation will NOT work

---
### 5️⃣ Call the Async Method

```java

@RestController
@RequestMapping("/api/async")
public class BranchController {

    @Autowired
    private AsyncUseCase asyncUseCase;

    @GetMapping
    public String sendMail() {
        asyncUseCase.sendEmail("mohan@gmai.com");
        return "OK";
    }

}

```

***🧠 Result:***

- API returns immediately
- but the async method runs in background thread

---

### 6️⃣ @Async vs Kafka / Message Queue

| @Async              | Kafka          |
|---------------------|----------------|
| In-memory	          | Durable        |
| Same JVM	           | Distributed    |
| Simple              | 	Event-driven  |
| Not fault-tolerant	 | Fault-tolerant |

- 📌 Use ***@Async*** for lightweight background tasks
- 📌 Use ***Kafka*** for inter-service, reliable communication

---

### 7️⃣ When NOT to Use @Async

- ❌ Business-critical workflows
- ❌ Guaranteed delivery use cases
- ❌ Heavy CPU-bound tasks
- ❌ Cross-service communication

---

### Summary

- @Async = simple background execution
- Always configure a custom thread pool
- No self-invocation
- Handle exceptions properly
- Use Kafka for reliable, distributed async

