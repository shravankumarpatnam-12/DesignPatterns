# Common Mistakes in Factory Method

## Why This Chapter Exists

Most design pattern books focus on:

```text
What the pattern solves
```

Few discuss:

```text
How developers misuse the pattern
```

This creates a dangerous situation.

Developers learn:

```java
protected abstract Product createProduct();
```

and begin applying Factory Method everywhere.

The result is often:

```text
More Classes
More Complexity
More Indirection
Less Readability
```

A design pattern is not automatically good.

A design pattern is a trade-off.

Factory Method solves specific problems.

When those problems do not exist, the pattern becomes accidental complexity.

This chapter explores the most common mistakes architects encounter in production systems.

---

# Mistake #1: Using Factory Method Without a Variability Problem

Consider:

```java
public class UserService {

    protected UserRepository createRepository() {

        return new UserRepository();
    }
}
```

Question:

How many repository implementations exist?

```text
One
```

How often will that change?

```text
Never
```

Then why introduce Factory Method?

There is no variability.

There is no extension point.

There is no business need.

The abstraction exists only because someone wanted to use a pattern.

---

# The Smell

If the answer to:

> What future implementation are we expecting?

is:

```text
None
```

then Factory Method is probably unnecessary.

---

# Mistake #2: Pattern-Driven Development

Some developers design software like this:

```text
Need Singleton
Need Factory
Need Builder
Need Strategy
Need Observer
```

before understanding the problem.

Patterns become goals.

This is backwards.

Correct thinking:

```text
Problem
    ↓
Constraints
    ↓
Trade-offs
    ↓
Pattern
```

Patterns are tools.

Not objectives.

---

# Mistake #3: Factory Method for Every Object

Consider:

```java
createUser()

createAddress()

createPhone()

createEmail()

createCountry()

createState()
```

Everything becomes a factory method.

The codebase explodes.

Instead of:

```java
new Address()
```

you now have:

```java
createAddress()
```

for no reason.

Simple object creation is often perfectly acceptable.

Not every object requires a factory.

---

# Mistake #4: Ignoring Simpler Alternatives

Developers sometimes write:

```java
public abstract class NotificationCreator {

    protected abstract Notification
            createNotification();
}
```

for a system supporting:

```text
Email
SMS
```

only.

A simple factory might be sufficient.

```java
NotificationFactory.create(type);
```

would be easier.

Architects always ask:

> What is the simplest solution that satisfies today's requirements?

---

# Mistake #5: Giant Inheritance Hierarchies

A common anti-pattern:

```text
AbstractCreator
        │
        ▼
BaseCreator
        │
        ▼
RegionalCreator
        │
        ▼
CountryCreator
        │
        ▼
SpecializedCreator
```

Five levels of inheritance.

Developers spend more time understanding the hierarchy than solving business problems.

Factory Method should simplify extension.

Not create inheritance mazes.

---

# Mistake #6: God Creator Classes

Developers sometimes move business logic into the creator.

Example:

```java
public abstract class PaymentProcessor {

    protected PaymentGateway createGateway() {

        validateUser();

        checkFraud();

        calculateTax();

        logTransaction();

        return new StripeGateway();
    }
}
```

Creation logic becomes mixed with business logic.

The factory method now violates:

```text
Single Responsibility Principle
```

The creator should focus on creation.

Not unrelated workflows.

---

# Mistake #7: Conditional Logic Inside Factory Method

Consider:

```java
protected PaymentGateway
createGateway(String type) {

    if(type.equals("STRIPE"))
        return new StripeGateway();

    if(type.equals("PAYPAL"))
        return new PaypalGateway();

    return null;
}
```

This is essentially:

```text
Simple Factory
```

disguised as:

```text
Factory Method
```

The extension point is gone.

We have reintroduced the original problem.

---

# The Smell

When you see:

```java
if(...)
else if(...)
else if(...)
```

inside a Factory Method,

ask yourself:

> Why are subclasses not being used?

---

# Mistake #8: Returning Concrete Types

Wrong:

```java
protected StripeGateway
createGateway()
```

Better:

```java
protected PaymentGateway
createGateway()
```

Factory Method should expose abstractions.

Not implementations.

