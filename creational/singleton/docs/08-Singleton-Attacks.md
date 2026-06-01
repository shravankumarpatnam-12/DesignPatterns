# Singleton Attacks

## Introduction

Many developers believe that making a constructor private is sufficient to guarantee a Singleton.

```java id="k0x5vg"
private Singleton() {
}
```

However, a private constructor alone does not guarantee that only one instance can exist.

Several mechanisms can break Singleton implementations and create multiple instances.

The most common attacks are:

1. Reflection Attack
2. Serialization Attack
3. Cloning Attack

Understanding these attacks is important when designing production-ready Singleton implementations.

---

# Reflection Attack

## What is Reflection?

Java Reflection allows programs to inspect and manipulate classes at runtime.

Reflection can bypass access modifiers such as:

* private
* protected
* package-private

As a result, it can invoke private constructors.

---

## Vulnerable Singleton

```java id="9v1u80"
public class Singleton {

    private static final Singleton INSTANCE =
            new Singleton();

    private Singleton() {
    }

    public static Singleton getInstance() {
        return INSTANCE;
    }
}
```

---

## Reflection Attack Example

```java id="r4uxyk"
import java.lang.reflect.Constructor;

public class ReflectionAttack {

    public static void main(String[] args)
            throws Exception {

        Singleton instance1 =
                Singleton.getInstance();

        Constructor<Singleton> constructor =
                Singleton.class.getDeclaredConstructor();

        constructor.setAccessible(true);

        Singleton instance2 =
                constructor.newInstance();

        System.out.println(instance1.hashCode());
        System.out.println(instance2.hashCode());
    }
}
```

---

## Output

```text id="0cmvzg"
12345678
87654321
```

Different hash codes indicate two different objects.

The Singleton guarantee is broken.

---

## Why Does This Happen?

Reflection bypasses:

```java id="20xy9g"
private Singleton() {
}
```

by using:

```java id="j8nkgc"
constructor.setAccessible(true);
```

This allows direct invocation of the private constructor.

---

## Protection Strategy

A common approach is to prevent constructor execution after the first instance has been created.

```java id="4t0p3v"
public class Singleton {

    private static boolean initialized =
            false;

    private Singleton() {

        if (initialized) {
            throw new RuntimeException(
                    "Singleton already initialized");
        }

        initialized = true;
    }
}
```

---

## Limitation

A determined attacker can still modify:

```java id="spjlwm"
initialized
```

using reflection.

Therefore, this protection is not foolproof.

---

# Serialization Attack

## What is Serialization?

Serialization converts an object into a stream of bytes.

```java id="wodm9e"
ObjectOutputStream
```

Deserialization reconstructs the object.

```java id="uc3q6h"
ObjectInputStream
```

---

## Vulnerable Singleton

```java id="49zthc"
public class Singleton
        implements Serializable {

    private static final Singleton INSTANCE =
            new Singleton();

    private Singleton() {
    }

    public static Singleton getInstance() {
        return INSTANCE;
    }
}
```

---

## Serialization Attack Example

### Serialize

```java id="5wlbsl"
ObjectOutputStream out =
        new ObjectOutputStream(
                new FileOutputStream("data.obj"));

out.writeObject(
        Singleton.getInstance());
```

---

### Deserialize

```java id="v0x6b7"
ObjectInputStream in =
        new ObjectInputStream(
                new FileInputStream("data.obj"));

Singleton instance =
        (Singleton) in.readObject();
```

---

## Problem

During deserialization:

```java id="c3e00e"
readObject()
```

creates a new object.

Result:

```text id="bn7e1r"
Original Singleton
        ≠
Deserialized Singleton
```

Two instances now exist.

---

## Protection Using readResolve()

```java id="a1jd1n"
private Object readResolve() {
    return INSTANCE;
}
```

Complete example:

```java id="s0spzj"
public class Singleton
        implements Serializable {

    private static final Singleton INSTANCE =
            new Singleton();

    private Singleton() {
    }

    public static Singleton getInstance() {
        return INSTANCE;
    }

    private Object readResolve() {
        return INSTANCE;
    }
}
```

