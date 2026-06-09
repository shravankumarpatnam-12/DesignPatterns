# Building an Adapter Step by Step

## From Problem to Production-Ready Solution

Most Adapter Pattern tutorials start with:

```java
Target
Adapter
Adaptee
```

and immediately jump into UML diagrams.

The problem is that developers learn the structure but never understand how they would naturally arrive at the solution.

In this chapter, we will build the Adapter Pattern exactly as it evolves in a real project.

---

# The Business Requirement

Imagine you're building an e-commerce platform.

Customers can pay using:

* Credit Card
* UPI
* Wallet
* Net Banking

Your business service should not care which payment provider is being used.

The desired code is:

```java
paymentProcessor.process(request);
```

Simple.

Consistent.

Maintainable.

---

# Step 1: Direct Integration (The Naive Approach)

The company decides to use Stripe.

The Stripe SDK looks like:

```java
public class StripeSdk {

    public void makePayment(
            double amount,
            String currency) {

        System.out.println("Stripe Payment Processed");
    }
}
```

Business code:

```java
public class OrderService {

    private final StripeSdk stripeSdk;

    public OrderService(StripeSdk stripeSdk) {
        this.stripeSdk = stripeSdk;
    }

    public void placeOrder() {

        stripeSdk.makePayment(
                1000,
                "INR"
        );
    }
}
```

Initially this seems fine.

---

# The First Problem Appears

Six months later.

Management says:

> We need PayPal support.

PayPal SDK:

```java
public class PaypalSdk {

    public void executeTransaction(
            double amount) {

        System.out.println("PayPal Payment Processed");
    }
}
```

Now business code changes.

```java
if(provider.equals("STRIPE")) {

    stripeSdk.makePayment(amount, currency);

}
else if(provider.equals("PAYPAL")) {

    paypalSdk.executeTransaction(amount);

}
```

The system starts growing in the wrong direction.

---

# Why This Design Fails

Every new provider requires:

* New conditions
* New dependencies
* New testing paths
* New deployment risks

Business logic now knows:

* Stripe
* PayPal
* Razorpay
* Future providers

This violates a fundamental rule:

> Business code should focus on business rules.

Not vendor-specific details.

---

# Step 2: Introduce an Abstraction

Instead of depending on vendors directly, create a common contract.

```java
public interface PaymentProcessor {

    PaymentResult process(
            PaymentRequest request);

}
```

Now business code depends on:

```java
PaymentProcessor
```

instead of:

```java
StripeSdk
PaypalSdk
```

---

# Step 3: Create Domain Models

Payment Request:

```java
public class PaymentRequest {

    private double amount;
    private String currency;

}
```

Payment Result:

```java
public class PaymentResult {

    private boolean success;
    private String transactionId;

}
```

These become the language of your business system.

Notice something important.

Business code now owns:

```java
PaymentRequest
PaymentResult
```

Not Stripe.

Not PayPal.

This is intentional.

---

# Step 4: Build the Stripe Adapter

Stripe expects:

```java
makePayment(amount, currency)
```

Business code expects:

```java
process(request)
```

Adapter:

```java
public class StripeAdapter
        implements PaymentProcessor {

    private final StripeSdk stripeSdk;

    public StripeAdapter(
            StripeSdk stripeSdk) {

        this.stripeSdk = stripeSdk;
    }

    @Override
    public PaymentResult process(
            PaymentRequest request) {

        stripeSdk.makePayment(
                request.getAmount(),
                request.getCurrency()
        );

        return new PaymentResult(
                true,
                "STRIPE-123"
        );
    }
}
```

The adapter performs translation.

---

# Step 5: Build the PayPal Adapter

PayPal expects:

```java
executeTransaction(amount)
```

Adapter:

```java
public class PaypalAdapter
        implements PaymentProcessor {

    private final PaypalSdk paypalSdk;

    public PaypalAdapter(
            PaypalSdk paypalSdk) {

        this.paypalSdk = paypalSdk;
    }

    @Override
    public PaymentResult process(
            PaymentRequest request) {

        paypalSdk.executeTransaction(
                request.getAmount()
        );

        return new PaymentResult(
                true,
                "PAYPAL-456"
        );
    }
}
```

