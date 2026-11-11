---
tags:
  - jee
  - course
  - revision
  - index
---

# JEE Course — Revision Index

> [!abstract] About this course
> A concise, self-contained revision course for **JEE/Java** topics: Java OOP & core recap (*Rappel*) and **Spring Boot** essentials including classic JPA usage. Content is split into focused pages with linked Excalidraw diagrams for quick visual review.

> [!info] Scope notes
> - **No Java history/background** included.
> - **Classic JPA only** (EntityManager methods and core concepts) — **no ORM mapping annotations and no UML/relationship mappings**.
> - `JEE/Excalidraw/` contains diagram sources (open in Obsidian Excalidraw view); `JEE/ressources/` contains original course PDFs.

---

## How to use this course

1. **Start with Rappel** to refresh core Java concepts used throughout JEE/Spring.
2. **Continue with Spring Boot** in order to build from architecture/IoC to DI, configuration, and persistence.
3. **Review the embedded diagrams** at the top of each page for a visual summary.
4. **Use the Series navigation** at the bottom of each page to move Previous/Next.

> [!tip] Revision strategy
> For each page: read the **Goal** callout → scan tables → review code examples → check the **Key takeaway** callout.

---

## Part 1 — Rappel (Java Core)

| # | Page | Focus | Diagram |
|---|---|---|---|
| 01 | [[JEE/Course/Rappel - 01 OOP Foundations]] | Overloading vs overriding, polymorphism, abstraction, interfaces | ![[JEE/Excalidraw/Rappel-01-OOP.excalidraw]] |
| 02 | [[JEE/Course/Rappel - 02 Type Hierarchy Visibility and Casting]] | Visibility, inheritance rules, `final`/static, functional interfaces, up/downcasting | ![[JEE/Excalidraw/Rappel-02-Hierarchy-Casting.excalidraw]] |
| 03 | [[JEE/Course/Rappel - 03 Exceptions and Date Time API]] | `throw`/`throws`, try-catch-finally, checked vs unchecked, `java.time` | ![[JEE/Excalidraw/Rappel-03-Exceptions-Time.excalidraw]] |
| 04 | [[JEE/Course/Rappel - 04 Collections and Maps]] | Collections vs Map, key types/implementations, iteration, safe modification | ![[JEE/Excalidraw/Rappel-04-Collections.excalidraw]] |
| 05 | [[JEE/Course/Rappel - 05 Ordering and Searching]] | `Comparable` vs `Comparator`, sorting APIs, searching, equality pitfalls | ![[JEE/Excalidraw/Rappel-05-Ordering.excalidraw]] |
| 06 | [[JEE/Course/Rappel - 06 Streams API]] | Stream pipeline, intermediate vs terminal operations, collectors, primitives | ![[JEE/Excalidraw/Rappel-06-Streams.excalidraw]] |
| 07 | [[JEE/Course/Rappel - 07 Optional]] | Creating/consuming `Optional`, fallbacks, `map`/`flatMap`/`filter`, pitfalls | ![[JEE/Excalidraw/Rappel-07-Optional.excalidraw]] |

### Rappel quick checklist
- [ ] Can explain compile-time vs runtime dispatch (overloading vs overriding/polymorphism)
- [ ] Can choose abstract class vs interface and apply visibility/casting safely
- [ ] Can handle checked/unchecked exceptions and use `java.time` immutably
- [ ] Can pick the right Collection/Map and iterate/modify safely
- [ ] Can sort/search correctly and avoid `TreeSet`/`TreeMap` comparator-equality pitfalls
- [ ] Can build Stream pipelines and choose appropriate intermediate/terminal operations
- [ ] Can use `Optional` to express absence and avoid unsafe `null`/`get()` handling

---

## Part 2 — Spring Boot & Classic JPA

| # | Page | Focus | Diagram |
|---|---|---|---|
| 01 | [[JEE/Course/Spring Boot - 01 Enterprise Needs and Spring Boot]] | Enterprise requirements, Spring modules, Spring Boot starters/auto-config/packaging | ![[JEE/Excalidraw/Spring-Boot-01-Overview.excalidraw]] |
| 02 | [[JEE/Course/Spring Boot - 02 Layered Architecture and IoC]] | Layered architecture, coupling problem, IoC principle, `ApplicationContext` | ![[JEE/Excalidraw/Spring-Boot-02-Layers-IoC.excalidraw]] |
| 03 | [[JEE/Course/Spring Boot - 03 Beans and Dependency Injection]] | Beans, stereotypes, component scanning, constructor/setter/field injection | ![[JEE/Excalidraw/Spring-Boot-03-DI.excalidraw]] |
| 04 | [[JEE/Course/Spring Boot - 04 Configuration Beans and Properties]] | `@Qualifier`/`@Primary`, `@Bean`/`@Configuration`, externalized values with `@Value` | ![[JEE/Excalidraw/Spring-Boot-03-DI.excalidraw]] |
| 05 | [[JEE/Course/Spring Boot - 05 Data Access and Spring Data JPA]] | Classic JPA: `EntityManager`, persistence context, CRUD/sync methods (no mapping) | ![[JEE/Excalidraw/Spring-Boot-04-JPA.excalidraw]] |

### Spring Boot quick checklist
- [ ] Can explain what Spring Boot adds (starters, auto-configuration, embedded server) and role of `@SpringBootApplication`
- [ ] Can describe layered responsibilities and how IoC/DI reduces coupling
- [ ] Can define beans, choose injection style (prefer constructor), and resolve ambiguity
- [ ] Can declare explicit beans and inject externalized properties
- [ ] Can use classic JPA `EntityManager` operations (`persist/find/merge/remove`, flush/detach/clear) correctly

---

## Additional diagrams

![[JEE/Excalidraw/Spring-Boot-Essentials.excalidraw]]

![[JEE/Excalidraw/Rappel - Curriculum map.excalidraw.md]]

## Resources

- `JEE/Excalidraw/` — diagram files (`.excalidraw`, `.excalidraw.md`) for all sections
- `JEE/ressources/` — source course PDFs for reference

> [!success] Outcome
> After completing both parts, you should be able to explain core Java/JEE concepts and implement/revise a basic Spring Boot application structure with IoC/DI, configuration, and classic JPA persistence operations.
