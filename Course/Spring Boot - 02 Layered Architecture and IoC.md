---
tags:
  - jee
  - spring
  - spring-boot
  - architecture
  - ioc
  - revision
---

# 02 — Layered Architecture & Inversion of Control (IoC)

> [!abstract] Goal
> Learn how a typical Spring Boot application is structured in layers and how **IoC** reduces coupling by delegating object creation/wiring to the container.

![[JEE/Excalidraw/Spring-Boot-02-Layers-IoC.excalidraw|1000]]

---

## 1. Layered architecture in a Spring Boot application

A common enterprise structure separates responsibilities into layers. This improves readability, testability, and maintainability.

| Layer | Main responsibility | Typical role | Examples of tasks |
|---|---|---|---|
| **Presentation / Web** | Receive requests, expose endpoints, return responses | `@Controller`, `@RestController` | Input handling, request/response mapping, calling services |
| **Business / Service** | Implement business rules and use-case orchestration | `@Component` | Validation rules, workflows, coordination of data-access components |
| **Persistence / Data Access** | Interact with data storage | `@Component` | CRUD/data access operations, query execution |
| **Database** | Store persistent data | *(external system)* | Tables/records managed by DB engine |

### Why layering matters

- **Separation of concerns:** each layer has a focused role.
- **Loose coupling:** changes in one layer (e.g., persistence details) have limited impact on others.
- **Testability:** business logic can be tested with mocked dependencies.
- **Maintainability/extensibility:** easier to evolve features without rewriting the whole app.

> [!warning] Common design smell
> Putting business logic directly in controllers or data-access logic in services/controllers tightly couples layers and makes maintenance/testing harder.

---

## 2. Coupling problem (without IoC)

In a tightly coupled design, a class creates its own dependencies:

```java
public class NotificationController {
    private SMSNotificationService notification = new SMSNotificationService();

    public void envoyer(String msg) {
        notification.notifier(msg);
    }
}
```

**Issues:**
- Hard to replace `SMSNotificationService` with another implementation
- Difficult to unit test in isolation
- Changes propagate across dependent classes
- Object creation and business behavior are mixed

> [!tip]
> Prefer depending on **abstractions (interfaces)** and letting another component provide the concrete implementation.

---

## 3. Inversion of Control (IoC) principle

**Inversion of Control (IoC)** means the control of object creation, configuration, and dependency wiring is transferred from application code to a framework/container.

### What changes with IoC

| Aspect | Traditional approach | With IoC |
|---|---|---|
| Dependency creation | Class does `new` itself | Container creates/provides dependencies |
| Coupling | Often concrete and strong | Looser via abstractions/interfaces |
| Flexibility | Hard to swap implementations | Easier to switch implementations/config |
| Testing | Harder to isolate | Easier with substitutes/mocks |
| Developer focus | Plumbing + business logic | Mostly business logic |

### Example with abstraction

```java
public interface INotification {
    void notifier(String message);
}

public class NotificationController {
    private final INotification notification; // depends on abstraction

    public NotificationController(INotification notification) {
        this.notification = notification;
    }

    public void envoyer(String msg) {
        notification.notifier(msg);
    }
}
```

- `NotificationController` **does not know** which concrete implementation is used.
- It only knows the **contract** (`INotification`).
- The implementation can be decided/configured externally.

---

## 4. Spring IoC container (ApplicationContext)

In Spring, the IoC container is responsible for:
- **Instantiating** objects (beans)
- **Assembling/wiring** dependencies between beans
- **Managing** bean lifecycle within the application context

The central container abstraction is **`org.springframework.context.ApplicationContext`**.

> [!note] Metadata-driven wiring
> The container uses configuration metadata (annotations, Java config, or XML) to know what to create and how to inject dependencies.

### Practical benefits for revision

- Promotes **programming to interfaces**
- Centralizes dependency management
- Supports consistent application structure across layers
- Makes enterprise applications easier to extend and test

> [!success] Key takeaway
> **IoC** moves object construction and wiring out of business classes into the container (`ApplicationContext`), enabling cleaner layered designs and lower coupling.

---
**Series:** Spring Boot · **Previous:** [[JEE/Course/Spring Boot - 01 Enterprise Needs and Spring Boot]] · **Next:** [[JEE/Course/Spring Boot - 03 Beans and Dependency Injection]]
