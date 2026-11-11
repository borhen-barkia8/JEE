---
tags:
  - jee
  - spring
  - spring-boot
  - configuration
  - revision
---

# 04 — Ambiguity, Explicit Beans & Externalized Properties

> [!abstract] Goal
> Handle multiple bean candidates, declare beans explicitly with `@Bean`, and inject simple configuration values from properties files.

![[JEE/Excalidraw/Spring-Boot-03-DI.excalidraw|1000]]

---

## 1. Ambiguity: multiple implementations of the same type

When several beans implement the same interface, the container may not know which one to inject. This causes an ambiguity/error unless you disambiguate.

### Resolution strategies

| Strategy | Annotation / approach | Use when |
|---|---|---|
| **Qualifier** | `@Qualifier("name")` | You need a specific implementation at a specific injection point |
| **Primary bean** | `@Primary` | One implementation should be the default choice |
| **Inject multiple beans** | Inject collection/map or distinct qualified fields/params | Consumer legitimately needs several implementations |

### Example with @Qualifier

```java
public interface INotification {
    void notifier(String message);
}

@Component
@Qualifier("email")
public class EmailNotificationService implements INotification {
    public void notifier(String message) { /* send email */ }
}

@Component
@Qualifier("sms")
public class SmsNotificationService implements INotification {
    public void notifier(String message) { /* send sms */ }
}

@Controller
public class NotificationController {
    private final INotification emailService;
    private final INotification smsService;

    public NotificationController(
            @Qualifier("email") INotification emailService,
            @Qualifier("sms") INotification smsService) {
        this.emailService = emailService;
        this.smsService = smsService;
    }
}
```

> [!warning] Pitfall
> Ambiguous dependencies are usually detected at startup. Resolve them explicitly (`@Qualifier`/`@Primary`) to keep wiring predictable.

---

## 2. Explicit bean declaration with @Bean

Annotation-based detection with `@Component` is great for classes you own, but sometimes you need to register objects you cannot annotate (e.g., third-party/library types) or need custom construction logic.

### @Configuration + @Bean

- `@Configuration`: declares a class that provides bean definitions.
- `@Bean`: placed on a method; the method **returns** the object to register as a bean.
- **Bean name**: by default, the **method name**.
- **Bean type**: method return type.
- **Dependencies**: `@Bean` method parameters can be injected by Spring.

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import java.time.format.DateTimeFormatter;

@Configuration
public class AppConfig {

    @Bean
    DateTimeFormatter formatter() { // bean name: "formatter"
        return DateTimeFormatter.ofPattern("dd/MM/yyyy");
    }
}
```

> [!tip]
> Use `@Bean` for controlled instantiation, external library objects, or when object creation requires custom logic.

---

## 3. Injecting simple values with @Value

Spring can inject simple values (strings, numbers, booleans, etc.) in addition to bean dependencies. This supports **externalized configuration**.

### Why externalize configuration?

- Avoid hard-coded values in source code
- Reuse the same build across environments (dev/test/prod) with different settings
- Make configuration changes safer and more visible

### application.properties example

```properties
mon.application.nom=MonApplication
mon.application.version=1.0.0
server.port=8080
```

### Injecting values into a bean

```java
@Component
public class MonService {

    @Value("${mon.application.nom}")
    private String nomDeLApp; // field injection of a value

    private final String version;

    public MonService(@Value("${mon.application.version}") String version) {
        this.version = version; // constructor injection of a value
    }

    public void afficherConfig() {
        System.out.println("Application : " + nomDeLApp + " (v" + version + ")");
    }
}
```

> [!note]
> `@Value("${...}")` reads a property key from Spring’s environment/property sources (e.g., `application.properties`).

---

## 4. Quick comparison: beans vs values

| Need | Typical approach | Example |
|---|---|---|
| Inject a collaborator object | DI with bean type/interface | `INotification notification` |
| Register non-annotatable/3rd-party object | `@Configuration` + `@Bean` | `DateTimeFormatter` bean |
| Inject simple external setting | `@Value("${key}")` | app name/version/port-related config |

> [!success] Key takeaway
> Disambiguate bean wiring with **`@Qualifier`/`@Primary`**, create explicit beans with **`@Bean`**, and keep environments flexible by injecting settings via **`@Value`** from external properties.

---
**Series:** Spring Boot · **Previous:** [[JEE/Course/Spring Boot - 03 Beans and Dependency Injection]] · **Next:** [[JEE/Course/Spring Boot - 05 Data Access and Spring Data JPA]]
