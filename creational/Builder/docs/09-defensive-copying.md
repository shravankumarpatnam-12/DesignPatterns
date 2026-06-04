# Defensive Copying

## Introduction

In the previous chapters, we built immutable objects using the Builder Pattern.

Consider the following class.

```java
public class User {

    private final String firstName;
    private final String lastName;

    public User(Builder builder) {
        this.firstName = builder.firstName;
        this.lastName = builder.lastName;
    }
}
```

This appears immutable.

Fields are:

```java
final
```

There are:

```java
No setters
```

The object looks safe.

However, many supposedly immutable classes are not actually immutable.

The problem usually appears when objects contain:

* Collections
* Arrays
* Maps
* Mutable objects

This chapter explores one of the most common immutability bugs in Java and how Defensive Copying solves it.

---

# The Hidden Problem

Consider a User with skills.

```java
public class User {

    private final List<String> skills;

    private User(Builder builder) {
        this.skills = builder.skills;
    }

    public List<String> getSkills() {
        return skills;
    }
}
```

Builder:

```java
public static class Builder {

    private List<String> skills;

    public Builder skills(
            List<String> skills) {

        this.skills = skills;
        return this;
    }
}
```

Looks reasonable.

Let's test it.

---

# External Mutation

Create a list.

```java
List<String> skills =
        new ArrayList<>();

skills.add("Java");
```

Build the user.

```java
User user =
    new User.Builder()
        .skills(skills)
        .build();
```

Current state:

```text
[Java]
```

Now modify the original list.

```java
skills.add("Spring");
```

What does the User contain?

```text
[Java, Spring]
```

The User object changed.

Without any setter.

Without any update method.

Without rebuilding the object.

---

# Why Did This Happen?

Consider:

```java
this.skills = builder.skills;
```

We copied the reference.

Not the data.

Memory representation:

```text
skills  ----------+
                  |
                  v
             [Java]

user.skills ------+
```

Both references point to the same object.

When one changes:

```text
Both observe the change
```

This is known as **Reference Sharing**.

---

# The Immutability Illusion

Developers often think:

```java
private final List<String> skills;
```

means immutable.

It does not.

`final` means:

```text
Reference cannot change
```

It does not mean:

```text
Object cannot change
```

Example:

```java
final List<String> skills =
        new ArrayList<>();
```

Still legal:

```java
skills.add("Java");
```

The reference remains the same.

The object changes.

---

# What Is Defensive Copying?

Defensive Copying means creating a new copy of incoming mutable data rather than storing the original reference.

Instead of:

```java
this.skills = builder.skills;
```

Use:

```java
this.skills =
        List.copyOf(builder.skills);
```

Now a new collection is created.

The User owns its own data.

---

# Fixing the Constructor

Bad:

```java
private User(Builder builder) {
    this.skills = builder.skills;
}
```

Good:

```java
private User(Builder builder) {
    this.skills =
        List.copyOf(builder.skills);
}
```

Now:

```java
skills.add("Spring");
```

does not affect:

```java
user.getSkills();
```

The User remains immutable.

---

# Protecting Getters

Consider:

```java
public List<String> getSkills() {
    return skills;
}
```

Even with constructor copying:

```java
user.getSkills().add("Kafka");
```

may still modify internal state if the collection itself is mutable.

The getter can leak internal data.

This is called:

```text
Representation Exposure
```

---

# Safe Getter Approaches

### Approach 1: Unmodifiable Collection

```java
return Collections.unmodifiableList(
        skills);
```

Attempts to modify:

```java
user.getSkills().add("Kafka");
```

Result:

```text
UnsupportedOperationException
```

---

### Approach 2: Return Copy

```java
return List.copyOf(skills);
```

Every caller receives a separate immutable copy.

This is often preferred in immutable designs.

---

# Builder Side Defensive Copying

Sometimes copying should happen even earlier.

Builder:

