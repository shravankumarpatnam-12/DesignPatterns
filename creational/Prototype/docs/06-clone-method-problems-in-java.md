# Clone Method Problems in Java

## Why This Chapter Exists

In the previous chapter, we learned:

```text id="j4k8pw"
Shallow Copy

Deep Copy
```

At this point, many developers naturally assume:

> Java already provides clone(), so Prototype must be easy.

Unfortunately, reality is very different.

In fact:

```text id="m7v2qx"
clone()
```

is one of the most controversial APIs in Java.

Many senior Java engineers avoid it completely.

Some of the most respected Java experts, including
Joshua Bloch,
have criticized Java's cloning mechanism for years.

Understanding these problems is critical because:

```text id="t3k9rn"
Prototype Pattern

≠

Java clone()
```

The pattern is valuable.

The Java implementation has flaws.

This chapter explores those flaws.

---

# The Common Assumption

Suppose we have:

```java id="p5m8tw"
User copy =
        original.clone();
```

Many developers assume:

```text id="v2k4qx"
A Perfect Copy Exists
```

Unfortunately:

```text id="n8v3rp"
Java Does Not Guarantee That
```

The behavior depends on:

```text id="x5k7qn"
Cloneable

clone()

Object Structure

Implementation Details
```

This surprises many developers.

---

# The Strange Design Of clone()

Let's look at the API.

Defined in:

```java id="r4m9tw"
java.lang.Object
```

Method:

```java id="s7v2qx"
protected Object clone()
        throws CloneNotSupportedException
```

Immediately several questions arise.

---

# Problem #1

The Method Is Protected

Example:

```java id="t8k4rp"
Object obj = new Object();

obj.clone();
```

Compilation error.

Why?

Because:

```text id="m3v7qn"
clone()
```

is protected.

Developers must override it.

This creates friction immediately.

---

# Problem #2

Cloneable Is A Marker Interface

Example:

```java id="x4k8pw"
public class User
        implements Cloneable {
}
```

Question:

What method does Cloneable define?

Answer:

```text id="r2v9qx"
None
```

Zero methods.

No contract.

No guidance.

No enforcement.

---

# Why This Is Strange

Most interfaces define behavior.

Example:

```java id="n7k3rn"
Runnable
```

defines:

```java id="p6m4tw"
run()
```

---

```java id="s5v8qx"
Callable
```

defines:

```java id="w2k4rp"
call()
```

---

Cloneable defines:

```text id="m8v3qn"
Nothing
```

This is one reason many architects consider it poorly designed.

---

# Problem #3

Without Cloneable, Clone Fails

Example:

```java id="x7k4pw"
public class User {
}
```

Attempt:

```java id="t2m8qx"
user.clone();
```

Result:

```text id="n4v7rp"
CloneNotSupportedException
```

Even though:

```text id="r5k3qn"
Object Already Has clone()
```

The behavior feels inconsistent.

---

# Problem #4

clone() Returns Object

Method signature:

```java id="p8m4tw"
protected Object clone()
```

Notice:

```text id="s2v7qx"
Object
```

not:

```text id="x5k4rp"
User
```

Usage:

```java id="w8m3qn"
User copy =
        (User) user.clone();
```

Requires casting.

This reduces type safety.

Modern Java APIs generally avoid such designs.

---

# Problem #5

Shallow Copy By Default

Perhaps the most dangerous issue.

Example:

```java id="m4k8pw"
User {

    Address address;
}
```

Clone:

```java id="t7v2qx"
User copy =
        (User) super.clone();
```

Result:

```text id="n3v4rp"
Same Address Reference
```

is copied.

Not:

```text id="x8k7qn"
New Address Object
```

Developers frequently expect deep copy.

Java provides shallow copy.

This mismatch causes bugs.

---

# Example Of The Bug

Original:

```java id="s6m4tw"
user1.address.city =
        "Hyderabad";
```

Clone:

```java id="p5v8qx"
User user2 =
        user1.clone();
```

Modify:

```java id="w2k3rp"
user2.address.city =
        "Berlin";
```

Result:

```text id="m7v4qn"
user1.address.city

↓

Berlin
```

Unexpected side effect.

---

# Problem #6

Deep Copy Becomes Manual

Suppose:

```java id="x4m8pw"
Address address;
```

must also be copied.

Developer must write:

```java id="r8v2qx"
copy.address =
        address.clone();
```

Then:

```java id="n5k4rp"
Country
```

inside address may also require cloning.

Soon:

```text id="s3v7qn"
Entire Object Graph
```

must be cloned manually.

Complexity grows rapidly.

---

# Problem #7

Inheritance Complications

Suppose:

```java id="t6m4tw"
Person
```

extends:

```java id="x2v8qx"
Human
```

and:

```java id="p9k4rp"
Employee
```

extends:

```java id="n7v3qn"
Person
```

Question:

