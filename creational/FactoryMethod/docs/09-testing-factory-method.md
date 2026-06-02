# Testing Factory Method

## Why This Chapter Exists

Most design pattern tutorials stop after explaining the implementation.

They show:

```java
public abstract class Application {

    protected abstract Product createProduct();
}
```

and declare success.

Real software engineering begins when code must be tested.

A design pattern is not valuable simply because it follows a textbook.

A design pattern is valuable if it improves:

* Maintainability
* Extensibility
* Testability

One of the biggest advantages of Factory Method is that it can significantly improve testing when used correctly.

However, it can also make testing harder when implemented poorly.

Understanding this distinction is critical.

---

# What Makes Code Difficult To Test?

Consider:

```java
public class PaymentService {

    public void process() {

        StripeGateway gateway =
                new StripeGateway();

        gateway.pay();
    }
}
```

Looks simple.

Testing becomes difficult because:

```java
new StripeGateway();
```

is hardcoded.

The test has no control over what gets created.

---

# The Core Testing Problem

Suppose:

```java
public class StripeGateway {

    public void pay() {

        // Real API Call
    }
}
```

Unit tests now depend on:

```text
Network
Credentials
External Service
API Availability
```

This violates one of the primary goals of unit testing:

> Tests should run in isolation.

---

# Why Hardcoded Creation Is Dangerous

Imagine:

```java
@Test
void shouldProcessPayment() {
}
```

During execution:

```java
new StripeGateway();
```

creates a real gateway.

Now your unit test becomes:

```text
Slow
Fragile
Environment Dependent
```

This is not true unit testing.

---

# Testability Principle

A test should be able to control dependencies.

Example:

```java
PaymentGateway gateway =
        mock(PaymentGateway.class);
```

The test chooses the dependency.

Not the production code.

This principle lies at the heart of testable software design.

---

# Enter Factory Method

Consider:

```java
public abstract class PaymentProcessor {

    protected abstract PaymentGateway
            createGateway();

    public void process() {

        PaymentGateway gateway =
                createGateway();

        gateway.pay();
    }
}
```

Notice what changed.

Object creation is no longer fixed.

Creation is delegated.

This gives tests more flexibility.

---

# Testing Through Subclassing

Production implementation:

```java
public class StripeProcessor
        extends PaymentProcessor {

    @Override
    protected PaymentGateway
    createGateway() {

        return new StripeGateway();
    }
}
```

Test implementation:

```java
public class TestProcessor
        extends PaymentProcessor {

    @Override
    protected PaymentGateway
    createGateway() {

        return new MockGateway();
    }
}
```

The workflow remains unchanged.

Only the dependency changes.

---

# Why This Works

Factory Method separates:

```text
Workflow
```

from

```text
Dependency Creation
```

Tests can replace creation logic.

Production code remains untouched.

This is a major improvement.

---

# Mock Objects

Modern testing frameworks use mocks.

Example:

```java
PaymentGateway gateway =
        mock(PaymentGateway.class);
```

Custom test processor:

```java
public class TestProcessor
        extends PaymentProcessor {

    @Override
    protected PaymentGateway
    createGateway() {

        return gateway;
    }
}
```

Now the test controls behavior completely.

---

# Verifying Behavior

Example:

```java
@Test
void shouldInvokePayment() {

    PaymentGateway gateway =
            mock(PaymentGateway.class);

    PaymentProcessor processor =
            new TestProcessor(gateway);

    processor.process();

    verify(gateway).pay();
}
```

We verify:

```text
Interaction
```

instead of:

```text
Implementation
```

This leads to more robust tests.

---

# Factory Method And Test Isolation

Without Factory Method:

```java
process()
      ↓
new StripeGateway()
      ↓
Real API
```

With Factory Method:

```java
process()
      ↓
createGateway()
      ↓
Mock Gateway
```

The dependency is isolated.

This is exactly what unit testing wants.

---

# The Hidden Testing Advantage

Many developers think Factory Method exists for extensibility.

That is true.

But it also provides:

```text
Substitutability
```

Anything returned by:

```java
createGateway()
```

can participate.

Including test doubles.

---

# Types Of Test Doubles

Factory Method works well with all common test doubles.

---

## Dummy

Used only to satisfy parameters.

```java
DummyGateway
```

