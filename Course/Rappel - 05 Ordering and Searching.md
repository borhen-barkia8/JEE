---
tags:
  - jee
  - java
  - sorting
  - comparator
  - revision
---


# 05 — Ordering & Searching

> [!abstract] Goal
> Sort and search Java collections correctly by understanding **natural vs custom ordering** (`Comparable`/`Comparator`) and the critical difference between **`equals`** and **comparison-based equality** in sorted collections.

![[JEE/Excalidraw/Rappel-05-Ordering.excalidraw|1000]]

---

## 1. Searching in lists

`List` provides positional search methods:
- `indexOf(Object o)` → index of first occurrence, or `-1`
- `lastIndexOf(Object o)` → index of last occurrence, or `-1`

These methods rely on **`equals(...)`**.

> [!note]
> `indexOf`/`lastIndexOf` are **`List`** methods (not declared on `Collection`). If you only have a `Collection` reference, you need a `List`-typed view/cast when appropriate.

```java
import java.util.*;

List<String> items = new ArrayList<>(List.of("a", "c", "b", "c"));
System.out.println(items.indexOf("c"));     // 1
System.out.println(items.lastIndexOf("c")); // 3
System.out.println(items.indexOf("x"));     // -1

Collection<String> col = items;
// col.indexOf("c"); // compile error
int pos = ((List<String>) col).indexOf("c"); // ok if actually a List
```

---

## 2. Natural ordering: Comparable<T>

A class implements `Comparable<T>` to define its **natural order**.

```java
public interface Comparable<T> {
    int compareTo(T o);
}
```

**Contract of return value:**
- negative → `this` comes before `o`
- zero → considered equal in ordering
- positive → `this` comes after `o`

```java
class Person implements Comparable<Person> {
    private final String name;
    Person(String name) { this.name = name; }
    public String name() { return name; }

    @Override
    public int compareTo(Person o) {
        return this.name.compareTo(o.name); // natural alphabetical order
    }
}
```

Used by APIs like `Collections.sort(list)` (for comparable elements) and sorted structures relying on natural ordering.

---

## 3. Custom/external ordering: Comparator<T>

`Comparator<T>` defines ordering **outside** the element class (useful for multiple sort criteria or classes you can’t modify).

```java
public interface Comparator<T> {
    int compare(T a, T b);
}
```

### Ways to provide a comparator

1. **Separate/anonymous class** implementing `compare`
2. **Lambda stored in a variable**
3. **Inline lambda** passed to sort methods or sorted collections

```java
List<String> fruits = new ArrayList<>(List.of("kiwi", "fig", "apple", "plum"));

// Inline comparator: by length, then alphabetical
fruits.sort((a, b) ->
    a.length() == b.length() ? a.compareTo(b) : Integer.compare(a.length(), b.length()));
// [fig, kiwi, plum, apple]
```

```java
// Chained comparator (multi-level ordering)
people.sort(Comparator
    .comparing(Person::name)
    .thenComparing(Person::age));
```

> [!tip]
> Prefer `Integer.compare(a, b)` over `a - b` for numeric comparisons to avoid integer overflow edge cases.

---

## 4. Sorting APIs and sorted collections

| API / structure | Ordering source | Notes |
|---|---|---|
| `Collections.sort(list)` | Natural order (`Comparable`) | Sorts list in place |
| `list.sort(comparator)` | Provided `Comparator` | Sorts list in place |
| `Collections.sort(list, comparator)` | Provided `Comparator` | Legacy style, still common |
| `new TreeSet<>()` | Natural order | Elements must be comparable (or provide comparator) |
| `new TreeSet<>(comparator)` | Provided `Comparator` | Keeps elements sorted by comparator |

```java
TreeSet<String> byLength = new TreeSet<>(
    (a, b) -> a.length() == b.length() ? a.compareTo(b) : Integer.compare(a.length(), b.length())
);
byLength.addAll(List.of("kiwi", "fig", "apple", "plum"));
System.out.println(byLength); // [fig, kiwi, plum, apple]
```

---

## 5. Equality semantics: critical pitfall

Different structures define “duplicate/equal” differently:

| Structure | Uses for equality/membership logic | Consequence |
|---|---|---|
| `List` (`contains`, `indexOf`) | `equals(...)` | Object equality determines matches |
| `HashSet` / `HashMap` keys | `equals(...)` + `hashCode()` | Both must be consistent |
| `TreeSet` / `TreeMap` | `compareTo` / `compare` result `== 0` | Elements/keys with compare result 0 are treated as duplicates |

> [!warning] Data-loss risk in sorted sets/maps
> If a comparator is **inconsistent with `equals`** (or lacks a tie-breaker), distinct objects may compare as `0` and one will be silently rejected/overwritten.

```java
// Risky: "kiwi" and "plum" have same length -> compare == 0 -> one may be dropped
TreeSet<String> risky = new TreeSet<>(Comparator.comparingInt(String::length));
risky.addAll(List.of("fig", "kiwi", "plum"));
// risky contains only one of {"kiwi","plum"}
```

**Fix:** add a tie-breaker to make comparison deterministic and consistent for intended uniqueness.

> [!success] Key takeaway
> Use `Comparable` for natural order and `Comparator` for flexible/custom ordering. When using `TreeSet`/`TreeMap`, remember: **`compare == 0` means duplicate**—keep comparison logic consistent with your equality requirements.

---
**Series:** Rappel (Java) · **Previous:** [[JEE/Course/Rappel - 04 Collections and Maps]] · **Next:** [[JEE/Course/Rappel - 06 Streams API]]
