# Copying vs Creating

## Why This Chapter Exists

In the previous chapter, we learned a critical lesson:

```text id="f6kpw2"
Object Allocation Is Usually Cheap

Initialization Is Often Expensive
```

This insight naturally leads to an important question:

> If creating objects is expensive, why not simply copy existing ones?

This question sits at the heart of the Prototype Pattern.

Before studying Prototype itself, we must understand the fundamental trade-off between:

```text id="y7xv8n"
Creating

vs

Copying
```

Because architects make this decision constantly.

---

# The Traditional Mindset

Most developers are taught:

```java id="q1h7mk"
new Object();
```

for every new object.

Example:

```java id="i6f7re"
User user =
        new User();
```

Need another user?

```java id="4s8f0m"
User user2 =
        new User();
```

Need 1000 users?

```java id="6ehkwi"
for (...) {

    new User();
}
```

The assumption is:

```text id="3qk4nj"
Creating Is The Default
```

And for many systems, that assumption is correct.

---

# The Hidden Question

Suppose we already have:

```java id="c3wj3q"
User prototype;
```

fully configured.

Question:

Why rebuild everything from scratch?

Why not start from what already exists?

This is where Prototype thinking begins.

---

# Creating From Scratch

Let's visualize object creation.

```java id="2zq8ux"
new ReportTemplate();
```

Internal steps:

```text id="1l8v0l"
Allocate Memory

Load Template

Load Images

Load Fonts

Parse Layout

Validate Structure

Build Object Graph
```

Result:

```text id="4k8mjc"
Fully Initialized Object
```

Every creation repeats the entire process.

---

# Copying Existing Object

Alternative:

```java id="7r5jmt"
ReportTemplate copy =
        template.clone();
```

Internal steps:

```text id="m6fyl9"
Duplicate Existing State
```

Result:

```text id="6ejz5w"
Fully Initialized Object
```

Most expensive work has already been completed.

---

# Real-World Analogy

Imagine building houses.

Option 1:

```text id="qj2nzz"
Design Every House

Create Blueprint

Approve Blueprint

Construct House
```

for each customer.

---

Option 2:

```text id="5myq8f"
Create One Approved Blueprint

Reuse Blueprint
```

for every house.

The second approach is dramatically more efficient.

Prototype follows the same philosophy.

---

# The Cost Comparison

Creating:

```text id="x2d6nm"
Initialization

Validation

Loading

Configuration
```

must occur every time.

---

Copying:

```text id="n3f7jq"
Reuse Existing State
```

avoids repeating the expensive work.

This is the economic advantage of Prototype.

---

# Example: Database Configuration

Suppose:

```java id="5sk3zv"
DatabaseConfiguration config;
```

contains:

```text id="v1y4zt"
Host

Port

Credentials

SSL Settings

Pool Configuration

Timeout Settings
```

Need another configuration with only one change?

Traditional approach:

```java id="i6j5ns"
new DatabaseConfiguration(...);
```

Everything rebuilt.

---

Prototype approach:

```java id="yd9j4z"
DatabaseConfiguration copy =
        config.clone();

copy.setRegion("Europe");
```

Only the difference changes.

Everything else is reused.

---

# Example: Workflow Engines

Workflow definition:

```text id="w4n5yc"
Parse XML

Validate Rules

Build State Machine
```

Creation cost:

```text id="t7v9qo"
High
```

Need 500 workflow instances?

Option 1:

```text id="r8s6yu"
Parse 500 Times
```

---

Option 2:

```text id="0v6txz"
Parse Once

Clone 500 Times
```

The difference can be enormous.

---

# Example: Game Development

Enemy creation:

```text id="n5x7lh"
Load Textures

Load Animations

Load AI Behavior

Load Sound Effects
```

Creating every enemy individually wastes resources.

Alternative:

```text id="x9w4qi"
Create Prototype Enemy

Clone It
```

Only runtime state changes.

Everything else is reused.

---

# The Reuse Principle

Prototype introduces a powerful principle:

```text id="g7c2br"
Reuse Existing Knowledge
```

Instead of rebuilding information:

```text id="h3m4kn"
Copy What Is Already Known
```

This principle appears throughout software architecture.

---

# Caching And Prototype

Notice the similarity.

Caching says:

```text id="n8z1pw"
Don't Recompute
```

Prototype says:

```text id="s4x9ef"
Don't Reconstruct
```

Both patterns optimize by reusing existing work.

---

# The Prototype Mindset

