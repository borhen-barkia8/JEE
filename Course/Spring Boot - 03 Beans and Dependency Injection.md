---
tags:
  - jee
  - spring
  - spring-boot
  - di
  - beans
  - revision
---

# 03 — Beans & Dependency Injection (DI)

> [!abstract] Goal
> Understand Spring **beans**, component discovery, and how **Dependency Injection (DI)** wires collaborators using constructor, setter, or field injection.

![[JEE/Excalidraw/Spring-Boot-03-DI.excalidraw|1000]]

---

## 1. What is a bean?

A **bean** is an object that is:
- **Instantiated** by the Spring IoC container
- **Assembled** (its dependencies are provided/wired)
- **Managed** by the container through its lifecycle in the application context

Spring discovers and registers beans using configuration metadata (annotations, Java config, XML). In annotation-based apps, **component scanning** finds eligible classes at startup.

> [!note]
> DI happens between Spring-managed beans: a bean is injected into another bean managed by the same container.

---

## 2. Declaring beans with annotations

Beans can be declared for detection and also communicate architectural intent. `@Component` is the generic annotation for Spring-managed components.

| Annotation / role | Layer / purpose | Meaning |
|---|---|---|
| `@Controller`, `@RestController` | Presentation/Web | Web controller (request handling) |
| `@Component` | Business | Service-layer component containing business logic/orchestration |
| `@Component` | Persistence | Data access component for persistence responsibilities |

### Example: layered components as beans

```java
@Component
public class SmsNotificationDao implements INotificationDao { /* ... */ }

@Component
public class SmsNotificationService implements INotification {
    private final INotificationDao dao;

    public SmsNotificationService(INotificationDao dao) {
        this.dao = dao;
    }
}

@Controller
public class NotificationController {
    private final INotification notification;

    public NotificationController(INotification notification) {
        this.notification = notification;
    }
}
```

---

## 3. Dependency Injection (DI) in Spring

**DI** is the mechanism that implements IoC: dependencies are provided by the container instead of being created inside the class.

- Injection points are often marked with `@Autowired` (depending on style/context).
- The container resolves compatible beans and injects them at creation/configuration time.
- Prefer depending on **interfaces/abstractions** to keep implementations swappable.

---

## 4. Injection types

### 4.1 Constructor injection (recommended)

Dependencies are passed through the constructor.

```java
@Controller
public class NotificationController {
    private final INotification notification;

    // @Autowired can be omitted when there is a single constructor
    public NotificationController(INotification notification) {
        this.notification = notification;
    }
}
```

**Advantages**
- Makes required dependencies explicit
- Supports immutability (`final` fields)
- Object is fully initialized after construction
- Easy to test (pass mocks/fakes in constructor)

> [!tip] Revision rule
> Prefer **constructor injection** for mandatory dependencies.

### 4.2 Setter injection

Dependencies are provided through setter methods (annotated with `@Autowired`).

```java
@Controller
public class NotificationController {
    private INotification notification;

    @Autowired
    public void setNotification(INotification notification) {
        this.notification = notification;
    }
}
```

**When useful**
- Optional dependencies
- Dependencies that may be reconfigured/replaced in specific scenarios

### 4.3 Field injection

Dependency is injected directly into the field.

```java
@Controller
public class NotificationController {
    @Autowired
    private INotification notification;
}
```

**Trade-offs**
- Concise syntax
- Hides dependencies in the class API
- Harder to instantiate/test without the container
- Less suitable for enforcing required/immutable collaborators

---

## 5. Comparison summary

| Criteria | Constructor | Setter | Field |
|---|---|---|---|
| Required dependencies | Excellent | Possible, less explicit | Implicit |
| Immutability (`final`) | Yes | No | No |
| Testability without container | Very good | Good | Weaker |
| Visibility of dependencies | Clear | Moderate | Hidden |
| Typical recommendation | **Preferred default** | Optional cases | Avoid for core dependencies |

> [!success] Key takeaway
> Declare classes as Spring **beans** (stereotypes/config), depend on abstractions, and let the container inject collaborators—**constructor injection** is the safest default for required dependencies.

---
**Series:** Spring Boot · **Previous:** [[JEE/Course/Spring Boot - 02 Layered Architecture and IoC]] · **Next:** [[JEE/Course/Spring Boot - 04 Configuration Beans and Properties]]
