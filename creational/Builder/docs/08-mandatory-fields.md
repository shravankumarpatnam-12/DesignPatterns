# Mandatory Fields

## Introduction

In the previous chapter, we used validation inside `build()` to prevent invalid objects from being created.

Example:

```java
User user =
    new User.Builder()
        .age(28)
        .build();
```

Result:

```text
IllegalStateException:
firstName is required
```

This works.

However, there is a problem.

The error is detected only when:

```java
build()
```

is executed.

In other words:

```text
Error Detection Time
=
Runtime
```

Can we do better?

Ideally, invalid object creation should be prevented by the compiler before the application even starts.

This chapter explores different approaches for enforcing mandatory fields and gradually moves toward compile-time guarantees.

---

# Required vs Optional Fields

Consider a User.

```java
public class User {

    private final String firstName;
    private final String lastName;
    private final int age;
    private final String email;
}
```

Business requirements:

Mandatory:

```text
firstName
lastName
```

Optional:

```text
age
email
```

Not all fields should be treated equally.

A good Builder should clearly distinguish between required and optional information.

---

# Approach 1: Runtime Validation

The most common approach:

```java
public User build() {

    if(firstName == null) {
        throw new IllegalStateException(
            "firstName required"
        );
    }

    if(lastName == null) {
        throw new IllegalStateException(
            "lastName required"
        );
    }

    return new User(this);
}
```

Usage:

```java
User user =
    new User.Builder()
        .build();
```

Result:

```text
Runtime Exception
```

This protects correctness.

However, the mistake is discovered late.

---

# The Problem with Runtime Validation

Imagine a large application.

```java
User user =
    createUserFromRequest();
```

Hours later:

```java
user.build();
```

Exception occurs.

The developer now needs to trace:

```text
Where was the builder created?

Who forgot to populate the field?

Which code path caused this?
```

The earlier an error is detected, the cheaper it is to fix.

---

# Approach 2: Builder Constructor with Required Fields

Instead of validating later:

Require mandatory values immediately.

Example:

```java
public static class Builder {

    private final String firstName;
    private final String lastName;

    private int age;
    private String email;

    public Builder(
            String firstName,
            String lastName) {

        this.firstName = firstName;
        this.lastName = lastName;
    }
}
```

Usage:

```java
User user =
    new User.Builder(
            "Shravan",
            "Patnam")
        .age(28)
        .build();
```

Now:

```java
new User.Builder();
```

fails to compile.

The compiler enforces required values.

---

# Why This Is Better

Without required constructor:

```java
new User.Builder();
```

Possible.

---

With required constructor:

```java
new User.Builder(
        "Shravan",
        "Patnam");
```

Mandatory fields become impossible to forget.

The compiler helps enforce correctness.

---

# Remaining Limitation

Consider:

```java
new User.Builder(
        null,
        null);
```

The code still compiles.

Therefore:

```text
Compile-Time Protection
≠
Business Validation
```

We still need validation.

```java
if(firstName == null) {
    throw ...
}
```

inside `build()`.

---

# Combining Constructor Enforcement and Validation

A common production approach:

```java
public User build() {

    if(firstName.isBlank()) {
        throw new IllegalStateException();
    }

    if(lastName.isBlank()) {
        throw new IllegalStateException();
    }

    return new User(this);
}
```

Benefits:

```text
Compiler ensures presence

Builder ensures correctness
```

Both layers work together.

---

# The Step Builder Problem

Suppose a business rule says:

```text
firstName required
lastName required
email required
```

We want this to be illegal:

```java
new User.Builder(
        "Shravan",
        "Patnam")
    .build();
```

Ideally:

```text
Compiler Error
```

How can we force users to supply email before build() becomes available?

This leads to a more advanced technique.

---

# Step Builder Pattern

The Step Builder Pattern guides developers through a predefined construction sequence.

Instead of:

```java
builder
    .firstName(...)
    .lastName(...)
    .email(...)
    .build();
```

