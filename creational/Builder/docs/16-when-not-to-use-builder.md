# When NOT to Use Builder

## Introduction

After studying twenty chapters on the Builder Pattern, it is easy to develop the following belief:

> Builder is the best way to create objects.

This conclusion is understandable.

Builder provides:

* Readability
* Immutability support
* Validation
* Extensibility
* API evolution
* Better developer experience

However, experienced architects know an important principle:

```text id="7z4fqe"
Every pattern solves a problem.

No pattern should be applied universally.
```

One of the biggest differences between a junior engineer and an architect is the ability to recognize when a pattern adds value and when it adds unnecessary complexity.

This chapter focuses on situations where Builder is the wrong tool.

---

# The Cost of Every Pattern

Patterns are not free.

A Builder introduces:

```text id="q4a9kp"
Additional Class

Additional Methods

Additional Maintenance

Additional Complexity
```

Example:

Without Builder:

```java id="a1xv5t"
Money money =
        new Money(100, "USD");
```

With Builder:

```java id="c8n7we"
Money money =
    new Money.Builder()
        .amount(100)
        .currency("USD")
        .build();
```

More code.

Same outcome.

The extra complexity may not be justified.

---

# Rule #1: Do Not Use Builder for Tiny Objects

Consider:

```java id="f5s8dr"
public record Point(
        int x,
        int y) {
}
```

Usage:

```java id="m2w6hj"
Point point =
        new Point(10, 20);
```

Clear.

Concise.

Readable.

---

Builder version:

```java id="r9p1tz"
Point point =
    new Point.Builder()
        .x(10)
        .y(20)
        .build();
```

The Builder provides no meaningful benefit.

---

# Small Value Objects

Examples:

```text id="v7n3mx"
Point

Coordinate

Money

Range

Percentage

Dimension
```

These objects usually have:

```text id="d8q5yl"
Few Fields

Few Rules

Few Variations
```

Constructors or records are often superior.

---

# Rule #2: Do Not Use Builder When All Fields Are Mandatory

Consider:

```java id="g1t7ky"
public record CurrencyPair(
        String base,
        String quote) {
}
```

Usage:

```java id="u9k4vc"
new CurrencyPair(
        "USD",
        "INR");
```

Every field is required.

There are no optional values.

Builder adds little value.

---

# Builder Excels with Optional Fields

Example:

```java id="w3j8pf"
DatabaseConfig
```

Possible fields:

```text id="b4n2zr"
Host

Port

SSL

Retries

Timeout

Compression

Metrics
```

Many are optional.

Builder becomes useful.

---

# Rule #3: Do Not Use Builder for Simple Records

Java Records significantly changed object design.

Example:

```java id="n7v1qy"
public record User(
        String firstName,
        String lastName) {
}
```

Usage:

```java id="f8q4tw"
User user =
        new User(
                "Shravan",
                "Patnam");
```

Extremely concise.

---

Adding:

```java id="e2k9pl"
User.Builder
```

may increase complexity without improving usability.

---

# Rule #4: Do Not Use Builder Solely Because a Framework Uses It

Developers often see:

```java id="j5m8qr"
HttpRequest.Builder
```

or:

```java id="r1x7uw"
WebClient.builder()
```

and conclude:

```text id="h9v3kc"
Everything should have a Builder.
```

This is incorrect.

Framework APIs face different constraints:

```text id="m4n7zs"
Public APIs

Backward Compatibility

Long-Term Evolution

Third-Party Consumers
```

Your internal domain model may not have the same requirements.

---

# Rule #5: Avoid Builder for Performance-Critical Tiny Objects

Consider:

```java id="c6t1vo"
Vector2D

Point

Pixel

Coordinate
```

created millions of times per second.

Example:

```java id="v8w2mn"
for(int i = 0;
    i < 1_000_000;
    i++) {

    Point point =
        new Point(x, y);
}
```

Builder overhead may become measurable.

---

Important:

This applies only to extremely allocation-heavy systems.

Examples:

```text id="n2k7pj"
Game Engines

Graphics Rendering

Simulation Engines

High-Frequency Trading
```

Most enterprise applications are not in this category.

