# Builder vs Constructor

## Introduction

By this point, the Builder Pattern may seem like the perfect solution for object creation.

It provides:

* Readability
* Validation
* Immutability support
* Defensive copying
* Flexibility

This naturally raises a question:

> Why not use Builder everywhere?

The answer is simple:

**Every design choice has a cost.**

Builder introduces additional:

* Classes
* Methods
* Complexity
* Maintenance overhead

A good engineer knows not only how to use a pattern, but also when not to use it.

This chapter explores the trade-offs between constructors and builders and provides a practical decision framework.

---

# The Purpose of a Constructor

A constructor exists to initialize an object.

Simple example:

```java
public class Point {

    private final int x;
    private final int y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
```

Usage:

```java
Point point =
        new Point(10, 20);
```

Simple.

Readable.

Easy to maintain.

No Builder required.

---

# The Purpose of a Builder

A Builder exists to simplify the creation of complex objects.

Example:

```java
User user =
    new User.Builder()
        .firstName("Shravan")
        .lastName("Patnam")
        .age(28)
        .email("shravan@gmail.com")
        .phoneNumber("9999999999")
        .address("Hyderabad")
        .build();
```

The Builder makes object construction easier when complexity grows.

---

# Constructor Strengths

## Less Code

Example:

```java
public record Point(
        int x,
        int y) {
}
```

Usage:

```java
Point point =
        new Point(10, 20);
```

Extremely concise.

---

## Easier to Understand

Most developers immediately understand:

```java
new Point(10, 20);
```

No additional Builder class is required.

---

## Lower Maintenance Cost

No need to maintain:

```java
Builder
build()
fluent methods
validation helpers
```

Fewer moving parts.

---

## Better for Small Objects

Example:

```java
Money money =
        new Money(100, "USD");
```

Builder would add unnecessary complexity.

---

# Constructor Weaknesses

As fields increase:

```java
User(
    firstName,
    lastName,
    age,
    email,
    phone,
    address,
    city,
    country,
    department
)
```

Problems appear.

---

## Poor Readability

Example:

```java
new User(
    "Shravan",
    "Patnam",
    28,
    "shravan@gmail.com",
    "9999999999",
    "Hyderabad"
);
```

Meaning is not obvious.

---

## Parameter Ordering Bugs

Example:

```java
new User(
    "Shravan",
    "Patnam",
    28,
    "9999999999",
    "shravan@gmail.com"
);
```

Compiles successfully.

Incorrect result.

---

## Optional Fields Become Awkward

Example:

```java
new User(
    "Shravan",
    "Patnam",
    28,
    null,
    null,
    null
);
```

Null placeholders reduce readability.

---

# Builder Strengths

## Self-Documenting Code

Example:

```java
new User.Builder()
    .firstName("Shravan")
    .lastName("Patnam")
    .build();
```

The code explains itself.

---

## Optional Fields Become Natural

Example:

```java
new User.Builder()
    .firstName("Shravan")
    .lastName("Patnam")
    .build();
```

No placeholders.

No constructor overloads.

---

## Centralized Validation

Example:

```java
build()
```

All rules can be enforced in one location.

---

## Supports Immutability

Builders work naturally with:

```java
final fields
no setters
defensive copying
```

---

## Easier API Evolution

Suppose Version 1 contains:

```java
builder
    .firstName(...)
    .lastName(...)
```

Version 2 adds:

```java
builder
    .department(...)
```

Existing client code continues working.

---

# Builder Weaknesses

Builder is not free.

---

## More Code

Example:

```java
User

User.Builder

builder methods

build()
```

The implementation becomes larger.

---

## More Maintenance

Whenever a field is added:

```java
private String country;
```

We must update:

```java
Builder

Constructor

Validation

Copy logic
```

More moving parts.

---

## Overkill for Small Objects

Consider:

```java
public record Point(
        int x,
        int y) {
}
```

Builder version:

```java
Point point =
    new Point.Builder()
        .x(10)
        .y(20)
        .build();
```

More code.

Same outcome.

---

## Additional Cognitive Load

Developers must understand:

```java
Builder

Fluent API

Validation

Construction lifecycle
```

Not always justified.

---

# Comparing Side by Side

## Small Object

