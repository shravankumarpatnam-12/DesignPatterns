# Testing Prototype Pattern

## Why This Chapter Exists

Most design pattern tutorials stop after implementation.

They show:

```java id="a1v7qx"
User copy =
        prototype.copy();
```

and declare the pattern complete.

Real software engineering begins when code must be:

```text id="b2k4rp"
Tested

Maintained

Debugged

Extended
```

Prototype introduces unique testing challenges because:

```text id="c3v8qn"
Objects Are Copied

State Is Reused

References May Be Shared
```

These characteristics create bugs that are often difficult to detect.

This chapter explores how architects test Prototype systems.

---

# The Core Testing Question

Whenever a prototype creates a copy, the first question is:

> Is the copy truly independent?

This single question drives most Prototype testing.

---

# Example

Suppose:

```java id="d4k2pw"
User original =
        prototype.copy();
```

and:

```java id="e5v7qn"
User clone =
        original.copy();
```

Question:

```text id="f6k4rp"
Can Changes In Clone Affect Original?
```

The answer determines whether the copy is correct.

---

# Why This Matters

Imagine:

```java id="g7v8qx"
clone.setName("John");
```

If:

```java id="h8k2rp"
original.getName()
```

also changes,

the Prototype implementation is broken.

The copy is not isolated.

---

# The Prototype Testing Principle

Always verify:

```text id="i9v7qn"
State Independence
```

This is the most important Prototype test.

---

# First Test

Example:

```java id="j1k4pw"
@Test
void shouldCreateSeparateObject() {

    User copy =
            original.copy();

    assertNotSame(
            original,
            copy);
}
```

This verifies:

```text id="k2v8qx"
Different Objects Exist
```

But this test alone is insufficient.

---

# Why Identity Is Not Enough

Example:

```java id="l3k2rp"
assertNotSame(
        original,
        copy);
```

passes.

However:

```java id="m4v7qn"
assertSame(
        original.getAddress(),
        copy.getAddress());
```

may also pass.

Meaning:

```text id="n5k4pw"
Shared References Exist
```

The copy is still dangerous.

---

# Testing State Isolation

Better test:

```java id="o6v8qx"
@Test
void shouldNotShareAddress() {

    User copy =
            original.copy();

    assertNotSame(
            original.getAddress(),
            copy.getAddress());
}
```

This verifies:

```text id="p7k2rp"
Deep Copy Behavior
```

for nested objects.

---

# The Shared Reference Test

A powerful technique.

Example:

```java id="q8v7qn"
copy.getAddress()
    .setCity("Berlin");
```

Verify:

```java id="r9k4pw"
assertEquals(
        "Hyderabad",
        original.getAddress()
                .getCity());
```

If original changes:

```text id="s1v8qx"
Shared State Exists
```

Bug detected.

---

# Why Mutation Tests Matter

Many Prototype bugs are invisible until:

```text id="t2k2rp"
State Changes
```

occur.

Therefore:

```text id="u3v7qn"
Modify Clone

Observe Original
```

is one of the most effective testing strategies.

---

# Testing Collections

Collections are a common source of bugs.

Example:

```java id="v4k4pw"
Order {

    List<Item> items;
}
```

Copy:

```java id="w5v8qx"
Order clone =
        order.copy();
```

Test:

```java id="x6k2rp"
clone.getItems()
     .add(new Item());
```

Verify:

```java id="y7v7qn"
assertEquals(
        originalSize,
        order.getItems().size());
```

If size changes:

```text id="z8k4pw"
Shared Collection Bug
```

exists.

---

# Testing Nested Object Graphs

Enterprise systems rarely contain:

```text id="aa9v8qx"
One Level
```

Example:

```text id="ab1k2rp"

Order
   ↓

Customer
   ↓

Address
   ↓

Country
```

Every level must be validated.

---

# Deep Copy Verification Strategy

Architects often test:

```text id="ac2v7qn"
Level 1

Level 2

Level 3

Collections

References
```

individually.

Because bugs frequently hide deep inside object graphs.

---

# The Identity Problem

Consider:

```java id="ad3k4pw"
Order copy =
        prototype.copy();
```

Question:

Should:

```text id="ae4v8qx"
Order ID
```

also be copied?

Testing must verify business expectations.

---

# Example

If IDs should be unique:

```java id="af5k2rp"
assertNotEquals(
        original.getId(),
        copy.getId());
```

---

If IDs should remain identical:

```java id="ag6v7qn"
assertEquals(
        original.getId(),
        copy.getId());
```

The correct answer depends on the domain.

---

# Testing Business Semantics

Prototype testing is not only technical.

It is also business-focused.

Question:

```text id="ah7k4pw"
What Does Copy Mean?
```

For:

```text id="ai8v8qx"
Order

Invoice

Policy

Workflow
```

