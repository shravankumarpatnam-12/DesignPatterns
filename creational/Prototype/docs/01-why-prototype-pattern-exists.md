# Why Prototype Pattern Exists

## Why This Chapter Exists

After learning:

```text
Factory Pattern

Factory Method

Abstract Factory

Builder
```

many developers assume they understand object creation completely.

A common belief emerges:

> If I can create objects using constructors, factories, or builders, why do I need Prototype?

This is a reasonable question.

Factories solve:

```text
Which Object Should Be Created?
```

Builder solves:

```text
How Should A Complex Object Be Constructed?
```

Prototype solves an entirely different problem:

```text
How Can I Avoid Expensive Object Creation?
```

Understanding this problem is the key to understanding Prototype.

---

# The Assumption Most Developers Make

Consider:

```java
User user = new User();
```

Most developers assume:

```text
Object Creation Is Cheap
```

For simple objects, this is true.

Example:

```java
public class Point {

    private int x;
    private int y;
}
```

Creating:

```java
new Point();
```

costs almost nothing.

No design pattern is needed.

---

# The Real World Is Different

Enterprise applications often create objects that are far more expensive.

Example:

```text
Large Configuration Objects

Document Templates

Workflow Definitions

Game Objects

UI Components

Report Structures
```

These objects may require:

```text
Database Access

File Loading

Network Calls

Complex Validation

Heavy Initialization
```

Object creation becomes expensive.

---

# A Real Example

Imagine a report system.

Creating a report template requires:

```text
Load Template File

Load Images

Load Fonts

Load Configuration

Build Internal Structure
```

Code:

```java
ReportTemplate template =
        new ReportTemplate();
```

appears simple.

Internally:

```text
Hundreds Of Operations
```

may execute.

Creating the object repeatedly becomes costly.

---

# The First Question

Suppose we already have:

```java
ReportTemplate template;
```

properly initialized.

Question:

Why build another one from scratch?

Why not simply copy the existing one?

This question led to Prototype.

---

# Creating vs Copying

There are two ways to obtain an object.

---

## Option 1

Create From Scratch

```java
new ReportTemplate();
```

---

## Option 2

Copy Existing Object

```java
template.clone();
```

If creation is expensive,

copying may be dramatically faster.

---

# The Printing Press Analogy

Imagine a printing company.

Suppose you need:

```text
10,000 Brochures
```

Option 1:

```text
Design Every Brochure From Scratch
```

Clearly absurd.

---

Option 2:

```text
Create One Master Copy

Duplicate It
```

Much faster.

Much cheaper.

Prototype follows the same idea.

---

# The Original Motivation

The Gang of Four observed a recurring problem.

Many systems contained objects that were:

```text
Expensive To Create

Cheap To Copy
```

The traditional solution:

```java
new Object();
```

performed unnecessary work.

A prototype could be copied instead.

---

# Example: Game Development

Imagine a game.

Creating an enemy requires:

```text
Load Textures

Load Animations

Load Weapons

Load AI Configuration
```

Creating:

```java
new Enemy();
```

for every enemy becomes expensive.

Alternative:

```java
Enemy enemy =
        existingEnemy.clone();
```

The initialized enemy becomes a template.

New enemies are copies.

This is a classic Prototype use case.

---

# Example: Document Editors

Suppose a company has:

```text
Invoice Template

Contract Template

Proposal Template
```

Every new document begins from an existing template.

The system copies:

```text
Existing Document Structure
```

instead of rebuilding it.

This is Prototype thinking.

---

# Why Factories Cannot Solve This

A common misconception:

> Can't Factory Method solve this?

Not really.

Factory Method answers:

```text
Which Product?
```

Example:

```java
createDocument();
```

---

Prototype answers:

```text
How Can I Reuse Existing Objects?
```

Different problem.

Different solution.

---

# Why Builder Cannot Solve This

Builder focuses on:

```text
Complex Construction
```

Example:

```java
User.builder()
    .name("Shravan")
    .email("shravan@email.com")
    .build();
```

Builder still creates a new object.

Prototype avoids creation entirely.

Again:

```text
Different Problem
```

---

# The Hidden Benefit

Most developers focus on:

```text
Performance
```

Architects notice another benefit:

```text
Preserving Configuration
```

Example:

```java
DatabaseConfiguration productionConfig;
```

Need another similar configuration?

Instead of rebuilding:

```java
new DatabaseConfiguration(...)
```

copy the existing one.

Then modify only what changed.

---

# The Template Concept

Prototype introduces an important idea:

```text
Template Object
```

or:

```text
Master Copy
```

The system creates:

```text
One Well-Configured Object
```

and uses it as the source for future copies.

This becomes the prototype.

---

# Why The Pattern Is Called Prototype

A prototype is:

> An original model from which copies are produced.

Example:

```text
Master Key

Blueprint

Template

Sample
```

Software uses the same concept.

The original object becomes the prototype.

Future objects are derived from it.

---

# Enterprise Example

Imagine a workflow engine.

Workflow creation requires:

```text
Parse XML

Validate Rules

Build State Machine

Load Metadata
```

Expensive process.

Instead:

```text
Create Once

Clone Many Times
```

This dramatically reduces startup cost.

---

# Architect Perspective

Junior developers think:

```text
Prototype
      ↓
clone()
```

Senior developers think:

```text
Prototype
      ↓
Object Copying
```

Architects think:

```text
Prototype
      ↓
Avoid Expensive Construction
```

The focus shifts from implementation to motivation.

---

# The Deep Insight

Factory patterns answer:

```text
Which Object Should Be Created?
```

Builder answers:

```text
How Should It Be Constructed?
```

Prototype answers:

```text
Why Create It Again If A Copy Already Exists?
```

This is the question that gave birth to the pattern.

---

# Key Takeaways

## What Problem Does Prototype Solve?

Expensive object creation.

---

## What Is The Core Idea?

Copy an existing object instead of building a new one.

---

## Why Not Always Use Constructors?

Some objects require expensive initialization.

---

## Why Not Use Builders?

Builders still create new objects.

Prototype reuses existing ones.

---

## Most Important Insight

Prototype exists because copying a well-configured object can be cheaper, faster, and safer than constructing a new one from scratch.

---