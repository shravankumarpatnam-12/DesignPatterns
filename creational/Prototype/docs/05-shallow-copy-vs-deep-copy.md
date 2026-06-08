# Shallow Copy vs Deep Copy

## Why This Chapter Exists

In the previous chapter, we learned:

```text id="h3k7rp"
Prototype Creates Objects By Copying Existing Objects
```

At this point, many developers think:

> Great. I'll just copy the object.

Unfortunately, this is where the most dangerous Prototype bugs begin.

The biggest challenge in Prototype is not:

```text id="n5v2qx"
Creating Copies
```

The biggest challenge is:

```text id="k9m4tw"
Creating Correct Copies
```

To understand this problem, we must learn the difference between:

```text id="s8j3vn"
Shallow Copy

Deep Copy
```

This distinction is the heart of the Prototype pattern.

Without understanding it, most Prototype implementations are broken.

---

# The Illusion Of Copying

Consider:

```java id="v7p2rk"
User user =
        original.clone();
```

Question:

Did we actually copy the object?

Many developers instinctively answer:

```text id="x2m5qp"
Yes
```

The correct answer is:

```text id="j6v8rn"
Maybe
```

It depends on what was copied.

---

# Understanding Object Structure

Let's start with a simple object.

```java id="c4k7tz"
public class Address {

    private String city;
}
```

And:

```java id="u8n2mp"
public class User {

    private String name;

    private Address address;
}
```

Visual representation:

```text id="t3q5vw"

User
 ├── name
 └── Address
         └── city
```

Notice something important.

The User object contains:

```text id="r5k9xn"
Primitive State

Reference State
```

This distinction changes everything.

---

# What Is A Reference?

Consider:

```java id="m4p8qy"
Address address =
        new Address();
```

Variable:

```text id="q7v3nk"
address
```

does not hold the object.

It holds:

```text id="v2m6rw"
Reference To The Object
```

Think of it as:

```text id="z9q4tx"
Pointer

Memory Address

Object Location
```

The variable points to the object.

---

# The First Copy

Suppose:

```java id="n6v2rk"
User user1 =
        original.clone();
```

Question:

Should the clone receive:

```text id="h8p4qw"
Same Address Object?
```

or

```text id="k3m7tx"
New Address Object?
```

This question defines shallow vs deep copy.

---

# Shallow Copy

Shallow copy duplicates:

```text id="s4v8rp"
Values
```

but not:

```text id="n2k7qx"
Referenced Objects
```

Example:

```java id="y5m3tw"
User clone =
        new User();

clone.name =
        original.name;

clone.address =
        original.address;
```

Notice:

```text id="r8v2nk"
Same Address Reference
```

is copied.

Not the address object itself.

---

# Visualizing Shallow Copy

Original:

```text id="p4m9qx"

User A
   │
   ▼

Address
   │
   ▼

Hyderabad
```

Clone:

```text id="z7k2rp"

User B
   │
   ▼

Address
   │
   ▼

Hyderabad
```

Both users point to:

```text id="j3v8qw"
The Same Address Object
```

This is shallow copy.

---

# Why Shallow Copy Looks Correct

Initially:

```java id="x9m4tw"
user1.getAddress()
```

returns:

```text id="q2k7vn"
Hyderabad
```

---

```java id="y8p3rq"
user2.getAddress()
```

also returns:

```text id="n4v7qx"
Hyderabad
```

Everything appears fine.

The bug is hidden.

---

# The Hidden Bug

Suppose:

```java id="r5m2tk"
user2.getAddress()
     .setCity("Berlin");
```

Question:

What should happen?

Most developers expect:

```text id="w8v3qn"
Only user2 Changes
```

Reality:

```text id="f7k4rp"
user1 Changes Too
```

Why?

Because both users share:

```text id="p3m9qx"
The Same Address Object
```

---

# The Shared State Problem

This is the most common Prototype bug.

Changing:

```java id="k8v2tw"
clone.address
```

changes:

```java id="s5m4rq"
original.address
```

because they reference the same object.

The copy is incomplete.

---

# Deep Copy

Deep copy duplicates:

```text id="v7p3qx"
Everything
```

including referenced objects.

Example:

```java id="n4m8tw"
User clone =
        new User();

clone.name =
        original.name;

clone.address =
        new Address();

clone.address.setCity(
        original.address.getCity());
```

Now:

```text id="x3v7qn"
New User

New Address
```

exist independently.

---

