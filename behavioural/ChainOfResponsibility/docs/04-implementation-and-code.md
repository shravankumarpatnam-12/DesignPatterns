# Implementation and Code

## Mapping the Architecture to Code

In the previous chapter, we transformed:

```text id="k4m7qp"
Centralized Decision Engine
```

into:

```text id="v8p3wr"
Processing Pipeline
```

Now we need to translate that architecture into code.

The Chain of Responsibility Pattern uses a sequence of handlers.

Each handler owns a single responsibility.

Each handler decides whether to:

```text id="m5k2xt"
Process

Forward

Stop
```

---

## Pattern Participants

### Handler

Defines the processing contract.

Responsible for:

```text id="r9m4pw"
Handle Request

Forward Request
```

---

### Concrete Handlers

Implement specific business rules.

Examples:

```text id="f7k3qn"
AuthenticationHandler

AuthorizationHandler

FraudHandler

ApprovalHandler
```

---

### Client

Creates the request and sends it to the chain.

The client does not know which handler processes the request.

---

## UML View

```text id="w2m8pr"
                 ┌──────────────┐
                 │   Handler    │
                 ├──────────────┤
                 │ next         │
                 │ handle()     │
                 └──────┬───────┘
                        ▲
                        │
      ┌─────────────────┼─────────────────┐
      │                 │                 │
      ▼                 ▼                 ▼

Authentication   Authorization     Approval
   Handler          Handler         Handler
```

The request flows through the chain.

---

## Step 1: Create the Handler Contract

```java id="a8p4mx"
public abstract class Handler {

    protected Handler next;

    public Handler setNext(
            Handler next) {

        this.next = next;

        return next;
    }

    public abstract void handle(
            Request request);
}
```

Every handler follows the same structure.

Notice:

```java id="q5m7wr"
protected Handler next;
```

This is what creates the chain.

---

## Step 2: Create the Request Object

```java id="u9k2pv"
public class Request {

    private final String user;

    public Request(String user) {
        this.user = user;
    }

    public String getUser() {
        return user;
    }
}
```

The request moves through all handlers.

---

## Step 3: Authentication Handler

```java id="v3m8qt"
public class AuthenticationHandler
        extends Handler {

    @Override
    public void handle(
            Request request) {

        System.out.println(
            "Authentication Passed"
        );

        if(next != null) {
            next.handle(request);
        }
    }
}
```

This handler performs one responsibility.

Nothing more.

---

## Step 4: Authorization Handler

```java id="k7p4wn"
public class AuthorizationHandler
        extends Handler {

    @Override
    public void handle(
            Request request) {

        System.out.println(
            "Authorization Passed"
        );

        if(next != null) {
            next.handle(request);
        }
    }
}
```

Again:

```text id="m2v5qr"
One Handler

One Responsibility
```

---

## Step 5: Approval Handler

```java id="r8k3px"
public class ApprovalHandler
        extends Handler {

    @Override
    public void handle(
            Request request) {

        System.out.println(
            "Request Approved"
        );
    }
}
```

The final handler completes processing.

---

## Step 6: Build the Chain

```java id="y4m9wt"
AuthenticationHandler auth =
        new AuthenticationHandler();

AuthorizationHandler authorize =
        new AuthorizationHandler();

ApprovalHandler approval =
        new ApprovalHandler();

auth.setNext(authorize)
    .setNext(approval);
```

Result:

```text id="g6k2pr"
Authentication
      ↓
Authorization
      ↓
Approval
```

The chain is assembled dynamically.

---

## Step 7: Send the Request

```java id="n3p8qx"
Request request =
        new Request("John");

auth.handle(request);
```

Output:

```text id="f7m4wr"
Authentication Passed

Authorization Passed

Request Approved
```

The client does not know:

```text id="u5k9pt"
How Many Handlers Exist

Which Handler Runs

What Order Is Used
```

The chain owns that knowledge.

---

## Short-Circuit Processing

One powerful feature of Chain is stopping execution.

Example:

```java id="x8m2qv"
public class AuthenticationHandler
        extends Handler {

    @Override
    public void handle(
            Request request) {

        boolean authenticated = false;

        if(!authenticated) {

            System.out.println(
                "Authentication Failed"
            );

            return;
        }

        next.handle(request);
    }
}
```

Output:

```text id="k2p7wr"
Authentication Failed
```

The chain stops immediately.

Remaining handlers never execute.

---

## Dynamic Chain Configuration

Different environments may require different pipelines.

### Standard

```text id="r5m8px"
Authentication
      ↓
Authorization
      ↓
Approval
```

---

### Enterprise

```text id="v9k3qt"
Authentication
      ↓
Authorization
      ↓
Fraud Check
      ↓
Compliance Check
      ↓
Approval
```

No existing handlers change.

Only composition changes.

This is a major architectural advantage.

---

## Middleware as Chain of Responsibility

Many modern frameworks implement this pattern.

### Spring Security

```text id="m7p4wr"
Authentication Filter
      ↓
Authorization Filter
      ↓
Security Context Filter
```

---

### ASP.NET Middleware

```text id="n2k8px"
Middleware A
      ↓
Middleware B
      ↓
Middleware C
```

---

### Express.js

```javascript id="t8m5qv"
app.use(authMiddleware);

app.use(loggingMiddleware);

app.use(validationMiddleware);
```

Each middleware behaves like a handler.

---

## The Open-Closed Principle in Action

Suppose business introduces:

```text id="f3k7pr"
Risk Assessment
```

Traditional approach:

```text id="u4m2wx"
Modify Existing Service
```

Chain approach:

```java id="r9p5qt"
public class RiskHandler
        extends Handler {

    @Override
    public void handle(
            Request request) {

        System.out.println(
            "Risk Checked"
        );

        next.handle(request);
    }
}
```

Then:

```text id="k8m4pw"
Authorization
      ↓
Risk Check
      ↓
Approval
```

No existing code changes.

---

## The Most Important Insight

Many developers think:

> Chain of Responsibility passes requests through handlers.

That is true.

But it misses the architectural value.

The real purpose is:

> Transforming a large, centralized processing engine into a collection of small, composable business rules.

Each rule evolves independently.

Each rule remains testable.

Each rule can be added, removed, reordered, or replaced.

That flexibility is what makes the pattern powerful.