---

## Stub

Returns predefined responses.

```java
StubGateway
```

---

## Mock

Verifies interactions.

```java
MockGateway
```

---

## Fake

Lightweight implementation.

```java
InMemoryGateway
```

---

## Spy

Records behavior while executing.

```java
SpyGateway
```

---

# Real World Example

Suppose:

```java
public class EmailSender {

    public void send() {
        // Real SMTP Server
    }
}
```

Testing this directly is painful.

Instead:

```java
public interface NotificationSender {

    void send();
}
```

Production:

```java
EmailSender
```

Test:

```java
FakeNotificationSender
```

Factory Method allows seamless substitution.

---

# Factory Method vs Dependency Injection

At this point many developers ask:

> Why not use Dependency Injection?

Good question.

Consider:

```java
public class PaymentProcessor {

    private final PaymentGateway gateway;

    public PaymentProcessor(
            PaymentGateway gateway) {

        this.gateway = gateway;
    }
}
```

Testing becomes trivial.

---

# Modern Reality

Today most applications prefer:

```text
Dependency Injection
```

over

```text
Factory Method
```

for simple dependency substitution.

Why?

Because DI avoids inheritance.

---

# Then Why Learn Factory Method?

Because many frameworks still use it.

Examples:

```text
Spring Internals
Hibernate
JUnit
Servlet Containers
Plugin Systems
```

Understanding Factory Method helps explain framework architecture.

---

# Common Testing Mistake

Consider:

```java
public class PaymentProcessor
        extends Processor {

    @Override
    protected PaymentGateway
    createGateway() {

        return new StripeGateway();
    }
}
```

Test:

```java
@Test
void shouldWork() {

    PaymentProcessor processor =
            new PaymentProcessor();

    processor.process();
}
```

This still creates:

```java
new StripeGateway();
```

The Factory Method exists but the test does not leverage it.

The pattern alone does not improve testing.

The test must use the extension point.

---

# Overriding For Tests

One common technique:

```java
PaymentProcessor processor =
        new PaymentProcessor() {

    @Override
    protected PaymentGateway
    createGateway() {

        return mockGateway;
    }
};
```

The test overrides the factory method.

No production code changes.

---

# Framework Example: Spring Tests

Spring frequently replaces production beans with test beans.

Production:

```java
@Bean
public PaymentGateway gateway() {

    return new StripeGateway();
}
```

Test:

```java
@TestConfiguration
public class TestConfig {

    @Bean
    public PaymentGateway gateway() {

        return new MockGateway();
    }
}
```

This follows the same idea.

Replace creation logic.

Preserve workflow.

---

# Testing Benefits

Factory Method improves:

### Dependency Isolation

Dependencies can be replaced.

---

### Mocking

Mocks become easier to inject.

---

### Repeatability

Tests behave consistently.

---

### Speed

No external systems required.

---

### Reliability

Tests become less fragile.

---

# Testing Drawbacks

Factory Method is not perfect.

---

## More Classes

Tests often require custom subclasses.

---

## More Complexity

Understanding inheritance hierarchies takes effort.

---

## Hidden Dependencies

Object creation may not be obvious.

---

## Harder Navigation

Execution flow spans multiple classes.

---

# Architect Perspective

Junior developers ask:

```text
Does the test pass?
```

Senior developers ask:

```text
Can I mock this dependency?
```

Architects ask:

```text
How much effort is required to isolate this component?
```

Factory Method exists partly to make isolation possible.

---

# The Deep Insight

A system that cannot replace dependencies easily is difficult to test.

Factory Method improves testability because it separates:

```text
Business Workflow
```

from

```text
Dependency Creation
```

Once those concerns are separated, tests gain control.

And testability is fundamentally about control.

---

# Key Takeaways

## Why Is Hardcoded Object Creation Problematic?

It prevents dependency substitution.

---

## How Does Factory Method Help?

It delegates object creation to an overridable method.

---

## What Testing Benefit Does This Provide?

Dependencies can be replaced with mocks, stubs, or fakes.

---

## Why Do Modern Applications Often Prefer DI?

Dependency Injection achieves the same goal with less inheritance.

---

## Most Important Insight

Factory Method is not only an extensibility pattern.

It is also a testability pattern because it gives tests control over dependency creation.

---