---

## How readResolve() Works

During deserialization:

```text id="wtv4or"
Deserialize Object
        ↓
Call readResolve()
        ↓
Return Existing INSTANCE
```

No new Singleton is exposed.

---

# Cloning Attack

## What is Cloning?

Java provides object cloning through:

```java id="u36iql"
clone()
```

If cloning is enabled, it may create additional instances.

---

## Vulnerable Singleton

```java id="wtls43"
public class Singleton
        implements Cloneable {

    private static final Singleton INSTANCE =
            new Singleton();

    private Singleton() {
    }

    public static Singleton getInstance() {
        return INSTANCE;
    }

    @Override
    protected Object clone()
            throws CloneNotSupportedException {

        return super.clone();
    }
}
```

---

## Cloning Attack Example

```java id="9jxk0e"
Singleton instance1 =
        Singleton.getInstance();

Singleton instance2 =
        (Singleton) instance1.clone();
```

---

## Result

```text id="n8vdha"
instance1 != instance2
```

Two objects now exist.

Singleton guarantee is broken.

---

## Protection Strategy

Override clone:

```java id="z41e7r"
@Override
protected Object clone()
        throws CloneNotSupportedException {

    throw new CloneNotSupportedException();
}
```

Alternative:

```java id="24b5dz"
@Override
protected Object clone() {
    return INSTANCE;
}
```

---

# Which Singleton Implementations Are Vulnerable?

| Implementation         | Reflection | Serialization | Cloning |
| ---------------------- | ---------- | ------------- | ------- |
| Eager Singleton        | ✅          | ✅             | ✅       |
| Lazy Singleton         | ✅          | ✅             | ✅       |
| Synchronized Singleton | ✅          | ✅             | ✅       |
| DCL Singleton          | ✅          | ✅             | ✅       |
| Bill Pugh Singleton    | ✅          | ✅             | ✅       |
| Enum Singleton         | ❌          | ❌             | ❌       |

---

# Why Is Enum Singleton Different?

Enum Singleton:

```java id="hxh6ya"
public enum Singleton {

    INSTANCE;
}
```

The JVM provides special guarantees.

---

## Reflection Protection

Attempting:

```java id="oz9f68"
constructor.newInstance();
```

results in:

```text id="qz2k83"
IllegalArgumentException:
Cannot reflectively create enum objects
```

---

## Serialization Protection

Deserialization automatically returns:

```java id="xduf33"
Singleton.INSTANCE
```

No additional code is required.

---

## Cloning Protection

Enums cannot be cloned.

The JVM prevents it.

---

# Architect-Level Perspective

Reflection, Serialization, and Cloning attacks demonstrate an important principle:

> A private constructor alone does not guarantee Singleton behavior.

Many traditional Singleton implementations rely on:

```java id="m8lwsq"
private Singleton() {
}
```

for protection.

However, advanced JVM features can bypass these restrictions.

When absolute Singleton guarantees are required, Enum Singleton is typically the safest choice.

---

# Best Practices

### Use Enum Singleton When Possible

```java id="8lhkrg"
public enum Singleton {

    INSTANCE;
}
```

Provides built-in protection against:

* Reflection
* Serialization
* Cloning

---

### If Using Traditional Singleton

Consider:

* Defensive constructor checks
* readResolve()
* clone() protection

---

### Avoid Exposing Mutable Global State

Even a technically correct Singleton can create architectural problems if it manages shared mutable state.

---

# Summary

### Reflection Attack

Uses reflection to invoke private constructors and create additional instances.

---

### Serialization Attack

Creates new objects during deserialization unless `readResolve()` is implemented.

---

### Cloning Attack

Creates additional objects through `clone()` unless cloning is disabled.

---

### Safest Implementation

```java id="ij33i8"
public enum Singleton {

    INSTANCE;
}
```

Enum Singleton is protected by JVM guarantees and is immune to the most common Singleton attacks.

Understanding these attack vectors is essential when evaluating whether a Singleton implementation is truly production-ready.
