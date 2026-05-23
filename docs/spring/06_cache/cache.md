---
title: Caching Concepts and Usage
parent: Cache
nav_order: 1
---

# Caching Concepts and Usage

Caching is the process of storing **frequently accessed data in memory** so that future requests can be served **faster**, without repeatedly querying the database or an external system.

Caching is primarily a **performance optimization technique**, not a data storage mechanism.

---

## What is Cache vs Database?

### Database
- Source of truth (system of record)
- Provides ACID guarantees
- Slower due to disk I/O and network latency
- Expensive for frequent read operations
- Strong consistency

### Cache
- Temporary data store
- In-memory (very fast)
- Used to reduce database load
- Optimized for read-heavy access
- Data can be slightly stale

> **Key Principle:**  
> Database = *System of record*  
> Cache = *Performance optimization layer*

---

## Why Do We Need Caching?

Without caching:
- Every request hits the database
- Increased response time
- Higher database load
- Poor scalability under traffic

Caching helps:
- Improve response time
- Reduce database load
- Handle high read traffic efficiently
- Improve overall system scalability

---

## Where Caching Should Be Used (Banking Context)

### ✅ Good Candidates for Caching
- Reference data
- Configuration values
- Branch details
- Product metadata
- Static business rules

### ❌ What Should NEVER Be Cached
- Money movement
- Account balances
- Financial transactions

> **Rule in banking systems:**  
> Cache only *read-heavy, non-transactional* data.

---

## TTL (Time To Live)

### What is TTL?
TTL defines **how long data remains in cache** before it is automatically removed.

Flow:
> Cache Entry → TTL expires → Entry removed automatically


### Why TTL is Critical in Banking Systems
- Prevents stale business rules
- Avoids manual cache cleanup
- Ensures periodic refresh from database
- Protects against long-lived incorrect data

> **Every cache entry MUST have a TTL**

---

## Cache Eviction Strategies

Eviction decides **which data to remove when cache is full**.

### Common Strategies
- LRU (Least Recently Used)
- LFU (Least Frequently Used)
- FIFO (First In, First Out)
- Random eviction

### Banking Preference
✅ **LRU + TTL**
- Predictable behavior
- Safe under load
- Easy to reason about

### What Banks Avoid
❌ Random eviction  
❌ Manual eviction without TTL

---

## Cache Consistency

### What is Cache Consistency?
Ensuring that cached data **matches the database within an acceptable time window**.

### Types of Consistency
- **Strong consistency**  
  Required for transactions and financial data
- **Eventual consistency**  
  Acceptable for reference and read-only data

> Banking systems carefully choose where eventual consistency is acceptable.

---

## Spring Cache Abstraction

Spring provides a **cache abstraction layer** that allows developers to use caching declaratively, without tying the application to a specific cache provider like Redis or EhCache.

### Why Spring Cache Abstraction?
- Clean and readable code
- Easy to switch cache providers
- Declarative caching via annotations

---

## Common Spring Cache Annotations

- `@EnableCaching`  
  Enables Spring’s caching mechanism.

- `@Cacheable`  
  Caches method return value and skips method execution if data exists in cache.

- `@CachePut`  
  Updates or adds data to cache while always executing the method.

- `@CacheEvict`  
  Removes cache entries when underlying data changes.

---

## How Caching Works in Spring Boot with Redis

### `@Cacheable`
1. Checks cache before method execution
2. If data exists → return from cache
3. If not → execute method, store result in Redis, return result

### `@CachePut`
- Always executes method
- Updates cache with latest data

### `@CacheEvict`
- Removes cache entry
- Used during update or delete operations to maintain consistency

---

### Example:

