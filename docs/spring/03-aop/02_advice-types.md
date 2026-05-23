---
title: Advice Types
parent: AOP
nav_order: 2
---

# Advice Types in AOP

 **Advice = What action should run + When it should run**

---

## 1. `@Before` Advice

Runs **BEFORE** the actual method executes

## Code Example

```java
@Aspect
@Component
class LoggingAspect {

    @Before("execution(* com.app.service.*.*(..))")
    public void beforeLog() {
        System.out.println("Before method execution");
    }
}
```

## Flow
Client → Proxy → @Before → Actual Method

## Use Cases
- Logging (start of method)  
- Security checks  
- Input validation  

## Important Behavior
- Cannot stop method execution  
- Cannot access return value  
- Cannot modify output  

###  Mental Model
👉 “Before doing anything, run this logic”

---

##  2. `@After` Advice

👉 Runs **AFTER method execution (always)**  

- Whether success or exception  

##  Code Example
```java
@After("execution(* com.app.service.*.*(..))")
public void afterLog() {
    System.out.println("After method execution");
}
```

##  Flow
Client → Proxy → Method → @After

##  Use Cases
- Cleanup  
- Logging completion  
- Resource release  

##  Important Behavior
- Runs even if exception occurs  
- No access to return value  

##  Mental Model
“No matter what happens, run this”

---

##  3. `@AfterReturning` Advice

👉 Runs **ONLY when method executes successfully**

##  Code Example
```java
@AfterReturning(
    pointcut = "execution(* com.app.service.*.*(..))",
    returning = "result"
)
public void afterSuccess(Object result) {
    System.out.println("Returned: " + result);
}
```

##  Flow
Client → Proxy → Method → SUCCESS → @AfterReturning

##  Use Cases
- Logging result  
- Auditing  
- Post-processing  

##  Important Behavior
- Runs only on success  
- Can access return value  
- Cannot modify execution  

##  Mental Model
👉 “If everything went well, run this”

---

##  4. `@AfterThrowing` Advice

👉 Runs **ONLY when method throws exception**

##  Code Example
```java
@AfterThrowing(
    pointcut = "execution(* com.app.service.*.*(..))",
    throwing = "ex"
)
public void afterError(Exception ex) {
    System.out.println("Exception: " + ex.getMessage());
}
```

##  Flow
Client → Proxy → Method → EXCEPTION → @AfterThrowing

##  Use Cases
- Error logging  
- Alerting  
- Exception handling  

##  Important Behavior
- Only runs on failure  
- Can access exception  
- Cannot stop exception  

##  Mental Model
👉 “If something fails, handle it”

---

# 5. `@Around` Advice 

👉 Wraps the entire method execution  
👉 Gives **FULL control**

##  Code Example
```java
@Around("execution(* com.app.service.*.*(..))")
public Object aroundAdvice(ProceedingJoinPoint joinPoint) throws Throwable {

    System.out.println("Before execution");

    Object result = joinPoint.proceed(); // actual method call

    System.out.println("After execution");

    return result;
}
```

##  Flow
Client → Proxy → @Around (Before)
                  ↓
             Actual Method
                  ↓
             @Around (After)

##  Key Power
👉 You can:
- Run code before  
- Run code after  
- Modify input  
- Modify output  
- Skip execution  

##  Use Cases
- Performance monitoring  
- Transaction management  
- Retry logic  
- Logging (entry + exit)  

##  Important Behavior
- Must call `joinPoint.proceed()`  
- If not → method won’t execute  

##  Mental Model
👉 “I control everything around this method”

---

# Comparison Table

| Advice Type | When it Runs | Access to Result | Access to Exception | Control |
|------------|-------------|-----------------|--------------------|--------|
| @Before | Before method | ❌ | ❌ | ❌ |
| @After | After (always) | ❌ | ❌ | ❌ |
| @AfterReturning | After success | ✅ | ❌ | ❌ |
| @AfterThrowing | After exception | ❌ | ✅ | ❌ |
| @Around | Before + After | ✅ | ✅ | ✅ |

---

# Real-World Example (Banking 🏦)

## Logging + Performance
```java
@Around("execution(* com.bank.service.*.*(..))")
public Object logExecution(ProceedingJoinPoint joinPoint) throws Throwable {

    long start = System.currentTimeMillis();

    Object result = joinPoint.proceed();

    long end = System.currentTimeMillis();

    System.out.println("Execution time: " + (end - start));

    return result;
}
```

---

## Internal Understanding

👉 All these advices are:
- Registered in proxy  
- Executed as **interceptors**  
- Applied before/after method call  

---

## What You Must Remember

✔ `@Around` is most powerful  
✔ `@Before` is simplest  
✔ `@After` always runs  
✔ `@AfterReturning` → success only  
✔ `@AfterThrowing` → failure only  

---

# If ALL Advices Are Defined — What Happens?

Suppose you define:

- @Before
- @After
- @AfterReturning
- @AfterThrowing
- @Around

Nothing overrides anything\
ALL applicable advices will execute\

**@Around does NOT replace other advices   
  👉 It wraps the entire execution**

## When method executes SUCCESSFULLY:

```
@Around (before part)
    ↓
@Before
    ↓
Actual Method
    ↓
@AfterReturning
    ↓
@After
    ↓
@Around (after part)
```

## When method throws EXCEPTION:

```
@Around (before part)
    ↓
@Before
    ↓
Actual Method (throws exception)
    ↓
@AfterThrowing
    ↓
@After
    ↓
@Around (after part)  ← only if handled properly
```


### Let’s See with Code

```java
@Aspect
@Component
class MyAspect {

    @Before("execution(* com.app.service.*.*(..))")
    public void before() {
        System.out.println("Before");
    }

    @After("execution(* com.app.service.*.*(..))")
    public void after() {
        System.out.println("After");
    }

    @AfterReturning("execution(* com.app.service.*.*(..))")
    public void afterReturning() {
        System.out.println("AfterReturning");
    }

    @AfterThrowing("execution(* com.app.service.*.*(..))")
    public void afterThrowing() {
        System.out.println("AfterThrowing");
    }

    @Around("execution(* com.app.service.*.*(..))")
    public Object around(ProceedingJoinPoint pjp) throws Throwable {

        System.out.println("Around Before");

        Object result = pjp.proceed();

        System.out.println("Around After");

        return result;
    }
}
```

## Output (Success Case)

```
Around Before
Before
[Actual Method]
AfterReturning
After
Around After
```

## Output (Exception Case)

```
Around Before
Before
[Exception thrown]
AfterThrowing
After
Around After (only if handled)
```

---

# Note

## 1. @Around is OUTERMOST

It wraps everything:

```
@Around {
   @Before
   Method
   @After...
}
```

## 2. If @Around DOES NOT call proceed()

```
pjp.proceed(); // missing ❌
```

👉 Then:

Method will NOT execute\
Other advices will NOT execute\

## 3. Exception Handling in @Around

```
try {
    pjp.proceed();
} catch (Exception e) {
    // if handled here
}
```

👉 Then:

`@AfterThrowing` may NOT run\

## 4. @After Always Runs

👉 Like finally block in Java

