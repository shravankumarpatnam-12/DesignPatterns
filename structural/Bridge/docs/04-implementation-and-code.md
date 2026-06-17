# Implementation and Code

## Mapping the Architecture to Code

The Bridge Pattern is built around two independent hierarchies.

```text
Abstraction Hierarchy
        |
        | delegates to
        ▼
Implementation Hierarchy
```

The abstraction focuses on **what** the system does.

The implementation focuses on **how** it does it.

This separation allows both hierarchies to evolve independently.

## Pattern Participants

### Abstraction

Defines the high-level business operation.

```text
Notification
```

### Refined Abstraction

Provides specialized business behavior.

```text
HighPriorityNotification
LowPriorityNotification
```

### Implementor

Defines implementation contracts.

```text
MessageSender
```

### Concrete Implementors

Provide actual implementations.

```text
EmailSender
SmsSender
PushSender
```

## UML View

```text
                    ┌─────────────────┐
                    │  Notification   │
                    ├─────────────────┤
                    │ sender          │──────────┐
                    │ send()          │          │
                    └─────────────────┘          │
                             ▲                   │
                             │                   │
          ┌──────────────────┴─────────────┐     │
          │                                │     │
┌─────────────────────┐      ┌─────────────────────┐
│HighPriorityNotif... │      │LowPriorityNotif...  │
└─────────────────────┘      └─────────────────────┘
                                               │
                                               ▼
                                 ┌─────────────────────┐
                                 │   MessageSender     │
                                 ├─────────────────────┤
                                 │ sendMessage()       │
                                 └─────────────────────┘
                                              ▲
                      ┌───────────────────────┼──────────────────────┐
                      │                       │                      │
              ┌──────────────┐      ┌──────────────┐      ┌──────────────┐
              │ EmailSender  │      │ SmsSender    │      │ PushSender   │
              └──────────────┘      └──────────────┘      └──────────────┘
```

The key observation:

> Notification does not know how messages are delivered.

It only knows that someone can deliver them.

## Step 1: Create the Implementor

The implementation hierarchy starts with an interface.

```java
public interface MessageSender {
    void sendMessage(String message);
}
```

## Step 2: Create Concrete Implementations

Email delivery:

```java
public class EmailSender implements MessageSender {

    @Override
    public void sendMessage(String message) {
        System.out.println("Email: " + message);
    }
}
```

SMS delivery:

```java
public class SmsSender implements MessageSender {

    @Override
    public void sendMessage(String message) {
        System.out.println("SMS: " + message);
    }
}
```

Push notification delivery:

```java
public class PushSender implements MessageSender {

    @Override
    public void sendMessage(String message) {
        System.out.println("Push: " + message);
    }
}
```

Notice that each implementation is focused on a single responsibility.

## Step 3: Create the Abstraction

The abstraction contains a reference to the implementor.

```java
public abstract class Notification {

    protected MessageSender sender;

    public Notification(MessageSender sender) {
        this.sender = sender;
    }

    public abstract void send(String message);
}
```

This is the actual bridge.

```java
protected MessageSender sender;
```

Without this reference, the two hierarchies remain disconnected.

## Step 4: Create Refined Abstractions

High priority notifications:

```java
public class HighPriorityNotification extends Notification {

    public HighPriorityNotification(MessageSender sender) {
        super(sender);
    }

    @Override
    public void send(String message) {
        sender.sendMessage(
            "[HIGH PRIORITY] " + message
        );
    }
}
```

Low priority notifications:

```java
public class LowPriorityNotification extends Notification {

    public LowPriorityNotification(MessageSender sender) {
        super(sender);
    }

    @Override
    public void send(String message) {
        sender.sendMessage(
            "[LOW PRIORITY] " + message
        );
    }
}
```

Notice something important.

Neither class contains email logic.

Neither class contains SMS logic.

They focus purely on priority behavior.

## Step 5: Client Code

Now we can mix and match behaviors dynamically.

```java
public class Main {

    public static void main(String[] args) {

        Notification notification1 =
            new HighPriorityNotification(
                new EmailSender()
            );

        notification1.send("Payment Failed");

        Notification notification2 =
            new LowPriorityNotification(
                new SmsSender()
            );

        notification2.send("Weekly Summary");
    }
}
```

Output:

```text
Email: [HIGH PRIORITY] Payment Failed

SMS: [LOW PRIORITY] Weekly Summary
```

## Why This Design Matters

Suppose tomorrow the business requests:

```text
WhatsAppSender
```

With inheritance, new combinations are required.

```text
HighPriorityWhatsAppNotification
LowPriorityWhatsAppNotification
```

With Bridge:

```java
public class WhatsAppSender
        implements MessageSender {

    @Override
    public void sendMessage(String message) {
        System.out.println("WhatsApp: " + message);
    }
}
```

No notification classes change.

No existing code changes.

The new feature plugs into the existing architecture.

## The Most Important Insight

Many developers think the Bridge Pattern is about creating interfaces.

It is not.

The real purpose is:

> Isolating business abstractions from implementation details so they can evolve independently.

The interfaces, abstract classes, and UML diagrams are merely tools used to achieve that architectural goal.
