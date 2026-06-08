# Understanding Prototype Pattern

## Why This Chapter Exists

In the previous chapters, we discovered:

```text id="p1x7cm"
Why Prototype Exists

The Cost Of Object Creation

Copying vs Creating
```

We learned that Prototype was created because:

```text id="s9w4nk"
Some Objects Are Expensive To Build

But Cheap To Copy
```

Now we are ready to study the actual pattern.

This chapter answers:

> What exactly is the Prototype Pattern?

More importantly:

> How does the pattern solve the expensive object creation problem?

---

# The Problem Recap

Suppose we have:

```java id="y8r2dt"
ReportTemplate template =
        new ReportTemplate();
```

Initialization performs:

```text id="x4m7qs"
Load Files

Parse Layout

Validate Structure

Build Object Graph
```

Creation is expensive.

Need another similar object?

Traditional approach:

```java id="f6j3av"
new ReportTemplate();
```

All expensive work repeats.

Prototype proposes:

```java id="m2n8pl"
template.clone();
```

Reuse existing knowledge.

Avoid rebuilding.

---

# GoF Definition

The Gang of Four defines Prototype as:

> Specify the kinds of objects to create using a prototypical instance, and create new objects by copying this prototype.

This definition is accurate.

But not beginner friendly.

---

# Beginner Definition

Prototype is:

> A pattern that creates new objects by copying existing objects.

Simple.

Direct.

Easy to understand.

---

# Architect Definition

Prototype is:

> A mechanism for reusing expensive initialization and configuration by duplicating preconfigured instances.

Notice something.

The architect definition focuses on:

```text id="j4n9ec"
Cost

Reuse

Knowledge Preservation
```

not cloning itself.

---

# Core Idea

Traditional creation:

```text id="c7k3ws"
Need Object

↓

Construct Object
```

Prototype creation:

```text id="n8p5fy"
Need Object

↓

Copy Existing Object
```

This is the entire pattern.

Everything else is implementation details.

---

# Pattern Structure

Prototype consists of four participants.

```text id="b2x7ml"
Prototype Interface

Concrete Prototype

Client

Clone Operation
```

Let's study each one.

---

# Step 1: Prototype Interface

The interface defines:

```text id="v4m9rk"
How Objects Are Copied
```

Example:

```java id="u7j5pq"
public interface Prototype {

    Prototype clone();
}
```

Every prototype must provide a copy operation.

---

# Why An Interface?

Without an interface:

```java id="e8k2zn"
User

Document

Configuration

Workflow
```

would all implement copying differently.

The interface standardizes behavior.

---

# Step 2: Concrete Prototype

Example:

```java id="a3r8xd"
public class Document
        implements Prototype {

    private String title;

    private String content;

    @Override
    public Document clone() {

        Document copy =
                new Document();

        copy.title =
                this.title;

        copy.content =
                this.content;

        return copy;
    }
}
```

This object knows how to copy itself.

---

# Important Observation

Notice something.

The client does not know:

```text id="q6v1mt"
How Copying Works
```

The object knows.

This is an important design principle.

---

# Encapsulating Copy Logic

Bad:

```java id="p4n8sk"
Document copy =
        new Document();

copy.setTitle(
        original.getTitle());

copy.setContent(
        original.getContent());
```

Client performs copying.

Knowledge becomes duplicated.

---

Good:

```java id="g5m3rw"
Document copy =
        original.clone();
```

The object owns its duplication logic.

Encapsulation improves.

---

# Step 3: Client

Client code:

```java id="x9q2lf"
Document copy =
        prototype.clone();
```

Simple.

No construction details.

No initialization logic.

No configuration rebuilding.

---

# UML Diagram

```text id="n7v5bz"

        Prototype
             ▲
             │
             │
      ┌──────┴──────┐
      │             │
      ▼             ▼

 Document      Workflow

      ▲
      │
      │ clone()
      │
      ▼

     Client
```

The client depends on the abstraction.

Not on construction details.

---

# Prototype Lifecycle

Step 1:

```text id="d2j7xt"
Create Original Object
```

---

Step 2:

```text id="j8m4pv"
Configure Object
```

---

Step 3:

```text id="m1q9kr"
Use As Prototype
```

---

Step 4:

```text id="z6p2nc"
Clone Prototype
```

---

Step 5:

```text id="c3v8ry"
Modify Differences
```

This lifecycle appears repeatedly in enterprise systems.

---

# Example: Report Templates

Prototype:

```text id="e9r4mx"
Invoice Template
```

Need:

```text id="g7k2pw"
Invoice #1001

Invoice #1002

Invoice #1003
```

