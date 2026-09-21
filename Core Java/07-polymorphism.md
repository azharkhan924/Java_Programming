# 🎭 Polymorphism — Overloading, Overriding & Hiding

---

## 1. What is Polymorphism?

> **Polymorphism** = One name, multiple forms.

```text
Polymorphism
     │
     ├── Compile-Time (Static Binding / Early Binding)
     │   ├── Method Overloading
     │   └── Static Method Hiding
     │
     └── Run-Time (Dynamic Binding / Late Binding)
         └── Method Overriding
```

---

## 2. Method Overloading (Compile-Time Polymorphism)

**Same class + same method name + different parameter list** = Overloading.

```java
class Calculator {
    void sum(int a, int b) {
        System.out.println(a + b);
    }
    void sum(int a, int b, int c) {
        System.out.println(a + b + c);
    }
}
```

### Parameter List Me Kya Change Ho Sakta Hai?

| Change | Example |
|--------|---------|
| **Number** of parameters | `show(int x)` vs `show(int x, int y)` |
| **Type** of parameters | `show(int x)` vs `show(double x)` |
| **Order** of parameter types | `show(int, double)` vs `show(double, int)` |

### ⚠️ Sirf Return Type Change ≠ Overloading

```java
int show(int x) { return x; }
double show(int x) { return x; }    // ❌ same parameter list — not valid overloading
```

---

## 3. Overloading Resolution Priority

Compiler more specific/suitable method ko prefer karta hai:

```text
1. Exact match
      ↓
2. Widening primitive conversion
      ↓
3. Boxing / unboxing
      ↓
4. Varargs (lowest priority)
```

```java
void show(int x) { }
void show(double x) { }

show(10);   // → show(int) — exact match
```

---

## 4. Overloading Ambiguity Trap

```java
void show(int x, double y) { }
void show(double x, int y) { }

show(10, 20);   // ❌ ambiguous — both equally applicable!
```

```text
10 → int (exact),   20 → double (widening)    → show(int, double)
10 → double (widening),  20 → int (exact)     → show(double, int)
```

Dono equally applicable → **compile-time ambiguity error**.

---

## 5. Method Overriding (Run-Time Polymorphism)

Inheritance se related — **parent-child relationship** zaroori.

### Conditions

- Same method name
- Same parameter list
- Instance methods honi chahiye
- Return type same ya **covariant** (compatible subtype)
- Access level ko reduce **nahi** kar sakte

```java
class A {
    void show() {
        System.out.println("Class A");
    }
}

class B extends A {
    @Override
    void show() {
        System.out.println("Class B");
    }
}
```

### Runtime Dispatch

```java
A obj = new B();
obj.show();      // Output: "Class B"
```

```text
Reference type = A    → compiler checks method availability
Actual object  = B    → runtime decides which implementation
```

> Overridden instance method ke liye **actual object** decide karta hai kaunsi implementation execute hogi.

---

## 6. Method Hiding (Static Methods)

Static methods **override nahi hoti** — agar parent-child me same signature ki static method ho, to ise **method hiding** kehte hain.

```java
class A {
    static void show() {
        System.out.println("Class A");
    }
}

class B extends A {
    static void show() {
        System.out.println("Class B");
    }
}
```

```java
A obj = new B();
obj.show();      // Output: "Class A" — reference type decides!
```

> Static method dispatch **reference type** ke basis par hota hai, actual object se nahi.

---

## 7. Overriding vs Hiding — Key Difference

| Feature | Method Overriding | Method Hiding |
|---------|------------------|---------------|
| Methods | Instance | Static |
| Inheritance | Required | Required |
| Name | Same | Same |
| Parameter list | Same | Same |
| Binding | **Runtime** | **Compile-time** |
| Depends on | **Actual object** | **Reference type** |
| `@Override` | ✅ Valid | ❌ Static methods don't override |

### Easy Memory Trick

```text
Instance → Object → Runtime → Overriding
Static   → Reference → Compile-time → Hiding
```

---

## 8. Binding

**Binding** = method call ko actual method implementation ke saath connect karna.

| Type | When | Examples |
|------|------|----------|
| Compile-Time Binding | Compile time par decide | Overloading, Static Hiding |
| Runtime Binding | Runtime par actual object ke basis par | Overriding |

---

## 9. Java Instance Methods — Virtual Dispatch

Java me normal instance methods **dynamically dispatched** hoti hain.

```java
A obj = new B();
obj.show();   // B ka show() execute hoga (if overridden)
```

> C++ me `virtual` keyword chahiye. Java me instance methods ke liye **separate `virtual` keyword nahi** — JVM automatically dynamic dispatch karta hai.

---

## 10. Overloading vs Overriding — Master Comparison

| Point | Overloading | Overriding |
|-------|------------|------------|
| Classes | Usually same class | Parent + child |
| Inheritance | Not required | Required |
| Method name | Same | Same |
| Parameters | **Must differ** | **Same** |
| Return type alone? | ❌ Not enough | ❌ Not enough |
| Binding | Compile-time | Runtime |
| Polymorphism | Compile-time | Runtime |
| Main purpose | Same name, different inputs | Child-specific implementation |

---

## 🧠 Interview Quick Traps

| Trap | Answer |
|------|--------|
| Sirf return type change karke overload ho sakta hai? | ❌ No |
| Static method override hoti hai? | ❌ No — it's **hiding** |
| `A obj = new B(); obj.staticMethod();` — kaunsa execute hoga? | `A` ka — reference type decides |
| `A obj = new B(); obj.instanceMethod();` — kaunsa execute hoga? | `B` ka — actual object decides |
| Overloading me ambiguity kab aati hai? | Jab multiple methods equally applicable hon |
| Varargs ki priority overloading me? | **Lowest** |

---

## ⚡ Super Short Memory Trick

```text
OVERLOADING
→ Same class
→ Same name
→ Different parameters
→ Compile-time

OVERRIDING
→ Parent + Child
→ Same signature
→ Instance method
→ Runtime
→ Object based

HIDING
→ Parent + Child
→ Same static method
→ Compile-time
→ Reference based
```

---

[⬅️ Previous: Abstract Class & Interface](./06-abstract-class-and-interface.md) · [📖 Back to Core Java Index](./README.md) · [Next → Access Modifiers ➡️](./08-access-modifiers.md)