Traditional mindset:

```text id="a5u8rv"
Need New Object

↓

Create New Object
```

Prototype mindset:

```text id="j7p3mf"
Need Similar Object

↓

Copy Existing Object
```

This subtle shift changes the design.

---

# Similarity Is The Key

Prototype becomes valuable when objects are:

```text id="f2w7ds"
Mostly Similar
```

Example:

```text id="s9x4kl"
Invoice #1001

Invoice #1002

Invoice #1003
```

Most fields are identical.

Only a few change.

Copying becomes attractive.

---

# The Cost Of Repetition

Suppose:

```text id="n6j2wy"
Initialization Cost = 100ms
```

Need:

```text id="m4q8pl"
10,000 Objects
```

Total:

```text id="u3y7rd"
1,000,000ms
```

or roughly:

```text id="v8c5kn"
16 Minutes
```

If copying requires:

```text id="p9f6tb"
2ms
```

the difference is dramatic.

---

# Why Not Always Copy?

Important question.

If copying is faster, why not use it everywhere?

Because copying introduces its own challenges:

```text id="q2k9ev"
Shared State

Reference Problems

Unexpected Side Effects

Copy Complexity
```

Prototype is not free.

Every optimization has trade-offs.

---

# The Identity Problem

Consider:

```java id="z8v5tr"
User user1 =
        prototype.clone();

User user2 =
        prototype.clone();
```

Question:

Should:

```text id="w1m7xy"
IDs

Timestamps

Audit Fields
```

also be copied?

Sometimes yes.

Sometimes no.

Copying is more complicated than it first appears.

---

# Copying Business Objects

Example:

```java id="k4j8pt"
Order prototype;
```

Should clone also copy:

```text id="x7n3zs"
Order ID?
```

Usually not.

Because:

```text id="v5q9lb"
Identity
```

must remain unique.

This is one reason Prototype can be tricky.

---

# Prototype Is Not About Performance Alone

Many developers believe:

```text id="t2v6kn"
Prototype
      =
Performance Optimization
```

Partially true.

But there is another benefit:

```text id="d8x4mw"
Configuration Reuse
```

Example:

```java id="h5n8qp"
CloudConfiguration prototype;
```

Instead of rebuilding configuration repeatedly:

```text id="r3j6yf"
Copy

Modify

Use
```

This improves consistency.

---

# Enterprise Example

Insurance policy template:

```text id="y9v3jk"
Coverage Rules

Compliance Rules

Tax Rules

Risk Settings
```

Every new policy starts from a template.

The system copies existing knowledge.

Not because creation is impossible.

Because recreation is unnecessary.

---

# Copying As Knowledge Transfer

This is a deeper way to think about Prototype.

The prototype contains:

```text id="c4p8ns"
Knowledge

Configuration

Validation

Defaults
```

Cloning transfers that knowledge.

The copy inherits everything already learned.

---

# Architect Perspective

Junior developers think:

```text id="m7k5xp"
Copy Object
```

Senior developers think:

```text id="v2q9jt"
Reuse Initialization
```

Architects think:

```text id="b6n3wd"
Reuse Knowledge
```

because most enterprise objects contain valuable configuration and business rules.

---

# Creating vs Copying Matrix

| Scenario                 | Create       | Copy            |
| ------------------------ | ------------ | --------------- |
| Simple Object            | ✅ Best       | ❌ Overkill      |
| Expensive Initialization | ❌ Costly     | ✅ Better        |
| Similar Objects          | ❌ Repetitive | ✅ Efficient     |
| Unique Objects           | ✅ Better     | ❌ Limited Value |
| Heavy Configuration      | ❌ Rebuild    | ✅ Reuse         |

---

# The Deep Insight

The Prototype Pattern is not really about:

```text id="n4x8wr"
Cloning
```

It is about:

```text id="p7m5qy"
Reusing Existing Knowledge
```

Instead of repeatedly rebuilding an object's state, we preserve and duplicate it.

This idea is the foundation of the entire pattern.

---

# Key Takeaways

## What Is The Core Trade-Off?

Creating objects versus copying existing ones.

---

## Why Is Copying Attractive?

It avoids repeating expensive initialization.

---

## When Is Prototype Most Valuable?

When objects are expensive to create and highly similar.

---

## Why Not Always Use Prototype?

Copying introduces identity and state-management challenges.

---

## Most Important Insight

Prototype is fundamentally about reusing knowledge and configuration rather than rebuilding it repeatedly.

---
