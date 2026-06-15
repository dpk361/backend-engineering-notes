---
title: summaryStatistics
parent: Stream API
nav_order: 3
---

# Java Streams - `summaryStatistics()`

## Why do we need `mapToInt()`?

Given:

```
list.stream().mapToInt(Integer::intValue).summaryStatistics();
```

Even though the list contains integers, `list.stream()` returns:

```
Stream<Integer>
```

and **`summaryStatistics()` is not available on `Stream<Integer>`**.

It is available only on primitive streams:

- `IntStream`
- `LongStream`
- `DoubleStream`

So we convert:

```
Stream<Integer>
```

to

```
IntStream
```

using:

```
mapToInt(Integer::intValue)
```

Then we can call:

```
summaryStatistics()
```

### Equivalent Lambda

```
list.stream()
    .mapToInt(i -> i)
    .summaryStatistics();
```

---

## What does `summaryStatistics()` provide?

In a **single traversal** of the stream, it computes:

- Count
- Sum
- Min
- Max
- Average

and returns an `IntSummaryStatistics` object.

---

## Example 1: Student Scores

```java
public static void main(String[] args) {

    List<Integer> scores = List.of(78, 92, 85, 67, 95, 88);

    IntSummaryStatistics stats = scores.stream()
            .mapToInt(Integer::intValue)
            .summaryStatistics();

    System.out.println("Count   : " + stats.getCount());
    System.out.println("Sum     : " + stats.getSum());
    System.out.println("Min     : " + stats.getMin());
    System.out.println("Max     : " + stats.getMax());
    System.out.println("Average : " + stats.getAverage());
}
```

### Output

```text
Count   : 6
Sum     : 505
Min     : 67
Max     : 95
Average : 84.16666666666667
```

---

## Without `summaryStatistics()`

To get all metrics separately:

```java
int min = scores.stream().mapToInt(Integer::intValue).min().orElse(0);

int max = scores.stream().mapToInt(Integer::intValue).max().orElse(0);

double avg = scores.stream().mapToInt(Integer::intValue).average().orElse(0);

long count = scores.size();

int sum = scores.stream().mapToInt(Integer::intValue).sum();
```

This requires multiple stream operations.

---

## Example 2: Employee Salaries

### Employee Class

```java
class Employee {
    private String name;
    private int salary;

    public Employee(String name, int salary) {
        this.name = name;
        this.salary = salary;
    }

    public int getSalary() {
        return salary;
    }
}
```

### Statistics

```java
public static void main(String[] args) {

    List<Employee> employees = List.of(
            new Employee("John", 50000),
            new Employee("Jane", 70000),
            new Employee("Mike", 60000)
    );

    IntSummaryStatistics salaryStats = employees.stream()
            .mapToInt(Employee::getSalary)
            .summaryStatistics();

    System.out.println(salaryStats);
}
```

### Output

```text
IntSummaryStatistics{
count=3,
sum=180000,
min=50000,
average=60000.000000,
max=70000
}
```

### Real-world Usage

Commonly used for:

- Salary reports
- Sales analytics
- Student marks analysis
- Dashboard metrics

---

## Example 3: Grouping + Statistics (Powerful)

### Employee Class

```java
class Employee {
    String department;
    int salary;

    Employee(String department, int salary) {
        this.department = department;
        this.salary = salary;
    }

    String getDepartment() {
        return department;
    }

    int getSalary() {
        return salary;
    }
}
```

### Department-wise Statistics

```java
public static void main(String[] args) {

    List<Employee> employees = List.of(
            new Employee("IT", 50000),
            new Employee("IT", 70000),
            new Employee("HR", 40000),
            new Employee("HR", 45000),
            new Employee("Finance", 90000)
    );

    Map<String, IntSummaryStatistics> statsByDept =
            employees.stream()
                    .collect(Collectors.groupingBy(
                            Employee::getDepartment,
                            Collectors.summarizingInt(Employee::getSalary)
                    ));

    statsByDept.forEach((dept, stats) ->
            System.out.println(dept + " -> " + stats));

}
```

### Output

```text
IT -> count=2, sum=120000, min=50000, average=60000.0, max=70000
HR -> count=2, sum=85000, min=40000, average=42500.0, max=45000
Finance -> count=1, sum=90000, min=90000, average=90000.0, max=90000
```

### Why this is powerful

With one collector, you get:

- Employee count per department
- Total salary per department
- Lowest salary
- Highest salary
- Average salary

---

## Alternative Using Collector

Instead of:

```
list.stream()
    .mapToInt(Integer::intValue)
    .summaryStatistics();
```

You can also use:

```
list.stream()
    .collect(Collectors.summarizingInt(Integer::intValue));
```

This returns the same `IntSummaryStatistics`.

Useful when already working inside a `collect()` pipeline.

---

## When should you use `summaryStatistics()`?

Use it when you need **two or more** of:

- Count
- Sum
- Min
- Max
- Average

Examples:

✅ Student score analysis

✅ Salary reports

✅ Sales dashboards

✅ Order analytics

✅ Department-wise summaries

---

## When NOT to use it

If you only need one metric:

```java
int max = list.stream()
              .mapToInt(Integer::intValue)
              .max()
              .orElse(0);
```

Using `summaryStatistics()` would be unnecessary.

---

## Interview Answer

> `summaryStatistics()` computes count, sum, min, max, and average in a single traversal of the stream. It is more efficient and expressive than executing multiple stream operations separately for each metric.