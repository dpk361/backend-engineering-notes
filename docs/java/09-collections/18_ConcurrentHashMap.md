---
title: ConcurrentHashMap (C)
parent: Collection Framework
nav_order: 18
---

# ConcurrentHashMap

## Overview

- `ConcurrentHashMap` is a **thread-safe Map implementation**
- Designed for **high concurrency with better performance than Hashtable**
- Part of `java.util.concurrent`

---

## Key Characteristics

| Feature | Description |
|--------|------------|
| **Thread Safety** | Yes (without full locking) |
| **Performance** | High (better than Hashtable) |
| **Null Keys/Values** | Not allowed |
| **Locking** | Fine-grained / partial locking |
| **Concurrency** | Multiple threads can read/write simultaneously |

---

## Why Not HashMap?

👉 `HashMap` is:
- Not thread-safe ❌
- Can cause:
    - Data corruption
    - Infinite loops (pre-Java 8)

---

## Why Not Hashtable?

👉 `Hashtable`:
- Fully synchronized (every method locked)
- Very slow ❌

---

## Solution

👉 `ConcurrentHashMap`:
- **Thread-safe + high performance** ✅

---

## Internal Working (Java 8+)

### Core Structure

```text
Node<K,V>[] table
```

Similar to HashMap but with concurrency control.


### Key Concept: Locking Strategy

##### Java 7 (Old)

Segment-based locking

> Map divided into segments → each segment locked separately

##### Java 8+ 

👉 No segments

Uses:

- CAS (Compare-And-Swap)
- Synchronized blocks on buckets (only when needed)


### How put() Works

> map.put("A", 10);

Steps:

- Compute hash
- Find bucket
- If empty:
  - Use CAS to insert (no lock)
- If collision:
  - Lock only that bucket
  - Update safely
  

### How get() Works
  
> map.get("A");

👉 Completely lock-free ✅

- Reads happen without blocking
- Very fast

## Internal Node Types

| Node Type         | Purpose                              |
| ----------------- | ------------------------------------ |
| `Node`            | Normal entry                         |
| `TreeNode`        | Red-Black tree node (for collisions) |
| `ForwardingNode`  | Used during resizing                 |
| `ReservationNode` | For compute operations               |


### Time Complexity

| Operation  | Time Complexity |
| ---------- | --------------- |
| Get        | O(1)            |
| Put        | O(1)            |
| Remove     | O(1)            |
| Worst Case | O(log n)        |


### Important Methods (Concurrency-Friendly)

| Method Signature                             | Description         | Behavior / Edge Case |
| -------------------------------------------- | ------------------- | -------------------- |
| `V putIfAbsent(K key, V value)`              | Inserts if absent   | Atomic operation     |
| `V computeIfAbsent(K key, Function fn)`      | Lazy initialization | Thread-safe          |
| `V compute(K key, BiFunction fn)`            | Recompute value     | Atomic               |
| `V merge(K key, V value, BiFunction fn)`     | Combine values      | Thread-safe          |
| `boolean replace(K key, V oldVal, V newVal)` | Conditional update  | Atomic               |


### Why Nulls NOT Allowed 

> map.put(null, "A"); // ❌ NullPointerException

👉 Reason:

- Avoid ambiguity:
> map.get(key) == null

- Does it mean:
  - key not present ❓
  - value is null ❓

In concurrent systems → unsafe

### ConcurrentHashMap vs HashMap vs Hashtable

| Feature     | HashMap   | Hashtable | ConcurrentHashMap |
| ----------- | --------- | --------- | ----------------- |
| Thread Safe | ❌         | ✅         | ✅                 |
| Performance | Fast      | Slow      | Fast              |
| Locking     | None      | Full      | Partial           |
| Null Keys   | 1 allowed | ❌         | ❌                 |


--------------------------------

# Synchronization Behavior

## Core Idea

- ❌ No method is fully synchronized (like Hashtable)
- ✅ Uses:
    - CAS (Compare-And-Swap)
    - Bucket-level locking (only when needed)
    - Lock-free reads

---

## Non-Synchronized (Lock-Free) Methods

| Method | Behavior |
|--------|--------|
| `V get(Object key)` | Completely lock-free |
| `boolean containsKey(Object key)` | Lock-free |
| `boolean containsValue(Object value)` | Mostly lock-free (may traverse) |
| `int size()` | Approximate, not strictly locked |
| `boolean isEmpty()` | Lock-free check |

👉 These methods:
- Do NOT block threads
- Provide **high performance**

---

## Partially Synchronized Methods

| Method | Behavior |
|--------|--------|
| `V put(K key, V value)` | Lock only bucket if needed |
| `V remove(Object key)` | Lock only affected bucket |
| `V replace(K key, V value)` | Conditional locking |
| `void clear()` | Locks multiple buckets internally |
| `void putAll(Map m)` | Iterative updates with locking |

👉 Locking scope:
```text
Only the bucket (or node), NOT entire map
```

| Feature         | Hashtable    | ConcurrentHashMap |
| --------------- | ------------ | ----------------- |
| Locking         | Whole method | Bucket-level      |
| Read operations | Locked       | Lock-free         |
| Concurrency     | Low          | High              |

-------------------------------------


## When to Use
- Multi-threaded applications
- Shared mutable data
- High-performance concurrent systems

## When NOT to Use
- Single-threaded → use HashMap
- Need ordering → use ConcurrentSkipListMap
