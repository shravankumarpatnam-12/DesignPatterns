# Telescoping Constructor Anti-Pattern

## Introduction

In the previous chapter, we learned that constructors become harder to use as the number of fields increases.

A common attempt to solve this problem is to create multiple overloaded constructors.

Initially, this seems reasonable.

However, as the number of optional fields grows, constructor overloading quickly turns into what is known as the **Telescoping Constructor Anti-Pattern**.

This chapter explores why it happens and why it becomes a maintenance nightmare.

---

# What Is a Telescoping Constructor?

A telescoping constructor occurs when a class provides multiple overloaded constructors with different combinations of parameters.

Consider a simple `User` class.

```java
public class User {

    private String firstName;
    private String lastName;
    private int age;

    public User(String firstName) {
        this(firstName, null);
    }

    public User(String firstName,
                String lastName) {
        this(firstName, lastName, 0);
    }

    public User(String firstName,
                String lastName,
                int age) {

        this.firstName = firstName;
        this.lastName = lastName;
        this.age = age;
    }
}
```

Usage:

```java
User user1 = new User("Shravan");

User user2 = new User(
        "Shravan",
        "Patnam");

User user3 = new User(
        "Shravan",
        "Patnam",
        28);
```

At first glance, this looks convenient.

---

# Why Developers Use It

Developers often choose overloaded constructors because:

* Constructors are familiar
* No additional classes are required
* Object creation remains simple
* Optional fields appear manageable

For a small object, this approach works well.

The problems appear when the class grows.

---

# The Growth Problem

Let's add more fields.

```java
private String email;
private String phoneNumber;
private String address;
private String company;
```

Now we need additional constructors.

```java
public User(String firstName)

public User(
        String firstName,
        String lastName)

public User(
        String firstName,
        String lastName,
        int age)

public User(
        String firstName,
        String lastName,
        int age,
        String email)

public User(
        String firstName,
        String lastName,
        int age,
        String email,
        String phoneNumber)

public User(
        String firstName,
        String lastName,
        int age,
        String email,
        String phoneNumber,
        String address)

...
```

The number of constructors keeps increasing.

Every new optional field introduces another constructor.

---

# Constructor Explosion

Imagine:

```java
Mandatory Fields:
- firstName
- lastName

Optional Fields:
- age
- email
- phone
- address
- company
- department
- city
- country
```

How many constructor combinations should we support?

Examples:

```java
User(
    firstName,
    lastName)

User(
    firstName,
    lastName,
    age)

User(
    firstName,
    lastName,
    email)

User(
    firstName,
    lastName,
    phone)

User(
    firstName,
    lastName,
    age,
    email)

User(
    firstName,
    lastName,
    age,
    phone)

...
```

The number of combinations grows rapidly.

Maintaining all possible constructor combinations becomes impossible.

This phenomenon is called **Constructor Explosion**.

---

# Readability Starts Declining

Consider:

```java
User user = new User(
        "Shravan",
        "Patnam",
        28,
        "shravan@gmail.com",
        "9999999999",
        "Hyderabad",
        "ABC Corp");
```

Can you immediately answer:

* Which String represents the email?
* Which String represents the company?
* Which String represents the address?

Probably not.

The constructor call becomes increasingly difficult to understand.

---

# Positional Parameter Bugs

Suppose a developer accidentally swaps values.

```java
User user = new User(
        "Shravan",
        "Patnam",
        28,
        "9999999999",
        "shravan@gmail.com");
```

The code compiles.

No compiler error.

No warning.

Yet the values are wrong.

Because constructors rely on position rather than meaning.

These bugs are common in enterprise systems.

---

# Maintenance Cost

Imagine a production system used by hundreds of services.

A new requirement arrives.

Business wants:

```java
private String preferredLanguage;
```

Now developers must:

1. Add a new field
2. Create new constructors
3. Update existing constructor chains
4. Update documentation
5. Update tests

A seemingly small change affects many places.

This increases maintenance cost.

---

# Real-World Example

Consider a database connection object.

```java
DatabaseConnection connection =
        new DatabaseConnection(
                host,
                port,
                username,
                password,
                sslEnabled,
                connectionTimeout,
                readTimeout,
                writeTimeout,
                retryCount,
                retryDelay);
```

Questions:

* What does the seventh parameter represent?
* What does the ninth parameter represent?

Even experienced developers struggle with such constructors.

---

# Why This Is an Anti-Pattern

An anti-pattern is a commonly used solution that creates more problems than it solves.

Telescoping constructors:

✓ Work initially

✗ Become difficult to read

✗ Become difficult to maintain

✗ Encourage constructor explosion

✗ Increase bug probability

✗ Scale poorly

The larger the object becomes, the worse the situation gets.

---

# Effective Java Recommendation

In the book
Effective Java

Joshua Bloch discusses telescoping constructors as one of the major motivations for using the Builder Pattern.

His recommendation is straightforward:

> When classes contain many optional parameters, consider using a Builder instead of overloaded constructors.

This advice is widely followed across modern Java applications and frameworks.

---

# The Missing Piece

Telescoping constructors solve one problem:

✓ Allow multiple ways to create an object

But they fail to solve:

✗ Readability

✗ Flexibility

✗ Maintainability

✗ Validation

✗ Immutability

We need another approach.

Many developers turned to JavaBeans.

At first, it seemed like the perfect solution.

However, it introduced a completely different set of problems.

---

# Key Takeaways

* Telescoping constructors use constructor overloading to support optional fields.
* They work for small objects but fail as complexity grows.
* Constructor explosion makes maintenance difficult.
* Long parameter lists reduce readability.
* Positional parameters are error-prone.
* This approach does not scale in enterprise applications.
* Builder Pattern was created to solve these limitations.

---