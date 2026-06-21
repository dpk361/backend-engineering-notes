---
title: Release Versioning and Migration Notes
parent: Java-10
nav_order: 5
---

# Release Versioning and Migration Notes

Java 10 is important because it arrived after Java changed its release model.

Java started moving faster:

- A feature release every six months.
- Some versions are LTS.
- Some versions are short-term releases.

Java 10 was short-term.

Java 11 was the next LTS.

---

## 1. Time-Based Release Versioning

### Why Was It Introduced?

Old Java releases took many years.

Example:

- Java 8: 2014
- Java 9: 2017

That made new features slow to reach developers.

Java moved to a predictable release cycle.

---

## Version Format

Java 10 uses this version idea:

```text
$FEATURE.$INTERIM.$UPDATE.$PATCH
```

Example:

```text
10.0.1
```

Meaning:

- `10` = feature release.
- `0` = interim number.
- `1` = update release.

In the six-month model:

- March 2018 release was JDK 10.
- September 2018 release was JDK 11.

---

## Developer Meaning

Do not parse Java version strings using old Java 8 assumptions.

Old style:

```text
1.8.0_202
```

Newer style:

```text
10.0.2
11.0.20
17.0.10
```

Use Java APIs when possible:

```
Runtime.Version version = Runtime.version();

System.out.println(version.feature());
System.out.println(version.update());
```

---

## Edge Case: Scripts Parsing java -version

Some scripts expect old output format.

Bad idea:

```bash
java -version | grep "1.8"
```

Better:

- Use build tool version checks.
- Use `Runtime.version()` in Java code.
- Use tested scripts for the newer version format.

---

## 2. Root Certificates

Java 10 provided a default set of root Certification Authority certificates in the JDK.

### Why It Matters

HTTPS needs trusted root certificates.

Without them, TLS calls can fail even when the server certificate is valid.

Common error:

```text
PKIX path building failed
```

Developer meaning:

- OpenJDK builds became more useful out of the box.
- HTTPS behavior became more consistent across JDK builds.

---

## Daily Debugging Tip

If HTTPS fails:

- Check certificate chain.
- Check corporate proxy certificate.
- Check custom truststore.
- Check `JAVA_HOME`.
- Check which JDK distribution is running.

Java 10 root certificates help, but they do not fix every TLS issue.

---

## 3. Removed javah Tool

Java 10 removed the old `javah` tool.

`javah` was used for JNI native header generation.

Use:

```bash
javac -h generated-headers NativeExample.java
```

instead of:

```bash
javah NativeExample
```

Developer meaning:

- Most backend developers never use this.
- It matters if you work with JNI or native libraries.
- Old build scripts may fail if they still call `javah`.

---

## 4. Additional Unicode Language-Tag Extensions

Java 10 improved support for Unicode locale extensions.

Supported areas include:

- Currency type.
- First day of week.
- Region override.
- Time zone.

Example:

```
Locale locale = Locale.forLanguageTag("en-US-u-fw-mon");
DayOfWeek firstDay = WeekFields.of(locale).getFirstDayOfWeek();

System.out.println(firstDay);
```

Developer meaning:

- Useful for international applications.
- Date, number, currency, and calendar behavior can depend on locale.
- Tests should set locale clearly when output matters.

---

## 5. JDK Repository Consolidation

Java 10 consolidated the old JDK source repositories into one repository.

Developer meaning:

- This matters mostly to JDK contributors.
- Normal backend application code is not affected.

---

## Java 10 Migration Checklist

If moving from Java 9 to Java 10:

- Check compiler errors from incorrect `var` usage.
- Check code style rules for `var`.
- Replace old `javah` usage with `javac -h`.
- Check scripts that parse `java -version`.
- Check tests that depend on exact locale formatting.
- Check HTTPS behavior if your app uses custom truststores.
- Keep build tools and IDE updated.

If moving from Java 8 directly to Java 10:

- Also review Java 9 module system impact.
- Check JDK internal API usage.
- Check reflection warnings.
- Check removed runtime layout assumptions like `rt.jar`.

---

## Best Practices

- Remember Java 10 was not LTS.
- For production baselines, most companies moved from Java 8 to Java 11, not Java 10.
- Learn Java 10 mainly for `var`, collection improvements, Optional cleanup, and release-model understanding.
- Do not write scripts that depend on old Java version string format.
- Pin locale/time zone in tests when formatting output.

---

## Quick Summary

Java 10 changed how Java versions are released and named. It also improved root certificates, removed `javah`, and added locale-related support. Most migration issues are around tooling, scripts, and build setup rather than application logic.

