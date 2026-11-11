---
tags:
  - jee
  - java
  - collections
  - map
  - revision
---

# 04 — Collections & Maps

> [!abstract] Goal
> Understand the Java Collections Framework: choose the right structure based on **duplicates**, **ordering**, **position/indexing**, and **key-value** access, and iterate safely.

![[JEE/Excalidraw/Rappel-04-Collections.excalidraw|1000]]

---

## 1. Framework overview (two roots)

| Root | Stores | Key idea |
|---|---|---|
| `Collection<E>` | Single elements | Groups of objects (`List`, `Set`, etc.) |
| `Map<K,V>` | Key → value pairs | **Not** a subtype of `Collection` |

- `Collection` defines common operations; concrete behavior comes from subinterfaces/implementations.
- `Map` is separate because it models associations, not a simple element group.

---

## 2. Main collection types

| Type | Duplicates | Ordering | Typical implementation | Notable operations/properties |
|---|---|---|---|---|
| `Set` | No | Unspecified (general contract) | `HashSet` (common) | uniqueness by equality/hash semantics |
| `SortedSet` | No | Sorted | `TreeSet` | `first()`, `last()` |
| `List` | Yes | By index/position | `ArrayList` | `get(i)`, `set(i,e)`, `add(i,e)`, `remove(i)` |
| `Queue/Deque` *(general)* | Usually yes | FIFO/LIFO depending on type | varies | head/tail operations (concept) |

### Common Collection operations
`add`, `remove`, `contains`, `size`, `isEmpty`, `iterator`, `toArray`

> [!note]
> `contains(...)` (and list searches) relies on **`equals(...)`** semantics of elements—override `equals`/`hashCode` consistently when needed.

```java
import java.util.*;

TreeSet<Integer> sorted = new TreeSet<>(List.of(5, 1, 9, 3));
System.out.println(sorted.first() + " " + sorted.last()); // 1 9

List<String> names = new ArrayList<>(List.of("a", "b", "a"));
System.out.println(names.get(1)); // b
```

---

## 3. Maps (keys and values)

| Type | Key duplicates | Ordering | Typical implementation | Notable operations/properties |
|---|---|---|---|---|
| `Map` | Keys unique | Unspecified (general) | `HashMap` | `put`, `get`, `containsKey`, `remove`, `size` |
| `SortedMap` | Keys unique | Keys sorted | `TreeMap` | `firstKey()`, `lastKey()` |

- `put(k, v)` with an existing key **replaces/overwrites** the value.
- Values may be duplicated; keys must be unique.
- `HashMap`: generally unordered, average O(1) lookups, allows one `null` key and `null` values.
- `TreeMap`: sorted keys (natural/comparator), does **not** allow `null` keys (comparison required).

```java
Map<String, Integer> ages = new HashMap<>();
ages.put("ali", 21);
ages.put("sara", 20);
ages.put("ali", 22);                 // overwrites value for key "ali"

System.out.println(ages.get("sara"));      // 20
System.out.println(ages.get("bob"));       // null (absent)
System.out.println(ages.containsKey("ali")); // true
```

### Map views (because Map has no iterator)

A `Map` does not extend `Collection`, so iterate via views:
- `keySet()` — set of keys
- `values()` — collection of values
- `entrySet()` — set of key-value entries (`Map.Entry<K,V>`)

```java
for (Map.Entry<String, Integer> e : ages.entrySet()) {
    System.out.println(e.getKey() + " -> " + e.getValue());
}

SortedMap<String, Integer> sortedAges = new TreeMap<>(ages);
System.out.println(sortedAges.firstKey() + " " + sortedAges.lastKey());
```

---

## 4. Iteration and safe modification

An `Iterator<E>` traverses elements sequentially:
- obtain with `collection.iterator()`
- `hasNext()` checks availability
- `next()` returns current element and advances
- `remove()` removes the last returned element **safely during iteration**

```java
Iterator<Integer> it = sorted.iterator();
while (it.hasNext()) {
    Integer x = it.next();
    if (x % 2 == 1) {
        it.remove(); // safe removal during traversal
    }
}
```

### Common pitfalls

| Pitfall | What happens | Correct approach |
|---|---|---|
| Removing in for-each loop | `ConcurrentModificationException` | Use `Iterator.remove()` (or collection-specific safe APIs) |
| `next()` on exhausted iterator | `NoSuchElementException` | Always check `hasNext()` |
| `List<Integer>.remove(int)` vs `remove(Object)` | Overload confusion (index vs value) | Use `remove(Integer.valueOf(x))` for value removal |
| `map.get(k)` returns `null` | Ambiguous (absent key vs stored null value) | Use `containsKey(k)` or `getOrDefault(k, def)` |
| Bad `equals/hashCode` on keys/elements | Lookup/membership fails unexpectedly | Implement consistently |

> [!success] Key takeaway
> Pick structures by guarantees: **Set** for uniqueness, **List** for positional access/duplicates, **Sorted(Set/Map)** for ordering, **Map** for key-value lookup—and iterate with `Iterator` when removing elements.

---
**Series:** Rappel (Java) · **Previous:** [[JEE/Course/Rappel - 03 Exceptions and Date Time API]] · **Next:** [[JEE/Course/Rappel - 05 Ordering and Searching]]
