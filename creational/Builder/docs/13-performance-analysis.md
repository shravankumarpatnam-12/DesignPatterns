# Builder Pattern in the JDK

## Introduction

A common question during design pattern discussions is:

> Does the Java Standard Library actually use the Builder Pattern?

The answer is:

```text id="gbp7w8"
Absolutely.
```

In fact, some of the most widely used APIs in modern Java rely heavily on Builder-style design.

Studying these implementations is valuable because they demonstrate how experienced API designers apply Builder concepts in production-quality libraries.

This chapter explores Builder usage across the Java ecosystem and analyzes the design decisions behind these APIs.

---

# Why Study JDK Builders?

Learning Builder from toy examples is useful.

However:

```java id="mnhc22"
User.Builder
```

does not teach us how Builder behaves in large-scale APIs.

The JDK teaches:

* API design
* Fluent interfaces
* Immutability
* Validation
* Evolution
* Usability

These lessons are often more valuable than the pattern itself.

---

# Example 1: HttpRequest.Builder

One of the best Builder implementations in modern Java.

Class:

```java id="4zm1j4"
java.net.http.HttpRequest
```

Creation:

```java id="u3dkoh"
HttpRequest request =
    HttpRequest.newBuilder()
        .uri(uri)
        .header(
            "Accept",
            "application/json")
        .timeout(Duration.ofSeconds(5))
        .GET()
        .build();
```

Notice several things:

* Readable
* Self-documenting
* Supports optional configuration
* Produces immutable objects

This is textbook Builder usage.

---

# Why Constructor Would Be Terrible

Imagine:

```java id="n2k1i8"
new HttpRequest(
    uri,
    headers,
    timeout,
    method,
    proxy,
    authenticator,
    ...
);
```

Problems:

```text id="72qhl7"
Unreadable

Error-Prone

Difficult to Evolve
```

Builder solves all three.

---

# Design Observation

Notice:

```java id="v5zx11"
HttpRequest.newBuilder()
```

instead of:

```java id="ly7jhj"
new HttpRequest.Builder()
```

This is a deliberate API design choice.

The class controls:

```text id="lz9u93"
How Builders Are Created
```

which improves encapsulation.

---

# Example 2: HttpClient.Builder

Class:

```java id="ys0mvu"
java.net.http.HttpClient
```

Usage:

```java id="3xuv7e"
HttpClient client =
    HttpClient.newBuilder()
        .connectTimeout(
            Duration.ofSeconds(10))
        .build();
```

Again:

```text id="s0n5l8"
Many Optional Settings
```

make Builder a natural fit.

---

# Example 3: ProcessBuilder

Class:

```java id="c6m31u"
java.lang.ProcessBuilder
```

Usage:

```java id="r8smfd"
Process process =
    new ProcessBuilder(
            "java",
            "-version")
        .start();
```

Interesting observation:

```text id="mzwzlj"
ProcessBuilder
is not a classic Builder.
```

It is mutable and reusable.

Example:

```java id="6v79s6"
ProcessBuilder builder =
        new ProcessBuilder();

builder.command("java");
```

This demonstrates that Builder implementations can vary depending on requirements.

---

# Builder vs Builder-Like APIs

Not every API ending with:

```text id="bwyx1y"
Builder
```

follows the pure Builder Pattern.

Some APIs borrow Builder concepts without fully implementing the pattern.

Examples:

```text id="qz0m7d"
ProcessBuilder

StringBuilder
```

These classes emphasize:

```text id="m77k4x"
Incremental Construction
```

rather than immutable object creation.

---

# Example 4: URIBuilder (Ecosystem)

Many third-party libraries provide:

```java id="6jjk1x"
URIBuilder
```

style APIs.

Example:

```java id="f5m4ya"
URI uri =
    new URIBuilder()
        .setScheme("https")
        .setHost("api.example.com")
        .setPath("/users")
        .build();
```

The same design principles appear repeatedly across the Java ecosystem.

---

# Example 5: DateTimeFormatterBuilder

Class:

```java id="0iuhbo"
java.time.format.DateTimeFormatterBuilder
```

Usage:

```java id="7s7l0w"
DateTimeFormatter formatter =
    new DateTimeFormatterBuilder()
        .appendPattern("yyyy")
        .appendLiteral("-")
        .appendPattern("MM")
        .toFormatter();
```

Interesting observation:

```text id="x5knr7"
Builder Produces Another Object
```

The final product is:

```java id="7s0xji"
DateTimeFormatter
```

not:

```java id="oq3szt"
DateTimeFormatterBuilder
```

This closely follows the classic Builder pattern.

---

# Example 6: Locale.Builder

Class:

```java id="c2yowf"
java.util.Locale.Builder
```

Usage:

```java id="2u2ysw"
Locale locale =
    new Locale.Builder()
        .setLanguage("en")
        .setRegion("US")
        .build();
```

