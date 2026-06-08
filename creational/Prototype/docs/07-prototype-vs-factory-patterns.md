# Prototype vs Factory Patterns

## Why This Chapter Exists

After learning Prototype, many developers become confused.

A common question appears:

> Factory creates objects.
>
> Builder creates objects.
>
> Prototype creates objects.
>
> Aren't they all solving the same problem?

At a very high level:

```text id="p4m8xn"
Yes
```

All belong to:

```text id="t7v2qp"
Creational Patterns
```

However, the reason they create objects is completely different.

This chapter is important because many developers misuse Prototype where Factory Method should be used.

Others use Builder where Prototype is the better solution.

Architects must understand:

```text id="x5k3rw"
Not Just How Patterns Work

But Why They Exist
```

The motivation behind a pattern determines when it should be used.

---

# The Evolution Of Creation Problems

Let's revisit the journey.

---

Simple Factory solved:

```text id="n8v4pq"
Centralized Creation
```

---

Factory Method solved:

```text id="m2k7xt"
Extensibility
```

---

Abstract Factory solved:

```text id="q4v9rn"
Product Family Consistency
```

---

Builder solved:

```text id="r7k2pw"
Complex Object Construction
```

---

Prototype solved:

```text id="s3v8qx"
Expensive Object Creation
```

Notice something important.

Each pattern solved a different problem.

---

# The Core Difference

Factory patterns ask:

```text id="x9m4rt"
What Should Be Created?
```

Prototype asks:

```text id="k5v2qn"
Why Create It Again?
```

This is the most important distinction.

---

# Simple Factory Thinking

Question:

```text id="p8k4rw"
Which Notification Service?
```

Options:

```text id="m3v7qn"
Email

SMS

Push Notification
```

Factory decides.

Problem solved:

```text id="r5k2px"
Object Selection
```

---

# Prototype Thinking

Question:

```text id="x7v4qm"
Why Build Another Notification Template?
```

Prototype decides.

Problem solved:

```text id="n2k8rw"
State Reuse
```

Different problem.

Different solution.

---

# Factory Method Thinking

Question:

```text id="s4v7qp"
Which Payment Gateway?
```

Options:

```text id="p9k3rw"
Stripe

PayPal

Razorpay
```

Factory Method chooses implementation.

---

Prototype Thinking

Question:

```text id="x2v8qn"
Need Another Stripe Configuration?
```

Instead of:

```java id="j4m7pw"
new StripeConfiguration();
```

Copy:

```java id="t5v2qr"
existingConfig.copy();
```

The concern shifts from:

```text id="r8k4px"
Selection
```

to:

```text id="m3v9qn"
Duplication
```

---

# Abstract Factory Thinking

Question:

```text id="p7k2rw"
Which Ecosystem?
```

Options:

```text id="x5v8qm"
AWS Family

Azure Family

Google Family
```

Abstract Factory selects:

```text id="n4k7px"
Entire Product Families
```

---

Prototype Thinking

Question:

```text id="m8v2qn"
Need Another AWS Configuration?
```

Copy existing AWS ecosystem configuration.

Again:

```text id="t3k9rw"
Reuse
```

becomes the goal.

---

# Builder Thinking

Question:

```text id="p2v7qm"
How Should This Complex Object Be Constructed?
```

Example:

```java id="x4k8pw"
User.builder()
    .name("Shravan")
    .email("shravan@email.com")
    .country("India")
    .build();
```

Builder focuses on:

```text id="r6v2qn"
Construction
```

---

Prototype focuses on:

```text id="n8k4rw"
Duplication
```

Very different concern.

---

# Visual Comparison

Factory:

```text id="s3v7qm"

Need Object

↓

Select Type

↓

Create Object
```

---

Builder:

```text id="p5k2rw"

Need Object

↓

Assemble State

↓

Build Object
```

---

Prototype:

```text id="x7v4qn"

Need Object

↓

Find Existing Object

↓

Copy Object
```

Different workflow.

Different mindset.

---

# Example: Document System

Requirement:

```text id="n2k8rw"
Create Invoice
```

---

Factory Method:

```java id="t4v7qp"
createDocument();
```

Selects:

```text id="m5k3rw"
Invoice

Contract

Proposal
```

---

Prototype:

```java id="x8v2qn"
invoiceTemplate.copy();
```

Creates:

```text id="r3k7px"
Another Invoice
```

using an existing template.

One selects.

One duplicates.

---

# Example: Cloud Platforms

Factory:

```java id="p7v4qm"
createStorage();
```

Result:

```text id="n5k2rw"
AWS Storage

Azure Storage

GCP Storage
```

---

Prototype:

```java id="x2v8qn"
existingAwsConfig.copy();
```

Result:

```text id="m8k4px"
Another AWS Configuration
```

Same family.

Different instance.

---

# Example: Game Development

Factory Method:

```java id="r4v7qm"
createEnemy();
```

Select:

```text id="s6k2rw"
Zombie

Dragon

Robot
```

