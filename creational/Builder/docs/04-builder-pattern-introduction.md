# Builder Pattern Introduction

## Introduction

So far, we have explored two common approaches for object creation.

### Constructors

Constructors provide a straightforward way to create objects.

However, as the number of fields increases:

* Readability decreases
* Constructor overloads grow
* Parameter ordering becomes error-prone

### JavaBeans

JavaBeans improve readability through setter methods.

However, they introduce:

* Partially constructed objects
* Mutable state
* Weak validation guarantees
* Thread-safety concerns

We need an approach that combines the strengths of both while avoiding their weaknesses.

This is where the Builder Pattern comes in.

---

# What Is the Builder Pattern?

The Builder Pattern is a creational design pattern that separates:

```text
Object Construction
        from
Object Representation
```

Instead of creating an object directly using a large constructor, we gradually construct it through a builder.

Once all required information is provided, the builder creates the final object.

---

# Builder Pattern Definition

According to the Gang of Four (GoF):

> Separate the construction of a complex object from its representation so that the same construction process can create different representations.

Although the original definition is broader, modern Java applications primarily use Builder to create complex objects in a readable and maintainable way.

---

# The Core Idea

Instead of this:

```java
User user = new User(
        "Shravan",
        "Patnam",
        28,
        "shravan@gmail.com",
        "9999999999");
```

We write:

```java
User user = new User.Builder()
        .firstName("Shravan")
        .lastName("Patnam")
        .age(28)
        .email("shravan@gmail.com")
        .phoneNumber("9999999999")
        .build();
```

Notice the difference.

Each value is associated with its meaning.

The code becomes self-documenting.

---

# First Impression

Look at the following code.

```java
User user = new User.Builder()
        .firstName("Shravan")
        .lastName("Patnam")
        .email("shravan@gmail.com")
        .build();
```

Even without seeing the implementation, we can immediately understand:

* Which fields are being set
* Which fields are optional
* What the object represents

This is one of the biggest advantages of the Builder Pattern.

---

# Basic Structure

A typical Builder implementation contains two classes:

```text
User
└── Builder
```

The builder collects data.

The target object receives that data during construction.

---

# Simple Implementation

## Product Class

```java
public class User {

    private final String firstName;
    private final String lastName;
    private final int age;

    private User(Builder builder) {
        this.firstName = builder.firstName;
        this.lastName = builder.lastName;
        this.age = builder.age;
    }

    public static class Builder {

        private String firstName;
        private String lastName;
        private int age;

        public Builder firstName(String firstName) {
            this.firstName = firstName;
            return this;
        }

        public Builder lastName(String lastName) {
            this.lastName = lastName;
            return this;
        }

        public Builder age(int age) {
            this.age = age;
            return this;
        }

        public User build() {
            return new User(this);
        }
    }
}
```

Usage:

```java
User user = new User.Builder()
        .firstName("Shravan")
        .lastName("Patnam")
        .age(28)
        .build();
```

---

# Understanding the Flow

Step 1

Create a builder.

```java
User.Builder builder =
        new User.Builder();
```

Builder state:

```text
firstName = null
lastName = null
age = 0
```

---

Step 2

Populate values.

```java
builder.firstName("Shravan");
builder.lastName("Patnam");
builder.age(28);
```

Builder state:

```text
firstName = Shravan
lastName = Patnam
age = 28
```

---

Step 3

Create final object.

```java
User user = builder.build();
```

Builder transfers its state to the User object.

---

# Why This Solves Previous Problems

## Readability

Before:

```java
new User(
    "Shravan",
    "Patnam",
    28,
    "shravan@gmail.com"
);
```

After:

```java
new User.Builder()
    .firstName("Shravan")
    .lastName("Patnam")
    .age(28)
    .email("shravan@gmail.com")
    .build();
```

Intent becomes explicit.

---

## No Constructor Explosion

Before:

```java
User(...)
User(..., ...)
User(..., ..., ...)
User(..., ..., ..., ...)
```

With Builder:

```java
User.Builder()
```

Only one construction mechanism is needed.

---

## Optional Fields Become Natural

Suppose email is optional.

Simply omit it.

```java
User user = new User.Builder()
        .firstName("Shravan")
        .lastName("Patnam")
        .build();
```

No null placeholders.

No additional constructors.

---

## Centralized Object Creation

All creation logic can be placed inside:

```java
build()
```

This becomes extremely valuable later when we add:

* Validation
* Business rules
* Defensive copying
* Immutable objects

---

# Participants in the Builder Pattern

The original GoF Builder Pattern contains four participants.

## Product

The object being created.

```java
User
```

---

## Builder

Defines construction steps.

```java
Builder
```

---

## Concrete Builder

Actual implementation of the builder.

```java
User.Builder
```

---

## Director

Controls construction order.

```java
director.buildPremiumUser();
director.buildGuestUser();
```

Modern Java applications often omit the Director because fluent builders are usually sufficient.

---

# Real-World Analogy

Imagine ordering a custom laptop.

You don't receive the laptop immediately.

Instead, you specify:

```text
RAM: 32 GB
CPU: Intel i9
Storage: 1 TB SSD
Operating System: Linux
```

The manufacturer collects all specifications.

Only after everything is finalized does assembly begin.

The Builder Pattern follows the same idea.

The builder collects configuration first.

The final object is created only when construction is complete.

---

# Why Java Developers Love Builder

Builder provides:

✓ Readability

✓ Flexibility

✓ Maintainability

✓ Support for optional fields

✓ Better validation opportunities

✓ Better support for immutable objects

✓ Easier API evolution

These benefits explain why Builder has become one of the most widely used patterns in modern Java development.

---

# Common Misconception

Many developers think:

> Builder Pattern exists only to avoid large constructors.

This is incomplete.

Builder is also about:

* Expressive APIs
* Safer object creation
* Immutability
* Validation
* Long-term maintainability

Avoiding large constructors is merely the starting point.

---

# Key Takeaways

* Builder is a creational design pattern.
* It separates object construction from object representation.
* Builders improve readability and flexibility.
* Optional parameters become easy to handle.
* Constructor explosion disappears.
* Object creation becomes centralized.
* Builder prepares the foundation for validation and immutability.
