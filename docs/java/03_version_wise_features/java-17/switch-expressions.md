---
title: Switch Expressions
parent: Java-17
nav_order: 4
---

# Switch Expressions in Java

Switch expressions were introduced in Java 14 and are fully supported in Java 17.

They improve:

- Readability
- Safety
- Conciseness
- Functional-style programming

Switch is no longer just a statement — it can now return a value.

---

## Traditional Switch (Old Style)

```text
int dayNumber;
switch (day) {
    case MONDAY:
        dayNumber = 1;
        break;
    case TUESDAY:
        dayNumber = 2;
        break;
    default:
        dayNumber = 0;
}
```

## Switch Expression (Modern Style)

```java

int dayNumber = switch (day) {
    case "MONDAY" -> 1;
    case "TUESDAY" -> 2;
    default -> 0;
};
```

✔ No break needed     
✔ Returns value     
✔ Cleaner syntax       

---

## Multiple Labels in One Case

```java
 int type = switch (day) {
    case "SATURDAY", "SUNDAY" -> 0;
    case "MONDAY", "TUESDAY", "WEDNESDAY", "THURSDAY", "FRIDAY" -> 1;
    default -> throw new IllegalStateException("Unexpected value: " + day);
};

```

Much cleaner than repeated cases.

---

## Block with yield

If logic is complex, use yield.

```java
int result = switch (number) {
    case 1 -> 10;
    case 2 -> {
        int value = number * 5;
        yield value;
    }
    default -> 0;
};
```
> **yield** returns a value from block.

---

**Java 17 (preview feature)** allows **pattern matching with guard conditions**
inside switch.

```text

    Object payment = new Double(250000.0);

    String message = switch (payment) {

        case Double amt && amt > 1000000 ->
            "Manual Approval Required";

        case Double amt && amt > 200000 ->
            "Secondary OTP Verification Required";

            case Double amt ->
                    "Standard Processing";

            default ->
                    "Unsupported Payment Type";
    };
    
```
---

## Exhaustiveness Check

If all enum values are covered, no default required.

```java
enum Status { SUCCESS, FAILED }

String result = switch (status) {
    case SUCCESS -> "OK";
    case FAILED -> "NOT OK";
};
```

Compiler ensures all cases handled.

---


---
## Switch Expression vs Switch Statement

| Feature           | Statement | Expression |
| ----------------- | --------- | ---------- |
| Returns value     | ❌ No      | ✔ Yes      |
| break required    | ✔ Yes     | ❌ No       |
| fall-through risk | ✔ Yes     | ❌ No       |
| Cleaner syntax    | ❌         | ✔          |

---

> Switch expressions allow switch to return values using a concise arrow syntax, eliminate fall-through errors, and support multiple labels and yield blocks.
> They make conditional logic cleaner and safer compared to traditional switch statements.