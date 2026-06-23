# Why Flyweight Exists

## The Real Problem

Most design patterns solve:

```text
Flexibility Problems

Creation Problems

Behavior Problems
```

Flyweight solves something different:

```text
Memory Problems
```

The pattern exists because applications sometimes create:

```text
Thousands

Millions

Even Billions
```

of similar objects.

At that scale:

```text
Memory becomes a bottleneck.
```

---

# A Simple Example

Suppose we build a text editor.

Document:

```text
HELLO WORLD
```

Naive design:

```java
Character character1 =
        new Character('H');

Character character2 =
        new Character('E');

Character character3 =
        new Character('L');
```

and so on.

---

# Looks Fine

For:

```text
10 Characters
```

No problem.

---

For:

```text
1,000 Characters
```

Still fine.

---

For:

```text
10 Million Characters
```

Now memory matters.

---

# What Is Being Repeated?

Consider:

```text
A
A
A
A
A
A
A
```

Naive approach:

```java
new Character('A');
new Character('A');
new Character('A');
new Character('A');
```

Millions of identical objects.

Huge waste.

---

# The Key Observation

Every object contains:

```text
State
```

Some state changes.

Some state does not.

Example:

```text
Character = A
```

never changes.

---

But:

```text
Position

Font Size

Color
```

may vary.

---

# Two Types Of State

This is the most important Flyweight concept.

---

## Intrinsic State

Shared state.

Example:

```text
Character = A
```

Every A looks the same.

Can be shared.

---

## Extrinsic State

Context-specific state.

Example:

```text
Position = 10

Color = Red

Font Size = 14
```

Changes for each occurrence.

Cannot be shared.

---

# Visual Example

Document:

```text
A A A A
```

Without Flyweight:

```text
[A]
[A]
[A]
[A]
```

Four separate objects.

---

With Flyweight:

```text
           [A]
            |
     ----------------
     |      |      |
Position Position Position
```

One shared object.

Multiple contexts.

---

# Why This Matters

Suppose:

```text
1 Million Characters
```

Each object consumes:

```text
100 Bytes
```

Memory:

```text
100 MB
```

---

If only:

```text
26 Unique Letters
```

exist:

```text
A-Z
```

We can store:

```text
26 Flyweights
```

instead of:

```text
1 Million Objects
```

Huge savings.

---

# Another Example

Imagine a game.

Forest:

```text
1,000,000 Trees
```

Naive design:

```java
new Tree(
    "Oak",
    texture,
    model,
    color
);
```

for every tree.

---

Problem

Each tree stores:

```text
Model

Texture

Color
```

repeatedly.

Massive waste.

---

# Better Idea

Store:

```text
Oak Tree Definition
```

once.

Share it.

Each tree only stores:

```text
X Coordinate

Y Coordinate
```

This is Flyweight.

---

# The General Pattern

Without Flyweight:

```text
Object

contains

Shared Data

+

Unique Data
```

for every instance.

---

With Flyweight:

```text
Flyweight

contains

Shared Data
```

and

```text
Context

contains

Unique Data
```

The two are combined when needed.

---

# What We Really Want

Instead of:

```text
1,000,000 Oak Objects
```

we want:

```text
1 Oak Definition
```

shared by:

```text
1,000,000 Tree Instances
```

---

# Why Not Use Singleton?

Common beginner question.

Singleton gives:

```text
One Object
```

for the entire application.

---

Flyweight gives:

```text
One Object

Per Shared State
```

Example:

```text
Oak

Pine

Birch
```

Three flyweights.

Not one.

---

# The Hidden Cost Of Object Creation

Every object introduces:

```text
Memory

GC Pressure

Allocation Cost

Cache Misses
```

Creating millions of identical objects hurts performance.

Flyweight reduces this burden.

---

# Real World Analogy

Think about:

```text
Country Flags
```

At a sporting event.

Without Flyweight:

```text
Each person owns
a complete flag design.
```

Wasteful.

---

With Flyweight:

```text
One flag design

Shared by everyone.
```

Each person only stores:

```text
Seat Number

Location
```

The shared design remains centralized.

---

# Where Flyweight Appears

You use Flyweight more often than you think.

Examples:

### String Pool

```java
String s1 = "Hello";
String s2 = "Hello";
```

Both reference the same object.

---

### Integer Cache

```java
Integer a = 100;
Integer b = 100;
```

Shared cached object.

---

### Game Engines

```text
Trees

Bullets

Particles

NPC Types
```

---

### IDEs

```text
Syntax Highlighting

AST Nodes

Editor Tokens
```

---

### GIS Systems

```text
Road Types

Map Symbols

Icons
```

---

# The Core Insight

Factory taught us:

```text
How to create objects.
```

Decorator taught us:

```text
How to add behavior.
```

Proxy taught us:

```text
How to control access.
```

Flyweight teaches:

```text
How to avoid creating
unnecessary objects.
```

That simple idea can save gigabytes of memory in large systems.

---

# Key Takeaways

* Flyweight solves memory optimization problems.
* It is useful when large numbers of similar objects exist.
* Shared state becomes intrinsic state.
* Context-specific state becomes extrinsic state.
* Flyweight dramatically reduces object count.
* String Pool and Integer Cache are classic Java examples.
* The essence of Flyweight is sharing immutable data efficiently.