Again:

```text id="a3xj9m"
Complex Object
+
Optional Configuration
```

Builder fits naturally.

---

# What These APIs Have in Common

Most successful Builder APIs share several characteristics.

---

## Fluent Interface

Example:

```java id="j01yrk"
builder
    .host(...)
    .port(...)
    .timeout(...)
```

Method chaining improves readability.

---

## Optional Parameters

Not every property must be specified.

Example:

```java id="ktm1zc"
HttpRequest.newBuilder()
        .uri(uri)
        .build();
```

Minimal configuration remains simple.

---

## Immutable Result

Example:

```java id="b3mjlwm"
HttpRequest request =
        builder.build();
```

The resulting object is immutable.

---

## Centralized Validation

Validation occurs during:

```java id="dxt1hm"
build()
```

or equivalent construction steps.

---

# Why the JDK Rarely Uses Huge Constructors

Imagine:

```java id="69fxmk"
new HttpClient(
    proxy,
    authenticator,
    timeout,
    executor,
    redirectPolicy,
    sslContext,
    version
);
```

Problems:

* Parameter ordering
* Readability
* Evolution
* Optional values

Builder avoids all of them.

---

# API Evolution Benefits

Suppose Java 22 adds:

```java id="n8c1vf"
requestPriority
```

Constructor approach:

```java id="nczv4z"
New Constructor Needed
```

Builder approach:

```java id="6pxu2w"
.priority(...)
```

Existing code continues working.

This is one reason Builder is common in long-lived APIs.

---

# Why StringBuilder Is Not a Classic Builder

Developers often assume:

```java id="mqw7lh"
StringBuilder
```

implements the Builder Pattern.

Not exactly.

Usage:

```java id="vjlwm5"
StringBuilder sb =
        new StringBuilder();

sb.append("Hello");
sb.append("World");
```

Goal:

```text id="6lmzb5"
Incrementally Construct Text
```

The object being modified and the object performing construction are the same.

This differs from the classic GoF Builder structure.

---

# Builder Pattern Characteristics in the JDK

Strong examples:

```text id="5ev88q"
HttpRequest.Builder

HttpClient.Builder

Locale.Builder

DateTimeFormatterBuilder
```

---

Builder-Inspired APIs:

```text id="w3sj9g"
ProcessBuilder

StringBuilder
```

---

Not Builder Candidates:

```text id="vbzqcf"
LocalDate

Duration

BigDecimal
```

These objects are small enough that constructors and static factories remain sufficient.

---

# Lessons from JDK Designers

The JDK demonstrates several important principles.

---

## Builders for Complexity

When configuration grows:

```text id="8pj6gs"
Builder Becomes Valuable
```

---

## Constructors for Simplicity

When objects remain small:

```text id="2jlwm0"
Constructor Remains Better
```

---

## Immutability Matters

Most modern Builder APIs produce immutable objects.

---

## Fluent APIs Improve Usability

Good APIs optimize for:

```text id="9rkl5q"
Developer Experience
```

not merely functionality.

---

# Real-World Impact

The APIs discussed in this chapter are used millions of times every day.

Their widespread adoption demonstrates that Builder is not merely an academic design pattern.

It is a practical solution to real API design challenges.

---

# Common Mistake #1

Assuming every class ending with:

```text id="aqw0p3"
Builder
```

implements the GoF Builder Pattern.

Many are Builder-inspired rather than pure implementations.

---

# Common Mistake #2

Ignoring API evolution.

Long-lived APIs often benefit significantly from Builder-based design.

---

# Common Mistake #3

Copying JDK Builders without understanding why they were chosen.

Patterns should solve problems.

They should not be applied mechanically.

---

# Interview Questions

### Give examples of Builder usage in the JDK.

Examples include:

* HttpRequest.Builder
* HttpClient.Builder
* Locale.Builder
* DateTimeFormatterBuilder

---

### Why is Builder heavily used in HttpRequest?

Because HTTP requests contain many optional configuration parameters.

---

### Is StringBuilder an implementation of the GoF Builder Pattern?

Not exactly.

It is closer to an incremental construction API.

---

### What benefit does Builder provide for API evolution?

New options can be added without breaking existing client code.

---

### Why do JDK designers often prefer Builder over large constructors?

Because Builders improve readability, flexibility, and maintainability.

---

# Key Takeaways

* The JDK makes extensive use of Builder-style APIs.
* Builder is particularly useful for complex configuration objects.
* HttpRequest.Builder is one of the best modern examples.
* Not every class named Builder implements the classic Builder Pattern.
* Builder improves API evolution and backward compatibility.
* Fluent interfaces improve usability.
* The JDK generally reserves Builders for complex objects and constructors for simple ones.
* Studying JDK APIs teaches practical Builder design principles.
