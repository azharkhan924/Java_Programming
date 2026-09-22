# Garbage Collection

---

## 1. What is a Garbage Object?

Jab koi object **unreferenced / unreachable** ho jaata hai — koi active reference usse point nahi karta — to wo **garbage object** ban jaata hai.

> **Important:** GC eligible ≠ immediately destroyed. GC kab run hoga ye **JVM decide** karti hai.

---

## 2. Five Ways to Make an Object GC Eligible

### 1. Null Assignment

```java
Demo d1 = new Demo();
d1 = null;     // object becomes GC eligible
```

```text
Before:  d1 ─────► [Object]
After:   d1 ─────► null        [Object] → GC eligible
```

### 2. Reassigning Reference

```java
Demo d1 = new Demo();    // Object 1
Demo d2 = new Demo();    // Object 2
d1 = d2;                 // Object 1 → GC eligible
```

```text
d1 ─────► [Object 2] ◄────── d2
           [Object 1] → GC eligible
```

> **Multiple references** ek object ko point kar sakti hain.

### 3. Local Object (Method Scope)

```java
void show() {
    Demo d = new Demo();     // local object
}
// method khatam → d out of scope → object GC eligible
```

> Jab tak reference method ke bahar escape nahi karta.

### 4. Anonymous Object

```java
new Demo().show();    // no stored reference
```

Expression evaluate hone ke baad agar koi reference nahi bacha → **GC eligible**.

### 5. Island of Isolation

Objects jo **ek dusre ko reference** karte hain but **bahar se koi active reference nahi** → sab GC eligible.

```java
class A {
    A i;

    public static void main(String[] args) {
        A a1 = new A();
        A a2 = new A();
        a1.i = a2;
        a2.i = a1;
        a1 = null;
        a2 = null;    // Island of Isolation
    }
}
```

```text
Before null:
a1 ──► [Obj1] ──► [Obj2] ──► [Obj1]  (circular)
a2 ──► [Obj2]

After null:
a1 → null    a2 → null

[Obj1] ◄──► [Obj2]   ← no active reference reaches them
        ↓
  Both GC eligible
```

---

## 3. Requesting GC

```java
System.gc();                       // conventional way
Runtime.getRuntime().gc();         // equivalent way
```

> Note: **Request / suggestion only!** JVM guarantee nahi deti ki GC immediately run hoga. Koi fixed percentage guarantee bhi nahi hai.

---

## 4. `finalize()` — Legacy Cleanup

```java
protected void finalize() throws Throwable
```

Historically, JVM object reclaim karne se pehle `finalize()` call kar sakti thi.

```java
class Demo {
    @Override
    protected void finalize() throws Throwable {
        System.out.println("Finalize called");
    }
}
```

### Note: Deprecated for Removal (Java 9+)

```text
@Deprecated(since = "9", forRemoval = true)
```

### Modern Alternatives

| Old Way | Modern Way |
|---------|-----------|
| `finalize()` | `try-with-resources` |
| `finalize()` | `AutoCloseable` + `close()` |
| `finalize()` | `Cleaner` class |

### Manual vs JVM finalize()

| Feature | Manual `d.finalize()` | JVM Finalization |
|---------|----------------------|------------------|
| Type | Ordinary method call | JVM-managed mechanism |
| Destroys object? | No | Associated with reclamation |
| Multiple calls? | Yes | At most once automatically |
| Exception handling | Normal Java rules | Uncaught exceptions ignored |

---

## 5. GC Flow

```text
Object becomes unreachable
        ↓
GC Eligible
        ↓
JVM may run GC (timing not guaranteed)
        ↓
Memory reclaimed
```

---

## 6. OutOfMemoryError & StackOverflowError

### OutOfMemoryError

```java
int[][] arr = new int[100000][100000];
// → java.lang.OutOfMemoryError: Java heap space
```

### StackOverflowError

```java
static void test() {
    test();     // infinite recursion
}
// → java.lang.StackOverflowError
```

> Both are **`Error`** (not `Exception`). Technically catchable but generally not recoverable.

---

## Interview Quick Traps

| Trap | Answer |
|------|--------|
| GC eligible = immediately destroyed? | No |
| `System.gc()` guaranteed hai? | No — request only |
| Island of Isolation me objects GC eligible hain? | Yes |
| `finalize()` use karna chahiye? | No — deprecated |
| `OutOfMemoryError` exception hai? | No — `Error` hai |
| Anonymous object ka reference hota hai? | No stored reference |
| `System.gc()` == `Runtime.getRuntime().gc()`? |  Effectively equivalent |

---

[Previous: BigInteger](./06-biginteger.md) · [Back to OOP Index](./README.md) · [Next: Cloning & Reflection](./08-cloning-and-reflection.md)