Constructor:

```java
new Money(100, "USD");
```

Builder:

```java
new Money.Builder()
    .amount(100)
    .currency("USD")
    .build();
```

Winner:

```text
Constructor
```

---

## Medium Object

Constructor:

```java
new User(
    "Shravan",
    "Patnam",
    28,
    "shravan@gmail.com"
);
```

Builder:

```java
new User.Builder()
    .firstName("Shravan")
    .lastName("Patnam")
    .age(28)
    .email("shravan@gmail.com")
    .build();
```

Winner:

```text
Builder
```

---

## Large Configuration Object

Constructor:

```java
new DatabaseConnection(
    host,
    port,
    username,
    password,
    ssl,
    timeout,
    retryCount,
    retryDelay
);
```

Builder:

```java
new DatabaseConnection.Builder()
    .host(host)
    .port(port)
    .ssl(true)
    .timeout(5000)
    .retryCount(3)
    .build();
```

Winner:

```text
Builder
```

---

# Effective Java Recommendation

In
Effective Java

Joshua Bloch provides a practical guideline:

### Few Parameters

Use constructors.

---

### Many Optional Parameters

Use Builder.

---

### Large Complex Objects

Strongly consider Builder.

This advice remains widely accepted across the Java ecosystem.

---

# Practical Decision Framework

## Use Constructor When

* Object has 2–4 fields
* Fields are all mandatory
* Readability remains high
* Validation is simple
* Object is unlikely to evolve

Examples:

```java
Point
Money
Coordinate
Range
```

---

## Use Builder When

* Many fields exist
* Several fields are optional
* Validation is complex
* Immutability is desired
* API evolution is expected

Examples:

```java
User
Order
HttpRequest
DatabaseConfig
CloudResourceConfig
```

---

# Java Records Change the Equation

Java Records reduce boilerplate significantly.

Example:

```java
public record User(
        String firstName,
        String lastName) {
}
```

Usage:

```java
User user =
        new User(
                "Shravan",
                "Patnam");
```

For small immutable objects:

```text
Records often remove the need for Builders.
```

This is an important modern consideration.

---

# Real-World Examples

## Constructor-Friendly

```java
Duration

BigDecimal

LocalDate

Point
```

Small.

Focused.

Simple.

---

## Builder-Friendly

```java
HttpRequest.Builder

WebClient.Builder

AWS SDK Requests

Database Configuration Objects
```

Complex.

Evolving.

Option-rich.

---

# Common Mistake #1

Using Builder for every class.

Bad:

```java
new Point.Builder()
    .x(10)
    .y(20)
    .build();
```

Unnecessary abstraction.

---

# Common Mistake #2

Avoiding Builder because it adds code.

Developers sometimes choose:

```java
20-parameter constructor
```

to reduce implementation effort.

The result is often harder to maintain.

---

# Common Mistake #3

Ignoring Future Growth

Today's object:

```java
name
email
```

Tomorrow:

```java
name
email
phone
address
department
country
preferences
```

Design should consider likely evolution.

---

# Cost-Benefit Analysis

Every Builder introduces cost.

Question:

```text
Does the readability and flexibility gained
justify the additional complexity?
```

For small objects:

```text
Usually no
```

For complex objects:

```text
Usually yes
```

---

# Interview Questions

### When should you use a Builder?

When objects have many fields, optional parameters, complex validation, or immutability requirements.

---

### When should you avoid Builder?

For small, simple objects where constructors remain clear and maintainable.

---

### Why are constructors still useful?

They are simple, concise, and easy to understand for straightforward object creation.

---

### How do Java Records impact Builder usage?

Records reduce boilerplate and often eliminate the need for Builders in simple immutable objects.

---

### What is the biggest advantage of Builder over constructors?

Improved readability and flexibility for complex object creation.

---

# Key Takeaways

* Builder is not automatically better than constructors.
* Constructors excel for small, simple objects.
* Builders excel for complex, evolving objects.
* Builders improve readability and support optional fields.
* Constructors minimize complexity and boilerplate.
* Java Records reduce the need for Builders in many simple cases.
* The right choice depends on object complexity, validation needs, and expected evolution.
* Good engineers choose patterns intentionally rather than applying them everywhere.
