# Understanding the Core Idea

## Before Learning Flyweight

Most developers memorize:

```text id="jv80qx"
Flyweight

↓

Factory

↓

Shared Objects
```

and move on.

The problem?

They never understand:

```text id="p56ewx"
Why state must be split.
```

Flyweight is impossible to understand until you understand:

```text id="9i0v3w"
Intrinsic State

and

Extrinsic State
```

Everything else follows from that idea.

---

# Step 1: Start With A Normal Object

Suppose we're building a map application.

Every map marker has:

```java id="m6n78w"
public class MapMarker {

    private String iconType;

    private byte[] iconImage;

    private int x;

    private int y;
}
```

Looks reasonable.

---

# Example Data

Restaurant marker:

```text id="l15f1r"
iconType = RESTAURANT

iconImage = restaurant.png

x = 100

y = 200
```

---

Another restaurant:

```text id="c0j41j"
iconType = RESTAURANT

iconImage = restaurant.png

x = 500

y = 900
```

---

Notice Something?

```text id="q3n2iw"
iconType

iconImage
```

are identical.

Only:

```text id="i6r3ph"
x

y
```

change.

---

# The Hidden Waste

Suppose:

```text id="n8z8b7"
500,000 Restaurants
```

exist.

Naive design creates:

```text id="q2t2la"
500,000 copies
```

of:

```text id="mkb8u1"
restaurant.png
```

This is wasteful.

---

# The Key Observation

Not all state behaves the same.

Some state:

```text id="l2z7w9"
Never Changes
```

Some state:

```text id="y9v4sx"
Changes Per Object
```

These should not be stored together.

---

# Intrinsic State

Definition:

```text id="mvb2po"
State that can be shared.
```

Characteristics:

* Immutable
* Common to many objects
* Independent of context

Example:

```text id="v7f3ij"
Restaurant Icon

Restaurant Image

Restaurant Metadata
```

Every restaurant marker shares these.

---

# Extrinsic State

Definition:

```text id="h0s6ky"
State supplied by the client.
```

Characteristics:

* Changes frequently
* Context dependent
* Cannot be shared

Example:

```text id="u8r1mb"
X Coordinate

Y Coordinate

Zoom Level
```

Every marker has different values.

---

# The Flyweight Split

Instead of:

```java id="b5x2no"
MapMarker {

    iconImage
    x
    y
}
```

Separate the state.

---

## Flyweight

```java id="q4y3an"
MapIcon {

    iconType
    iconImage
}
```

Shared.

---

## Context

```java id="v1j8tc"
MapMarker {

    MapIcon icon

    x
    y
}
```

Unique.

---

# Visual Representation

Without Flyweight:

```text id="z5u2on"
Marker 1
    iconImage

Marker 2
    iconImage

Marker 3
    iconImage
```

Repeated data.

---

With Flyweight:

```text id="n0t8wl"
        RestaurantIcon
              |
     -------------------
     |        |        |
 Marker1 Marker2 Marker3
```

One shared icon.

Many locations.

---

# Why Sharing Works

Suppose:

```text id="m4k9zi"
Restaurant Icon
```

changes.

All restaurant markers should use:

```text id="w7n1bo"
The Same Icon
```

anyway.

Sharing is perfectly safe.

---

# The Flyweight Rule

Only share state that is:

```text id="r6v7uy"
Immutable
```

If state changes:

```text id="o9j2kb"
Sharing becomes dangerous.
```

---

# Bad Flyweight Example

Imagine:

```java id="g8x4pc"
class User {

    String name;
}
```

Shared between users.

Problem:

```java id="l7z5ae"
user.setName("John");
```

Now everyone sees:

```text id="f0n3vm"
John
```

Sharing mutable state causes corruption.

---

# Why Extrinsic State Exists

A common beginner question:

> Why not store everything in the Flyweight?

Because:

```text id="t4b9ri"
Not everything can be shared.
```

Example:

Restaurant location:

```text id="e6p1ka"
100,200
```

and

```text id="v3m7zs"
500,900
```

must remain different.

The client provides these values when needed.

---

# Another Example: Text Editor

Document:

```text id="w8k2nd"
HELLO
```

Naive design:

```java id="y1f8lo"
Character {

    char value;

    int position;
}
```

For every letter.

---

Notice:

```text id="d7r5mu"
'H'
```

never changes.

Can be shared.

---

Flyweight:

```java id="u6n4ki"
CharacterGlyph {

    char value;
}
```

Context:

```java id="o2v9wr"
CharacterPosition {

    CharacterGlyph glyph;

    int position;
}
```

Now:

```text id="a4m6tx"
Millions of H characters
```

share one glyph.

---

# The Factory Becomes Necessary

Question:

```text id="q9u7pj"
How do we guarantee sharing?
```

Without control:

```java id="v5c1or"
new RestaurantIcon()
```

creates duplicates.

Flyweight requires:

```text id="r3n9ks"
Centralized Creation
```

This is usually done through:

```text id="t8p4zm"
Flyweight Factory
```

---

# Why Factory Is Essential

Factory maintains:

```text id="c2y7lh"
Shared Object Registry
```

Example:

```text id="j0m8an"
RESTAURANT

↓

RestaurantIcon
```

If the icon already exists:

```text id="e7r4bo"
Return Existing Object
```

Otherwise:

```text id="m5u1cq"
Create New Flyweight
```

---

# The Real Formula

Without Flyweight:

```text id="w2r9el"
N Objects

×

Shared Data
```

Memory grows rapidly.

---

With Flyweight:

```text id="b8x4iy"
Shared Data

+

N Context Objects
```

Huge reduction.

---

# Common Misunderstanding

Many developers think:

```text id="h5u7zn"
Flyweight = Cache
```

Not exactly.

A cache stores:

```text id="x2j6pl"
Reusable Results
```

Flyweight stores:

```text id="v4n1qo"
Reusable State
```

Different goals.

We'll compare them later.

---

# The Mental Model

Think:

```text id="z6o2kt"
Template
```

and

```text id="i9w5rm"
Usage
```

The Flyweight is the template.

The Context is the usage instance.

---

# What Architects Notice

Junior developers see:

```java id="q7v1sn"
MapIcon
```

Architects see:

```text id="f3n8kd"
A shared immutable resource.
```

---

Junior developers see:

```java id="u4x9ml"
CharacterGlyph
```

Architects see:

```text id="b1k6yo"
Memory deduplication.
```

---

The domain is irrelevant.

The optimization principle is what matters.

---

# The Bigger Insight

Most applications optimize:

```text id="k2t4jq"
Algorithms

Queries

Network Calls
```

Flyweight optimizes:

```text id="w5m8ar"
Object Memory Footprint
```

It is one of the few patterns primarily focused on memory efficiency.

---

# The Architectural Insight

Flyweight works because it recognizes:

```text id="z1r7mv"
Not all state belongs
inside every object.
```

Some state belongs:

```text id="d8u2ok"
Inside the shared flyweight.
```

Some state belongs:

```text id="p6v4jt"
Outside in the context.
```

This separation is the heart of the pattern.

Once you understand that, you've understood Flyweight.

---

# Key Takeaways

* Flyweight depends on separating intrinsic and extrinsic state.
* Intrinsic state is shared and immutable.
* Extrinsic state is supplied by the client and varies by context.
* Sharing mutable state is dangerous.
* Flyweight factories enforce reuse.
* The pattern reduces memory consumption by avoiding duplicate data.
* State separation is the core idea behind Flyweight.
