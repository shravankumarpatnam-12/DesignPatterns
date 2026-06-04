# JavaBeans Approach

## Introduction

In the previous chapter, we explored the limitations of telescoping constructors.

As the number of optional fields increased, constructors became difficult to read, maintain, and evolve.

A natural solution emerged:

Instead of passing everything through constructors, create the object first and then set the required values using setter methods.

This approach became known as the **JavaBeans Pattern** and was widely adopted across Java applications, frameworks, and enterprise systems.

At first glance, it appears to solve many of the problems introduced by telescoping constructors.

Let's see why.

---

# The JavaBeans Pattern

Consider the following class.

```java
public class User {

    private String firstName;
    private String lastName;
    private int age;
    private String email;
    private String phoneNumber;

    public User() {
    }

    public void setFirstName(String firstName) {
        this.firstName = firstName;
    }

    public void setLastName(String lastName) {
        this.lastName = lastName;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public void setPhoneNumber(String phoneNumber) {
        this.phoneNumber = phoneNumber;
    }
}
```

Object creation becomes:

```java
User user = new User();

user.setFirstName("Shravan");
user.setLastName("Patnam");
user.setAge(28);
user.setEmail("shravan@gmail.com");
user.setPhoneNumber("9999999999");
```

Compared to a long constructor, this code is significantly easier to read.

---

# Why Developers Liked It

The JavaBeans pattern solved several problems introduced by telescoping constructors.

### Improved Readability

Instead of:

```java
new User(
    "Shravan",
    "Patnam",
    28,
    "shravan@gmail.com",
    "9999999999"
);
```

We now have:

```java
user.setFirstName("Shravan");
user.setLastName("Patnam");
user.setAge(28);
```

The code clearly communicates intent.

---

### No Constructor Explosion

Previously:

```java
User(
    String firstName,
    String lastName)

User(
    String firstName,
    String lastName,
    int age)

User(
    String firstName,
    String lastName,
    int age,
    String email)
```

With JavaBeans:

```java
User user = new User();
```

Only one constructor is required.

---

### Flexible Object Creation

Developers can set only the fields they need.

```java
User user = new User();

user.setFirstName("Shravan");
user.setLastName("Patnam");
```

Optional fields can be ignored.

This provides significant flexibility.

---

# The Hidden Problem

Although JavaBeans improved readability, it introduced a more serious issue.

Consider:

```java
User user = new User();
```

At this point:

```java
firstName = null
lastName = null
email = null
phoneNumber = null
age = 0
```

The object already exists.

But it is incomplete.

---

# Partially Constructed Objects

Imagine:

```java
User user = new User();

user.setFirstName("Shravan");

// Application crashes here
```

The object now exists in memory with an invalid state.

```java
firstName = "Shravan"
lastName = null
```

The system cannot guarantee that all required fields are populated.

This is known as a **Partially Constructed Object**.

---

# Violating Object Invariants

An invariant is a rule that must always be true.

For example:

```text
A User must always have:
- firstName
- lastName
```

With JavaBeans:

```java
User user = new User();
```

The invariant is already violated.

Nothing prevents developers from creating invalid objects.

---

# Validation Becomes Difficult

Suppose:

```java
age >= 18
```

is a business rule.

Where should validation happen?

Setter?

```java
public void setAge(int age) {
    if(age < 18) {
        throw new IllegalArgumentException();
    }
}
```

Constructor?

```java
No constructor exists.
```

Final validation method?

```java
user.validate();
```

Now developers must remember to call validation manually.

Validation logic becomes scattered across the class.

---

# Mutable State

Consider:

```java
User user = new User();

user.setFirstName("Shravan");
```

Later:

```java
user.setFirstName("John");
```

And later:

```java
user.setFirstName("Alex");
```

The object continuously changes.

This is called **Mutability**.

---

# Why Mutability Can Be Dangerous

Mutable objects are harder to reason about.

Consider:

```java
processUser(user);
```

Question:

Will the object remain the same after processing?

Maybe.

Maybe not.

The answer depends on who has access to the object.

Mutable state often leads to:

* Unexpected behavior
* Difficult debugging
* Hidden side effects
* Concurrency problems

---

# Thread Safety Issues

Imagine two threads.

Thread A:

```java
user.setEmail("a@gmail.com");
```

Thread B:

```java
user.setEmail("b@gmail.com");
```

Which value wins?

The answer depends on execution timing.

Without synchronization, behavior becomes unpredictable.

JavaBeans objects are typically not thread-safe.

---

# Real-World Example

Imagine an order in an e-commerce application.

```java
Order order = new Order();
```

Required fields:

```text
customerId
shippingAddress
items
```

A developer forgets:

```java
order.setShippingAddress(...);
```

The order object exists.

The system may proceed.

The error is discovered much later.

Possibly in production.

The root cause becomes difficult to trace.

---

# The Core Trade-Off

JavaBeans solved:

✓ Constructor explosion

✓ Readability issues

✓ Optional parameters

But introduced:

✗ Partially constructed objects

✗ Weak validation guarantees

✗ Mutable state

✗ Broken invariants

✗ Thread-safety concerns

---

# Why This Matters

An object should ideally be:

* Valid when created
* Complete when created
* Safe to use after creation

JavaBeans cannot guarantee these properties.

The pattern prioritizes flexibility over correctness.

For many enterprise systems, that trade-off is unacceptable.

---

# Key Takeaways

* JavaBeans uses a no-argument constructor and setter methods.
* It improves readability compared to telescoping constructors.
* It eliminates constructor explosion.
* Objects can exist in an incomplete state.
* Validation becomes harder to enforce.
* Mutable state introduces maintenance and concurrency challenges.
* JavaBeans solves some problems but creates entirely new ones.
