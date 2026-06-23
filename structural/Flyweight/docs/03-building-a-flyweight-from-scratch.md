# Building a Flyweight From Scratch

## The Goal

Most tutorials show:

```java
Character a1 = factory.get('A');
Character a2 = factory.get('A');
```

and stop there.

That demonstrates sharing.

It does not demonstrate how Flyweight is built in production systems.

Real Flyweight implementations must handle:

* Object sharing
* Factory management
* Memory optimization
* Thread safety
* Cache growth
* Immutable state

Let's build one properly.

---

# Problem Statement

Suppose we are building a map application.

The map contains:

```text
Restaurants

Hospitals

Hotels

Airports
```

Potentially:

```text
Millions of markers
```

on screen.

---

# Naive Design

```java
public class MapMarker {

    private final String type;

    private final byte[] icon;

    private final int x;

    private final int y;
}
```

Every marker stores:

```text
Type

Icon

Coordinates
```

---

# The Waste

Imagine:

```text
1,000,000 Restaurants
```

Every object contains:

```text
restaurant.png
```

repeatedly.

Huge memory waste.

---

# Step 1

## Identify Intrinsic State

Ask:

```text
What data is identical
for every Restaurant?
```

Answer:

```text
Type

Icon

Display Metadata
```

This becomes Flyweight state.

---

# Step 2

## Create Flyweight

```java
public final class MapIcon {

    private final String type;

    private final byte[] icon;

    public MapIcon(
            String type,
            byte[] icon) {

        this.type = type;
        this.icon = icon;
    }

    public String getType() {
        return type;
    }

    public byte[] getIcon() {
        return icon;
    }
}
```

Notice:

```text
Immutable
```

No setters.

This is critical.

---

# Why Immutable?

Suppose:

```java
restaurantIcon.setType("Hotel");
```

Every shared object becomes corrupted.

Flyweights should almost always be:

```text
Immutable
```

---

# Step 3

## Create Context Object

The context stores:

```text
Extrinsic State
```

Example:

```java
public class MapMarker {

    private final MapIcon icon;

    private final int x;

    private final int y;

    public MapMarker(
            MapIcon icon,
            int x,
            int y) {

        this.icon = icon;
        this.x = x;
        this.y = y;
    }
}
```

Now:

```text
Coordinates

remain unique
```

while:

```text
Icon Data

is shared.
```

---

# Step 4

## Build Flyweight Factory

Without a factory:

```java
new MapIcon(...)
```

creates duplicates.

We need:

```text
Controlled Creation
```

---

## Implementation

```java
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;

public class MapIconFactory {

    private static final Map<String, MapIcon>
            CACHE =
            new ConcurrentHashMap<>();

    public static MapIcon getIcon(
            String type) {

        return CACHE.computeIfAbsent(
                type,
                MapIconFactory::createIcon
        );
    }

    private static MapIcon createIcon(
            String type) {

        System.out.println(
                "Loading icon: " + type
        );

        byte[] imageData =
                loadFromDisk(type);

        return new MapIcon(
                type,
                imageData
        );
    }

    private static byte[] loadFromDisk(
            String type) {

        return new byte[1024];
    }
}
```

---

# Why ConcurrentHashMap?

Multiple threads may request:

```text
Restaurant Icon
```

simultaneously.

Without synchronization:

```text
Duplicate Flyweights
```

may be created.

The factory must be thread-safe.

---

# Step 5

## Create Markers

```java
MapIcon restaurantIcon =
        MapIconFactory.getIcon(
                "RESTAURANT"
        );

MapMarker marker1 =
        new MapMarker(
                restaurantIcon,
                100,
                200
        );

MapMarker marker2 =
        new MapMarker(
                restaurantIcon,
                500,
                900
        );
```

---

# What Is Shared?

```text
marker1.icon
        |
        |
marker2.icon
```

Both point to:

```text
Same Flyweight
```

in memory.

---

# Proof

```java
System.out.println(
        marker1.getIcon()
                ==
        marker2.getIcon()
);
```

Output:

```text
true
```

One shared object.

---

# Memory Visualization

Without Flyweight:

```text
Marker1
    icon

Marker2
    icon

Marker3
    icon
```

Three copies.

---

With Flyweight:

```text
            Icon
              |
      -----------------
      |       |       |
   M1      M2      M3
```

One shared instance.

---

# Step 6

## Measuring Savings

Assume:

```text
Icon Size = 100 KB
```

and:

```text
1,000,000 Markers
```

---

Without Flyweight:

```text
100 KB × 1,000,000

≈ 100 GB
```

---

With Flyweight:

```text
100 KB

+

Marker Coordinates
```

Huge reduction.

---

# Another Example

## Text Editor

Flyweight:

```java
public final class CharacterGlyph {

    private final char value;

    public CharacterGlyph(
            char value) {

        this.value = value;
    }
}
```

Factory:

```java
public class GlyphFactory {

    private final Map<Character,
            CharacterGlyph> cache =
            new HashMap<>();

    public CharacterGlyph getGlyph(
            char c) {

        return cache.computeIfAbsent(
                c,
                CharacterGlyph::new
        );
    }
}
```

---

Document:

```text
HELLO HELLO HELLO
```

The same:

```text
H

E

L

O
```

objects are reused repeatedly.

---

# Step 7

## Factory Growth Problem

Suppose:

```text
Millions of Unique Keys
```

appear.

Factory cache grows forever.

Potential result:

```text
Memory Leak
```

---

## Enterprise Solutions

Examples:

```text
LRU Cache

Weak References

TTL Expiration

Eviction Policies
```

Production Flyweights often require lifecycle management.

---

# Step 8

## Weak Reference Flyweight

Example:

```java
WeakHashMap
```

allows unused flyweights to be garbage collected.

Useful when:

```text
Large Number
of Rarely Used Objects
```

exist.

---

# Common Mistake #1

## Sharing Mutable State

Bad:

```java
public class MapIcon {

    private String type;

    public void setType(...) {
    }
}
```

Dangerous.

One change affects:

```text
Every Consumer
```

of that flyweight.

---

# Common Mistake #2

## Everything Becomes Flyweight

Developers sometimes try to share:

```text
Orders

Customers

Users
```

Usually wrong.

Those objects contain:

```text
Highly Unique State
```

Little sharing opportunity exists.

---

# Common Mistake #3

## Forgetting Extrinsic State

Bad:

```java
MapIcon {

    icon

    x

    y
}
```

Coordinates cannot be shared.

This defeats the purpose.

---

# Common Mistake #4

## No Factory

Without a factory:

```java
new MapIcon(...)
```

creates duplicates.

No sharing occurs.

Flyweight depends heavily on centralized creation.

---

# Factory Is The Hidden Hero

Many developers think:

```text
Flyweight = Shared Object
```

Incomplete.

The real formula is:

```text
Flyweight

+

Factory

+

State Separation
```

Remove any piece.

The pattern breaks.

---

# What Architects Notice

Junior developers see:

```java
MapIconFactory
```

Architects see:

```text
Memory Deduplication Strategy
```

---

Junior developers see:

```java
ConcurrentHashMap
```

Architects see:

```text
Shared Resource Registry
```

---

Junior developers see:

```java
CharacterGlyph
```

Architects see:

```text
Intrinsic State Container
```

---

# The Bigger Insight

Flyweight is fundamentally about:

```text
Moving repeated data
out of individual objects
into shared objects.
```

Everything else is implementation detail.

---

# The Architectural Insight

Large-scale systems often fail because:

```text
Too Much Data

Stored Too Many Times
```

Flyweight attacks that problem directly.

Instead of optimizing:

```text
Algorithms

Queries

Network Calls
```

it optimizes:

```text
Memory Footprint
```

through intelligent sharing.

That makes Flyweight one of the few patterns whose primary goal is resource efficiency.

---

# Key Takeaways

* Flyweights contain intrinsic shared state.
* Context objects contain extrinsic state.
* Factories guarantee reuse.
* Flyweights should usually be immutable.
* Thread safety matters in flyweight factories.
* Weak references and eviction policies may be needed in large systems.
* Flyweight dramatically reduces memory usage when large amounts of repeated state exist.