the answer differs.

---

# Example: Invoice Copy

Original:

```text id="aj9k2rp"
Invoice #1001
```

Clone:

```text id="ak1v7qn"
Invoice #1002
```

Question:

Should invoice number be copied?

Usually:

```text id="al2k4pw"
No
```

Business rules matter.

---

# Example: Configuration Copy

Configuration:

```text id="am3v8qx"
Database Settings
```

Question:

Should values be copied?

Usually:

```text id="an4k2rp"
Yes
```

Configuration reuse is the goal.

---

# Prototype Contract Testing

When multiple prototypes exist:

```text id="ao5v7qn"
Invoice

Contract

Proposal
```

architects often define:

```text id="ap6k4pw"
Copy Contract
```

Example:

```java id="aq7v8qx"
public interface Prototype<T> {

    T copy();
}
```

Every implementation must satisfy:

```text id="ar8k2rp"
Independent Copy

Valid State

Correct Semantics
```

---

# Reusable Prototype Tests

Abstract test:

```java id="as9v7qn"
abstract class PrototypeTest<T> {

    protected abstract
    T createPrototype();
}
```

Each implementation inherits:

```text id="at1k4pw"
Common Copy Validation
```

This improves consistency.

---

# Testing Prototype Registries

Suppose:

```java id="au2v8qx"
Map<String, Prototype>
```

stores prototypes.

Tests should verify:

```text id="av3k2rp"
Correct Lookup

Correct Type

Correct Copy Behavior
```

---

# Example

```java id="aw4v7qn"
Prototype invoice =
        registry.get("invoice");
```

Verify:

```text id="ax5k4pw"
Invoice Prototype Returned
```

Then verify:

```text id="ay6v8qx"
Copied Instance Is Independent
```

---

# Testing Performance Assumptions

Many teams introduce Prototype for:

```text id="az7k2rp"
Performance
```

Question:

Did performance actually improve?

Architects verify with:

```text id="ba8v7qn"
Benchmarks
```

not assumptions.

---

# Example

Measure:

```java id="bb9k4pw"
new Workflow()
```

versus:

```java id="bc1v8qx"
workflow.copy()
```

Sometimes the difference is substantial.

Sometimes it is negligible.

Always measure.

---

# Common Testing Mistake #1

Only verifying:

```java id="bd2k2rp"
assertNotSame(
        original,
        copy);
```

This proves:

```text id="be3v7qn"
Object Identity
```

not:

```text id="bf4k4pw"
State Independence
```

---

# Common Testing Mistake #2

Ignoring nested references.

Example:

```text id="bg5v8qx"
Address

Collections

Maps

Lists
```

often contain hidden sharing bugs.

---

# Common Testing Mistake #3

Testing implementation details.

Bad:

```java id="bh6k2rp"
assertTrue(
        copy instanceof User);
```

Low value.

Better:

```text id="bi7v7qn"
Verify Behavior
```

and:

```text id="bj8k4pw"
Verify Independence
```

---

# Common Testing Mistake #4

Assuming deep copy.

Never assume.

Verify explicitly.

Many implementations accidentally perform:

```text id="bk9v8qx"
Shallow Copy
```

when deep copy was intended.

---

# Test Data Builders And Prototype

Interesting relationship.

Many testing frameworks use:

```text id="bl1k2rp"
Prototype Objects
```

as test templates.

Example:

```java id="bm2v7qn"
User defaultUser =
        TestUsers.standardUser();
```

Each test:

```java id="bn3k4pw"
defaultUser.copy();
```

and customizes only required fields.

This reduces test duplication.

---

# Enterprise Testing Pattern

Common workflow:

```text id="bo4v8qx"
Create Master Test Object

↓

Copy

↓

Customize

↓

Execute Test
```

Many teams use Prototype without realizing it.

---

# Architect Perspective

Junior developers test:

```text id="bp5k2rp"
Object Creation
```

Senior developers test:

```text id="bq6v7qn"
Reference Independence
```

Architects test:

```text id="br7k4pw"
State Ownership Semantics
```

because Prototype correctness depends on who owns state after copying.

---

# The Deep Insight

Most Prototype bugs are not:

```text id="bs8v8qx"
Creation Bugs
```

They are:

```text id="bt9k2rp"
Ownership Bugs
```

The critical testing question is:

> After copying, who owns the state?

Every Prototype test ultimately attempts to answer that question.

---

# Key Takeaways

## What Is The Most Important Prototype Test?

Verify state independence.

---

## Why Is assertNotSame() Insufficient?

It only verifies object identity.

---

## What Causes Most Prototype Bugs?

Shared references and incorrect ownership.

---

## Why Test Business Semantics?

Different domains have different copying rules.

---

## Most Important Insight

Testing Prototype is fundamentally about verifying ownership and isolation of state after copying.

---