```java

import com.branch.entity.Branch;
import com.branch.entity.BranchDTO;
import com.branch.repository.BranchRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cache.annotation.CacheEvict;
import org.springframework.cache.annotation.CachePut;
import org.springframework.cache.annotation.Cacheable;
import org.springframework.stereotype.Service;

import java.util.List;


@Service
public class BranchService {

    @Autowired
    private BranchRepository branchRepository;

    @CachePut(value = "branchCache", key = "'branch:' + #result.branchCode")
    public Branch createBranch(BranchDTO branchDTO) {
        if (branchRepository.findByBranchCode(branchDTO.getBranchCode()).isPresent()) {
            throw new RuntimeException("Branch code must be unique");
        }

        Branch branch = new Branch();
        branch.setName(branchDTO.getName());
        branch.setBranchCode(branchDTO.getBranchCode());
        branch.setAddress(branchDTO.getAddress());

        return branchRepository.save(branch);
    }

    public Branch getBranchById(Long id) {
        return branchRepository.findById(id)
                .orElseThrow(() -> new RuntimeException("Branch not found"));
    }

    public List<Branch> getAllBranches() {
        return branchRepository.findAll();
    }

    @CacheEvict(value = "branchCache", key = "'branch:' + #branch.branchCode")
    public Branch updateBranch(Long id, BranchDTO branchDTO) {

        Branch branch = getBranchById(id);
        branch.setName(branchDTO.getName());
        branch.setBranchCode(branchDTO.getBranchCode());
        branch.setAddress(branchDTO.getAddress());

        return branchRepository.save(branch);
    }

    @CacheEvict(value = "branchCache", key = "'branch:' + #existing.branchCode")
    public void deleteBranch(Long id) {
        Branch existing = getBranchById(id);
        branchRepository.delete(existing);
    }

    public boolean isBranchExists(String branchCode) {
        return branchRepository.existsByBranchCode(branchCode);
    }

    @Cacheable(value = "branchCache", key = "'branch:' + #branchCode")
    public Branch getBranchByBranchCode(String branchCode) {
        return branchRepository.findByBranchCode(branchCode)
                .orElseThrow(() -> new RuntimeException("Branch not found"));
    }
}

```


```java

import com.fasterxml.jackson.annotation.JsonTypeInfo;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.jsontype.impl.LaissezFaireSubTypeValidator;
import com.fasterxml.jackson.datatype.jsr310.JavaTimeModule;
import com.github.benmanes.caffeine.cache.Caffeine;
import org.springframework.cache.CacheManager;
import org.springframework.cache.caffeine.CaffeineCacheManager;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Primary;
import org.springframework.data.redis.cache.RedisCacheConfiguration;
import org.springframework.data.redis.cache.RedisCacheManager;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.serializer.GenericJackson2JsonRedisSerializer;
import org.springframework.data.redis.serializer.RedisSerializationContext;

import java.time.Duration;
import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.TimeUnit;

@Configuration
public class CacheConfig {

    @Bean
    public CacheManager cacheManager(){
        CaffeineCacheManager cacheManager = new CaffeineCacheManager("branchCache");
        cacheManager.setCaffeine(Caffeine.newBuilder().initialCapacity(100).maximumSize(10000).expireAfterWrite(30, TimeUnit.MINUTES).recordStats());

        return cacheManager;
    }


    @Bean
    @Primary
    public RedisCacheManager redisCacheManager(RedisConnectionFactory factory) {

        ObjectMapper mapper = new ObjectMapper();
        mapper.registerModule(new JavaTimeModule());

        // 🔥 THIS IS THE KEY LINE
        mapper.activateDefaultTyping(
                LaissezFaireSubTypeValidator.instance,
                ObjectMapper.DefaultTyping.NON_FINAL,
                JsonTypeInfo.As.PROPERTY
        );

        GenericJackson2JsonRedisSerializer serializer =
                new GenericJackson2JsonRedisSerializer(mapper);

        RedisCacheConfiguration defaultConfig =
                RedisCacheConfiguration.defaultCacheConfig()
                        .serializeValuesWith(
                                RedisSerializationContext.SerializationPair
                                        .fromSerializer(serializer))
                        .disableCachingNullValues()
                        .entryTtl(Duration.ofMinutes(10));

        Map<String, RedisCacheConfiguration> cacheConfigs = new HashMap<>();
        cacheConfigs.put("branchCache",
                defaultConfig.entryTtl(Duration.ofHours(6)));

        return RedisCacheManager.builder(factory)
                .cacheDefaults(defaultConfig)
                .withInitialCacheConfigurations(cacheConfigs)
                .build();
    }
}

```

```
spring:
  redis:
    host: localhost
    port: 6379
    timeout: 2000ms
  devtools:
    restart:
      enabled: false
  cache:
    type: redis

```


## Best Practices for Redis Caching in Spring Boot

- Use **unique cache keys** to avoid collisions
- Always set **TTL** to avoid stale data
- Evict stale data during updates and deletes
- Monitor Redis memory usage and cache hit ratio
- Choose the right caching pattern:
  - Read-through
  - Write-through
  - Write-behind (use with caution)

