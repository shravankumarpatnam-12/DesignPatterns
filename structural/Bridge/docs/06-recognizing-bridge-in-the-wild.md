# Recognizing Bridge in the Wild

## Why Most Developers Miss the Bridge Pattern

When developers learn the Bridge Pattern, they often understand the UML diagram but fail to recognize it in real systems.

The reason is simple:

Real-world implementations rarely have classes named:

```text
Abstraction
Implementor
ConcreteImplementor
```

Instead, they use business-oriented names.

As a result, developers use Bridge unknowingly but never recognize it.

An architect should be able to identify the pattern regardless of naming.

## The Recognition Formula

Whenever you see:

```text
Business Abstraction
        ×
Technical Implementation
```

You should investigate whether a Bridge exists.

Examples:

```text
Report
    ×
Export Format
```

```text
Notification
    ×
Delivery Channel
```

```text
Payment
    ×
Payment Provider
```

```text
Document
    ×
Storage Provider
```

```text
Media Player
    ×
Rendering Engine
```

The pattern is often hidden behind business terminology.

## A Practical Detection Technique

Ask three questions.

### Question 1

Can these two hierarchies evolve independently?

Example:

```text
Notification Type
```

and

```text
Delivery Channel
```

A new notification type does not require a new delivery mechanism.

A new delivery mechanism does not require a new notification type.

This is the first signal.

---

### Question 2

Are new combinations expected?

Example:

```text
Email + High Priority
SMS + High Priority
Push + Low Priority
```

If combinations continuously increase, inheritance becomes dangerous.

This is the second signal.

---

### Question 3

Would inheritance create combinatorial growth?

If the answer is yes, Bridge becomes a strong candidate.

## Example: Payment Systems

Many teams start like this:

```text
CreditCardStripePayment
CreditCardPayPalPayment

UpiStripePayment
UpiPayPalPayment

WalletStripePayment
WalletPayPalPayment
```

This is a warning sign.

Notice the dimensions:

```text
Payment Method
      ×
Payment Provider
```

Both change independently.

Bridge separates them.

```text
Payment Method
├── CreditCard
├── UPI
└── Wallet

Payment Provider
├── Stripe
├── PayPal
└── Razorpay
```

Adding a new provider affects only one hierarchy.

Adding a new payment method affects only the other.

## Example: Cloud Storage

Without separation:

```text
AwsImageStorage
AwsVideoStorage

AzureImageStorage
AzureVideoStorage

GcpImageStorage
GcpVideoStorage
```

The design is already showing symptoms.

The real dimensions are:

```text
File Type
      ×
Cloud Provider
```

Bridge allows both to evolve independently.

## A Common Misunderstanding

Developers often think:

> More classes means worse design.

Architects think differently.

The goal is not minimizing classes.

The goal is minimizing coupling.

Consider:

### Design A

```text
5 Classes
High Coupling
Hard to Change
```

### Design B

```text
12 Classes
Low Coupling
Easy to Extend
```

Design B is usually superior.

Class count is not the primary metric.

Changeability is.

## The Architect's Checklist

Before introducing Bridge, verify:

### Independent Change

```text
✓ Different reasons to change
```

### Long-Term Evolution

```text
✓ New variations expected
```

### Combination Growth

```text
✓ Inheritance creates explosion
```

### Stable Abstraction

```text
✓ Core business concept is clear
```

If all four conditions are true, Bridge is likely a strong fit.

## Final Mental Model

Do not memorize the pattern like this:

```text
Abstraction
Implementor
Refined Abstraction
Concrete Implementor
```

Memorize it like this:

```text
Dimension A
      ×
Dimension B
```

Then ask:

> Should these dimensions evolve independently?

If the answer is yes, you're no longer thinking like someone applying a pattern.

You're thinking like an architect designing for change.