# Visualizing Deep Copy

Original:

```text id="m2k9rp"

User A
   │
   ▼

Address A
   │
   ▼

Hyderabad
```

Clone:

```text id="r8v4qx"

User B
   │
   ▼

Address B
   │
   ▼

Hyderabad
```

Different address objects.

No shared state.

---

# Why Deep Copy Is Safer

Change:

```java id="t5m2rw"
clone.address
     .setCity("Berlin");
```

Result:

```text id="s9v3qx"
Original
    Hyderabad

Clone
    Berlin
```

Behavior is isolated.

This is usually what developers expect.

---

# Enterprise Example: Order System

Order:

```java id="k7m4tx"
Order {

    Customer customer;

    Address shippingAddress;
}
```

Shallow copy:

```text id="x5v8qn"
Shares Customer

Shares Address
```

Changing customer information in one order may unexpectedly affect another.

Serious bug.

---

# Enterprise Example: Workflow Engine

Workflow:

```java id="n8k2rp"
Workflow {

    List<State> states;
}
```

Shallow copy:

```text id="m3v7qx"
Same State Objects
```

Changing a cloned workflow may corrupt the original.

Deep copy is required.

---

# Collections Make It Worse

Example:

```java id="p6m4tw"
List<Task> tasks;
```

Shallow copy:

```java id="y8v2rq"
clone.tasks =
        original.tasks;
```

Now both objects share:

```text id="t4k9qn"
The Same List
```

Adding a task to one affects the other.

This bug appears frequently.

---

# Nested Object Graphs

Real enterprise systems rarely contain:

```text id="s2m7qx"
One Level
```

Instead:

```text id="v8k4rp"

Order
   ↓

Customer
   ↓

Address
   ↓

Country
```

Deep copying becomes increasingly difficult.

Every level must be copied correctly.

---

# Why Java's clone() Is Controversial

Many developers assume:

```java id="w5m2tx"
super.clone();
```

creates a perfect copy.

It does not.

By default:

```text id="k3v8qn"
Java clone()
       ↓
Shallow Copy
```

This surprises many developers.

It is one reason cloning is controversial in Java.

---

# When Shallow Copy Is Correct

Not every object requires deep copy.

Example:

```java id="q7m4rp"
User {

    String firstName;

    String lastName;
}
```

Strings are immutable.

Sharing them is safe.

Shallow copy may be sufficient.

---

# When Deep Copy Is Required

Deep copy is usually necessary when objects contain:

```text id="x8v3qw"
Mutable References

Collections

Nested Objects

Shared State
```

These structures can create unexpected side effects.

---

# Performance Trade-Off

Deep copy provides:

```text id="n2k7rp"
Isolation
```

but costs:

```text id="m9v4qx"
More Memory

More CPU

More Complexity
```

Architects must balance:

```text id="t7k3qn"
Safety

vs

Performance
```

---

# Immutable Objects Change Everything

Consider:

```java id="s4m8tw"
Address
```

is immutable.

Then:

```text id="r5v2qx"
Sharing Is Safe
```

because state cannot change.

Modern systems often combine:

```text id="k8m4rp"
Prototype

+

Immutability
```

to avoid deep-copy complexity.

---

# The Prototype Rule

Whenever implementing Prototype, ask:

> What state should be shared?

and

> What state should be duplicated?

These questions determine correctness.

Not the cloning mechanism itself.

---

# Architect Perspective

Junior developers think:

```text id="x3v7qn"
Copy Object
```

Senior developers think:

```text id="t8k4rp"
Copy References?
```

Architects think:

```text id="m5v2qx"
What State Ownership Model Exists?
```

because Prototype is fundamentally a state management problem.

---

# The Deep Insight

Most Prototype bugs are not caused by:

```text id="r7k3qn"
clone()
```

They are caused by:

```text id="s8v4qx"
Incorrect Ownership Of State
```

Shallow copy shares state.

Deep copy duplicates state.

Understanding which behavior is correct is the real challenge.

---

# Key Takeaways

## What Is Shallow Copy?

Copies references.

Referenced objects are shared.

---

## What Is Deep Copy?

Copies referenced objects as well.

Objects become independent.

---

## Why Is Shallow Copy Dangerous?

Changes in one object may affect another.

---

## Why Is Deep Copy Expensive?

More memory, CPU, and complexity.

---

## Most Important Insight

The hardest part of Prototype is not copying objects.

It is deciding which state should be shared and which state should be duplicated.

---