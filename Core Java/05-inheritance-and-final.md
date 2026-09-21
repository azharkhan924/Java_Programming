# 🧬 Inheritance & Final Keyword

---

## 1. Inheritance Types in Java

### ✅ Supported with Classes

```text
1. Single Inheritance       → A → B
2. Multilevel Inheritance   → A → B → C
3. Hierarchical Inheritance → A → B, A → C
```

### ❌ Not Supported with Classes

```text
Multiple Inheritance  → A, B → C  (❌)
Hybrid Inheritance    → Combination (❌)
```

```java
class C extends A, B { }   // ❌ invalid — multiple class inheritance
```

### Why Not?

Java avoids the **ambiguity** associated with inheriting same members from multiple classes (Diamond Problem).

### ✅ But Multiple Interfaces Allowed

```java
interface A { }
interface B { }

class C implements A, B { }   // ✅ valid
```

---

## 2. Why Use Inheritance?

Inheritance allows a subclass to **reuse and extend** the behavior/properties of an existing superclass.

```text
Existing class (Parent/Super)
        ↓
     inherit
        ↓
New class (Child/Sub) = reused + additional behavior
```

---

## 3. Method Overriding

Subclass **apni implementation** provide karta hai inherited superclass method ki.

```java
class A {
    void show() {
        System.out.println("A");
    }
}

class B extends A {
    @Override
    void show() {
        System.out.println("B");
    }
}
```

### What Cannot Be Overridden?

| Type | Can Override? |
|------|-------------|
| Normal instance method | ✅ Yes |
| `final` method | ❌ No — cannot be overridden |
| `private` method | ❌ No — not inherited, so not overriding |
| `static` method | ❌ No — it's **hiding**, not overriding |

### Access Modifier Rule for Overriding

Child method **weaker access** nahi de sakta:

```text
private   → not overridden (not inherited)
default   → default / protected / public
protected → protected / public
public    → public only
```

> Child can maintain or **increase** accessibility, but **cannot reduce** it.

---

## 4. `final` Keyword

`final` ke **3 main uses** hain:

### 📌 Final Variable — Cannot Reassign

```java
final int x = 10;
x = 20;   // ❌ cannot assign a value to final variable
```

### 📌 Final Method — Cannot Override

```java
class A {
    final void show() { }
}

class B extends A {
    void show() { }   // ❌ cannot override final method
}
```

### 📌 Final Class — Cannot Inherit

```java
final class A { }

class B extends A { }   // ❌ cannot extend final class
```

> Example: `String` class is `final` — koi subclass nahi bana sakta.

---

## 5. Blank Final Variable

Ek important distinction:

```java
final int x;    // blank final — NOT automatically 0
```

Blank final variable ko **exactly once** initialize karna mandatory hai.

### Instance Blank Final

```java
class A {
    final int x;

    A() {
        x = 10;    // ✅ assigned in constructor
    }
}
```

**Har constructor me assign hona chahiye:**

```java
class A {
    final int x;

    A() {
        x = 10;       // ✅
    }

    A(int val) {
        x = val;       // ✅ — har constructor path me assigned
    }
}
```

### Instance Initializer Block Me

```java
class A {
    final int x;

    {
        x = 100;    // ✅ instance initializer block me bhi assign possible
    }
}
```

### Static Blank Final

```java
class A {
    static final int X;

    static {
        X = 100;    // ✅ static initializer block me assign
    }
}
```

---

## 6. Cyclic Inheritance ❌

```java
class A extends A { }   // ❌ cyclic inheritance involving A
```

---

## 7. Private Methods and Overriding

Private methods **inherited nahi hote** — so overriding nahi hoti.

```java
class A {
    private void show() { }
}

class B extends A {
    private void show() { }   // separate method — NOT overriding
}
```

---

## 🧠 Interview Quick Traps

| Trap | Answer |
|------|--------|
| `final int x;` ko default value (0) milti hai? | ❌ No — blank final must be explicitly assigned |
| Abstract class me blank final variable? | ✅ Possible — concrete subclass constructor me assign karna hoga |
| `final` class ko inherit kar sakte hain? | ❌ No |
| `final` method ko override kar sakte hain? | ❌ No |
| `private` method override hota hai? | ❌ No — inherited hi nahi hota |
| `String` class ko extend kar sakte hain? | ❌ No — it's `final` |
| Hierarchical inheritance supported hai? | ✅ Yes |
| Multiple class inheritance supported hai? | ❌ No |

---

## ⚡ Quick Revision

```text
INHERITANCE
→ single, multilevel, hierarchical ✅
→ multiple class inheritance ❌
→ multiple interfaces ✅

FINAL
→ variable: cannot reassign
→ method: cannot override
→ class: cannot extend
→ blank final: must be explicitly assigned

OVERRIDING
→ child changes inherited method implementation
→ private methods: not overridden (not inherited)
→ final methods: cannot be overridden
→ access cannot be reduced (only same or wider)
```

---

[⬅️ Previous: Static Keyword](./04-static-keyword.md) · [📖 Back to Core Java Index](./README.md) · [Next → Abstract Class & Interface ➡️](./06-abstract-class-and-interface.md)
