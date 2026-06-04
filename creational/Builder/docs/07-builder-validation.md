# Validation in Builder

## Introduction

In the previous chapter, we learned how Builder enables immutable object creation.

However, immutability alone is not enough.

Consider the following object:

```java
User user =
    new User.Builder()
        .firstName("")
        .lastName(null)
        .age(-100)
        .build();
```

The object is immutable.

But it is clearly invalid.

Immutability guarantees that state cannot change.

It does not guarantee that the state is correct.

This is where validation becomes important.

One of the biggest advantages of the Builder Pattern is that it provides a centralized place to validate object state before the object is created.

---

# Why Validation Matters

Consider a banking application.

```java
Account account =
    new Account.Builder()
        .accountNumber(null)
        .balance(-5000)
        .build();
```

Should this object exist?

Probably not.

Allowing invalid objects into the system creates problems later.

Common consequences include:

* Runtime failures
* Corrupted business data
* Database inconsistencies
* Security vulnerabilities
* Difficult debugging

The best strategy is:

> Prevent invalid objects from being created in the first place.

---

# The Principle of Fail Fast

A common engineering principle is:

> Fail as early as possible.

Bad:

```java
User user =
    new User.Builder()
        .age(-10)
        .build();

saveToDatabase(user);
```

Error appears much later.

---

Good:

```java
User user =
    new User.Builder()
        .age(-10)
        .build();
```

Exception is thrown immediately.

The bug is discovered at the source.

This is called the **Fail Fast Principle**.

---

# Why Build() Is the Perfect Validation Point

Object creation flows through one place:

```java
build()
```

Instead of validating in:

```java
constructor
setter
service
controller
database
```

we validate once.

```java
build()
```

This creates a single source of truth.

---

# Basic Validation Example

Consider:

```java
public User build() {
    return new User(this);
}
```

Let's add validation.

```java
public User build() {

    if(firstName == null) {
        throw new IllegalStateException(
            "firstName is required"
        );
    }

    return new User(this);
}
```

Usage:

```java
User user =
    new User.Builder()
        .lastName("Patnam")
        .build();
```

Result:

```text
IllegalStateException:
firstName is required
```

The invalid object never gets created.

---

# Validating Mandatory Fields

Suppose our business rule says:

```text
Every User must have:
- firstName
- lastName
```

Validation:

```java
public User build() {

    if(firstName == null ||
       firstName.isBlank()) {

        throw new IllegalStateException(
            "firstName is mandatory"
        );
    }

    if(lastName == null ||
       lastName.isBlank()) {

        throw new IllegalStateException(
            "lastName is mandatory"
        );
    }

    return new User(this);
}
```

Now the object is guaranteed to contain required information.

---

# Numeric Validation

Consider age.

Business rule:

```text
Age must be between 18 and 100
```

Validation:

```java
if(age < 18 || age > 100) {

    throw new IllegalArgumentException(
        "Invalid age"
    );
}
```

Invalid values are rejected immediately.

---

# Email Validation Example

Suppose:

```java
email
```

is optional.

But if provided, it must be valid.

```java
if(email != null &&
   !email.contains("@")) {

    throw new IllegalArgumentException(
        "Invalid email address"
    );
}
```

Now:

```java
new User.Builder()
        .email("invalid")
        .build();
```

fails immediately.

---

# Cross-Field Validation

Many business rules involve multiple fields.

Example:

```text
A minor cannot register
without a guardian.
```

Model:

```java
private int age;
private String guardianName;
```

Validation:

```java
if(age < 18 &&
   guardianName == null) {

    throw new IllegalStateException(
        "Guardian required"
    );
}
```

This is known as **cross-field validation**.

The Builder provides a natural place to enforce such rules.

---

# Domain Validation

Consider an Order.

```java
Order order =
    new Order.Builder()
        .customerId(customerId)
        .items(items)
        .build();
```

Business rule:

```text
An order must contain
at least one item.
```

Validation:

```java
if(items == null ||
   items.isEmpty()) {

    throw new IllegalStateException(
        "Order must contain items"
    );
}
```

The object can never exist in an invalid state.

---

# Validation Inside Constructor vs Builder

Some developers ask:

> Why not validate inside the constructor?

Example:

```java
public User(
        String firstName,
        String lastName) {

    if(firstName == null) {
        throw new IllegalArgumentException();
    }

    this.firstName = firstName;
    this.lastName = lastName;
}
```

This works.

However, for large objects:

```java
20+ fields
multiple rules
cross-field checks
```

constructors become difficult to maintain.

Builders centralize validation in a dedicated construction workflow.

---

# Validation Layers

In enterprise systems, validation often exists at multiple levels.

### API Validation

Example:

```java
@NotNull
@NotBlank
```

Purpose:

```text
Validate incoming requests
```

---

### Builder Validation

Purpose:

```text
Protect domain object creation
```

---

### Database Validation

Purpose:

```text
Enforce persistence constraints
```

Example:

```sql
NOT NULL
CHECK
UNIQUE
```

---

Important:

Builder validation should not be skipped simply because another layer exists.

Domain objects should protect themselves.

---

# Real-World Example

Consider a money transfer.

```java
Transfer transfer =
    new Transfer.Builder()
        .fromAccount(source)
        .toAccount(destination)
        .amount(amount)
        .build();
```

Business rules:

```text
Amount > 0

Source account != destination account
```

Validation:

```java
if(amount <= 0) {
    throw new IllegalArgumentException(
        "Amount must be positive"
    );
}

if(fromAccount.equals(toAccount)) {
    throw new IllegalStateException(
        "Source and destination must differ"
    );
}
```

Invalid transfers never enter the system.

---

# Common Mistake: No Validation

Many developers write:

```java
public User build() {
    return new User(this);
}
```

without validation.

This turns Builder into nothing more than a fancy constructor.

A production-grade Builder should protect object invariants.

---

# Common Mistake: Validation in Every Setter

Bad:

```java
public Builder age(int age) {

    if(age < 18) {
        throw new IllegalArgumentException();
    }

    this.age = age;
    return this;
}
```

Sometimes acceptable.

However, many rules depend on multiple fields.

Centralizing validation in:

```java
build()
```

usually keeps logic simpler.

---

# Common Mistake: Trusting External Validation

Developers often assume:

```text
Frontend validates

API validates

Therefore Builder doesn't need validation
```

Dangerous assumption.

Objects should enforce their own rules.

Never trust external callers.

---

# Real Enterprise Pattern

A common production approach:

```java
public User build() {

    validateMandatoryFields();

    validateBusinessRules();

    validateDomainConstraints();

    return new User(this);
}
```

This keeps validation organized and scalable.

---

# Interview Questions

### Why is build() a good place for validation?

Because all object creation flows through a single point.

---

### What is Fail Fast?

Detecting invalid state as early as possible instead of allowing it to propagate through the system.

---

### Should validation exist only in the Builder?

No.

Validation may exist at API, domain, and database layers.

Builder validation protects domain object creation.

---

### Can immutable objects still be invalid?

Yes.

Immutability prevents changes.

It does not guarantee correctness.

Validation ensures correctness.

---

# Key Takeaways

* Immutability does not guarantee valid state.
* Builder provides a centralized place for validation.
* Fail Fast helps detect problems early.
* Mandatory field validation is common in build().
* Cross-field validation is easier in Builder than constructors.
* Domain objects should protect their own invariants.
* A Builder without validation often provides limited value.
* Validation ensures objects are both immutable and correct.
