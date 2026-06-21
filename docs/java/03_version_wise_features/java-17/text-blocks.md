---
title: Text Blocks (Java 15+)
parent: Java-17
nav_order: 5
---

# Text Blocks (Java 15+)

**Text Blocks** are a feature introduced in **Java 15 (preview in 13/14)** that allow you to write multi-line string literals using **triple double-quotes """**.

They are designed to make writing **SQL, JSON, XML, HTML, and large formatted strings** much cleaner and more readable compared to traditional string concatenation.

## Syntax

```java
String message = """
Hello Mohan,
Welcome to backend engineering.
Regards,
Team
""";
```

### Key Rules

- Starts and ends with """
- Must start on a new line after opening delimiter
- Preserves line breaks automatically
- Handles indentation intelligently

---

## Why Text Blocks Were Introduced

### Before Java 15:

```java
String json = "{\n" +
"  \"name\": \"Mohan\",\n" +
"  \"role\": \"Software Engineer\"\n" +
"}";

```

**Problems:**

- Hard to read
- Error-prone
- Lots of \n and \"
- Ugly string concatenation

### With Text Blocks:

```java

String json = """
{
"name": "Mohan",
"role": "Software Engineer"
}
""";
```

Much cleaner and maintainable.

---

###  Writing SQL Queries

```java
String query = """
SELECT u.id, u.name, u.email
FROM users u
WHERE u.status = 'ACTIVE'
ORDER BY u.created_at DESC
""";

```

This is extremely useful for:

- JDBC template
- Native JPA queries
- Complex joins
- Reporting queries

### JSON Payload (Kafka / REST Mocking)

```java
String payload = """
{
"transactionId": "TXN123",
"amount": 1000,
"currency": "INR",
"status": "SUCCESS"
}
""";
```

Helpful for:

- Writing integration tests
- Mock Kafka events
- Testcontainers
- WireMock stubs

### HTML Template (Email Service)

```java

String htmlTemplate = """
<html>
<body>
<h1>Transaction Successful</h1>
<p>Your transaction ID is: %s</p>
</body>
</html>
""".formatted("TXN123");
```

---

## Indentation Handling

Java automatically removes common leading whitespace.

Example:

```java
String text = """
Line 1
Line 2
Line 3
""";
```

The leading indentation (same for all lines) is stripped automatically.     

If indentation differs, only common whitespace is removed.

---

## Escape Sequences Still Work

You can still use:

- \n
- \t
- \"
- \\

**Example:**

```java
String text = """
Path: C:\\Users\\Mohan
Tab\tSeparated
""";
```

---

## Controlling New Lines

By default, text blocks include a newline at the end.

If you want to avoid the final newline:

```java
String text = """
Hello World""";
```

Notice closing delimiter is on same line.

---

## Advanced: String Formatting with Text Blocks

Java 15+ supports .formatted():

```java
String sql = """
INSERT INTO transactions (id, amount, currency)
VALUES ('%s', %d, '%s')
""".formatted("TXN1", 5000, "INR");
```

Much cleaner than **String.format().**

---

### What problem do Java Text Blocks solve?

They improve **readability and maintainability of multi-line string literals** by eliminating **escape sequences, concatenation, and manual newline management.**

---

## Summary 

- Introduced in Java 15
- Use """ triple quotes
- Ideal for SQL, JSON, XML, HTML
- Supports .formatted()
- Improves readability drastically
