# Building Your First Facade

## Learning by Refactoring a Real Problem

So far we have learned:

* Why Facade exists
* What Facade is
* How it reduces complexity

Now it's time to build one.

Instead of starting with the final solution, we'll start with a design that many developers create naturally.

Then we'll discover why it becomes problematic.

Finally, we'll refactor it into a Facade.

This mirrors how Facade emerges in real projects.

---

# The Business Requirement

An e-commerce application needs to place an order.

To complete an order, the system must:

1. Validate inventory
2. Process payment
3. Create shipment
4. Send confirmation

Simple enough.

Let's build it.

---

# Step 1: Create the Subsystems

## Inventory Service

```java
public class InventoryService {

    public boolean reserveItems(Order order) {

        System.out.println("Items reserved");

        return true;
    }
}
```

---

## Payment Service

```java
public class PaymentService {

    public boolean processPayment(Order order) {

        System.out.println("Payment processed");

        return true;
    }
}
```

---

## Shipping Service

```java
public class ShippingService {

    public void createShipment(Order order) {

        System.out.println("Shipment created");
    }
}
```

---

## Notification Service

```java
public class NotificationService {

    public void sendConfirmation(Order order) {

        System.out.println("Confirmation sent");
    }
}
```

---

# Step 2: Client Uses All Services Directly

```java
public class CheckoutController {

    private final InventoryService inventoryService =
            new InventoryService();

    private final PaymentService paymentService =
            new PaymentService();

    private final ShippingService shippingService =
            new ShippingService();

    private final NotificationService notificationService =
            new NotificationService();

    public void placeOrder(Order order) {

        inventoryService.reserveItems(order);

        paymentService.processPayment(order);

        shippingService.createShipment(order);

        notificationService.sendConfirmation(order);
    }
}
```

At first glance, this seems perfectly reasonable.

Many real systems start exactly like this.

---

# Why This Design Becomes a Problem

The controller now knows:

```text
Inventory Workflow

Payment Workflow

Shipping Workflow

Notification Workflow
```

The controller is no longer acting as a controller.

It has become an orchestrator.

It owns business workflow knowledge.

---

# The Coupling Problem

The controller depends on:

```text
InventoryService
PaymentService
ShippingService
NotificationService
```

Four dependencies.

Imagine six more services being added.

The controller grows.

The workflow becomes harder to maintain.

Complexity spreads.

---

# The Change Propagation Problem

Suppose payment processing changes.

Old API:

```java
paymentService.processPayment(order);
```

New API:

```java
paymentService.authorize(order);

paymentService.capture(order);
```

Now every client must change.

This creates maintenance overhead.

The more clients you have, the bigger the problem becomes.

---

# Identifying the Missing Abstraction

Ask yourself:

> What does the client really want?

Not:

```text
Reserve Inventory

Process Payment

Create Shipment

Send Notification
```

The client wants:

```text
Place Order
```

This is the abstraction we are missing.

---

# Step 3: Create the Facade

```java
public class OrderFacade {

    private final InventoryService inventoryService;

    private final PaymentService paymentService;

    private final ShippingService shippingService;

    private final NotificationService notificationService;

    public OrderFacade(
            InventoryService inventoryService,
            PaymentService paymentService,
            ShippingService shippingService,
            NotificationService notificationService) {

        this.inventoryService = inventoryService;
        this.paymentService = paymentService;
        this.shippingService = shippingService;
        this.notificationService = notificationService;
    }

    public void placeOrder(Order order) {

        inventoryService.reserveItems(order);

        paymentService.processPayment(order);

        shippingService.createShipment(order);

        notificationService.sendConfirmation(order);
    }
}
```

Notice what happened.

The workflow moved into a dedicated object.

The orchestration logic is now centralized.

---

# Step 4: Simplify the Client

Before:

```java
inventoryService.reserveItems(order);

paymentService.processPayment(order);

shippingService.createShipment(order);

notificationService.sendConfirmation(order);
```

After:

```java
orderFacade.placeOrder(order);
```

The client now focuses on business intent.

Not implementation details.

---

# Visualizing the Difference

## Before Facade

```text
CheckoutController
       |
       +--> InventoryService
       |
       +--> PaymentService
       |
       +--> ShippingService
       |
       +--> NotificationService
```

Multiple dependencies.

Workflow knowledge is distributed.

---

## After Facade

```text
CheckoutController
       |
       v
   OrderFacade
       |
       +--> InventoryService
       |
       +--> PaymentService
       |
       +--> ShippingService
       |
       +--> NotificationService
```

Single dependency.

Workflow knowledge is centralized.

---

# Adding Business Rules

Facades often become the natural place for workflow coordination.

Example:

```java
public void placeOrder(Order order) {

    boolean reserved =
            inventoryService.reserveItems(order);

    if (!reserved) {
        throw new OutOfStockException();
    }

    boolean paid =
            paymentService.processPayment(order);

    if (!paid) {
        throw new PaymentFailedException();
    }

    shippingService.createShipment(order);

    notificationService.sendConfirmation(order);
}
```

Now the facade coordinates the entire process.

Clients remain simple.

---

# What Changed Architecturally?

Many developers see:

```text
Added One More Class
```

Architects see:

```text
Created A Business Boundary
```

This is a crucial distinction.

The facade now owns:

* Workflow coordination
* Dependency orchestration
* Process sequencing
* Business operation exposure

The client owns none of these.

---

# Where Should Logic Live?

A common question is:

> Should business logic go inside a Facade?

The answer:

### Yes, for orchestration logic.

Good examples:

```text
Call sequencing

Workflow coordination

Process orchestration

Cross-service interaction
```

---

### No, for subsystem-specific logic.

Bad examples:

```text
Payment calculations

Inventory algorithms

Shipping pricing
```

Those belong inside their respective services.

The facade coordinates.

It should not replace subsystem responsibilities.

---

# The Facade Is Not a God Object

A common mistake:

```java
OrderFacade
```

becomes

```java
placeOrder()

cancelOrder()

refundOrder()

generateReport()

calculateRevenue()

createUser()

sendMarketingEmail()

exportData()
```

The facade grows endlessly.

This violates the Single Responsibility Principle.

A facade should represent a coherent business capability.

---

# Benefits Achieved

After introducing the facade:

### Simpler Client Code

```java
orderFacade.placeOrder(order);
```

---

### Reduced Coupling

One dependency instead of many.

---

### Centralized Workflow

Process knowledge exists in one place.

---

### Easier Maintenance

Internal subsystem changes stay behind the facade.

---

### Better Readability

Business intent becomes obvious.

---

# Architect's Perspective

At small scale:

Facade simplifies code.

At enterprise scale:

Facade defines system boundaries.

The pattern becomes less about hiding classes and more about controlling how complexity flows through the system.

This is why the same idea appears in:

* API Gateways
* Service Layers
* Integration Layers
* Legacy Wrappers
* Framework APIs

The implementation changes.

The principle remains the same.

---

# Key Takeaways

* Facade emerges naturally when workflows become complex.
* Clients should depend on business operations, not subsystem details.
* The facade centralizes orchestration.
* It reduces coupling and change propagation.
* It improves readability and maintainability.
* A facade coordinates services but should not absorb their responsibilities.
* At scale, a facade becomes an architectural boundary.

---

# Chapter Summary

In this chapter, we transformed a tightly coupled design into a Facade-based design.

We discovered that Facade is not merely a wrapper.

It is a dedicated abstraction that exposes business capabilities while hiding subsystem coordination.

This separation allows systems to evolve internally without constantly impacting clients.

The larger the system becomes, the more valuable this boundary becomes.
