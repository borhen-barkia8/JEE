---
tags:
  - jee
  - java
  - rappel
  - streams
  - revision
---

# 06 — Streams API

> [!abstract] Goal
> Use the Java **Stream API** to process collections and sequences of data declaratively with pipelines of intermediate and terminal operations.

![[JEE/Excalidraw/Rappel-06-Streams.excalidraw|1000]]

---

## 1. What is a Stream?

A **Stream** is a sequence of elements supporting aggregate operations. It is **not** a data structure and does not store elements; it conveys elements from a source through a pipeline.

| Characteristic | Meaning |
|---|---|
| **No storage** | Elements come from a source (collection, array, generator, I/O channel, etc.) |
| **Functional style** | Operations are described with lambdas/method references |
| **Lazy intermediate ops** | Intermediate steps are not executed until a terminal operation runs |
| **Single-use** | Once a terminal operation is invoked, the stream is consumed |
| **Possibly unbounded** | Streams can represent finite or infinite sequences (use `limit`/short-circuit ops when needed) |

### Creating streams

```java
import java.util.*;
import java.util.stream.*;

List<String> names = Arrays.asList("Ali", "Sana", "Bob", "Amir");

Stream<String> s1 = names.stream();                 // from collection
Stream<Integer> s2 = Stream.of(1, 2, 3, 4);         // from values
Stream<String> s3 = Arrays.stream(new String[]{"a", "b"});
IntStream s4 = IntStream.range(1, 5);               // 1,2,3,4
```

---

## 2. Stream pipeline

A pipeline consists of:
1. **Source** — where elements come from
2. **Intermediate operations** — transform/filter and return a new stream (lazy)
3. **Terminal operation** — produces a result or side effect and consumes the stream

```java
List<String> result = names.stream()          // source
        .filter(n -> n.length() >= 4)         // intermediate
        .map(String::toUpperCase)             // intermediate
        .sorted()                             // intermediate
        .collect(Collectors.toList());        // terminal

// result: [AMIR, SANA] (order depends on sorted)
```

> [!note]
> Because intermediate operations are lazy, computation is fused and elements flow through the pipeline as needed. Short-circuit operations like `findFirst()` or `limit()` can stop early.

---

## 3. Common intermediate operations

| Operation | Purpose | Example |
|---|---|---|
| `filter(Predicate)` | Keep elements matching condition | `stream.filter(x -> x > 10)` |
| `map(Function)` | Transform each element | `stream.map(String::length)` |
| `flatMap(Function)` | Flatten nested structures/streams | `lists.stream().flatMap(List::stream)` |
| `sorted()` / `sorted(Comparator)` | Order elements | `stream.sorted(Comparator.reverseOrder())` |
| `distinct()` | Remove duplicates (uses `equals`/`hashCode`) | `stream.distinct()` |
| `limit(n)` | Truncate to first `n` elements | `stream.limit(5)` |
| `skip(n)` | Discard first `n` elements | `stream.skip(2)` |
| `peek(Consumer)` | Inspect elements (debugging) without consuming as terminal | `stream.peek(System.out::println)` |

### map vs flatMap

```java
List<List<Integer>> nested = Arrays.asList(
    Arrays.asList(1, 2),
    Arrays.asList(3, 4)
);

// map: Stream<List<Integer>>
// flatMap: Stream<Integer> -> [1, 2, 3, 4]
List<Integer> flat = nested.stream()
        .flatMap(List::stream)
        .collect(Collectors.toList());
```

---

## 4. Common terminal operations

| Operation | Purpose | Returns / Effect |
|---|---|---|
| `collect(Collector)` | Accumulate into collection/map/string/etc. | Result container/value |
| `forEach(Consumer)` | Perform action for each element | Side effect (no result value) |
| `reduce(...)` | Combine elements into one value | `Optional<T>` or value |
| `count()` | Count elements | `long` |
| `min(Comparator)` / `max(Comparator)` | Find extremum | `Optional<T>` |
| `findFirst()` / `findAny()` | Retrieve an element | `Optional<T>` |
| `anyMatch` / `allMatch` / `noneMatch` | Test predicate | `boolean` |
| `toArray()` | Convert to array | array |

### Reduction and collectors

```java
List<Integer> nums = Arrays.asList(1, 2, 3, 4, 5);

int sum = nums.stream().reduce(0, Integer::sum);            // 15
Optional<Integer> max = nums.stream().max(Integer::compareTo);

String joined = names.stream()
        .filter(n -> n.startsWith("A"))
        .collect(Collectors.joining(", "));                 // e.g. "Ali, Amir"

Map<Integer, List<String>> byLen = names.stream()
        .collect(Collectors.groupingBy(String::length));
```

---

## 5. Primitive streams and best practices

Primitive specializations avoid boxing overhead and provide numeric helpers.

| Type | Created by | Useful methods |
|---|---|---|
| `IntStream` | `mapToInt`, `IntStream.range`, `Arrays.stream(int[])` | `sum()`, `average()`, `min()`, `max()` |
| `LongStream` | `mapToLong`, ranges | numeric aggregates |
| `DoubleStream` | `mapToDouble` | numeric aggregates |

```java
int totalLength = names.stream()
        .mapToInt(String::length)
        .sum();
```

> [!warning] Common pitfalls
> - **Reusing a stream:** after a terminal operation, create a new stream from the source.
> - **Side effects in lambdas:** avoid mutating external state in `map`/`filter`; prefer collectors/reduction.
> - **`parallelStream()` is not free:** useful for large, CPU-bound, independent operations but can add overhead and ordering/concurrency issues.

> [!success] Key takeaway
> Think in **pipelines**: build transformations with lazy intermediate operations and produce a result with one terminal operation. Streams make data processing concise, but keep operations stateless and clear.

---
**Series:** Rappel (Java) · **Previous:** [[JEE/Course/Rappel - 05 Ordering and Searching]] · **Next:** [[JEE/Course/Rappel - 07 Optional]]
