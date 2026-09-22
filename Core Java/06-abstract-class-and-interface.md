# Abstract Class & Interface

---

## 1. Abstract Class

Abstract class `abstract` keyword se declare hoti hai.

```java
abstract class Vehicle {
    abstract void start();
}
```

### Cannot Instantiate Directly

```java
Vehicle v = new Vehicle();   // Vehicle is abstract; cannot be instantiated
```

---

## 2. Abstract Class — Important Interview Trap

### Note: Abstract class me abstract method hona zaroori NAHI hai!

```java
abstract class Vehicle {
    void start() {
        System.out.println("Start");   // concrete method
    }
}
```

Ye **completely valid** hai — zero abstract methods ke saath.

### Correct Rule

> If a class **contains an abstract method**, the class **must** be declared abstract.
>
> But an abstract class **may contain zero abstract methods**.

---

## 3. Abstract Method

Abstract method ka **body nahi hota** — sirf declaration:

```java
abstract void show();
```

### Concrete Subclass Must Implement

```java
abstract class A {
    abstract void show();
}

class B extends A {
    @Override
    void show() {
        System.out.println("Hello");
    }
}
```

Agar implement nahi kiya, to `B` khud **abstract** hona chahiye:

```java
abstract class B extends A {
    // show() not implemented — B stays abstract
}
```

---

## 4. Interface

Interface `interface` keyword se declare hota hai.

```java
interface Printable {
    void print();
}
```

### Cannot Instantiate Directly

```java
Printable p = new Printable();   // Error:
```

### Class Implements Interface

```java
class Document implements Printable {
    public void print() {
        System.out.println("Printing...");
    }
}
```

---

## 5. Interface Methods — Java 8 Rules

### Normal Abstract Methods

Interface ke normal methods implicitly **`public abstract`** hote hain:

```java
interface A {
    void show();
    // same as: public abstract void show();
}
```

### Java 8 Additions

Java 8 se interfaces me ye bhi allowed hain:

- **`default` methods** — body ke saath
- **`static` methods** — body ke saath

```java
interface A {
    void show();                              // public abstract

    default void greet() {                    // default method
        System.out.println("Hello!");
    }

    static void info() {                      // static method
        System.out.println("Interface A");
    }
}
```

> Clean rule: Normal interface methods = `public abstract`. `default` aur `static` methods alag cases hain.

---

## 6. Implementing Interface Methods — Access Rule

Interface method **`public`** hoti hai, to implementation me **access reduce nahi kar sakte**.

```java
interface A {
    void show();    // implicitly public abstract
}

// Correct
class B implements A {
    public void show() { }
}

// Incorrect — weaker access
class B implements A {
    void show() { }   // attempting to assign weaker access privileges
}
```

```text
Interface method → public
Implementation  → must be public (cannot be less accessible)
```

---

## 7. Abstract Class vs Interface

| Feature | Abstract Class | Interface |
|---------|---------------|-----------|
| Keyword | `abstract class` | `interface` |
| Methods | Abstract + concrete | Abstract + default + static (Java 8+) |
| Variables | Instance + static + final + non-final | Implicitly `public static final` |
| Constructor |  Allowed | Not allowed |
| Multiple inheritance |  Single class only |  Multiple interfaces |
| Access modifiers | All allowed | Methods implicitly `public` |
| Instantiation |  Cannot |  Cannot |
| `extends` vs `implements` | Class extends abstract class | Class implements interface |

### When to Use What?

```text
Abstract Class → jab related classes ke beech common code share karna ho
Interface → jab unrelated classes me common capability define karni ho
```

---

## Interview Quick Traps

| Trap | Answer |
|------|--------|
| Abstract class me abstract method hona zaroori hai? | No — zero abstract methods allowed |
| Abstract class ko instantiate kar sakte hain? | No |
| Interface me constructor hota hai? | No |
| Interface ke variables kaise hote hain? | `public static final` (implicitly) |
| Interface method implement karte waqt access reduce kar sakte hain? | No — must be `public` |
| Java me multiple interface implement kar sakte hain? | Yes |
| `default` method interface me allowed hai? | Yes — Java 8 onwards |

---

## ⚡ Quick Revision

```text
ABSTRACT CLASS
→ abstract keyword se declare
→ cannot instantiate directly
→ may contain zero or more abstract methods
→ class containing abstract method MUST be abstract
→ concrete subclass must implement all abstract methods

INTERFACE
→ interface keyword se declare
→ cannot instantiate directly
→ class implements interface
→ normal abstract methods are public abstract (implicit)
→ Java 8: default + static methods with body allowed
→ implementation cannot reduce access
→ variables are public static final (implicit)
→ multiple interfaces can be implemented
```

---

[Previous: Inheritance & Final](./05-inheritance-and-final.md) · [Back to Core Java Index](./README.md) · [Next: Polymorphism](./07-polymorphism.md)
