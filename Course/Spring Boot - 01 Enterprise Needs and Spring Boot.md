---
tags:
  - jee
  - spring
  - spring-boot
  - revision
---

# 01 — Enterprise Needs & Spring Boot Fundamentals

> [!abstract] Goal
> Understand what enterprise Java applications require, why Spring/Spring Boot exist, and the core Spring Boot features that speed up development.

![[JEE/Excalidraw/Spring-Boot-01-Overview.excalidraw|1000]]

---

## 1. Enterprise application requirements

Enterprise applications must satisfy both **functional** and **non-functional** needs.

| Category | What it means | Typical concerns |
|---|---|---|
| **Functional** | Business capabilities the system must provide | Domain workflows, use cases, business rules |
| **Performance** | Efficient response and scalability behavior | Response time, throughput, handling load growth |
| **Availability & resilience** | Service continuity under failures | High availability, fault tolerance, recovery |
| **Maintainability** | Ability to evolve safely over time | Open to extension, limited ripple effects, readable structure |
| **Security** | Protection of data and operations | AuthN/AuthZ, data protection, safe defaults |
| **Portability & distribution** | Run/integrate across environments and systems | Deployment flexibility, interoperability, remote communication |
| **Multi-client support** | Serve different kinds of clients | Web UI, mobile apps, APIs, other systems |

> [!tip] Design implication
> Meeting these needs reliably requires proven **architectures, frameworks, design patterns, and standards**—not ad-hoc reinvention.

---

## 2. Why a framework like Spring is useful

Building enterprise features directly from low-level APIs leads to heavy boilerplate, tight coupling, and configuration complexity.

**Spring** addresses this by providing:
- A **lightweight container** that manages application objects and their wiring.
- **Modularity**: include only needed modules instead of a monolithic runtime.
- Integration with common enterprise technologies while promoting good application structure.
- Core infrastructure for **Inversion of Control (IoC)** and **Dependency Injection (DI)** (detailed in next pages).

### Spring module groups (high level)

| Group | Purpose (revision view) |
|---|---|
| **Core Container** | Beans, context, IoC/DI, lifecycle management |
| **AOP / Instrumentation** | Cross-cutting concerns (e.g., logging/transactions-like aspects) separated from business code |
| **Data Access / Integration** | Support for persistence and integration concerns |
| **Web** | Building web/MVC/REST-style application layers |
| **Test** | Support for testing Spring components |

> [!note]
> You don’t need every module for every app—Spring’s modular design lets you pick what fits.

---

## 3. What Spring Boot adds on top of Spring

**Spring Boot** is an extension that simplifies creating, configuring, and running Spring applications.

| Spring Boot feature | What it does | Why it matters |
|---|---|---|
| **Starters** | Curated dependency descriptors (coherent dependency sets/versions) | Faster setup, fewer compatibility issues |
| **Auto-configuration** | Configures beans/defaults based on classpath and environment | Less manual configuration, quicker start |
| **Embedded server** | Packages/runs web apps with an embedded servlet container (e.g., Tomcat) | Simple local run and deployment as a standalone app |
| **Executable packaging** | Commonly run as a JAR with a JRE | Streamlined deployment workflow |
| **Opinionated defaults** | Sensible conventions out of the box | Focus on business logic earlier |

### @SpringBootApplication

`@SpringBootApplication` is typically placed on the main class and acts as a composed annotation that enables:
- **Bootstrapping** of the Spring Boot application
- **Component scanning** (detecting Spring-managed components)
- **Auto-configuration** triggers

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;

@SpringBootApplication
public class MyApplication {
    public static void main(String[] args) {
        ApplicationContext ctx = SpringApplication.run(MyApplication.class, args);
        for (String name : ctx.getBeanDefinitionNames()) {
            System.out.println(name);
        }
    }
}
```

> [!success] Key takeaway
> Spring provides the core programming model and container; **Spring Boot** reduces setup/configuration friction through starters, auto-configuration, and embedded runtime support so development can focus on domain logic.

---
**Series:** Spring Boot · **Previous:** [[JEE/Course/Rappel - 07 Optional]] · **Next:** [[JEE/Course/Spring Boot - 02 Layered Architecture and IoC]]
