# E-Commerce Checkout Facade

## Moving from Textbook Examples to Production Systems

Most Facade examples stop here:

```java
orderFacade.placeOrder(order);
```

and everyone is happy.

Unfortunately, real systems are not that simple.

In production, placing an order is rarely a single operation.

It is a business workflow involving multiple domains, teams, and services.

This is where Facade becomes truly valuable.

---

# The Reality of Checkout Systems

When a customer clicks:

```text
Place Order
```

many things happen behind the scenes.

```text
Validate Customer
↓
Validate Inventory
↓
Reserve Inventory
↓
Calculate Pricing
↓
Apply Discounts
↓
Calculate Tax
↓
Process Payment
↓
Create Order
↓
Create Shipment
↓
Send Notification
↓
Write Audit Logs
```

What appears to be one action is actually a coordinated workflow.

---

# The Naive Design

Many systems start like this:

```java
public class CheckoutController {

    public void checkout(OrderRequest request) {

        customerService.validate(request);

        inventoryService.reserve(request);

        pricingService.calculate(request);

        taxService.calculate(request);

        paymentService.process(request);

        orderService.create(request);

        shippingService.createShipment(request);

        notificationService.send(request);

        auditService.record(request);
    }
}
```

Looks harmless.

But this design contains serious architectural problems.

---

# Problem 1: Controllers Become Workflow Engines

Controllers should handle:

```text
Request Handling
Response Generation
Authentication
Authorization
```

They should not own business workflows.

Yet here, the controller knows:

* Which services exist
* Call sequence
* Business dependencies
* Failure handling

The controller has become a workflow engine.

---

# Problem 2: Workflow Duplication

Soon another requirement appears.

```text
Web Checkout

Mobile Checkout

Partner API Checkout

Admin Checkout
```

Now every entry point needs the same workflow.

Developers start copying logic.

Duplication appears.

Maintenance cost increases.

---

# Problem 3: Business Knowledge Is Scattered

Suppose checkout changes.

A new fraud check must happen before payment.

```text
Fraud Check
↓
Payment
```

Now every workflow implementation must be updated.

Miss one.

You introduce a production bug.

---

# The Missing Business Capability

The system exposes technical operations:

```text
reserveInventory()

calculateTax()

processPayment()

createShipment()
```

But the business capability is:

```text
Checkout
```

This is the abstraction we should expose.

---

# Introducing CheckoutFacade

```java
public class CheckoutFacade {

    public Order checkout(OrderRequest request) {

        customerService.validate(request);

        inventoryService.reserve(request);

        pricingService.calculate(request);

        taxService.calculate(request);

        paymentService.process(request);

        Order order =
                orderService.create(request);

        shippingService.createShipment(order);

        notificationService.send(order);

        auditService.record(order);

        return order;
    }
}
```

Now clients see:

```java
checkoutFacade.checkout(request);
```

instead of

```java
8 different service calls
```

---

# What Did We Gain?

The number of subsystem operations did not decrease.

The complexity still exists.

But the complexity is now contained.

This is the essence of Facade.

---

# Production Challenge #1: Failure Handling

Real systems fail.

Imagine:

```text
Inventory Reserved ✅

Payment Processed ✅

Shipment Creation ❌
```

What happens now?

The system is inconsistent.

Inventory is reserved.

Payment is captured.

Shipment does not exist.

---

# Facade as Workflow Coordinator

A facade often becomes responsible for coordinating failure strategies.

Example:

```java
public Order checkout(OrderRequest request) {

    inventoryService.reserve(request);

    try {

        paymentService.process(request);

        Order order =
                orderService.create(request);

        shippingService.createShipment(order);

        return order;

    } catch (Exception ex) {

        inventoryService.release(request);

        throw ex;
    }
}
```

The facade orchestrates recovery.

Subsystems remain focused on their own responsibilities.

---

# Production Challenge #2: Transaction Boundaries

In monolithic systems:

```java
@Transactional
public Order checkout(OrderRequest request) {
   ...
}
```

The facade often becomes the transaction boundary.

This is common in Spring applications.

Why?

Because the facade represents the complete business operation.

---

# Production Challenge #3: Workflow Evolution

Initially:

```text
Inventory
↓
Payment
↓
Shipping
```

Later:

```text
Inventory
↓
Fraud Detection
↓
Payment
↓
Reward Points
↓
Shipping
```

Without a facade:

Every client changes.

With a facade:

Only the workflow implementation changes.

Clients remain untouched.

---

# Why Architects Love Facades

Architects care deeply about:

```text
Change Isolation
```

A checkout workflow changes frequently.

Client applications should not.

The facade acts as a shock absorber.

Changes stop at the boundary.

---

# Facade vs Service Layer

Developers often ask:

> Isn't CheckoutFacade just a service?

Sometimes yes.

Sometimes no.

The difference is intent.

A normal service focuses on a domain.

```text
PaymentService

InventoryService

ShippingService
```

A facade focuses on coordination.

```text
CheckoutFacade
```

It orchestrates multiple domains.

---

# Spring Applications and Facades

A very common structure:

```text
Controller
    ↓
Facade
    ↓
Domain Services
    ↓
Repositories
```

Example:

```java
checkoutController.checkout()

       ↓

checkoutFacade.checkout()

       ↓

inventoryService.reserve()

paymentService.process()

shippingService.ship()
```

This structure scales surprisingly well.

Many enterprise systems unknowingly implement Facade every day.

---

# Enterprise Evolution

As systems grow:

```text
Controller
     ↓
CheckoutFacade
     ↓
Inventory Service
Payment Service
Shipping Service
Tax Service
Fraud Service
Notification Service
```

The facade becomes the single entry point for a business capability.

This improves:

* Maintainability
* Readability
* Testability
* Change isolation

---

# A Hidden Benefit: Better Testing

Without Facade:

Tests require many dependencies.

```java
InventoryService

PaymentService

ShippingService

NotificationService
```

With Facade:

Clients mock only:

```java
CheckoutFacade
```

Complexity is hidden.

Testing becomes easier.

---

# Architect's Perspective

Junior developers see:

> A class that calls multiple services.

Architects see:

> A business workflow boundary.

The difference is significant.

A facade is not valuable because it reduces lines of code.

A facade is valuable because it controls where business workflows live.

---

# Key Takeaways

* Real checkout systems involve many subsystems.
* Controllers should not own business workflows.
* Facades centralize workflow coordination.
* Facades improve change isolation.
* Facades often become transaction boundaries.
* Facades help manage failures and orchestration.
* Enterprise systems frequently use facades as business capability boundaries.

---

# Chapter Summary

The checkout process demonstrates why Facade becomes increasingly important as systems grow.

What starts as a simple wrapper eventually evolves into a stable business-facing boundary that coordinates multiple domains, manages workflow evolution, and isolates clients from internal complexity.

This is where Facade moves beyond a design pattern and becomes an architectural tool.
