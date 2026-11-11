---
tags:
  - jee
  - java
  - oop
  - revision
---

# 01 — OOP Foundations

> [!abstract] Goal
> Master the core OOP mechanisms in Java: **overloading**, **overriding**, **polymorphism**, and **abstraction** (abstract classes/interfaces), since later topics (collections, comparators, casting) depend on them.

![[JEE/Excalidraw/Rappel-01-OOP.excalidraw|1000]]

---

## 1. Overloading vs Overriding

| Aspect | Overloading | Overriding |
|---|---|---|
| **Binding time** | Compile-time | Runtime (dynamic dispatch) |
| **Where** | Same class (or inherited name available) | Subclass redefines inherited method |
| **Method signature** | Same name, **different parameter list** (type/number/order) | **Same signature** (name + params) and compatible return |
| **Return type** | Not sufficient to distinguish overloads | Must be same/covariant as overridden method |
| **Purpose** | Convenience/variants of an operation | Specialize/replace inherited behavior |

### Key points

- **Overloading**: the compiler chooses the method based on the **static types** of arguments at compile time.
- **Overriding**: the JVM selects the implementation based on the **dynamic (actual) object type** at runtime.
- Mark overrides with `@Override` to catch signature mistakes early (e.g., when intending to override `toString()` or `equals()`).

```java
class Printer {
    void print(int x) { System.out.println("int: " + x); }      // overload
    void print(String s) { System.out.println("str: " + s); }    // overload
}
```

---

## 2. Polymorphism (dynamic dispatch)

**Polymorphism** means one call site can execute different behavior depending on the actual object referenced.

```java
abstract class Shape {
    public abstract double surface();

    @Override
    public String toString() {
        return getClass().getSimpleName() + " -> " + surface();
    }
}

class Square extends Shape {
    private final double side;
    Square(double side) { this.side = side; }

    @Override
    public double surface() { return side * side; }
}

Shape s = new Square(3);          // static type: Shape, dynamic type: Square
System.out.println(s);            // Square -> 9.0
```

- You can program against a **general type** (`Shape`) while getting behavior of the **specific type** (`Square`).
- This is essential for frameworks, collections of base types, and callback/strategy-style designs.

---

## 3. Abstraction: abstract classes and methods

An **abstract method** declares a contract without implementation:
```java
public abstract double surface();
```

An **abstract class**:
- May contain **abstract and concrete** members
- **Cannot be instantiated** directly
- Forces concrete subclasses to implement all inherited abstract methods
- Is useful when subclasses share state/behavior but must provide specific implementations

```java
abstract class Animal {
    abstract void speak();               // must be implemented
    void breathe() { /* shared */ }      // concrete behavior
}

class Dog extends Animal {
    @Override void speak() { System.out.println("woof"); }
}
```

> [!warning]
> If a subclass does not implement all abstract methods, it must itself be declared `abstract`.

---

## 4. Interfaces

An **interface** defines a capability/contract:
- Methods are implicitly `public abstract` (except `default`/`static` methods with bodies)
- Fields are implicitly `public static final` (constants)
- No constructors and no instance state
- A class can **implement multiple interfaces** (`implements A, B`)

```java
interface Payable {
    double amount();                 // abstract method
    default String currency() {       // optional default behavior
        return "EUR";
    }
}

class Invoice implements Payable {
    public double amount() { return 120.0; }
}
```

### Abstract class vs interface (revision view)

| Criteria | Abstract class | Interface |
|---|---|---|
| Instantiation | No | No |
| State/constructors | Can have instance fields/constructors | No instance state/constructors |
| Method kinds | Concrete + abstract | Abstract + `default`/`static` |
| Inheritance/implementation | Class extends **one** class | Class implements **many** interfaces |
| Use when | Share base implementation/state | Define capabilities/contracts across hierarchies |

> [!success] Key takeaway
> **Overloading** is chosen by the compiler; **overriding** enables runtime polymorphism. Use **abstract classes** for shared base behavior/state and **interfaces** for contracts/capabilities.

---
**Series:** Rappel (Java) · **Next:** [[JEE/Course/Rappel - 02 Type Hierarchy Visibility and Casting]]