---

# Rule #6: Avoid Builder When Construction Logic Is Trivial

Consider:

```java id="f3z9ka"
new Age(18);
```

Builder:

```java id="s1m6yx"
Age.builder()
   .value(18)
   .build();
```

The Builder provides no additional clarity.

---

# Rule #7: Avoid Builder for Temporary Internal Objects

Example:

```java id="k7r5tx"
private UserCacheKey createKey(
        String id,
        String region) {

    return new UserCacheKey(
            id,
            region);
}
```

The object:

```text id="u4m8wn"
Simple

Private

Internal
```

A Builder often adds unnecessary ceremony.

---

# Rule #8: Avoid Pattern Cargo Culting

One of the most common design mistakes.

Pattern cargo culting means:

```text id="v1k9ph"
Using a pattern
because it is popular
rather than necessary.
```

Example:

```java id="y7w2nz"
Point.Builder

Money.Builder

Age.Builder

Percentage.Builder
```

Every class gets a Builder.

The codebase becomes larger without becoming better.

---

# Signs a Builder Is Probably Unnecessary

If most of these are true:

```text id="m5q3jr"
Less than 4 fields

No optional fields

No validation

No future growth expected

No readability issues

Record works well
```

Builder may be unnecessary.

---

# Signs a Builder Is Probably Valuable

If most of these are true:

```text id="r8t6yn"
Many fields

Many optional fields

Complex validation

Immutability required

API evolution expected

Public API
```

Builder is likely a good choice.

---

# Real-World Comparison

## Good Builder Candidate

```java id="d2x4zc"
DatabaseConfig
```

Fields:

```text id="f1w9mq"
host

port

ssl

timeout

retryCount

metrics

compression

proxy
```

Builder improves readability significantly.

---

## Poor Builder Candidate

```java id="n6v3qs"
Point
```

Fields:

```text id="a7k2wt"
x

y
```

Builder adds complexity without meaningful benefit.

---

# Architect Decision Framework

Before creating a Builder, ask:

### Question 1

```text id="p9x6kj"
Will object construction
become difficult to read?
```

---

### Question 2

```text id="r4m8yt"
Will optional fields exist?
```

---

### Question 3

```text id="t7k5vn"
Will validation become complex?
```

---

### Question 4

```text id="u2n9qp"
Will the API evolve over time?
```

---

### Question 5

```text id="w5q3jr"
Does the Builder provide
more value than complexity?
```

If most answers are:

```text id="x8m4ka"
No
```

a Builder is probably unnecessary.

---

# The Architect's View

Junior developers often ask:

```text id="e1t7yp"
How do I apply this pattern?
```

Architects often ask:

```text id="n4v2zs"
Should this pattern be applied at all?
```

The second question is usually more important.

---

# Common Mistake #1

Using Builder because:

```text id="z7k3mw"
It looks professional.
```

Patterns are not status symbols.

---

# Common Mistake #2

Creating Builders for every DTO.

Many DTOs are perfectly represented by records.

---

# Common Mistake #3

Ignoring maintenance cost.

Every Builder must evolve when the model evolves.

---

# Common Mistake #4

Assuming Builder always improves readability.

For very small objects, constructors are often clearer.

---

# Interview Questions

### When should Builder be avoided?

For small, simple objects with few fields and little configuration complexity.

---

### Are Java Records reducing Builder usage?

Yes, especially for small immutable objects.

---

### Why can Builder become an anti-pattern?

When it adds complexity without solving a real problem.

---

### Is Builder appropriate for every domain model?

No.

The decision depends on complexity, optional fields, validation, and future evolution.

---

### What is pattern cargo culting?

Applying patterns automatically without understanding whether they solve an actual problem.

---

# Key Takeaways

* Builder is powerful but not universally appropriate.
* Every pattern introduces complexity.
* Small objects often benefit more from constructors or records.
* Builders shine when configuration complexity grows.
* Public APIs frequently benefit from Builder-based design.
* Pattern cargo culting should be avoided.
* Architects evaluate trade-offs rather than blindly applying patterns.
* Knowing when not to use Builder is as important as knowing how to implement it.