Who performs cloning?

Parent?

Child?

Both?

Inheritance introduces significant complexity.

---

# Example

```java id="s5k8pw"
class Human {

    protected Object clone() {
    }
}
```

---

```java id="m2v4qx"
class Person
        extends Human {

}
```

---

```java id="x7k3rp"
class Employee
        extends Person {

}
```

Copying behavior becomes difficult to reason about.

---

# Problem #8

Constructors Are Bypassed

This surprises many developers.

When cloning:

```java id="t4m8qn"
super.clone();
```

Java does not call:

```java id="r6v2qx"
Constructors
```

Normal creation:

```java id="p3k7rp"
new User();
```

calls constructors.

Clone:

```java id="s8v4qn"
user.clone();
```

does not.

---

# Why Is This Dangerous?

Suppose constructor performs:

```text id="m5k2pw"
Validation

Default Initialization

Security Checks
```

Cloning bypasses all of them.

The cloned object may violate assumptions.

---

# Problem #9

Final Fields

Consider:

```java id="x4v8qx"
private final String id;
```

Cloning final fields introduces complications.

Modifying identity after cloning becomes difficult.

This often conflicts with business requirements.

---

# Problem #10

Resource Ownership

Suppose object contains:

```java id="t2k4rp"
Socket

Connection

File Handle
```

Question:

Should clone copy:

```text id="p7v3qn"
The Resource?
```

or:

```text id="r8m4pw"
A Reference To The Resource?
```

Neither option is always correct.

The semantics become unclear.

---

# Joshua Bloch's Criticism

In the book:

Effective Java

one of the most famous recommendations is:

```text id="s6v2qx"
Avoid Cloneable
```

unless absolutely necessary.

Why?

Because cloning is:

```text id="n4k8rp"
Fragile

Confusing

Error-Prone
```

especially in complex object hierarchies.

---

# The Modern Alternative

Instead of:

```java id="x5m4qn"
clone()
```

many teams use:

```java id="t8v2qx"
Copy Constructors
```

Example:

```java id="p3k4rp"
public User(User other) {

    this.name =
            other.name;

    this.email =
            other.email;
}
```

Usage:

```java id="m7v8qn"
User copy =
        new User(original);
```

Much clearer.

---

# Builder-Based Copying

Another common approach.

Example:

```java id="s4k2pw"
User copy =
        User.builder()
            .name(original.getName())
            .email(original.getEmail())
            .build();
```

Readable.

Explicit.

Easy to understand.

---

# Static Factory Copy Method

Example:

```java id="x8v4qx"
public static User copyOf(
        User other) {

    return new User(other);
}
```

Usage:

```java id="r2k7rp"
User copy =
        User.copyOf(original);
```

This is increasingly common in modern Java.

---

# Important Architectural Insight

Notice something.

Prototype Pattern requires:

```text id="m5v3qn"
Object Copying
```

It does not require:

```text id="p7k4pw"
Java clone()
```

This distinction is critical.

The pattern survives.

The API is optional.

---

# Prototype Without clone()

Example:

```java id="t4v8qx"
public interface Prototype<T> {

    T copy();
}
```

Implementation:

```java id="x3k2rp"
public User copy() {

    return new User(this);
}
```

Perfectly valid Prototype.

No Cloneable required.

---

# Enterprise Reality

Most enterprise Java systems today prefer:

```text id="r6v4qn"
Copy Constructors

Factory Methods

Builder Copies
```

over:

```text id="m8k3pw"
Cloneable
```

The pattern remains useful.

The API is often avoided.

---

# Architect Perspective

Junior developers think:

```text id="s5v2qx"
Prototype
      ↓
clone()
```

Senior developers think:

```text id="n7k4rp"
Prototype
      ↓
Copying Strategy
```

Architects think:

```text id="x4v3qn"
State Duplication Semantics
```

because the real challenge is deciding:

```text id="p8k2pw"
What Should Be Copied?
```

not:

```text id="r3v7qx"
Which API Performs The Copy?
```

---

# The Deep Insight

Prototype Pattern and Java's cloning mechanism are not the same thing.

Prototype is a design concept.

```text id="m4k8rp"
Reuse Existing State
```

Java's:

```text id="s7v2qn"
Cloneable

clone()
```

are merely one implementation approach.

The pattern remains valuable even when the API is avoided.

---

# Key Takeaways

## What Is The Biggest Problem With clone()?

Shallow copy by default.

---

## Why Is Cloneable Controversial?

It provides no actual cloning contract.

---

## Why Do Many Experts Avoid clone()?

It is fragile, confusing, and difficult to implement correctly.

---

## What Are Common Alternatives?

```text id="t2k4pw"
Copy Constructors

Builder Copies

copyOf Methods
```

---

## Most Important Insight

Prototype Pattern does not depend on Java's clone() mechanism.

The pattern is about copying state, not about using a specific API.

---