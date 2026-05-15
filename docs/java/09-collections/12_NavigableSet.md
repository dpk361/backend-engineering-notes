---
title: NavigableSet (I)
parent: Collection Framework
nav_order: 12
---

# NavigableSet Interface 

## Overview

- `NavigableSet` extends **SortedSet**
- Provides **navigation methods** to:
    - Find closest matches
    - Traverse in both directions
- Implemented mainly by **TreeSet**

---

## Key Characteristics

| Feature | Description |
|--------|------------|
| **Order** | Sorted (ascending by default) |
| **Duplicates** | Not allowed |
| **Null Values** | Not allowed |
| **Traversal** | Forward + Reverse |
| **Implementation** | TreeSet |

---

## Core Idea

👉 Adds **"closest match" operations** on top of SortedSet

Instead of just:
- first()
- last()

You now get:
- lower()
- floor()
- ceiling()
- higher()

---

## NavigableSet-Specific Methods

| Method Signature | Description | Behavior / Edge Case |
|------------------|------------|----------------------|
| `E lower(E e)` | Greatest element < e | Returns null if none |
| `E floor(E e)` | Greatest element ≤ e | Returns null if none |
| `E ceiling(E e)` | Smallest element ≥ e | Returns null if none |
| `E higher(E e)` | Smallest element > e | Returns null if none |
| `E pollFirst()` | Removes & returns first element | Returns null if empty |
| `E pollLast()` | Removes & returns last element | Returns null if empty |
| `NavigableSet<E> descendingSet()` | Reverse order view | Backed by same set |
| `Iterator<E> descendingIterator()` | Reverse iterator | Iterates high → low |

---

## Range Methods (Enhanced)

| Method Signature | Description | Behavior / Edge Case |
|------------------|------------|----------------------|
| `subSet(E from, boolean fromInc, E to, boolean toInc)` | Range with control | Inclusive/exclusive configurable |
| `headSet(E to, boolean inclusive)` | Elements before | Inclusion configurable |
| `tailSet(E from, boolean inclusive)` | Elements after | Inclusion configurable |

---

## Code Examples

### 1. Closest Element Methods

```java
public static void main(String[] args) {
  NavigableSet<Integer> set = new TreeSet<>();

  set.add(10);
  set.add(20);
  set.add(30);
  set.add(40);

  System.out.println(set.lower(25));   // 20
  System.out.println(set.floor(20));   // 20
  System.out.println(set.ceiling(25)); // 30
  System.out.println(set.higher(30));  // 40

}
```

### 2. Poll Methods

```java
public static void main(String[] args) {
  NavigableSet<Integer> set = new TreeSet<>();

  set.add(10);
  set.add(20);
  set.add(30);

  System.out.println(set.pollFirst()); // 10
  System.out.println(set.pollLast());  // 30

  System.out.println(set); // [20]
}
```

### 3. Descending Order

```java
public static void main(String[] args) {
  NavigableSet<Integer> set = new TreeSet<>();

  set.add(10);
  set.add(20);
  set.add(30);

  System.out.println(set.descendingSet()); // [30, 20, 10]
}
```

### 4. Range with Control

```java
public static void main(String[] args) {
  NavigableSet<Integer> set = new TreeSet<>();

  set.add(10);
  set.add(20);
  set.add(30);
  set.add(40);

  System.out.println(set.subSet(10, true, 30, false));
// [10, 20]
}
```

### Visual Understanding

#### Given

> `[10, 20, 30, 40]`

For element `25`:

| Method      | Result |
| ----------- | ------ |
| lower(25)   | 20     |
| floor(25)   | 20     |
| ceiling(25) | 30     |
| higher(25)  | 30     |


### Time Complexity (TreeSet)

| Operation  | Time Complexity |
| ---------- | --------------- |
| Add        | O(log n)        |
| Remove     | O(log n)        |
| Search     | O(log n)        |
| Navigation | O(log n)        |

----

## When to Use

- When you need:
  - Sorted + nearest element lookup
  - Range queries with precision

- Real-world:
  - Auto-suggestions
  - Leaderboards
  - Scheduling systems


## SortedSet vs NavigableSet

| Feature           | SortedSet | NavigableSet |
| ----------------- | --------- | ------------ |
| Basic sorting     | Yes       | Yes          |
| Range operations  | Limited   | Advanced     |
| Closest match     | No        | Yes          |
| Reverse traversal | No        | Yes          |


## Developer Notes 
- All methods are backed by TreeSet (Red-Black Tree)
- Range methods return views (not copies)
- Modifications affect original set
- No null elements allowed