Instead of rebuilding:

```text id="z4n8tv"
Copy Existing Template
```

Modify:

```text id="h5j7kr"
Customer Name

Amount

Date
```

Everything else remains unchanged.

---

# Example: Game Development

Prototype enemy:

```text id="w2m6qp"
Texture

Animation

Weapon

AI Settings
```

Clone:

```java id="t8p4rv"
Enemy enemy =
        prototype.clone();
```

Modify:

```text id="v3k9dj"
Position

Health

Level
```

Expensive setup reused.

---

# Example: Workflow Engines

Workflow creation:

```text id="b9x4rl"
Parse XML

Validate Rules

Build State Machine
```

Prototype:

```text id="j6m2zt"
Preconfigured Workflow
```

New instances:

```java id="f4n8yk"
workflow.clone();
```

Initialization avoided.

---

# The Hidden Power

Most developers see:

```text id="n2v7pw"
Object Copying
```

Architects see:

```text id="h8m5qs"
Configuration Reuse
```

This distinction is important.

The prototype preserves:

```text id="r4j9kx"
Defaults

Rules

Validation

Knowledge
```

The copy inherits everything.

---

# Prototype Registry

Many enterprise systems store prototypes.

Example:

```java id="p7m3tw"
Map<String, Prototype>
```

Contents:

```text id="u5k8rx"
invoice

contract

proposal
```

Client requests:

```java id="k9n4pz"
registry.get("invoice")
        .clone();
```

This is called:

```text id="f2v6mj"
Prototype Registry
```

and is a common implementation.

---

# Why Prototype Registry Exists

Without registry:

```java id="z3q7ly"
new InvoiceTemplate()

new ContractTemplate()

new ProposalTemplate()
```

must be managed manually.

Registry centralizes prototypes.

---

# Relationship To Factories

A common misconception:

> Prototype replaces factories.

Wrong.

Factories answer:

```text id="q8v4nm"
Which Object?
```

Prototype answers:

```text id="r7j2xt"
Copy Which Existing Object?
```

Different problems.

Different patterns.

---

# Relationship To Builder

Builder:

```text id="w5m9kp"
Construct Complex Object
```

Prototype:

```text id="x4n7rz"
Copy Existing Object
```

Builder creates.

Prototype duplicates.

They solve different problems.

---

# Advantages

## Faster Creation

Expensive initialization is reused.

---

## Reduced Construction Complexity

Clients avoid construction logic.

---

## Configuration Reuse

Existing knowledge is preserved.

---

## Runtime Flexibility

Objects can be cloned dynamically.

---

## Lower Coupling

Clients depend on prototype abstraction.

---

# Disadvantages

Every pattern introduces trade-offs.

---

## Copying Can Be Complex

Some objects contain:

```text id="s6p8jq"
Nested Objects

Collections

References
```

Copying correctly becomes difficult.

---

## Identity Problems

Should IDs be copied?

Should timestamps be copied?

The answer depends on context.

---

## Hidden State

Objects may contain:

```text id="t3k5rx"
Caches

Connections

Resources
```

Blind copying may be dangerous.

---

## Clone Semantics

Different developers may expect different copying behavior.

This can create confusion.

---

# The Most Important Question

Whenever Prototype appears, ask:

> What exactly should be copied?

This question determines the correctness of the implementation.

Many Prototype bugs originate here.

---

# Architect Perspective

Junior developers think:

```text id="u8m3qw"
Prototype
      ↓
clone()
```

Senior developers think:

```text id="v2j7kp"
Reuse Expensive Initialization
```

Architects think:

```text id="n4x9rt"
Preserve Knowledge

Reuse Configuration

Avoid Rebuilding State
```

The abstraction becomes much larger than cloning.

---

# The Deep Insight

The Prototype Pattern is not fundamentally about:

```text id="g7m4px"
Copying Objects
```

It is about:

```text id="k8v2rq"
Reusing Existing State
```

instead of repeatedly reconstructing it.

Cloning is merely the mechanism.

State reuse is the goal.

---

# Key Takeaways

## What Is Prototype?

A pattern that creates new objects by copying existing ones.

---

## What Problem Does It Solve?

Expensive or repetitive object construction.

---

## What Is The Core Mechanism?

Clone an existing prototype.

---

## What Is The Biggest Advantage?

Reuse initialization and configuration.

---

## What Is The Biggest Challenge?

Determining what should and should not be copied.

---

## Most Important Insight

Prototype is not about cloning.

It is about avoiding unnecessary reconstruction of already-known state.

---