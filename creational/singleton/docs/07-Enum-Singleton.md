# Enum Singleton

## Introduction

The Enum Singleton is widely regarded as the safest and simplest Singleton implementation in Java.

It was popularized by Joshua Bloch in *Effective Java*.

Unlike traditional Singleton implementations, Enum Singleton provides:

* Thread Safety
* Serialization Safety
* Reflection Safety
* Simple Implementation

all with minimal code.

---

## Implementation

```java id="kp9y8u"
public enum Singleton {

    INSTANCE;

    public void performOperation() {
        System.out.println("Singleton Method");
    }
}
```

Usage:

```java id="8njv1u"
Singleton.INSTANCE.performOperation();
```

---

## Why Is Enum a Singleton?

An enum can have only a fixed number of instances.

In this case:

```java id="v9yyj5"
INSTANCE
```

is the only enum constant.

Therefore, only one instance can ever exist.

---

## What Does the Compiler Generate?

Many developers think:

```java id="o9q2d4"
public enum Singleton {

    INSTANCE;
}
```

is special syntax.

Internally, the compiler generates something conceptually similar to:

```java id="c0j6e4"
public final class Singleton
        extends Enum<Singleton> {

    public static final Singleton INSTANCE =
            new Singleton();

    private Singleton() {
        super("INSTANCE", 0);
    }
}
```

The JVM enforces strict rules around enum creation.

---

## Why Is It Thread Safe?

Enum instances are created during class initialization.

```text id="t5n3jb"
Class Loading
      ↓
Class Initialization
      ↓
INSTANCE Created
```

The JVM guarantees that class initialization is thread-safe.

Therefore:

```java id="e3s2wp"
Singleton.INSTANCE
```

will always refer to the same object.

---

## Why Is It Reflection Safe?

Traditional Singletons rely on:

```java id="pnz5aq"
private Singleton() {
}
```

Reflection can bypass private constructors.

Example:

```java id="emf6cb"
constructor.setAccessible(true);
```

and create another instance.

However, enums are different.

Attempting:

```java id="6c8qvs"
constructor.newInstance();
```

results in:

```text id="u9s0wh"
java.lang.IllegalArgumentException:
Cannot reflectively create enum objects
```

The JVM explicitly prevents reflective creation of enum instances.

---

## Why Is It Serialization Safe?

Consider a traditional Singleton.

```java id="xk8rq2"
ObjectOutputStream
ObjectInputStream
```

During deserialization, a new object may be created.

This breaks the Singleton guarantee.

To fix this, traditional Singletons require:

```java id="i7cz4m"
private Object readResolve() {
    return INSTANCE;
}
```

Enum Singleton does not require any additional code.

The JVM automatically guarantees that deserialization returns the existing enum constant.

---

## Why Is It Simple?

Compare Bill Pugh Singleton:

```java id="g8g4yn"
private static class Holder {

    private static final Singleton INSTANCE =
            new Singleton();
}
```

with Enum Singleton:

```java id="n8l65x"
public enum Singleton {

    INSTANCE;
}
```

No:

* volatile
* synchronized
* holder classes
* readResolve()
* lock management

are required.

---

## Adding State and Behavior

Enums can contain fields and methods.

```java id="u6m91n"
public enum ConfigurationManager {

    INSTANCE;

    private String environment = "PROD";

    public String getEnvironment() {
        return environment;
    }
}
```

Usage:

```java id="i1l4kt"
ConfigurationManager.INSTANCE
                    .getEnvironment();
```

Output:

```text id="8qqdh2"
PROD
```

---

## Advantages

### Thread Safe

Guaranteed by JVM class initialization.

---

### Reflection Safe

Cannot be instantiated via reflection.

---

### Serialization Safe

No need for `readResolve()`.

---

### Simple Implementation

Minimal code.

---

### Easy to Maintain

Less code means fewer bugs.

---

## Disadvantages

### Not Lazily Initialized

Enum instances are created when the enum class is initialized.

```text id="huxp1g"
Class Loaded
      ↓
INSTANCE Created
```

Unlike Bill Pugh Singleton, creation cannot be delayed until first use.

---

### Less Flexible

Enums cannot extend other classes.

```java id="z53ufd"
public enum Singleton extends SomeClass
```

This is not allowed.

---

### Unfamiliar for Some Developers

Some developers may not immediately associate enums with Singleton implementations.

---

## Enum Singleton vs Bill Pugh Singleton

| Feature              | Bill Pugh | Enum Singleton |
| -------------------- | --------- | -------------- |
| Lazy Initialization  | ✅         | ❌              |
| Thread Safe          | ✅         | ✅              |
| Reflection Safe      | ❌         | ✅              |
| Serialization Safe   | ❌         | ✅              |
| Uses Synchronization | ❌         | ❌              |
| Simplicity           | High      | Very High      |

---

## When Should You Use Enum Singleton?

Use Enum Singleton when:

* You need a Singleton.
* Reflection attacks must be prevented.
* Serialization safety is required.
* Simplicity is preferred.

Examples:

* Configuration Managers
* Feature Flag Managers
* Application-Level Services
* Shared Utility Components

---

## Interview Questions

### Why does Effective Java recommend Enum Singleton?

Because it provides thread safety, reflection safety, and serialization safety with minimal code.

---

### Why can't reflection create enum instances?

The JVM explicitly prohibits reflective creation of enum objects.

---

### Does Enum Singleton require `readResolve()`?

No.

The JVM automatically preserves Singleton behavior during serialization and deserialization.

---

### Is Enum Singleton thread-safe?

Yes.

Enum constants are initialized during JVM class initialization, which is guaranteed to be thread-safe.

---

## Summary

### Pros

* Thread Safe
* Reflection Safe
* Serialization Safe
* Extremely Simple
* Easy to Maintain

### Cons

* Not Lazily Initialized
* Cannot Extend Classes
* Less Flexible Than Traditional Classes

### Production Recommendation

For most Java applications, Enum Singleton is the safest and simplest Singleton implementation. It eliminates many of the pitfalls associated with traditional Singleton patterns and is the approach recommended in *Effective Java*.