Returning concrete types increases coupling.

---

# Mistake #9: Breaking Open/Closed Principle

Developers sometimes create:

```java
public abstract class DocumentCreator {
}
```

but still modify existing creators whenever new products appear.

Example:

```java
if(type.equals("PDF"))
```

added repeatedly.

The pattern exists specifically to avoid this.

If every new implementation requires modification,

the primary benefit has been lost.

---

# Mistake #10: Using Factory Method Instead of Dependency Injection

Modern Java developers occasionally write:

```java
public abstract class Service {

    protected abstract Repository
            createRepository();
}
```

when:

```java
public Service(
        Repository repository)
```

would be simpler.

Remember:

Factory Method emerged before modern DI containers.

Today:

```text
Spring
Guice
Dagger
```

often provide a cleaner solution.

---

# Mistake #11: Factory Explosion

One of the most common enterprise problems.

Example:

```text
UserCreator
AddressCreator
OrderCreator
CustomerCreator
PaymentCreator
InvoiceCreator
CountryCreator
CityCreator
```

Hundreds of creator classes.

The architecture becomes difficult to navigate.

This is known as:

# Factory Explosion

A pattern intended to improve maintainability creates maintenance problems.

---

# Mistake #12: Creating Extension Points Nobody Uses

Architects frequently ask:

> Who is expected to extend this?

If the answer is:

```text
Nobody
```

the extension point may be unnecessary.

Factory Method shines when extensibility is a real requirement.

Not a hypothetical possibility.

---

# Mistake #13: Overengineering Small Applications

Example:

```text
Single Team
Two Developers
Three Screens
One Database
```

Yet the architecture includes:

```text
Factory Method
Abstract Factory
Strategy
Builder
Adapter
Facade
```

The architecture becomes more complex than the business problem.

This is a common symptom of overengineering.

---

# Mistake #14: Forgetting Composition

Many developers instinctively reach for inheritance.

Factory Method depends heavily on inheritance.

Modern architecture often prefers:

```text
Composition
```

over:

```text
Inheritance
```

because composition tends to be more flexible.

Architects should always evaluate both options.

---

# When Factory Method Is Actually The Wrong Tool

Factory Method is often a poor choice when:

```text
Only One Implementation Exists
Requirements Are Stable
No Extension Point Is Needed
Dependency Injection Is Available
```

In these cases:

```java
new Something()
```

or constructor injection may be sufficient.

---

# Warning Signs

You may be overusing Factory Method if you notice:

```text
Too Many Creator Classes
Deep Inheritance Trees
Unused Extension Points
Factory Methods Everywhere
Developers Confused About Flow
```

These are architectural smells.

---

# What Good Factory Method Looks Like

Good Factory Method usage usually has:

```text
Clear Extension Point
Multiple Implementations
Stable Workflow
Frequent Change
Independent Teams
```

The pattern solves a genuine problem.

---

# Real Enterprise Example

Bad:

```java
protected Address createAddress() {
    return new Address();
}
```

No extension.

No value.

---

Good:

```java
protected PaymentGateway
createGateway();
```

because:

```text
New Providers Arrive Frequently
Business Rules Vary
Independent Teams Contribute
```

The extension point has purpose.

---

# Architect Perspective

Junior developers ask:

```text
Can I use Factory Method here?
```

Senior developers ask:

```text
Should I use Factory Method here?
```

Architects ask:

```text
What future change am I optimizing for?
```

That question determines whether the pattern is valuable.

---

# The Deep Insight

Most architectural problems are not caused by missing patterns.

They are caused by unnecessary patterns.

Factory Method is powerful because it creates extension points.

But every extension point has a maintenance cost.

Good architects create extension points only where change is expected.

---

# Key Takeaways

## What Is The Biggest Factory Method Mistake?

Using it when no variability exists.

---

## What Is Factory Explosion?

Too many creator classes creating unnecessary complexity.

---

## Why Is Overengineering Dangerous?

The architecture becomes harder than the business problem.

---

## When Should Factory Method Be Avoided?

When requirements are stable and no meaningful extension point exists.

---

## Most Important Insight

Factory Method should be introduced to solve a known extensibility problem—not because a design pattern catalog says it exists.

---
