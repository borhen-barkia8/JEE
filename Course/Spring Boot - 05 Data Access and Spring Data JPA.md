---
tags:
  - jee
  - spring
  - spring-boot
  - jpa
  - revision
---

# 05 — Classic JPA Essentials (No Mapping / Relationships)

> [!abstract] Goal
> Understand **classic JPA** usage in a Spring/JEE context: the role of JPA and its provider, `EntityManager`/persistence context, and the core methods used to create, read, update, and delete entities—**without** ORM mapping annotations and **without** UML/relationship mappings.

![[JEE/Excalidraw/Spring-Boot-04-JPA.excalidraw|1000]]

---

## 1. What is JPA?

**JPA (Jakarta/Java Persistence API)** is a **specification** that standardizes how Java applications manage persistent data (objects stored in a database).

| Element | Meaning |
|---|---|
| **JPA** | The API/specification (interfaces and contracts) |
| **Persistence Provider** | Implementation of JPA (e.g., Hibernate, EclipseLink) that executes operations against the database |
| **Persistence Context** | A managed set of entity instances tracked by JPA during a unit of work |
| **EntityManager** | Main API used by application code to interact with the persistence context/database |

> [!note]
> Application code typically works against the JPA API (`EntityManager`) while the provider handles SQL generation, synchronization, and database interaction.

---

## 2. EntityManager and access/injection

`EntityManager` performs persistence operations and manages entity state within a **persistence context**.

### Obtaining an EntityManager

| Context | Typical approach |
|---|---|
| **Java EE / Jakarta EE** | Inject with `@PersistenceContext` |
| **Spring** | Inject with `@PersistenceContext` (container-managed) or obtain via JPA configuration |
| **Standalone Java SE** | Create from `EntityManagerFactory` |

```java
import jakarta.persistence.EntityManager;
import jakarta.persistence.PersistenceContext;
import org.springframework.stereotype.Component;

@Component
public class ProductService {

    @PersistenceContext
    private EntityManager entityManager; // container-managed EntityManager
}
```

> [!tip]
> In managed environments, the container/Spring handles `EntityManager` lifecycle and ties it to transactions; application code focuses on persistence operations.

---

## 3. Entity instance states (conceptual)

Understanding states helps predict when SQL operations happen.

| State | Description |
|---|---|
| **New (transient)** | Object created with `new`, not yet associated with a persistence context |
| **Managed** | Associated with a persistence context; changes can be synchronized to DB |
| **Detached** | Was managed but persistence context is closed/cleared; no longer tracked |
| **Removed** | Scheduled for deletion when the persistence context is synchronized/flushed/committed |

---

## 4. Classic JPA CRUD methods (EntityManager)

### Create (Add)

- **`persist(entity)`**: makes a **new** entity instance managed (scheduled for insertion).

```java
Product p = new Product();
p.setName("Keyboard");
p.setPrice(50.0);

entityManager.persist(p); // add/insert (on flush/commit)
```

### Read (Find)

- **`find(EntityClass.class, id)`**: loads by primary key (returns `null` if not found).
- **`getReference(EntityClass.class, id)`**: returns a reference/proxy (may defer loading; throws if accessed and missing depending on context).

```java
Product p = entityManager.find(Product.class, 1L);
Product ref = entityManager.getReference(Product.class, 1L);
```

### Update

- **Managed entities**: modify fields while managed; changes are detected and synchronized automatically (dirty checking) at flush/commit.
- **`merge(detachedEntity)`**: copies state of a detached entity into a managed instance and returns the managed copy.

```java
// Update managed entity
Product p = entityManager.find(Product.class, 1L);
if (p != null) {
    p.setPrice(45.0); // synchronized at flush/commit
}

// Update detached entity
Product managed = entityManager.merge(detachedProduct);
```

> [!warning]
> `merge` does **not** make the passed object managed; it returns a different managed instance. Use the returned object for further tracked changes.

### Delete (Remove)

- **`remove(entity)`**: marks a **managed** entity for removal.

```java
Product p = entityManager.find(Product.class, 1L);
if (p != null) {
    entityManager.remove(p); // delete (on flush/commit)
}
```

---

## 5. Synchronization and useful operations

| Method | Purpose |
|---|---|
| `flush()` | Synchronize persistence context changes with the database now (within transaction) |
| `clear()` | Detach all managed entities (clear persistence context) |
| `detach(entity)` | Detach a specific managed entity |
| `contains(entity)` | Check if an entity instance is managed in the current persistence context |
| `refresh(entity)` | Reload entity state from the database, overwriting managed changes |

```java
entityManager.flush();          // force synchronization
boolean managed = entityManager.contains(p);
entityManager.detach(p);        // stop tracking p
entityManager.clear();          // detach all
```

> [!note]
> Actual SQL timing depends on provider behavior, flush mode, and transaction boundaries, but `persist/merge/remove` changes are applied to the database when the persistence context is synchronized (e.g., flush/commit).

---

## 6. Quick revision summary

| Goal | Classic JPA method |
|---|---|
| Add new entity | `persist(entity)` |
| Read by id | `find(Class, id)` / `getReference(Class, id)` |
| Update managed entity | modify fields (dirty checking) |
| Update detached entity | `merge(entity)` |
| Delete managed entity | `remove(entity)` |
| Sync/check state | `flush()`, `contains()`, `detach()`, `clear()`, `refresh()` |

> [!success] Key takeaway
> Classic JPA centers on **`EntityManager`** and the **persistence context**: use `persist`, `find`, `merge`, and `remove` for CRUD, while the provider synchronizes managed state with the database at flush/commit.

---
**Series:** Spring Boot · **Previous:** [[JEE/Course/Spring Boot - 04 Configuration Beans and Properties]]