the API enforces:

```java
builder
    .firstName(...)
    .lastName(...)
    .email(...)
    .build();
```

in the correct order.

Missing a required step becomes impossible.

---

# Basic Step Builder Structure

Define stages.

```java
public interface FirstNameStep {
    LastNameStep firstName(
            String firstName);
}

public interface LastNameStep {
    EmailStep lastName(
            String lastName);
}

public interface EmailStep {
    BuildStep email(
            String email);
}

public interface BuildStep {
    User build();
}
```

Each step exposes only the next valid operation.

---

# Usage

```java
User user =
    User.builder()
        .firstName("Shravan")
        .lastName("Patnam")
        .email("shravan@gmail.com")
        .build();
```

This works.

---

Attempt:

```java
User.builder()
    .firstName("Shravan")
    .build();
```

Compiler error.

Because:

```text
build()
```

is not available yet.

---

# Advantages of Step Builder

### Strong Compile-Time Guarantees

Required fields cannot be skipped.

---

### Better API Guidance

IDE autocomplete naturally guides users.

---

### Self-Documenting Construction Flow

Developers understand the required sequence immediately.

---

### Reduced Runtime Errors

Many mistakes are eliminated before execution.

---

# Drawbacks of Step Builder

Step Builders introduce complexity.

Simple Builder:

```java
Builder
```

One class.

---

Step Builder:

```java
Multiple interfaces
Multiple implementations
Additional boilerplate
```

The codebase becomes larger.

---

# When Step Builder Is Worth It

Good candidates:

```text
Financial systems

Healthcare systems

Security-sensitive domains

Complex business workflows

Public SDKs
```

---

Poor candidates:

```text
Simple DTOs

Internal data classes

Small utility objects
```

The complexity often outweighs the benefits.

---

# Real-World Examples

Many enterprise SDKs enforce mandatory information before requests can be executed.

Examples include:

* Cloud SDKs
* Security frameworks
* Payment gateways
* Infrastructure configuration APIs

These systems prefer compile-time safety because runtime mistakes can be expensive.

---

# Builder API Design Guidelines

A practical rule:

### Few Required Fields

Use:

```java
Builder constructor
```

Example:

```java
new Builder(
        firstName,
        lastName);
```

---

### Many Required Fields

Consider:

```java
Step Builder
```

---

### Complex Domain Rules

Use:

```java
Builder
+
Validation
```

because not every rule can be enforced by the compiler.

---

# Common Mistake

Developers often believe:

```text
Step Builder eliminates validation
```

False.

Example:

```java
.firstName("")
```

still compiles.

The compiler can verify presence.

It cannot verify business correctness.

Validation remains necessary.

---

# Runtime vs Compile-Time Safety

Runtime Validation:

```text
Detects errors while running
```

Example:

```java
build()
```

throws exception.

---

Compile-Time Safety:

```text
Detects errors before execution
```

Example:

```java
Missing required step
```

causes compilation failure.

Compile-time protection is generally preferable whenever practical.

---

# Interview Questions

### Why use mandatory fields in a Builder?

To prevent creation of incomplete objects.

---

### What is the limitation of runtime validation?

Errors are detected only during execution.

---

### What advantage does constructor-enforced Builder provide?

The compiler ensures required values are supplied.

---

### What is the Step Builder Pattern?

A Builder variation that enforces required construction steps through interfaces and type safety.

---

### Does Step Builder remove the need for validation?

No.

It ensures required information is present but cannot verify all business rules.

---

# Key Takeaways

* Not all fields should be treated equally.
* Mandatory fields should be enforced whenever possible.
* Runtime validation protects correctness.
* Constructor-enforced Builders provide stronger guarantees.
* Step Builders move error detection from runtime to compile time.
* Compile-time safety improves developer experience.
* Validation is still required even when using Step Builders.
* Choosing the right approach depends on complexity and business requirements.
