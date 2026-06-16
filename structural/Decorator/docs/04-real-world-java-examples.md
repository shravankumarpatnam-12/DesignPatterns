# Real-World Java Examples

## Why This Chapter Matters

One of the biggest misconceptions about Decorator is:

> "Nobody uses this pattern directly in real projects."

Actually, Java developers use Decorator almost every day.

The difference is that enterprise frameworks hide the implementation.

Once you learn to recognize it, you'll see Decorator everywhere.

---

# Example 1: Java I/O (The Classic Decorator)

This is one of the most famous Decorator implementations ever written.

Base component:

```java
InputStream
```

Concrete component:

```java
FileInputStream
```

```java
InputStream stream =
    new FileInputStream("data.txt");
```

Now suppose we need buffering.

Instead of creating:

```java
BufferedFileInputStream
```

Java decorates the stream:

```java
InputStream stream =
    new BufferedInputStream(
        new FileInputStream("data.txt")
    );
```

Need object deserialization?

```java
InputStream stream =
    new ObjectInputStream(
        new BufferedInputStream(
            new FileInputStream("data.txt")
        )
    );
```

Need compression?

```java
InputStream stream =
    new GZIPInputStream(
        new BufferedInputStream(
            new FileInputStream("data.txt")
        )
    );
```

---

## Why This Is Brilliant

Each decorator adds one capability:

```text
FileInputStream
        +
BufferedInputStream
        +
ObjectInputStream
        +
GZIPInputStream
```

No subclass explosion.

No duplicated code.

Unlimited combinations.

Exactly the problem Decorator was invented to solve.

---

# Example 2: Java Writer Hierarchy

Same idea.

Base component:

```java
Writer
```

Concrete component:

```java
FileWriter
```

Decorators:

```java
BufferedWriter
PrintWriter
```

Example:

```java
Writer writer =
    new PrintWriter(
        new BufferedWriter(
            new FileWriter("log.txt")
        )
    );
```

Capabilities are added dynamically.

---

# Example 3: Spring HttpServletRequest Wrappers

Servlet API provides:

```java
HttpServletRequest
```

Need additional behavior?

Wrap it.

Example:

```java
HttpServletRequestWrapper
```

```java
public class LoggingRequestWrapper
        extends HttpServletRequestWrapper {

    public LoggingRequestWrapper(
            HttpServletRequest request) {

        super(request);
    }

    @Override
    public String getParameter(String name) {

        System.out.println("Reading parameter");

        return super.getParameter(name);
    }
}
```

This is Decorator.

The request gains behavior without changing its implementation.

---

# Example 4: Spring Security Filter Chain

This is one of the most important enterprise examples.

Request enters:

```text
HTTP Request
```

Then passes through:

```text
Authentication Filter

Authorization Filter

Session Filter

CSRF Filter

Exception Filter
```

Each layer:

```text
Adds behavior

Delegates request
```

Exactly like:

```java
new LoggingDecorator(
    new EncryptionDecorator(
        component
    )
);
```

The architecture evolved from the same idea.

---

# Example 5: Spring AOP

Consider:

```java
@Service
public class PaymentService {

    public void process() {
        // business logic
    }
}
```

Now add:

```java
@Transactional
```

```java
@Retryable
```

```java
@Cacheable
```

Did Spring modify your class?

No.

Spring creates a wrapper around it.

Conceptually:

```text
TransactionDecorator

RetryDecorator

CachingDecorator

PaymentService
```

This is Decorator combined with Proxy.

---

# Example 6: Logging Frameworks

Suppose application logs to:

```text
Console
```

Later requirements change.

Need:

```text
Console

File

Kafka

Cloud Storage
```

Instead of modifying the logger repeatedly:

```java
Logger
```

gets wrapped with additional behaviors.

Many logging frameworks internally use decorator-like pipelines.

---

# Example 7: HTTP Client Pipelines

Imagine:

```java
client.execute(request);
```

Requirements:

* Logging
* Retry
* Metrics
* Tracing
* Authentication

Architecture often becomes:

```text
Logging Handler

Retry Handler

Metrics Handler

Authentication Handler

HTTP Client
```

Each layer:

```text
Adds behavior

Delegates
```

Decorator again.

---

# Example 8: Message Processing Systems

Kafka consumers often follow:

```text
Deserialize

Validate

Transform

Audit

Persist
```

Implementation:

```text
Processor

ValidationDecorator

AuditDecorator

MetricsDecorator
```

This makes processing pipelines highly composable.

---

# Example 9: Caching Layer

Base service:

```java
ProductService
```

```java
Product getProduct(id);
```

Add caching.

Without decorator:

```java
ProductService
```

becomes responsible for:

* Business logic
* Cache management

Violation of SRP.

Instead:

```java
CachingProductService
```

wraps:

```java
ProductService
```

Flow:

```text
Check Cache

If miss

Call Service

Store Result

Return
```

Business logic remains untouched.

---

# Example 10: Monitoring and Metrics

Modern systems collect:

* Response time
* Throughput
* Error rate

Decorator implementation:

```java
MetricsDecorator
```

```java
long start = System.currentTimeMillis();

notification.send(message);

recordMetric();
```

No business code changes.

Observability becomes plug-and-play.

---

# The Pattern Hidden Everywhere

Once you learn Decorator, you'll start recognizing a common shape:

```text
Receive Request

Add Behavior

Delegate

Add Behavior

Return Response
```

Whether it's:

* Spring Filters
* AOP
* Middleware
* Interceptors
* HTTP Pipelines
* Logging
* Metrics
* Tracing

the same fundamental idea exists.

---

# What Architects Notice

Junior developers see:

```java
BufferedInputStream
```

Architects see:

```text
Composable Capability System
```

The actual business capability is less important.

The architecture allows capabilities to be:

* Added
* Removed
* Reordered
* Combined

without changing existing code.

That flexibility is the real value.

---

# Key Takeaways

* Java I/O is the most famous Decorator implementation.
* Spring uses Decorator concepts extensively.
* Filters, interceptors, middleware, and processing pipelines are decorator-like structures.
* Enterprise systems use Decorator to add cross-cutting concerns.
* Logging, caching, retry, metrics, tracing, and security are common decorator responsibilities.
* Decorator is not a toy pattern; it is one of the most heavily used patterns in modern frameworks.
