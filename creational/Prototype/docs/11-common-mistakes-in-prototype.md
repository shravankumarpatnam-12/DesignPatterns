# Chapter 11: Lombok @Builder Deep Dive

## Introduction

By this point, we have implemented Builder manually and explored advanced topics such as:

* Validation
* Immutability
* Defensive Copying
* Inheritance
* Recursive Generics

While these implementations are educational, writing Builders manually can become repetitive.

Consider a class with:

```text
20 fields
20 builder methods
constructor
build()
validation
getters
```

A significant amount of code exists solely to support object construction.

To reduce boilerplate, many Java projects use Lombok.

One of Lombok's most popular features is:

```java
@Builder
```

At first glance, it appears magical.

However, understanding what Lombok generates is essential before using it in production systems.

---

# What Is Lombok?

[Project Lombok](https://projectlombok.org?utm_source=chatgpt.com)

Lombok is a Java library that generates code during compilation through annotation processing.

Instead of writing:

```java
constructor
getters
setters
equals()
hashCode()
toString()
builder
```

developers can use annotations.

Example:

```java
@Getter
@Builder
```

and Lombok generates the required code automatically.

---

# Traditional Builder

Without Lombok:

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

        public Builder firstName(
                String firstName) {

            this.firstName = firstName;
            return this;
        }

        public Builder lastName(
                String lastName) {

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

This is straightforward but repetitive.

---

# Lombok Builder

The same class can be written as:

```java
import lombok.Builder;

@Builder
public class User {

    private String firstName;
    private String lastName;
    private int age;
}
```

Usage:

```java
User user =
    User.builder()
        .firstName("Shravan")
        .lastName("Patnam")
        .age(28)
        .build();
```

Much less code.

---

# What Lombok Actually Generates

Many developers incorrectly assume:

```text
@Builder
=
Magic
```

It is not magic.

Lombok generates code similar to:

```java
public static UserBuilder builder() {
    return new UserBuilder();
}
```

Builder class:

```java
public static class UserBuilder {

    private String firstName;
    private String lastName;
    private int age;

    public UserBuilder firstName(
            String firstName) {

        this.firstName = firstName;
        return this;
    }

    public UserBuilder lastName(
            String lastName) {

        this.lastName = lastName;
        return this;
    }

    public UserBuilder age(int age) {

        this.age = age;
        return this;
    }

    public User build() {

        return new User(
                firstName,
                lastName,
                age);
    }
}
```

Understanding generated code is crucial for debugging and code reviews.

---

# Builder on Constructors

Many teams prefer:

```java
@Builder
```

on constructors rather than classes.

Example:

```java
public class User {

    private final String firstName;
    private final String lastName;
    private final int age;

    @Builder
    public User(
            String firstName,
            String lastName,
            int age) {

        this.firstName = firstName;
        this.lastName = lastName;
        this.age = age;
    }
}
```

Reason:

```text
Explicit construction rules
```

remain visible.

---

# Builder on Static Factory Methods

Lombok can also generate builders for factory methods.

Example:

```java
@Builder
public static User createAdmin(
        String firstName,
        String lastName) {

    return new User(
            firstName,
            lastName,
            0);
}
```

Usage:

```java
User admin =
    User.builder()
        .firstName("Shravan")
        .lastName("Patnam")
        .build();
```

Useful when construction logic belongs inside a factory.

---

# @Builder.Default

One of the most misunderstood Lombok features.

Consider:

```java
@Builder
public class User {

    private String role = "USER";
}
```

Usage:

```java
User user =
        User.builder()
            .build();
```

Expected:

```text
role = USER
```

Actual:

```text
role = null
```

Why?

Because Lombok Builder bypasses field initialization.

---

# Fixing It with @Builder.Default

```java
@Builder
public class User {

    @Builder.Default
    private String role = "USER";
}
```

Now:

```java
User.builder()
    .build();
```

produces:

```text
role = USER
```

Always use:

```java
@Builder.Default
```

when default values are required.

---

# @Singular

Suppose:

```java
private List<String> skills;
```

Without:

```java
@Singular
```

usage becomes:

```java
User.builder()
    .skills(
        List.of(
            "Java",
            "Spring"))
    .build();
```

---

Using:

```java
@Singular
private List<String> skills;
```

Lombok generates:

```java
skill(String skill)
```

and

```java
skills(Collection<String>)
```

Usage:

```java
User.builder()
    .skill("Java")
    .skill("Spring")
    .skill("Kafka")
    .build();
```

This greatly improves API usability.

---

# Generated Code for @Singular

Conceptually:

```java
private List<String> skills =
        new ArrayList<>();
```

Builder method:

```java
public UserBuilder skill(
        String skill) {

    this.skills.add(skill);

    return this;
}
```

Build method:

```java
return Collections.unmodifiableList(
        skills);
```

Lombok automatically creates immutable collections.

---

# toBuilder()

Sometimes we want to create a modified copy of an immutable object.

Example:

```java
@Builder(toBuilder = true)
public class User {
}
```

Usage:

```java
User updated =
    existingUser
        .toBuilder()
        .age(30)
        .build();
```

This pattern is common in:

* Immutable domain models
* Event sourcing
* Functional programming styles

---

# Generated Flow

Original:

```java
User user =
    User.builder()
        .firstName("Shravan")
        .age(28)
        .build();
```

Copy:

```java
User updated =
    user.toBuilder()
        .age(30)
        .build();
```

The original object remains unchanged.

---

# @SuperBuilder

One limitation of:

```java
@Builder
```

is inheritance.

Recall the inheritance problems from previous chapters.

Lombok provides:

```java
@SuperBuilder
```

Example:

Parent:

```java
@SuperBuilder
public class Person {
}
```

Child:

```java
@SuperBuilder
public class Employee
        extends Person {
}
```

Usage:

```java
Employee.builder()
        .firstName("Shravan")
        .employeeId("EMP-001")
        .build();
```

Lombok internally uses recursive generics to make this work.

---

# Why @Builder Is Popular

Benefits:

```text
Less Boilerplate

Cleaner Classes

Faster Development

Consistent APIs

Reduced Maintenance
```

For DTOs and configuration objects, it can dramatically reduce code volume.

---

# Production Concerns

Before adopting Lombok, consider the trade-offs.

---

# Hidden Code

This class:

```java
@Builder
public class User {
}
```

looks tiny.

However, the generated code may be hundreds of lines long.

New developers often struggle because they cannot immediately see what exists.

---

# IDE Dependency

Proper Lombok support requires IDE plugins.

Without Lombok support:

```text
False compilation errors

Missing methods

Poor navigation
```

may occur.

---

# Debugging Complexity

When debugging:

```java
User.builder()
```

the actual generated implementation is hidden.

Developers must understand Lombok internals.

---

# Code Review Challenges

Reviewers see:

```java
@Builder
```

but not the generated code.

Important questions become harder to answer:

```text
How is build() implemented?

What collections are copied?

Are defaults applied?
```

Knowledge of Lombok behavior becomes essential.

---

# Enterprise Guideline

A common rule:

Use Lombok for:

```text
DTOs

Configuration Objects

Request Models

Response Models
```

---

Be more cautious for:

```text
Core Domain Objects

Complex Validation

Security-Sensitive Models

Rich Business Entities
```

because explicit code is sometimes easier to understand and maintain.

---

# Common Mistake #1

Assuming:

```java
@Builder
```

automatically provides validation.

It does not.

You still need:

```java
constructor validation

factory validation

domain validation
```

where appropriate.

---

# Common Mistake #2

Forgetting:

```java
@Builder.Default
```

and expecting field initializers to work.

---

# Common Mistake #3

Using:

```java
@Builder
```

for every class.

Sometimes:

```java
record User(
        String firstName,
        String lastName) {
}
```

is simpler.

---

# Common Mistake #4

Not understanding generated code.

Developers should be able to manually implement Builder before relying on Lombok.

---

# Interview Questions

### What does Lombok @Builder generate?

A builder class, builder methods, a build() method, and a static builder() factory method.

---

### Does @Builder perform validation?

No.

Validation must be implemented separately.

---

### Why use @Builder.Default?

Because field initializers are ignored by Lombok Builders unless explicitly marked.

---

### What does @Singular do?

It generates builder methods for adding individual collection elements.

---

### What problem does @SuperBuilder solve?

Builder inheritance and fluent chaining across class hierarchies.

---

### Should developers understand manual Builder implementations before Lombok?

Yes.

Lombok generates code; understanding that code is essential for debugging and maintenance.

---

# Key Takeaways

* Lombok reduces Builder boilerplate through annotation processing.
* @Builder generates code similar to a manually written Builder.
* Understanding generated code is essential.
* @Builder.Default preserves default field values.
* @Singular improves collection handling.
* toBuilder() supports immutable object updates.
* @SuperBuilder solves inheritance-related Builder issues.
* Lombok improves productivity but introduces hidden complexity.
* Good engineers understand the generated code, not just the annotation.
