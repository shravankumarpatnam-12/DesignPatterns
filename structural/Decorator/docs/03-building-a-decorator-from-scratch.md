# Building a Decorator From Scratch

## The Goal

Most tutorials stop here:

```java
new LoggingDecorator(
    new EncryptionDecorator(
        new EmailNotification()
    )
);
```

But production systems require much more:

* Consistent structure
* Reusable decorators
* Easy testing
* Extensibility
* Clear ordering rules

Let's build a proper implementation.

---

# Step 1: Define the Component

```java
public interface Notification {
    void send(String message);
}
```

Concrete implementation:

```java
public class EmailNotification
        implements Notification {

    @Override
    public void send(String message) {

        System.out.println(
            "Sending Email: " + message
        );
    }
}
```

---

# Step 2: Create an Abstract Decorator

Most examples skip this.

In real projects it reduces duplication.

```java
public abstract class NotificationDecorator
        implements Notification {

    protected final Notification notification;

    protected NotificationDecorator(
            Notification notification) {

        this.notification = notification;
    }
}
```

Why?

Without it:

```java
private final Notification notification;
```

must be repeated in every decorator.

With an abstract decorator:

```java
LoggingDecorator
EncryptionDecorator
RetryDecorator
AuditDecorator
```

all inherit common behavior.

---

# Step 3: Implement Logging

```java
public class LoggingDecorator
        extends NotificationDecorator {

    public LoggingDecorator(
            Notification notification) {

        super(notification);
    }

    @Override
    public void send(String message) {

        System.out.println(
            "[LOG] Sending message"
        );

        notification.send(message);
    }
}
```

---

# Step 4: Implement Encryption

```java
public class EncryptionDecorator
        extends NotificationDecorator {

    public EncryptionDecorator(
            Notification notification) {

        super(notification);
    }

    @Override
    public void send(String message) {

        String encrypted =
                "[encrypted]" + message;

        notification.send(encrypted);
    }
}
```

---

# Step 5: Implement Audit

```java
public class AuditDecorator
        extends NotificationDecorator {

    public AuditDecorator(
            Notification notification) {

        super(notification);
    }

    @Override
    public void send(String message) {

        notification.send(message);

        System.out.println(
            "[AUDIT] Notification stored"
        );
    }
}
```

Notice:

Audit executes after delegation.

Logging executes before delegation.

Decorator can add behavior:

```text
Before execution

After execution

Before and after execution
```

This is extremely important.

---

# Step 6: Compose Features

```java
Notification notification =
    new AuditDecorator(
        new LoggingDecorator(
            new EncryptionDecorator(
                new EmailNotification()
            )
        )
    );
```

Execution:

```text
LoggingDecorator

    ↓

EncryptionDecorator

    ↓

EmailNotification

    ↑

AuditDecorator
```

Output:

```text
[LOG] Sending message

Sending Email: [encrypted]Hello

[AUDIT] Notification stored
```

---

# Understanding Execution Order

This is where many developers get confused.

Consider:

```java
new LoggingDecorator(
    new EncryptionDecorator(
        new EmailNotification()
    )
);
```

Flow:

```text
Logging starts

Encryption happens

Email sends
```

Now reverse them:

```java
new EncryptionDecorator(
    new LoggingDecorator(
        new EmailNotification()
    )
);
```

Flow:

```text
Encryption starts

Logging happens

Email sends
```

Same decorators.

Different result.

Order matters.

---

# Real Production Example

Imagine:

```java
AuthenticationDecorator
AuthorizationDecorator
LoggingDecorator
CachingDecorator
```

Should logging happen before authentication?

After authentication?

Should cache execute before authorization?

Wrong ordering can create:

* Security bugs
* Performance issues
* Data leaks

Decorator chains must be carefully designed.

---

# Stateful vs Stateless Decorators

Most decorators should be:

```text
Stateless
```

Example:

```java
LoggingDecorator
EncryptionDecorator
CompressionDecorator
```

Safe for concurrent execution.

---

Sometimes state is required.

Example:

```java
RetryDecorator
```

```java
private int retryCount;
```

Now concurrency becomes important.

Questions arise:

```text
Can multiple threads share it?

Should state be mutable?

Should state be externalized?
```

Architects immediately look for these issues.

---

# Error Handling Decorators

One powerful use case:

```java
RetryDecorator
```

```java
public class RetryDecorator
        extends NotificationDecorator {

    @Override
    public void send(String message) {

        for(int i=0;i<3;i++) {

            try {
                notification.send(message);
                return;
            }
            catch(Exception e) {
                // retry
            }
        }
    }
}
```

Now resilience is added without changing:

```java
EmailNotification
```

This is a huge advantage.

---

# Testing Decorators

Testing becomes easy.

Mock the wrapped component.

Example:

```java
Notification mock =
        Mockito.mock(Notification.class);

Notification decorator =
        new LoggingDecorator(mock);
```

Verify delegation:

```java
decorator.send("Hello");

verify(mock).send("Hello");
```

Decorator testing usually focuses on:

1. Added behavior
2. Delegation correctness

---

# Common Mistake #1

Forgetting delegation.

Bad:

```java
public void send(String message) {

    System.out.println("Logged");
}
```

The original behavior never executes.

Decorator becomes a replacement.

Not an extension.

---

# Common Mistake #2

Changing contract behavior.

Bad:

```java
public void send(String message) {
    throw new RuntimeException();
}
```

Decorator should enhance behavior.

Not violate expectations.

---

# Common Mistake #3

Too Much Logic

Bad:

```java
LoggingDecorator
```

that performs:

* validation
* security
* transformation
* persistence
* caching

Decorator should have a focused responsibility.

---

# Common Mistake #4

Decorator Explosion

Creating:

```text
LoggingDecorator
FileLoggingDecorator
DatabaseLoggingDecorator
KafkaLoggingDecorator
CloudLoggingDecorator
```

Often Strategy Pattern should handle those variations.

Decorator and Strategy frequently work together.

---

# Decorator Checklist

A proper decorator should:

✅ Implement the same interface

✅ Hold a reference to the same interface

✅ Add behavior

✅ Delegate execution

✅ Preserve contract expectations

✅ Remain focused on one responsibility

---

# The Architectural Insight

Junior developers see:

```java
LoggingDecorator
```

Architects see:

```text
Pipeline of responsibilities
```

This idea eventually evolves into:

* Spring Filters
* Spring AOP
* Servlet Filters
* Security Chains
* Netty Pipelines
* Message Processing Pipelines

Decorator is the simplest form of a much larger architectural concept:

```text
Composable behavior
```

And that concept appears everywhere in enterprise systems.

---

# Key Takeaways

* Production decorators usually inherit from an abstract decorator.
* Order of decorators matters.
* Decorators can execute before or after delegation.
* Most decorators should remain stateless.
* Testing decorators is straightforward because delegation is explicit.
* A decorator must extend behavior, not replace it.
* Decorator chains are the foundation of many enterprise middleware architectures.
