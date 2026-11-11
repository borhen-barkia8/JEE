---
tags:
  - jee
  - java
  - exceptions
  - datetime
  - revision
---

# 03 — Exceptions & Date/Time API

> [!abstract] Goal
> Handle abnormal conditions correctly with Java exceptions, and work with the modern immutable `java.time` API for dates, times, periods, durations, parsing, and formatting.

![[JEE/Excalidraw/Rappel-03-Exceptions-Time.excalidraw|1000]]

---

## 1. Exception basics

An exception is an object representing an abnormal condition during execution. Exception classes derive from `Throwable`.

An exception can be:
- **Propagated** to the caller (declared with `throws`)
- **Handled** locally with `try` / `catch` / `finally`

### Core keywords

| Keyword | Role | Example idea |
|---|---|---|
| `throw` | Raise/throw an exception instance | `throw new MyException("msg");` |
| `throws` | Declare that a method may propagate an exception | `void m() throws MyException` |
| `try` | Wrap risky code | attempt operation |
| `catch` | Handle a specific exception type | recover/log/fallback |
| `finally` | Always executes (success or failure) | cleanup/release resources |

```java
class InsufficientFundsException extends Exception { // checked
    public InsufficientFundsException(String msg) { super(msg); }
}

public class Bank {
    private double balance = 100.0;

    public void withdraw(double amount) throws InsufficientFundsException {
        if (amount > balance)
            throw new InsufficientFundsException("Balance too low: " + balance);
        balance -= amount;
    }
}
```

```java
Bank b = new Bank();
try {
    b.withdraw(500);
} catch (InsufficientFundsException e) {
    System.out.println("Handled: " + e.getMessage());
    e.printStackTrace(); // diagnostics: call stack
} finally {
    System.out.println("Cleanup always runs");
}
```

---

## 2. Checked vs unchecked exceptions

| Type | Typical base | Declared in signature? | When used (common view) |
|---|---|---|---|
| **Checked** | `Exception` (not `RuntimeException`) | Yes, with `throws` (or must be caught) | Recoverable/expected conditions caller should handle |
| **Unchecked** | `RuntimeException` | No (not required) | Programming errors or cases not forced by compiler |

```java
public static int divide(int a, int b) {
    if (b == 0) throw new ArithmeticException("Cannot divide by zero"); // unchecked
    return a / b;
}
```

> [!tip]
> Catch specific exception types when possible, and use `finally` (or try-with-resources where applicable) for reliable cleanup.

### Propagation vs handling

- **Propagate** when the current method cannot meaningfully recover: declare with `throws`.
- **Handle** when you can recover, retry, translate, or report appropriately: use `try/catch`.
- Declaring a broader type (e.g., `throws Exception`) allows subclasses to be thrown, but is less precise.

---

## 3. java.time API essentials

The `java.time` package provides **immutable** date/time types created mainly via **static factory methods** (no `new`).

### Core types

| Type | Represents | Default format (common) | Example creation |
|---|---|---|---|
| `LocalDate` | Date (no time, no zone) | `yyyy-MM-dd` | `LocalDate.of(2026, 11, 11)` |
| `LocalTime` | Time (no date, no zone) | `HH:mm:ss` | `LocalTime.of(14, 30, 0)` |
| `LocalDateTime` | Date + time | ISO-8601-like | `LocalDateTime.of(date, time)` |
| `Period` | Date-based amount | years/months/days | `Period.between(d1, d2)` |
| `Duration` | Time-based amount | hours/minutes/seconds/nanos | `Duration.ofHours(5)` |
| `ChronoUnit` | Unit for measuring differences | e.g. days | `ChronoUnit.DAYS.between(d1, d2)` |

```java
import java.time.*;
import java.time.format.DateTimeFormatter;
import java.time.temporal.ChronoUnit;

LocalDate date = LocalDate.of(2026, 10, 8);
LocalTime time = LocalTime.of(14, 30, 0);
LocalDateTime dateTime = LocalDateTime.of(date, time);

LocalDate nextWeek = date.plusWeeks(1);              // immutable → returns new object
Period period = Period.between(date, nextWeek);      // 7 days (calendar units)
Duration duration = Duration.ofHours(5);             // exact time amount
long days = ChronoUnit.DAYS.between(date, nextWeek);
```

---

## 4. Parsing and formatting

- **`parse(...)`**: convert `String` → temporal object
- **`format(...)`**: convert temporal object → `String`
- **`DateTimeFormatter`**: immutable, reusable pattern (e.g., `ofPattern("dd/MM/yyyy")`)

```java
DateTimeFormatter fmt = DateTimeFormatter.ofPattern("dd/MM/yyyy");
String text = date.format(fmt);               // 08/10/2026
LocalDate parsed = LocalDate.parse("08/10/2026", fmt);
```

> [!warning]
> `java.time` objects are **immutable**: methods like `plusWeeks(...)` do not modify the original instance—always use the returned value.

> [!success] Key takeaway
> Use `throw/throws/try-catch-finally` to control exception flow (know checked vs unchecked), and prefer immutable `java.time` types with `DateTimeFormatter` for reliable date/time handling.

---
**Series:** Rappel (Java) · **Previous:** [[JEE/Course/Rappel - 02 Type Hierarchy Visibility and Casting]] · **Next:** [[JEE/Course/Rappel - 04 Collections and Maps]]
