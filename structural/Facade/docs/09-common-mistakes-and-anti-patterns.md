# Common Mistakes and Anti-Patterns

## When a Good Facade Becomes a Bad Design

Every design pattern solves a problem.

Every design pattern can also create problems when misused.

Facade is no exception.

In fact, because Facade sits at important architectural boundaries, mistakes here can have a large impact on the entire system.

This chapter focuses on mistakes commonly seen in production systems and how experienced architects avoid them.

---

# The Facade Paradox

The Facade Pattern exists to:

```text id="1vud34"
Reduce Complexity
```

Ironically, many poorly designed facades become:

```text id="yqg5z0"
The Most Complex Classes In The System
```

Understanding why this happens is essential.

---

# Anti-Pattern #1: The God Facade

This is by far the most common mistake.

---

## How It Starts

A team creates:

```java id="9m6sy6"
CheckoutFacade
```

Everything looks good.

---

A few months later:

```java id="a4vt8r"
checkoutFacade.placeOrder();
checkoutFacade.cancelOrder();
```

Still reasonable.

---

A year later:

```java id="xhhslj"
checkoutFacade.placeOrder();
checkoutFacade.cancelOrder();
checkoutFacade.refundOrder();
checkoutFacade.generateInvoice();
checkoutFacade.sendMarketingEmail();
checkoutFacade.exportReport();
checkoutFacade.calculateRevenue();
checkoutFacade.manageCoupons();
```

The facade becomes enormous.

---

## The Problem

The class now owns multiple responsibilities.

It violates:

```text id="v1g93r"
Single Responsibility Principle
```

Changes become risky.

Testing becomes harder.

Understanding the class becomes difficult.

---

## Better Design

Split by business capability.

```text id="u9l4t8"
CheckoutFacade

RefundFacade

ReportingFacade

MarketingFacade
```

Each facade owns one boundary.

---

# Anti-Pattern #2: Leaky Facade

A facade should hide complexity.

A leaky facade exposes it.

---

Bad Example:

```java id="m24v8q"
checkoutFacade.checkout(
    order,
    inventoryFlag,
    paymentFlag,
    shippingMode,
    auditOptions,
    retryStrategy,
    transactionMode
);
```

Clients now understand subsystem details.

The facade failed.

---

Good Example:

```java id="3q6xrn"
checkoutFacade.checkout(order);
```

Implementation details remain hidden.

---

## Rule

If clients must understand internal workflow details, the facade is leaking.

---

# Anti-Pattern #3: Pass-Through Facade

Some teams create facades like this:

```java id="llg3ux"
public User getUser(Long id) {

    return userService.getUser(id);
}
```

Nothing else happens.

---

Another method:

```java id="nk1nci"
public User saveUser(User user) {

    return userService.saveUser(user);
}
```

Again, no value added.

---

## The Problem

The facade becomes a useless middle layer.

It increases complexity instead of reducing it.

---

## When Is This Acceptable?

Sometimes teams intentionally create facades to:

```text id="im3h7d"
Create Stable Contracts

Protect Future Evolution

Define Architectural Boundaries
```

If those goals exist, the facade may still be justified.

Otherwise, it's unnecessary.

---

# Anti-Pattern #4: Business Logic Dumping Ground

Developers often think:

> Facade coordinates workflows.

Correct.

Then they conclude:

> Therefore all business logic belongs in the facade.

Wrong.

---

Bad Example:

```java id="gbv8yx"
checkoutFacade.calculateTax();

checkoutFacade.calculateDiscount();

checkoutFacade.calculateShippingCost();

checkoutFacade.calculateRevenueForecast();
```

The facade now owns domain logic.

---

Good Example:

```java id="emc7ar"
taxService.calculate();

discountService.calculate();

shippingService.calculate();
```

The facade orchestrates.

Subsystems execute.

---

## Rule

Facade should coordinate logic.

Not replace domain services.

---

# Anti-Pattern #5: Exposing Subsystems Anyway

Example:

```java id="q0ytu3"
public class CheckoutFacade {

    public PaymentService getPaymentService() {

        return paymentService;
    }
}
```

