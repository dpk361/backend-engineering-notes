---
title: LinkedHashSet (C)
parent: Collection Framework
nav_order: 10
---

# LinkedHashSet

## Overview

- `LinkedHashSet` is a **HashSet with predictable iteration order**.
- It maintains **insertion order** using a **linked list + hash table**.
- Internally backed by **LinkedHashMap**.

---

## Internal Working

- Uses:
    - **Hash table** → for fast operations (like HashSet)
    - **Doubly Linked List** → to maintain insertion order
- Each element is linked to:
    - Previous element
    - Next element

---

## Key Characteristics

| Feature | Description |
|--------|------------|
| **Duplicates** | Not allowed |
| **Order** | Maintains insertion order |
| **Null** | Allows one null |
| **Thread Safety** | Not synchronized |
| **Performance** | Slightly slower than HashSet |

---

## Time Complexity

| Operation | Time Complexity |
|----------|----------------|
| Add | O(1) |
| Remove | O(1) |
| Contains | O(1) |

> Slight overhead due to maintaining linked list

---

## Constructors

| Constructor | Description |
|------------|------------|
| `LinkedHashSet()` | Default capacity (16), load factor (0.75) |
| `LinkedHashSet(int initialCapacity)` | Custom capacity |
| `LinkedHashSet(int initialCapacity, float loadFactor)` | Custom capacity + load factor |
| `LinkedHashSet(Collection<? extends E> c)` | Creates set from collection |

---

## LinkedHashSet-Specific Behavior Methods

> No new methods introduced — behavior differs from HashSet (order preservation)

| Method Signature | Description | Behavior / Edge Case |
|------------------|------------|----------------------|
| `boolean add(E e)` | Adds element | Maintains insertion order; returns false if duplicate |
| `Iterator<E> iterator()` | Returns iterator | Iterates in insertion order |
| `boolean remove(Object o)` | Removes element | Order of remaining elements preserved |

---

## Internal Structure

A LinkedHashMap maintains order using a doubly linked list running through all entries.

So each entry is not just a bucket node — it also has links:

> `[prev] ←→ [current node] ←→ [next]`

Each element in LinkedHashSet is stored like this internally:

```java
class Entry {
    int hash;
    K key;
    Entry next;   // for hash bucket (collision handling)

    Entry before; // for insertion order (linked list)
    Entry after;  // for insertion order (linked list)
}
```

Let’s say:

```
set.add(10);
set.add(5);
set.add(20);
```

#### Internally:

##### Buckets (random placement)

```
Bucket[2] → 10
Bucket[5] → 5
Bucket[1] → 20
```

##### Linked List (order maintained)

`10 ⇄ 5 ⇄ 20`


| Concern     | Data Structure Used |
| ----------- | ------------------- |
| Fast lookup | Hash buckets        |
| Order       | Doubly linked list  |


---
## Code Example

###  Insertion Order Preservation

```java

public static void main(String[] args) {
    LinkedHashSet<Integer> set = new LinkedHashSet<>();

    set.add(10);
    set.add(5);
    set.add(20);

    System.out.println(set); // Output: [10, 5, 20] ✅

    for (Integer i : set) {
        System.out.print(i + " ");  // Output: 10, 5, 20

    }
    
}
```

👉 Unlike HashSet, order is maintained

### HashSet vs LinkedHashSet

| Feature     | HashSet      | LinkedHashSet             |
| ----------- | ------------ | ------------------------- |
| Order       | No guarantee | Maintains insertion order |
| Performance | Faster       | Slightly slower           |
| Structure   | Hash table   | Hash table + linked list  |


### When to Use

- When you need:
    - **Uniqueness + insertion order**
- Useful in:
  - Caching
  - Removing duplicates while preserving order

### When NOT to Use

- When sorting required → use TreeSet
- When maximum performance needed → use HashSet

