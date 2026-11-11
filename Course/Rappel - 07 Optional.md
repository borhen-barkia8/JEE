---
tags:
  - jee
  - java
  - rappel
  - optional
  - revision
---

# 07 — Optional

> [!abstract] Goal
> Use `java.util.Optional` to represent the possible **absence of a value** explicitly and handle it safely without scattering `null` checks.

![[JEE/Excalidraw/Rappel-07-Optional.excalidraw|1000]]

---

## 1. Why Optional?

`Optional<T>` is a container object that may or may not contain a non-null value. It makes absence explicit in APIs and encourages deliberate handling.

| Approach | Problem / Benefit |
|---|---|
| Returning/using `null` | Easy to forget checks → `NullPointerException`; meaning of absence is implicit |
| `Optional<T>` | Absence is part of the type/contract; provides fluent, explicit handling methods |

> [!note]
> `Optional` is most useful as a **method return type** to signal "value may be absent". It is generally not intended for fields, method parameters, or serialization models.

---

## 2. Creating Optional instances

| Factory method | Use when | Behavior if value is null |
|---|---|---|
| `Optional.of(value)` | Value is guaranteed non-null | Throws `NullPointerException` |
| `Optional.ofNullable(value)` | Value may be null | Returns empty `Optional` for `null` |
| `Optional.empty()` | You want to represent absence explicitly | Returns empty `Optional` |

```java
import java.util.Optional;

Optional<String> a = Optional.of("JEE");          // present
Optional<String> b = Optional.ofNullable(null);   // empty
Optional<String> c = Optional.empty();            // empty
```

---

## 3. Checking and consuming values

### Presence checks

- `isPresent()` — `true` if a value exists
- `isEmpty()` — `true` if no value (Java 11+)
- `ifPresent(Consumer)` — run action only when present
- `ifPresentOrElse(Consumer, Runnable)` — handle present and empty cases (Java 9+)

```java
Optional<String> name = Optional.ofNullable(findName());

if (name.isPresent()) {
    System.out.println(name.get()); // safe here, but prefer alternatives below
}

name.ifPresent(n -> System.out.println("Name: " + n));

name.ifPresentOrElse(
    n -> System.out.println("Found: " + n),
    () -> System.out.println("Not found")
);
```

> [!warning]
> Avoid calling `get()` without verifying presence. On an empty `Optional`, `get()` throws `NoSuchElementException`.

---

## 4. Providing fallback values

| Method | When fallback is evaluated | Typical use |
|---|---|---|
| `orElse(T other)` | **Always** evaluated (argument computed eagerly) | Cheap constant/default value |
| `orElseGet(Supplier)` | Only when empty (lazy) | Expensive computation, I/O, object creation |
| `orElseThrow()` | Only when empty | Throw `NoSuchElementException` if absent |
| `orElseThrow(Supplier)` | Only when empty | Throw a meaningful/domain-specific exception |

```java
String v1 = b.orElse("default");                  // "default"
String v2 = b.orElseGet(() -> computeDefault());  // computed only if empty

String v3 = a.orElseThrow();                      // returns "JEE"
String v4 = b.orElseThrow(() -> new IllegalStateException("Missing value"));
```

> [!tip]
> Prefer `orElseGet(...)` over `orElse(...)` when the default is costly to produce, because `orElse` evaluates its argument even if a value is present.

---

## 5. Transforming and filtering values

`Optional` supports functional-style transformations similar to streams (but for 0 or 1 element).

| Method | Purpose | Result |
|---|---|---|
| `map(Function)` | Transform value if present | `Optional<U>` (empty if empty) |
| `flatMap(Function)` | Transform with a function that already returns `Optional<U>` | `Optional<U>` (avoids `Optional<Optional<U>>`) |
| `filter(Predicate)` | Keep value only if predicate matches | same `Optional` or empty |

```java
Optional<String> email = Optional.ofNullable("  user@example.com  ");

Optional<String> normalized = email
        .map(String::trim)
        .filter(e -> e.contains("@"))
        .map(String::toLowerCase);

Optional<Integer> length = normalized.map(String::length);

// flatMap example: when mapper returns Optional
Optional<String> domain = normalized.flatMap(e -> extractDomain(e));

static Optional<String> extractDomain(String e) {
    int i = e.indexOf('@');
    return (i >= 0 && i < e.length() - 1)
            ? Optional.of(e.substring(i + 1))
            : Optional.empty();
}
```

---

## 6. Practical patterns and pitfalls

### Good practices
- Return `Optional` from query/find methods where "not found" is a normal outcome.
- Prefer expressive operations: `map`, `filter`, `flatMap`, `orElseThrow`.
- Keep `Optional` usage simple and readable; deep chains can hurt clarity.

### Pitfalls to avoid

| Pitfall | Why problematic | Better approach |
|---|---|---|
| `optional.get()` without check | Can throw at runtime | `orElse`, `orElseGet`, `ifPresent`, `orElseThrow` |
| Using `Optional` for fields/params | Adds complexity/overhead, not always supported by frameworks/serialization | Use normal types and nullability conventions/validation |
| Wrapping then immediately unwrapping | Pointless ceremony | Use direct value handling when simpler |
| `Optional.of(null)` | Throws NPE | Use `Optional.ofNullable(...)` |

> [!success] Key takeaway
> `Optional` makes absence explicit. Create it correctly (`of`/`ofNullable`/`empty`), avoid unchecked `get()`, and use `map`/`filter`/`flatMap` plus `orElse*` to handle missing values clearly.

---
**Series:** Rappel (Java) · **Previous:** [[JEE/Course/Rappel - 06 Streams API]] · **Next:** [[JEE/Course/Spring Boot - 01 Enterprise Needs and Spring Boot]]