Clients now do:

```java id="ev6v17"
checkoutFacade
        .getPaymentService()
        .processPayment();
```

The boundary disappears.

The facade becomes meaningless.

---

## Rule

If everyone bypasses the facade, the facade provides little value.

---

# Anti-Pattern #6: Facade Knows Too Much

A facade should understand workflow coordination.

Not every detail of every subsystem.

---

Bad Example:

```java id="mjlwmz"
CheckoutFacade
```

contains knowledge about:

```text id="cfhqyl"
Database Schema

Payment Provider APIs

Shipping Algorithms

Discount Rules

Inventory Calculations
```

The class becomes tightly coupled to everything.

---

Good Example:

```text id="qrmj8z"
CheckoutFacade
```

knows:

```text id="lq6ynx"
Call Inventory

Call Payment

Call Shipping
```

and nothing more.

---

# Anti-Pattern #7: Multiple Facades for the Same Capability

Example:

```text id="wgvb70"
CheckoutFacade

OrderFacade

PurchaseFacade
```

All performing similar operations.

---

## The Problem

Developers become confused.

Responsibilities overlap.

Business workflows become fragmented.

---

## Better Design

One business capability.

One primary facade.

Clear ownership.

---

# Anti-Pattern #8: Turning Facades into Frameworks

A common enterprise mistake.

Developers continuously add features.

Soon:

```java id="y6y1t4"
AbstractCheckoutFacadeFactoryBuilderProvider
```

appears.

---

The original problem was simple.

The solution became over-engineered.

---

## Rule

Facade should simplify.

If the facade itself becomes difficult to understand, something has gone wrong.

---

# How Experienced Architects Design Facades

They follow a few simple rules.

---

## Rule 1

Expose business capabilities.

Not technical workflows.

Good:

```java id="rxtw61"
checkout();
```

Bad:

```java id="tb1rwl"
reserveInventoryAndCapturePayment();
```

---

## Rule 2

Keep orchestration centralized.

Avoid workflow duplication.

---

## Rule 3

Keep domain logic in domain services.

---

## Rule 4

Hide implementation details.

---

## Rule 5

Design for change.

Assume subsystems will evolve.

---

# A Practical Evaluation Checklist

Ask these questions.

### Does the facade hide complexity?

If not:

Remove it.

---

### Does it expose business operations?

If not:

Redesign it.

---

### Does it coordinate multiple subsystems?

If not:

It may not need to exist.

---

### Is it becoming a God Object?

If yes:

Split responsibilities.

---

### Are clients bypassing it?

If yes:

Its value is questionable.

---

# Real Enterprise Example

Bad Design:

```text id="nwhs3r"
EnterpriseFacade
```

containing:

```text id="wtfkkg"
Payments

Customers

Orders

Reports

Invoices

Marketing

Notifications
```

Thousands of lines.

Hundreds of methods.

Nobody understands it.

---

Good Design:

```text id="v4xjlwm"
PaymentFacade

CustomerFacade

CheckoutFacade

ReportingFacade
```

Clear boundaries.

Clear ownership.

Clear responsibilities.

---

# Architect's Perspective

Junior developers often ask:

> Can I use a Facade here?

Architects ask:

> Will this boundary remain valuable as the system evolves?

Because a facade is not merely another class.

It is an architectural commitment.

A bad boundary creates long-term pain.

A good boundary creates long-term flexibility.

---

# Key Takeaways

* Facades should simplify, not accumulate complexity.
* Avoid God Facades.
* Avoid leaking subsystem details.
* Keep orchestration separate from domain logic.
* Hide implementation details effectively.
* Design facades around business capabilities.
* Continuously evaluate whether the boundary still provides value.

---

# Chapter Summary

Most Facade failures occur because developers forget the original purpose of the pattern.

Facade exists to create a simple, stable boundary over complex and evolving systems.

When it starts exposing details, accumulating responsibilities, or becoming a dumping ground for logic, it loses that purpose.

The best facades are often the ones that feel obvious, simple, and boring—because all the complexity is hidden where it belongs.
