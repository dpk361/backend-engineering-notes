---
title: List.copyOf vs Collections.unmodifiableList
parent: Collections
nav_order: 19
---

# List.copyOf vs Collections.unmodifiableList

```java
public static void main(String[] args) {

    List<String> original = new ArrayList<>();
    original.add("A");

    List<String> view = Collections.unmodifiableList(original);
    List<String> copy = List.copyOf(original);

    original.add("B");

    System.out.println(view); // [A, B]
    System.out.println(copy); // [A]
}
```

`Collections.unmodifiableList` is a read-only view of the original list.

`List.copyOf` creates an unmodifiable copy.
