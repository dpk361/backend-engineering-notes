---
title: SortedSet (I)
parent: Collection Framework
nav_order: 11
---

# SortedSet Interface

## Overview

- `SortedSet` is a **Set that maintains elements in sorted order**.
- Sorting is based on:
    - **Natural ordering** (Comparable)
    - OR **Custom comparator** (Comparator)
- It extends the `Set` interface.

---

## Key Characteristics

| Feature | Description |
|--------|------------|
| **Duplicates** | Not allowed |
| **Order** | Always sorted |
| **Null Values** | Not allowed (throws exception in most implementations like TreeSet) |
| **Index-based access** | Not supported |
| **Sorting Type** | Natural order or Comparator |

---

## Common Implementation

| Implementation | Description |
|---------------|------------|
| **TreeSet** | Implements SortedSet using Red-Black Tree |

---

## Sorting Mechanism

### 1. Natural Ordering

```java
public static void main(String[] args) {
    SortedSet<Integer> set = new TreeSet<>();
    set.add(30);
    set.add(10);
    set.add(20);

    System.out.println(set); // Output: [10, 20, 30]
}
```

- Uses Comparable (compareTo())

### 2. Custom Comparator

```java
public static void main(String[] args) {
    SortedSet<Integer> set = new TreeSet<>((a, b) -> b - a);

    set.add(10);
    set.add(20);
    set.add(30);

    System.out.println(set); // Output: [30, 20, 10]
}
```


## SortedSet-Specific Methods

| Method Signature                                  | Description             | Behavior / Edge Case                     |
| ------------------------------------------------- | ----------------------- | ---------------------------------------- |
| `Comparator<? super E> comparator()`              | Returns comparator used | Returns null if natural ordering         |
| `E first()`                                       | Returns lowest element  | Throws `NoSuchElementException` if empty |
| `E last()`                                        | Returns highest element | Throws `NoSuchElementException` if empty |
| `SortedSet<E> headSet(E toElement)`               | Elements < toElement    | toElement NOT included                   |
| `SortedSet<E> tailSet(E fromElement)`             | Elements ≥ fromElement  | fromElement included                     |
| `SortedSet<E> subSet(E fromElement, E toElement)` | Range view              | from inclusive, to exclusive             |


### Range View Examples

```java
public static void main(String[] args) {
    SortedSet<Integer> set = new TreeSet<>();
    set.add(10);
    set.add(20);
    set.add(30);
    set.add(40);

    System.out.println(set.headSet(30)); // [10, 20]
    System.out.println(set.tailSet(20)); // [20, 30, 40]
    System.out.println(set.subSet(10, 40)); // [10, 20, 30]
}
```

## Time Complexity (TreeSet)

| Operation | Time Complexity |
| --------- | --------------- |
| Add       | O(log n)        |
| Remove    | O(log n)        |
| Contains  | O(log n)        |


## Important Rules

- Elements must be:
    - **Comparable**, OR
    - Provided with a **Comparator**
- Otherwise:
  - **ClassCastExceptio**


## Comparison: Set vs SortedSet

| Feature        | Set                    | SortedSet     |
| -------------- | ---------------------- | ------------- |
| Order          | No guarantee           | Always sorted |
| Null           | Allowed (1 in HashSet) | Not allowed   |
| Performance    | O(1) (HashSet)         | O(log n)      |
| Implementation | HashSet, LinkedHashSet | TreeSet       |



## Developer Notes

- Internally uses Red-Black Tree
- Sorting is always maintained
- Range methods return views, not copies
- Modifying the view affects original set