Same contract.

Different implementation.

---

# Step 6: Business Code Becomes Stable

Order service:

```java
public class OrderService {

    private final PaymentProcessor
            paymentProcessor;

    public OrderService(
            PaymentProcessor paymentProcessor) {

        this.paymentProcessor =
                paymentProcessor;
    }

    public void placeOrder() {

        PaymentRequest request =
                new PaymentRequest(
                        1000,
                        "INR"
                );

        paymentProcessor.process(request);
    }
}
```

Notice what disappeared.

No:

```java
StripeSdk
PaypalSdk
if-else
switch
```

Business code now focuses entirely on business behavior.

---

# Runtime Flow

When using Stripe:

```text
OrderService
      ↓
PaymentProcessor
      ↓
StripeAdapter
      ↓
StripeSdk
```

When using PayPal:

```text
OrderService
      ↓
PaymentProcessor
      ↓
PaypalAdapter
      ↓
PaypalSdk
```

OrderService remains unchanged.

---

# Step 7: Dependency Injection

In Spring Boot:

```java
@Configuration
public class PaymentConfig {

    @Bean
    public PaymentProcessor
    paymentProcessor() {

        return new StripeAdapter(
                new StripeSdk()
        );
    }
}
```

OrderService:

```java
@Service
public class OrderService {

    private final PaymentProcessor
            paymentProcessor;

    public OrderService(
            PaymentProcessor paymentProcessor) {

        this.paymentProcessor =
                paymentProcessor;
    }
}
```

Spring injects the adapter.

Business code never sees Stripe.

---

# Step 8: Switching Providers

Today:

```java
@Bean
public PaymentProcessor
paymentProcessor() {

    return new StripeAdapter(
            new StripeSdk()
    );
}
```

Tomorrow:

```java
@Bean
public PaymentProcessor
paymentProcessor() {

    return new PaypalAdapter(
            new PaypalSdk()
    );
}
```

No business code changes.

This is the real power of Adapter.

---

# Step 9: Unit Testing Becomes Easier

Testing OrderService:

```java
@Test
void shouldPlaceOrder() {

    PaymentProcessor mockProcessor =
            mock(PaymentProcessor.class);

    OrderService service =
            new OrderService(
                    mockProcessor
            );

    service.placeOrder();

    verify(mockProcessor)
            .process(any());
}
```

We don't need:

* Stripe
* PayPal
* Network calls

The service depends only on the abstraction.

---

# A More Realistic Enterprise Version

In real systems adapters often do more than method translation.

They also handle:

## Request Mapping

```java
PaymentRequest
       ↓
StripeRequest
```

---

## Response Mapping

```java
StripeResponse
       ↓
PaymentResult
```

---

## Exception Translation

```java
StripeException
       ↓
PaymentFailedException
```

---

## Logging

```java
Request Logging
Response Logging
Latency Tracking
```

---

## Monitoring

```java
Metrics
Tracing
Auditing
```

Adapters frequently become integration boundaries.

---

# Common Beginner Mistake

Many developers create adapters like:

```java
public class StripeAdapter {

    public void makePayment() {

    }
}
```

This is not an adapter.

This is merely a wrapper.

An adapter must convert one interface into another.

Without interface translation, there is no Adapter Pattern.

---

# How Architects Think About It

Developers often see:

```text
Adapter = Design Pattern
```

Architects see:

```text
Adapter = Boundary
```

The adapter separates:

```text
Business World
       ↓
Adapter
       ↓
Vendor World
```

This separation allows:

* Vendor replacement
* Cloud migration
* Legacy integration
* Technology evolution

without changing business logic.

---

# Key Takeaways

## Building an Adapter

1. Identify incompatible interfaces
2. Create a business-facing abstraction
3. Implement the abstraction in an adapter
4. Translate requests and responses
5. Inject the adapter into business code

## Benefits

* Loose coupling
* Easier testing
* Better maintainability
* Vendor independence
* Technology flexibility

## Architect Perspective

The Adapter Pattern is not merely a coding technique.

It is a strategy for protecting business logic from external change.

Every large-scale enterprise system eventually becomes a collection of adapters surrounding a stable business core.
