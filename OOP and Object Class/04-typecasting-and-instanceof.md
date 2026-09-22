# 🔄 Type Casting & instanceof

---

## 1. Upcasting — Child Object in Parent Reference

Parent reference → Child object:

```java
class A { }
class B extends A { }

A a1 = new B();    // ✅ Upcasting — implicit
```

```text
A (parent)
↑
B (child)
```

> Upcasting **automatically / implicitly** hoti hai — explicit cast ki zaroorat nahi.

---

## 2. Upcasting ki Limitation

```java
Object o = new Employee();

System.out.println(o.id);     // ❌ compile-time error: cannot find symbol
```

### Why?

Compiler **reference variable ka type** dekhta hai:

```text
Compile time → Reference type (Object) → accessible members
Runtime      → Actual object (Employee) → overridden methods
```

`Object` me `id` nahi hai — isliye compile error.

---

## 3. Downcasting — Parent Reference to Child Reference

```java
Object o = new Employee();

Employee e = (Employee) o;     // ✅ explicit downcast

System.out.println(e.id);      // ✅ now accessible
```

> Casting **reference ka declared type** change karti hai — object ko physically convert nahi karti.

---

## 4. Class Hierarchy Example

```java
class A { }
class B extends A { }
class C extends A { }
```

```text
      A
     / \
    B   C
```

### All Type Casting Cases

| # | Code | Compile | Runtime | Reason |
|---|------|---------|---------|--------|
| 1 | `B b = new A();` | ❌ | — | Parent object → child reference (implicit) not allowed |
| 2 | `C c = new B();` | ❌ | — | Sibling classes — no relationship |
| 3 | `A a = new B();` | ✅ | ✅ | Upcasting — valid |
| 4 | `A a = new A(); B b = (B) a;` | ✅ | ❌ `ClassCastException` | Actual object is A, not B |
| 5 | `A a = new B(); B b = (B) a;` | ✅ | ✅ | Actual object genuinely B |
| 6 | `A a = new C(); B b = (B) a;` | ✅ | ❌ `ClassCastException` | Actual object is C, not B |

---

## 5. ClassCastException

`ClassCastException` ek **`RuntimeException`** hai → **unchecked exception**.

```java
A a1 = new A();
B b1 = (B) a1;    // ✅ compiles → ❌ ClassCastException at runtime
```

> Runtime object requested target type ke compatible nahi hota → `ClassCastException`.

---

## 6. Golden Rule of Type Casting

```text
Casting parent-child relationship ke context mein possible hoti hai.

But successful downcasting ke liye:
→ Actual object MUST be an instance of the target child type.
```

```java
A a = new B();
B b = (B) a;       // ✅ actual object is B

A a = new C();
B b = (B) a;       // ❌ ClassCastException — actual object is C, not B
```

---

## 7. Method Overriding with Upcasting

```java
class A {
    void show() { System.out.println("A"); }
}

class B extends A {
    @Override
    void show() { System.out.println("B"); }
}
```

```java
A a1 = new B();
a1.show();         // Output: "B" — actual object decides
```

This is **Runtime Polymorphism / Dynamic Method Dispatch**.

### What If Parent Has No Method?

```java
class A { }
class B extends A {
    void show() { System.out.println("B"); }
}

A a1 = new B();
a1.show();         // ❌ compile-time error — A has no show()
```

> Compiler reference type check karta hai — A me `show()` nahi hai.

### What If Child Doesn't Override?

```java
class A {
    void show() { System.out.println("A"); }
}
class B extends A { }

A a1 = new B();
a1.show();         // Output: "A" — inherited method
```

---

## 8. `instanceof` Operator

Runtime par check karta hai — kya object kisi type ka instance hai?

```java
object instanceof ClassName    // → true / false
```

### Basic Example

```java
A a1 = new B();

System.out.println(a1 instanceof A);   // true  — B is-a A
System.out.println(a1 instanceof B);   // true  — actual object is B
```

### Hierarchy Check (A → B, A → C)

```java
A a1 = new A();
B b1 = new B();
C c1 = new C();
```

| Check | Result | Reason |
|-------|--------|--------|
| `a1 instanceof A` | ✅ `true` | A object is A |
| `a1 instanceof B` | `false` | A object is not B |
| `a1 instanceof C` | `false` | A object is not C |
| `b1 instanceof A` | ✅ `true` | B is-a A |
| `b1 instanceof B` | ✅ `true` | B is B |
| `b1 instanceof C` | ❌ compile error | Sibling classes |
| `c1 instanceof A` | ✅ `true` | C is-a A |
| `c1 instanceof B` | ❌ compile error | Sibling classes |
| `c1 instanceof C` | ✅ `true` | C is C |

---

## 9. null instanceof — Very Important!

```java
Object o = null;

System.out.println(o instanceof Object);   // false
System.out.println(null instanceof String); // false
```

### Rule

```text
null instanceof AnyReferenceType → always false
```

> `null` kisi object ko refer nahi karta — kisi type ka instance nahi hai.

### Use in equals()

```java
if (!(o instanceof Employee))
    return false;
```

Ye automatically `null` ko bhi handle kar leta hai — kyunki `null instanceof Employee` → `false`.

---

## 10. instanceof ka Internal Logic

```text
Actual object ka runtime type
          ↓
Kya requested type ka instance hai?
          ↓
       true / false
```

```java
A a1 = new B();

a1 instanceof B    // reference type A, but actual object B → true
a1 instanceof A    // B is-a A → true
```

---

## 🧠 Interview Quick Traps

| Trap | Answer |
|------|--------|
| Upcasting implicit hoti hai? | ✅ Yes |
| Downcasting implicit hoti hai? | ❌ No — explicit cast required |
| `ClassCastException` checked ya unchecked? | Unchecked (`RuntimeException`) |
| Sibling classes me casting possible hai? | ❌ No — compile error |
| `null instanceof Object` ka result? | `false` |
| `A a = new A(); B b = (B) a;` compile hota hai? | ✅ Yes — but runtime ClassCastException |
| Compile time par kaun decide karta hai? | Reference type |
| Runtime par kaun decide karta hai? | Actual object |

---

## ⚡ Quick Rules

```text
UPCASTING (Child → Parent reference)
→ Implicit / automatic
→ Accessible: only parent members
→ Overridden method: child's implementation runs

DOWNCASTING (Parent reference → Child reference)
→ Explicit cast required
→ Actual object must genuinely be of target type
→ Otherwise: ClassCastException

instanceof
→ Runtime type check
→ null instanceof X → always false
→ Sibling instanceof → compile error
```

---

[⬅️ Previous: equals() & ==](./03-equals-and-identity.md) · [📖 Back to OOP Index](./README.md) · [Next → Strings & String Pool ➡️](./05-strings-and-pool.md)
