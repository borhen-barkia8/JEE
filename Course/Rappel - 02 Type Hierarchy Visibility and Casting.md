---
tags:
  - jee
  - java
  - types
  - casting
  - revision
---

# 02 — Type Hierarchy, Visibility & Casting

> [!abstract] Goal
> Understand how Java controls member access and inheritance, and how references change view through **upcasting/downcasting** safely.

![[JEE/Excalidraw/Rappel-02-Hierarchy-Casting.excalidraw|1000]]

---

## 1. Visibility (access modifiers)

Java provides four visibility levels, from most restrictive to most open:

| Modifier | Visible in same class | Same package | Subclasses (other package) | Everywhere |
|---|---:|---:|---:|---:|
| `private` | ✅ | ❌ | ❌ | ❌ |
| *(package-private)* (no modifier) | ✅ | ✅ | ❌ | ❌ |
| `protected` | ✅ | ✅ | ✅ | ❌ |
| `public` | ✅ | ✅ | ✅ | ✅ |

**Guideline:** expose the smallest API necessary (encapsulation), and increase visibility only when required.

---

## 2. Inheritance and implementation rules

| Relationship | Keyword | Notes |
|---|---|---|
| Class → Class | `extends` | **Single inheritance** only for classes |
| Interface → Interface | `extends` | **Multiple inheritance** allowed |
| Class → Interface(s) | `implements` | A class can implement multiple interfaces |

- `extends` models an “is-a” specialization (class hierarchy).
- `implements` promises to fulfill one or more contracts (interfaces).
- Interfaces avoid instance-state diamond issues, which is why multiple interface inheritance is allowed.

---

## 3. final, static, and non-overridable members

| Modifier / kind | Effect |
|---|---|
| `final` class | Cannot be subclassed |
| `final` method | Cannot be overridden in subclasses |
| `static` method | Belongs to class; not overridden polymorphically (can be hidden, different concept) |
| `private` method | Not visible to subclasses; cannot be overridden |
| `abstract` method | Must be overridden/implemented by a concrete subclass |

> [!warning]
> `static`, `private`, and `final` methods cannot be redefined polymorphically, so they cannot be declared `abstract`.

### Functional interface (important for lambdas/comparators)

A **functional interface** has exactly **one abstract method** (it may include `default`/`static` methods). This allows lambda expressions to provide its implementation.

```java
@FunctionalInterface
interface TextFormatter {
    String format(String text);
}

TextFormatter upper = t -> t.toUpperCase();
System.out.println(upper.format("hello")); // HELLO
```

---

## 4. Casting along a type hierarchy

Assume `class Dog extends Animal`.

### Upcasting (widening reference)
- **Implicit and safe**
- You treat a specific object as a more general type
- The reference exposes only the **static type’s API**

```java
Animal a = new Dog();   // upcast
// a.bark();            // compile-time error: bark() not in Animal
```

### Downcasting (narrowing reference)
- **Explicit**: `(Dog) a`
- Restores access to subtype-specific members
- Checked at runtime; wrong cast throws `ClassCastException`

```java
Animal a = new Dog();

// Preferred: pattern matching instanceof (Java 16+)
if (a instanceof Dog d) {
    d.bark();
}

// Unguarded cast: risky
Dog d2 = (Dog) a; // OK here, but fails at runtime if a is not a Dog
```

### Static type vs dynamic type

| Concept | Meaning | Why it matters |
|---|---|---|
| **Static type** | Type known at compile time (variable/reference type) | Determines which members are accessible/callable |
| **Dynamic type** | Actual object type at runtime | Determines overridden method executed (polymorphism) |

> [!tip]
> Upcasting reduces visible API but enables polymorphic code; downcast only after verifying the actual type (e.g., `instanceof`) to avoid runtime failures.

---

## 5. Quick revision pitfalls

- Calling subclass-only methods on an upcast reference without casting → **compile error**.
- Blind downcast on incompatible object → **`ClassCastException`**.
- Confusing method hiding (`static`) with overriding (runtime dispatch).
- Forgetting that a class can extend only one class but implement many interfaces.

> [!success] Key takeaway
> The **static type** controls what you can call; the **dynamic type** controls which overridden behavior runs. Use **upcasting** for abstraction and **guarded downcasting** to regain specific behavior safely.

---
**Series:** Rappel (Java) · **Previous:** [[JEE/Course/Rappel - 01 OOP Foundations]] · **Next:** [[JEE/Course/Rappel - 03 Exceptions and Date Time API]]
