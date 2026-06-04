# Building Immutable Objects

## Introduction

Up to this point, we have learned:

* Why constructors become difficult to maintain
* Why JavaBeans introduce mutability problems
* How Builder improves object creation
* How Fluent Interfaces make APIs expressive

However, the biggest benefit of Builder is not readability.

The biggest benefit is that it enables the creation of **immutable objects**.

In modern Java applications, Builder and immutability are often used together.

Understanding why requires understanding what immutability actually means.

---

# What Is an Immutable Object?

An immutable object is an object whose state cannot change after it is created.

Example:

```java
String name = "Shravan";
```

After creation:

```java
name = "John";
```

The original String object is not modified.

A new String object is created.

This is why String is considered immutable.

---

# Characteristics of Immutable Objects

An immutable class typically follows these rules:

### Rule 1: Fields Are Final

```java
private final String firstName;
private final String lastName;
```

---

### Rule 2: No Setters

Bad:

```java
public void setFirstName(String firstName) {
    this.firstName = firstName;
}
```

Good:

```java
No setters at all
```

---

### Rule 3: State Is Initialized During Construction

```java
public User(String firstName,
            String lastName) {

    this.firstName = firstName;
    this.lastName = lastName;
}
```

After construction, values cannot change.

---

# A Mutable User Class

Consider:

```java
public class User {

    private String firstName;
    private String lastName;

    public void setFirstName(String firstName) {
        this.firstName = firstName;
    }

    public void setLastName(String lastName) {
        this.lastName = lastName;
    }
}
```

Usage:

```java
User user = new User();

user.setFirstName("Shravan");
user.setLastName("Patnam");

user.setFirstName("John");
```

The object changes over time.

This is mutability.

---

# An Immutable User Class

Now consider:

```java
public class User {

    private final String firstName;
    private final String lastName;

    public User(String firstName,
                String lastName) {

        this.firstName = firstName;
        this.lastName = lastName;
    }

    public String getFirstName() {
        return firstName;
    }

    public String getLastName() {
        return lastName;
    }
}
```

Usage:

```java
User user =
        new User(
                "Shravan",
                "Patnam");
```

After creation:

```java
user.setFirstName("John");
```

Impossible.

The object never changes.

---

# Why Immutability Matters

Many software bugs come from changing state.

Consider:

```java
processUser(user);
```

Question:

Will the object still contain the same values after processing?

With mutable objects:

```text
Maybe
```

With immutable objects:

```text
Definitely yes
```

The state cannot change.

This dramatically reduces complexity.

---

# Immutability Improves Predictability

Suppose:

```java
User user =
        new User(
                "Shravan",
                "Patnam");
```

Five minutes later:

```java
System.out.println(
        user.getFirstName());
```

Output:

```text
Shravan
```

Always.

The object behaves consistently throughout its lifetime.

---

# Why JavaBeans Struggle Here

Recall the JavaBeans pattern.

```java
User user = new User();

user.setFirstName("Shravan");
user.setLastName("Patnam");
```

JavaBeans depend on setters.

Setters imply mutability.

As a result:

```text
JavaBeans and Immutability
```

are fundamentally at odds with each other.

---

# Why Builder Fits Perfectly

A Builder is mutable during construction.

The final object is immutable after construction.

Example:

```java
User user =
    new User.Builder()
        .firstName("Shravan")
        .lastName("Patnam")
        .build();
```

The builder changes state.

```java
builder.firstName("Shravan");
builder.lastName("Patnam");
```

But once:

```java
build()
```

is called, the resulting object becomes immutable.

This separation is extremely powerful.

---

# Typical Immutable Builder Implementation

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

Notice:

```java
private final String firstName;
```

The object becomes immutable immediately after creation.

---

# Thread Safety Benefits

Imagine:

```java
User user =
        new User.Builder()
            .firstName("Shravan")
            .build();
```

Two threads read the object.

Thread A:

```java
user.getFirstName();
```

Thread B:

```java
user.getFirstName();
```

No synchronization required.

No locks required.

No race conditions.

Because the state never changes.

---

# Why Immutable Objects Are Naturally Thread-Safe

Race conditions occur when:

```text
Multiple threads
        +
Shared mutable state
```

exist together.

Remove mutable state:

```text
Multiple threads
        +
Immutable state
```

No race condition.

This is one reason immutable objects are heavily used in high-concurrency systems.

---

# Real-World Examples

## String

```java
String name = "Java";
```

Immutable.

---

## LocalDate

```java
LocalDate date =
        LocalDate.now();
```

Immutable.

---

## BigDecimal

```java
BigDecimal amount =
        new BigDecimal("100");
```

Immutable.

Operations return new objects.

---

## HTTP Requests

Many modern frameworks use immutable request objects.

Example:

```java
HttpRequest request =
        HttpRequest.newBuilder()
                .uri(uri)
                .build();
```

After creation:

```text
Request configuration cannot change
```

This reduces bugs significantly.

---

# Advantages of Immutable Objects

### Easier Reasoning

State never changes.

---

### Better Thread Safety

No shared mutable state.

---

### Easier Testing

Objects behave consistently.

---

### Fewer Side Effects

Methods cannot unexpectedly modify data.

---

### Improved Reliability

Valid state is preserved.

---

# Potential Drawbacks

Immutability is not free.

### More Object Creation

Instead of modifying objects:

```java
user.setAge(30);
```

you create new objects.

```java
User updatedUser =
        new User.Builder()
            .firstName(user.getFirstName())
            .age(30)
            .build();
```

---

### More Boilerplate

Without Lombok or records:

```java
final fields
constructor
getters
builder
```

can create additional code.

---

### Large Object Graphs

Deeply nested immutable objects may require more copying.

Modern JVMs handle this efficiently in most business applications.

---

# Builder and Immutability: The Perfect Partnership

Builder solves object construction.

Immutability solves object safety.

Together they provide:

```text
Readable Construction
            +
Safe Objects
            +
Strong Validation
            +
Thread Safety
```

This combination explains why Builder has become the preferred approach for many modern Java APIs.

---

# Common Mistake

Developers often write:

```java
User user =
        new User.Builder()
            .firstName("Shravan")
            .build();
```

but still include setters.

```java
public void setFirstName(String firstName) {
    this.firstName = firstName;
}
```

This defeats the purpose.

A Builder does not automatically make a class immutable.

Immutability requires:

* Final fields
* No setters
* Proper encapsulation

---

# Interview Questions

### Why are Builder and Immutability often used together?

Builder handles complex object creation while allowing the final object to remain immutable.

---

### Are immutable objects thread-safe?

Generally yes, because their state cannot change after creation.

---

### Does Builder automatically make a class immutable?

No.

The class must be explicitly designed to be immutable.

---

### What is the biggest advantage of immutable objects?

Predictability and thread safety.

---

# Key Takeaways

* Immutable objects cannot change after creation.
* Immutability requires final fields and no setters.
* Builder is commonly used to create immutable objects.
* Immutable objects are easier to reason about and test.
* Immutable objects greatly simplify concurrency.
* Builder and immutability complement each other extremely well.
* A Builder does not automatically make a class immutable; the class design must enforce immutability.
