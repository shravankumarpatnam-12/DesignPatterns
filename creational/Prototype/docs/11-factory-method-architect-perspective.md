# Common Mistakes in Prototype

## Why This Chapter Exists

Most Prototype tutorials teach:

```java id="a1p7qx"
User copy =
        original.copy();
```

and stop there.

Unfortunately:

```text id="b2k4rp"
Prototype Is Easy To Learn

But Easy To Misuse
```

Many production bugs involving Prototype are not caused by:

```text id="c3v8qn"
The Pattern Itself
```

They are caused by:

```text id="d4k2pw"
Incorrect Copying Decisions
```

Architects care deeply about these mistakes because:

```text id="e5v7qn"
Copy Bugs

↓

Data Corruption

↓

Production Incidents
```

Unlike many design patterns, Prototype can silently create incorrect state.

These bugs are often difficult to diagnose.

---

# The Most Important Rule

Before discussing mistakes, remember:

> Prototype is a state management pattern disguised as a creation pattern.

Most developers focus on:

```text id="f6k4rp"
Creating Copies
```

Architects focus on:

```text id="g7v8qx"
Managing State Ownership
```

Almost every mistake originates from misunderstanding this principle.

---

# Mistake #1

Using Prototype For Cheap Objects

Example:

```java id="h8k2rp"
Point point =
        prototype.copy();
```

Object:

```java id="i9v7qn"
class Point {

    int x;
    int y;
}
```

Creation cost:

```text id="j1k4pw"
Almost Zero
```

Prototype provides no meaningful benefit.

---

# Why This Is Bad

You introduce:

```text id="k2v8qx"
Copy Logic

Testing Overhead

Maintenance Cost
```

without solving a real problem.

---

# Architect Rule

If:

```java id="l3k2rp"
new Object();
```

is trivial,

Prototype is usually unnecessary.

---

# Mistake #2

Confusing Prototype With clone()

Many developers think:

```text id="m4v7qn"
Prototype
      =
clone()
```

Wrong.

Prototype is:

```text id="n5k4pw"
Object Duplication Strategy
```

Java's:

```java id="o6v8qx"
clone()
```

is merely one implementation option.

The pattern survives even when clone() is avoided.

---

# Mistake #3

Accidental Shallow Copy

Perhaps the most common production bug.

Example:

```java id="p7k2rp"
copy.address =
        original.address;
```

Looks innocent.

Result:

```text id="q8v7qn"
Shared State
```

Two objects unexpectedly affect each other.

---

# Why It Happens

Developers copy:

```text id="r9k4pw"
References
```

instead of:

```text id="s1v8qx"
Objects
```

The copy appears correct until state changes.

---

# Production Example

```java id="t2k2rp"
copy.getAddress()
     .setCity("Berlin");
```

Suddenly:

```java id="u3v7qn"
original.getAddress()
```

also changes.

Unexpected side effect.

---

# Mistake #4

Deep Copying Everything

After learning shallow-copy dangers, some developers swing to the opposite extreme.

They deep copy:

```text id="v4k4pw"
Every Field

Every Object

Every Collection
```

regardless of necessity.

---

# Why This Is Bad

Deep copy introduces:

```text id="w5v8qx"
More Memory

More CPU

More Complexity
```

Sometimes shared state is perfectly safe.

---

# Example

Immutable object:

```java id="x6k2rp"
String name;
```

Deep-copying Strings is unnecessary.

They are already immutable.

---

# Architect Rule

Copy:

```text id="y7v7qn"
Mutable State
```

Share:

```text id="z8k4pw"
Immutable State
```

whenever appropriate.

---

# Mistake #5

Copying Identity

Consider:

```java id="aa9v8qx"
Order
```

contains:

```java id="ab1k2rp"
orderId
```

Question:

Should clone copy:

```text id="ac2v7qn"
Order ID?
```

Many implementations do.

This creates serious problems.

---

# Example

Original:

```text id="ad3k4pw"
Order #1001
```

Clone:

```text id="ae4v8qx"
Order #1001
```

Two different orders.

Same identity.

Potential data corruption.

---

# Architect Question

Always ask:

> Is identity part of the copy?

The answer depends on the business domain.

---

# Mistake #6

Ignoring Business Semantics

Many developers implement:

```java id="af5k2rp"
copy()
```

as a technical exercise.

Question:

What does:

```text id="ag6v7qn"
Copy
```

mean in business terms?

---

# Example

Invoice copy:

Should:

```text id="ah7k4pw"
Invoice Number
```

be copied?

Usually:

```text id="ai8v8qx"
No
```

---

Configuration copy:

Should:

```text id="aj9k2rp"
Timeout Settings
```

be copied?

Usually:

```text id="ak1v7qn"
Yes
```

Business meaning matters.

---

# Mistake #7

Copying External Resources

Suppose object contains:

```java id="al2k4pw"
Socket

Connection

File Handle
```

Question:

What should clone do?

---

Option 1:

```text id="am3v8qx"
Share Resource
```

May create contention.

---

Option 2:

```text id="an4k2rp"
Duplicate Resource
```

May be impossible.

---

Option 3:

```text id="ao5v7qn"
Create New Resource
```

May be expensive.

No universal answer exists.

---

# Why This Is Dangerous

External resources introduce:

```text id="ap6k4pw"
Ownership Ambiguity
```

which Prototype handles poorly.

---

# Mistake #8

Copying Large Object Graphs Blindly

Example:

```text id="aq7v8qx"

Order
   ↓

Customer
   ↓

Address
   ↓

Country
   ↓

Region
```

Developers often copy everything.

Result:

```text id="ar8k2rp"
Huge Memory Usage
```

and:

```text id="as9v7qn"
Poor Performance
```

---

# Architect Rule

Copy only what requires independent ownership.

Not everything reachable.

---

# Mistake #9

Forgetting Collection Ownership

Example:

```java id="at1k4pw"
List<Item> items;
```

Bad copy:

```java id="au2v8qx"
copy.items =
        original.items;
```

Now:

```text id="av3k2rp"
Shared List
```

exists.

One object's modification affects another.

---

# Mistake #10

Prototype Without Testing

Developers often assume:

```java id="aw4v7qn"
copy()
```

works.

No verification.

Months later:

```text id="ax5k4pw"
Shared-State Bug
```

appears in production.

Prototype demands strong testing.

---

# Mistake #11

Using Prototype For Persistence Entities

JPA entity:

```java id="ay6v8qx"
@Entity
class User {
}
```

Blind copying may duplicate:

```text id="az7k2rp"
Primary Keys

Version Fields

Audit Fields
```

creating persistence issues.

---

# Why This Happens

Database identity and object identity become mixed.

Prototype must be applied carefully to persistent entities.

---

# Mistake #12

Ignoring Immutability

Modern systems often use:

```text id="ba8v7qn"
Immutable Objects
```

Example:

```java id="bb9k4pw"
record User(
        String name,
        String email) {
}
```

No Prototype needed.

New object creation is already safe and simple.

---

# Architect Rule

Before introducing Prototype, ask:

> Would immutability solve the problem more simply?

Often the answer is yes.

---

# Mistake #13

Premature Optimization

Developer sees:

```java id="bc1v8qx"
new User();
```

and immediately introduces:

```java id="bd2k2rp"
userPrototype.copy();
```

No measurements.

No evidence.

No bottleneck.

---

# Architect Rule

Prototype should be driven by:

```text id="be3v7qn"
Actual Cost
```

not assumptions.

---

# Mistake #14

Prototype Registry Explosion

Developers create:

```text id="bf4k4pw"
User Prototype

Address Prototype

Country Prototype

Region Prototype

City Prototype
```

for everything.

Result:

```text id="bg5v8qx"
Complex Registry

Difficult Maintenance

Little Value
```

Not every object deserves a prototype.

---

# Mistake #15

Treating Prototype As A Performance Pattern Only

Many teams think:

```text id="bh6k2rp"
Prototype
      ↓
Performance
```

Architects often care more about:

```text id="bi7v7qn"
Knowledge Reuse

Template Reuse

Configuration Reuse
```

Performance is only one benefit.

---

# Warning Signs

You may be misusing Prototype if:

```text id="bj8k4pw"
Objects Are Cheap

No Templates Exist

Identity Is Unclear

Copy Semantics Are Undefined

Tests Are Missing
```

These are architectural smells.

---

# Healthy Prototype Usage

Good implementations usually have:

```text id="bk9v8qx"
Clear Copy Rules

Defined Ownership

Reusable Templates

Measurable Benefits

Strong Tests
```

The value is obvious.

---

# Architect Perspective

Junior developers ask:

```text id="bl1k2rp"
Can I Copy This Object?
```

Senior developers ask:

```text id="bm2v7qn"
How Should I Copy This Object?
```

Architects ask:

```text id="bn3k4pw"
Should This Object Be Copied At All?
```

That final question often determines whether Prototype is appropriate.

---

# The Deep Insight

Most Prototype failures are not caused by:

```text id="bo4v8qx"
Bad Copy Logic
```

They are caused by:

```text id="bp5k2rp"
Unclear Ownership Rules
```

When ownership is clear:

```text id="bq6v7qn"
Copy Decisions Become Obvious
```

When ownership is unclear:

```text id="br7k4pw"
Prototype Becomes Dangerous
```

The pattern succeeds or fails based on state ownership.

---

# Key Takeaways

## What Is The Most Common Mistake?

Accidental shallow copy.

---

## What Is The Most Dangerous Mistake?

Copying identity incorrectly.

---

## Why Is Prototype Often Overused?

Developers assume every object benefits from copying.

---

## What Should Drive Prototype Adoption?

Real construction cost and reuse requirements.

---

## Most Important Insight

Prototype is fundamentally a state ownership pattern. Most mistakes occur when ownership semantics are not clearly defined.

---

# What's Next

We have now covered:

```text id="bs8v8qx"
Why Prototype Exists

Creation Cost

Copying vs Creating

Pattern Structure

Shallow vs Deep Copy

Clone Problems

Pattern Comparisons

Enterprise Systems

Spring Framework

Testing

Common Mistakes
```

Only one chapter remains:

```text id="bt9k2rp"
12-prototype-architect-perspective.md
```

There we will leave implementation details behind and answer:

> How do architects think about Prototype differently from developers?

We'll connect performance, state ownership, configuration reuse, templates, and system evolution into one architectural view of the pattern.
