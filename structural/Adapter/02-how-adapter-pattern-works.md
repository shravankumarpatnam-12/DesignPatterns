# How Adapter Pattern Works

## Understanding the Solution Before Writing Code

In the previous chapter, we learned why Adapter Pattern exists.

We discovered that the real problem is not functionality.

The real problem is interface incompatibility.

Two systems want to work together.

But they speak different languages.

The Adapter Pattern acts as a translator between them.

Before writing Java code, let's understand the mechanics behind the pattern.

---

# The Core Idea

Imagine a customer support executive who speaks only English.

A customer arrives who speaks only Japanese.

Neither side can communicate.

To solve the problem, we introduce:

```text
Translator
```

Now communication becomes possible.

```text
Customer
   ↓
Translator
   ↓
Support Executive
```

The translator understands both sides.

The translator converts one language into another.

This is exactly what an Adapter does.

---

# The Four Participants

Every Adapter Pattern contains four key participants.

```text
Client
Target
Adapter
Adaptee
```

Let's understand each one.

---

# 1. Client

The Client is the code that needs a service.

Example:

```java
OrderService
```

The client doesn't care how the work is performed.

It only expects a particular interface.

---

# 2. Target

The Target is the interface expected by the client.

```java
public interface PaymentProcessor {

    PaymentResult process(PaymentRequest request);

}
```

The client only understands this interface.

Everything in the application is built around this contract.

---

# 3. Adaptee

The Adaptee is the existing class that has useful functionality.

Unfortunately, it exposes a different interface.

Example:

```java
public class StripeSdk {

    public void makePayment(
            double amount,
            String currency) {

        // payment logic

    }
}
```

The functionality is useful.

The interface is incompatible.

---

# 4. Adapter

The Adapter sits between the Target and Adaptee.

```java
public class StripeAdapter
        implements PaymentProcessor {

}
```

Its job is simple:

```text
Receive request from Client
        ↓
Convert request
        ↓
Call Adaptee
        ↓
Convert response
        ↓
Return result
```

---

# Visualizing the Flow

Without Adapter:

```text
Client
   ↓
PaymentProcessor.process()

StripeSdk.makePayment()
```

Problem:

```text
Method mismatch
Parameter mismatch
Return type mismatch
```

The connection is impossible.

---

With Adapter:

```text
Client
   ↓
PaymentProcessor
   ↓
StripeAdapter
   ↓
StripeSdk
```

Now the communication works.

---

# Request Translation

The Adapter's primary responsibility is translation.

Suppose the client sends:

```java
PaymentRequest request
```

But Stripe requires:

```java
amount
currency
```

The adapter converts:

```text
PaymentRequest
        ↓
amount + currency
```

before invoking Stripe.

---

# Response Translation

The opposite process happens on the way back.

Stripe may return:

```java
StripeResponse
```

But the application expects:

```java
PaymentResult
```

The adapter converts:

```text
StripeResponse
        ↓
PaymentResult
```

before returning control.

---

# Exception Translation

Real systems rarely stop at methods and parameters.

External systems often throw completely different exceptions.

Example:

```java
StripeApiException
```

Business code expects:

```java
PaymentFailedException
```

The adapter can translate exceptions as well.

```text
StripeApiException
        ↓
PaymentFailedException
```

This prevents vendor-specific exceptions from leaking into business logic.

---

# Data Model Translation

This is where Adapter becomes extremely valuable.

Suppose your application uses:

```java
PaymentRequest
```

Stripe uses:

```java
StripePaymentRequest
```

PayPal uses:

```java
PayPalTransactionRequest
```

Bank API uses:

```java
BankTransferCommand
```

The adapter becomes responsible for mapping data.

```text
Internal Model
        ↓
Adapter
        ↓
Vendor Model
```

This keeps vendor-specific objects away from business code.

---

# Runtime Flow Step by Step

Let's walk through an actual request.

### Step 1

Client initiates payment.

```java
paymentProcessor.process(request);
```

---

### Step 2

Adapter receives the request.

```java
stripeAdapter.process(request);
```

---

### Step 3

Adapter extracts required fields.

```java
request.getAmount();
request.getCurrency();
```

---

### Step 4

Adapter calls Stripe.

```java
stripeSdk.makePayment(
        amount,
        currency
);
```

---

### Step 5

Stripe returns a response.

```java
StripeResponse response;
```

---

### Step 6

Adapter converts the response.

```java
PaymentResult result;
```

---

### Step 7

Result returns to business code.

```java
return result;
```

Business code never knows Stripe exists.

---

# The Most Important Architectural Benefit

Notice what happened.

The client depends on:

```java
PaymentProcessor
```

The client does NOT depend on:

```java
StripeSdk
PayPalSdk
BankApi
```

This creates a stable boundary.

```text
Business Layer
        ↓
Abstraction
        ↓
Adapters
        ↓
External Systems
```

This structure allows technologies to change without affecting business logic.

---

# Real Enterprise Example

Consider cloud storage.

Your application expects:

```java
StorageService
```

Today:

```text
StorageService
      ↓
S3Adapter
      ↓
Amazon S3
```

Tomorrow:

```text
StorageService
      ↓
AzureBlobAdapter
      ↓
Azure Blob Storage
```

Business code remains identical.

Only the adapter changes.

This is exactly how cloud-agnostic systems are built.

---

# What Adapter Does NOT Do

Many developers misunderstand Adapter.

Adapter does NOT:

* Add new behavior
* Enhance functionality
* Optimize performance
* Secure communication

Its primary responsibility is:

```text
Translation
```

Nothing more.

Nothing less.

---

# Mental Model for Interviews

Whenever you see:

```text
System A works
System B works

But their interfaces are incompatible
```

Think:

```text
Adapter Pattern
```

Whenever you hear:

```text
Third-party integration
Legacy system integration
Vendor migration
API normalization
```

Think:

```text
Adapter Pattern
```

---

# Key Takeaways

## Participants

* Client
* Target
* Adapter
* Adaptee

## Adapter Responsibilities

* Method translation
* Parameter translation
* Response translation
* Exception translation
* Data model translation

## Architect Perspective

An Adapter is a protective layer between your business logic and external systems.

The larger the system becomes, the more valuable this layer becomes.

This simple pattern eventually evolves into enterprise concepts such as:

* Ports and Adapters
* Hexagonal Architecture
* Anti-Corruption Layers
* Integration Boundaries

Understanding this runtime flow is essential before learning the two major Adapter implementations:

1. Object Adapter
2. Class Adapter