```java
public Builder skills(
        List<String> skills) {

    this.skills =
        List.copyOf(skills);

    return this;
}
```

Benefits:

```text
Builder owns data immediately
```

External modifications become irrelevant.

---

# Arrays Are Even Worse

Arrays are mutable by design.

Example:

```java
private final String[] skills;
```

Builder:

```java
this.skills = builder.skills;
```

Problem:

```java
builderSkills[0] = "Hacking";
```

The object's state changes.

---

# Defensive Copying for Arrays

Use:

```java
this.skills =
        Arrays.copyOf(
            builder.skills,
            builder.skills.length
        );
```

Now the object owns its own array.

---

# Deep Copy vs Shallow Copy

Understanding the difference is important.

Suppose:

```java
List<Address> addresses;
```

Where:

```java
Address
```

is mutable.

---

# Shallow Copy

```java
List.copyOf(addresses);
```

Copies the list structure.

But elements remain shared.

```text
New List
   |
   +----> Address
```

The Address object is still shared.

---

# Deep Copy

```java
addresses.stream()
         .map(Address::copy)
         .toList();
```

Both:

```text
List
Address Objects
```

are copied.

Changes to original objects cannot affect the copy.

---

# Real-World Example

Consider an Order.

```java
Order order =
    new Order.Builder()
        .items(items)
        .build();
```

Without defensive copying:

```java
items.clear();
```

might unexpectedly modify the order.

This can lead to:

* Incorrect invoices
* Missing products
* Data corruption

A defensive copy prevents such failures.

---

# Modern Java Recommendation

For collections:

```java
List.copyOf(...)
Set.copyOf(...)
Map.copyOf(...)
```

These methods:

* Create immutable collections
* Reject null elements
* Simplify defensive copying

Introduced in Java 10.

---

# Defensive Copying and Immutability

True immutability requires:

### Final Fields

```java
private final List<String> skills;
```

---

### No Setters

```java
No mutators
```

---

### Defensive Copying

```java
List.copyOf(...)
```

---

### Safe Getters

```java
Return immutable view
```

Missing any one of these can break immutability.

---

# Common Mistake #1

Assuming:

```java
final
```

means immutable.

It does not.

---

# Common Mistake #2

Copying in constructor but exposing mutable getters.

Example:

```java
public List<String> getSkills() {
    return skills;
}
```

Internal state can still leak.

---

# Common Mistake #3

Using:

```java
new ArrayList<>(list)
```

and assuming immutability.

This creates a new list.

But the list remains mutable.

Callers can still modify it.

---

# Common Mistake #4

Ignoring nested mutable objects.

Example:

```java
List<Address>
```

A shallow copy may not be sufficient.

---

# Performance Considerations

Developers sometimes worry:

```text
Copying creates overhead
```

True.

However:

* Most business applications are not limited by collection copying.
* Correctness is usually more valuable than micro-optimizations.
* JVM optimizations often reduce the impact.

Prefer correctness first.

Optimize only when profiling proves a bottleneck.

---

# Interview Questions

### What is Defensive Copying?

Creating a copy of mutable input instead of storing its reference.

---

### Why is Defensive Copying important?

It prevents external code from modifying internal object state.

---

### Does final make collections immutable?

No.

It only prevents reassignment of the reference.

---

### What is Representation Exposure?

Exposing internal mutable state through references or getters.

---

### What is the difference between shallow copy and deep copy?

A shallow copy duplicates references.

A deep copy duplicates the underlying objects as well.

---

# Key Takeaways

* Many immutable classes are accidentally mutable.
* Reference sharing is a common source of bugs.
* Defensive copying prevents external modification.
* `final` does not imply immutability.
* Collections, arrays, and mutable objects require special care.
* Safe getters are as important as safe constructors.
* `List.copyOf()`, `Set.copyOf()`, and `Map.copyOf()` are preferred modern approaches.
* Defensive copying is a critical part of production-grade immutable object design.
