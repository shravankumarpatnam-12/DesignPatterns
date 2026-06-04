## Is a Typical Builder Thread-Safe?

Consider a simple Builder:

```java
public static class Builder {

    private String firstName;
    private String lastName;

    public Builder firstName(
            String firstName) {

        this.firstName = firstName;

        return this;
    }

    public Builder lastName(
            String lastName) {

        this.lastName = lastName;

        return this;
    }
}
```

Notice what happens inside each method.

```java
this.firstName = firstName;
```

The Builder modifies its internal state.

Every call changes the values stored inside the Builder.

For example:

```java
Builder builder =
        new Builder();

builder.firstName("Shravan");
```

Before:

```text
firstName = null
```

After:

```text
firstName = Shravan
```

The object's state has changed.

Because the Builder's state changes over time, it is considered a **mutable object**.

---

## Why Mutability Matters

Mutability itself is not a problem.

The real problem appears when mutable state is shared between multiple threads.

Consider:

```java
List<String> names =
        new ArrayList<>();
```

Thread A:

```java
names.add("Java");
```

Thread B:

```java
names.add("Spring");
```

Both threads modify the same object.

Without synchronization, the final state depends on execution timing.

This is the root cause of most concurrency bugs.

The same principle applies to Builders.

---

## When Can a Builder Become Unsafe?

A Builder becomes unsafe only when the same Builder instance is accessed by multiple threads.

Example:

```java
User.Builder builder =
        new User.Builder();
```

Thread A:

```java
builder.firstName("Shravan");
```

Thread B:

```java
builder.firstName("John");
```

Both threads modify:

```java
private String firstName;
```

at the same time.

Possible execution:

```text
Thread A:
firstName = Shravan

Thread B:
firstName = John
```

Final value:

```text
John
```

Or:

```text
Shravan
```

depending on which thread executes last.

This situation is known as a **race condition**.

A race condition occurs when the correctness of a program depends on the unpredictable timing of multiple threads.

---

## Does This Happen in Normal Builder Usage?

Usually no.

Most Builders are used like this:

```java
User user =
    new User.Builder()
        .firstName("Shravan")
        .lastName("Patnam")
        .build();
```

Lifecycle:

```text
Create Builder
      ↓
Populate Fields
      ↓
Build Object
      ↓
Discard Builder
```

Everything happens inside a single thread.

No sharing occurs.

No concurrency issue exists.

This is why most Builder implementations are not synchronized.

---

## Real Situations Where Problems Can Occur

### Shared Static Builder

Bad:

```java
private static final User.Builder
        BUILDER =
            new User.Builder();
```

Multiple threads may use the same Builder.

---

### Singleton Service Holding a Builder

Bad:

```java
@Service
public class UserService {

    private final User.Builder builder =
            new User.Builder();
}
```

Since Spring services are typically singleton-scoped, multiple request threads may access the same Builder.

---

### Reusing Builders for Performance

Bad:

```java
return SHARED_BUILDER
        .firstName(name)
        .build();
```

Sharing a mutable Builder introduces concurrency risks.

---

### Async Processing

Bad:

```java
CompletableFuture.runAsync(
        () -> builder.firstName("Shravan"));

CompletableFuture.runAsync(
        () -> builder.firstName("John"));
```

Multiple asynchronous tasks mutate the same Builder instance.

---

## Why Most Builders Remain Unsynchronized

Builders are intended to be:

```text
Short-Lived
Mutable
Used by One Thread
```

Adding synchronization to every Builder method would increase:

* Complexity
* Lock contention
* Runtime overhead

without solving a real-world problem in typical usage.

Therefore most Builder implementations deliberately remain non-thread-safe.

---

## Builder Thread Safety vs Object Thread Safety

It is important not to confuse the Builder with the object it creates.

Builder:

```text
Mutable
Usually Not Thread-Safe
```

Built Object:

```text
Often Immutable
Often Thread-Safe
```

Example:

```java
User user =
    new User.Builder()
        .firstName("Shravan")
        .build();
```

Once the User object is created:

```java
user.getFirstName();
```

can be safely called by multiple threads if the object is truly immutable.

The Builder may be unsafe.

The final object may be completely safe.

---

## Key Insight

The Builder Pattern assumes:

```text
Builder
    ↓
Used by One Thread
    ↓
Creates Object
    ↓
Builder Discarded
```

The object may be shared across threads.

The Builder should not be.

This is why Builder implementations are typically mutable and non-thread-safe, while the objects they create are often immutable and thread-safe.