---

Prototype:

```java id="p9v4qn"
dragonPrototype.copy();
```

Create:

```text id="x3k7px"
Another Dragon
```

No selection.

Only duplication.

---

# Creation Source Comparison

Factory source:

```text id="n4v8qm"
Class
```

Example:

```java id="t2k7rw"
new Dragon();
```

---

Prototype source:

```text id="m7v3qn"
Existing Object
```

Example:

```java id="p5k8px"
dragonPrototype.copy();
```

This is a fundamental difference.

---

# Knowledge Source Comparison

Factory:

```text id="r2v7qm"
Class Contains Knowledge
```

Example:

```java id="x4k3rw"
Dragon.class
```

defines creation.

---

Prototype:

```text id="n8v5qn"
Object Contains Knowledge
```

Example:

```java id="m3k7px"
dragonPrototype
```

already contains configuration.

Knowledge moves from:

```text id="t7v2qm"
Class
```

to:

```text id="p4k8rw"
Instance
```

This is a profound architectural shift.

---

# Runtime Flexibility

Factories require:

```text id="x5v3qn"
Class Definitions
```

Prototype requires:

```text id="n2k7px"
Object Instances
```

This means prototypes can often be modified:

```text id="r8v4qm"
At Runtime
```

without changing classes.

This is one reason game engines frequently use Prototype.

---

# Prototype Registry vs Factory Registry

Factory registry:

```text id="m4k2rw"
Type Name

↓

Class
```

---

Prototype registry:

```text id="p7v8qn"
Type Name

↓

Object Instance
```

Subtle difference.

Huge impact.

---

# When Factory Is Better

Use Factory when:

```text id="x3k7px"
Need Different Types

Need Extensibility

Need Product Selection
```

Examples:

```text id="n5v2qm"
Database Drivers

Payment Gateways

Notification Providers
```

---

# When Builder Is Better

Use Builder when:

```text id="r8k4rw"
Object Has Many Parameters

Construction Is Complex

Readability Matters
```

Examples:

```text id="m2v7qn"
User

Order

Configuration Objects
```

---

# When Prototype Is Better

Use Prototype when:

```text id="p4k8px"
Objects Are Expensive To Create

Objects Are Similar

State Reuse Is Valuable
```

Examples:

```text id="x7v3qm"
Templates

Workflows

Game Objects

Configurations
```

---

# Common Mistake

Developers sometimes replace:

```java id="n2k7rw"
new User()
```

with:

```java id="m8v4qn"
userPrototype.copy();
```

even when:

```text id="p5k2px"
User Creation Is Cheap
```

No benefit exists.

Prototype introduces unnecessary complexity.

Always justify the pattern.

---

# The Relationship Between Builder And Prototype

An advanced pattern combination.

Create prototype using Builder:

```java id="x4v7qm"
User prototype =
        User.builder()
            .name("Default User")
            .country("India")
            .build();
```

Then:

```java id="r3k8rw"
prototype.copy();
```

for future instances.

Many enterprise systems combine these patterns.

---

# The Relationship Between Factory And Prototype

Factories can return:

```text id="m7v2qn"
Copies Of Prototypes
```

instead of:

```text id="p8k4px"
New Objects
```

Example:

```java id="n4v7qm"
createDocument();
```

internally performs:

```java id="t2k8rw"
prototype.copy();
```

The patterns can collaborate.

They are not competitors.

---

# Architect Perspective

Junior developers ask:

```text id="x5v3qn"
How Do I Create The Object?
```

Senior developers ask:

```text id="r8k2px"
How Should It Be Constructed?
```

Architects ask:

```text id="m4v7qm"
Where Should Knowledge Live?
```

Factories place knowledge in:

```text id="p7k8rw"
Classes
```

Prototypes place knowledge in:

```text id="n2v4qn"
Instances
```

This distinction drives many architectural decisions.

---

# Pattern Comparison Matrix

| Pattern          | Primary Question            |
| ---------------- | --------------------------- |
| Simple Factory   | Which Object?               |
| Factory Method   | Which Implementation?       |
| Abstract Factory | Which Family?               |
| Builder          | How To Construct?           |
| Prototype        | Why Rebuild Existing State? |

This table summarizes the entire creational pattern family.

---

# The Deep Insight

Factory patterns create objects by:

```text id="x8k3px"
Executing Creation Logic
```

Prototype creates objects by:

```text id="m5v7qm"
Reusing Existing State
```

The difference is not technical.

The difference is philosophical.

One creates.

One duplicates.

Understanding that distinction is the key to mastering Prototype.

---

# Key Takeaways

## What Problem Do Factory Patterns Solve?

Object selection.

---

## What Problem Does Builder Solve?

Complex construction.

---

## What Problem Does Prototype Solve?

State reuse and expensive creation.

---

## Can Prototype Replace Factories?

No.

They solve different problems.

---

## Most Important Insight

Factories decide what to create.

Prototype decides whether creation is necessary at all.

---