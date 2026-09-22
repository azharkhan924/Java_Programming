# OOP & Object Class — Master Quick Revision & Cheat Sheet

> **Summary:** Ek single page par pure module (Files 01 to 10) ka revision, critical comparison tables, interview traps, aur golden rules.

---

## 1. Topic-Wise Summary Matrix

| # | Topic | Core Concept | Golden Rule |
|---|-------|--------------|-------------|
| **01** | **Object Class** | Root of all Java classes | Every class directly/indirectly inherits 11/12 methods of `Object`. |
| **02** | **toString & hashCode** | Object string & hash representation | Default `toString()` = `ClassName@HexHash`. Always override both together! |
| **03** | **equals() & ==** | Identity vs Content comparison | `==` checks heap reference/address. `equals()` checks content (if overridden). |
| **04** | **Typecasting & instanceof** | Upcasting (safe) vs Downcasting (risky) | Downcasting without `instanceof` check leads to `ClassCastException`! `null instanceof X` is always `false`. |
| **05** | **Strings & Pool** | String Immutability & SCP | String literals go to SCP. `new String()` creates in Heap + SCP. `StringBuilder` is mutable. |
| **06** | **BigInteger** | Arbitrary-precision integers | No 64-bit overflow. Immutable objects. Arithmetic via methods (`add()`, `multiply()`). |
| **07** | **Garbage Collection** | Automatic memory reclamation | 5 ways to make eligible. `System.gc()` is just a request! `finalize()` is deprecated. |
| **08** | **Cloning & Reflection** | Object duplication & Runtime inspection | `Cloneable` is marker. Default clone is Shallow. Reflection inspects metadata at runtime. |
| **09** | **Singleton Pattern** | Only ONE instance per application | Private constructor + static getter. Double-checked locking or Enum for safety. |
| **10** | **File Handling** | Byte/Character streams & I/O | `FileOutputStream(..., true)` for append. `read()` returns `int`, `-1` is EOF. |

---

## 2. ⚖ Crucial Comparison Tables

### A. `==` Operator vs `equals()` Method

| Feature | `==` Operator | `equals()` Method |
|---------|---------------|-------------------|
| **What it is** | Binary operator | Method defined in `Object` class |
| **Default behavior** | Compares memory references / addresses | Calls `==` by default unless overridden |
| **Primitives** | Compares raw values (`10 == 10`) | Primitive types par call nahi ho sakta |
| **Custom classes** | Compares identity | Override to compare meaningful fields |

---

### B. Shallow Cloning vs Deep Cloning

| Feature | Shallow Cloning | Deep Cloning |
|---------|-----------------|--------------|
| **Mechanism** | `super.clone()` directly | Clones outer + explicitly clones inner objects |
| **Primitives** | Values copied | Values copied |
| **Nested Objects** | Shared memory address (side-effect risk!) | Completely separate fresh instances |
| **Performance** | Very fast (native bitwise) | Slower, requires more allocations |

---

### C. Eager vs Lazy vs Enum Singleton

| Feature | Eager Singleton | Lazy Singleton (DCL) | Enum Singleton (Best) |
|---------|-----------------|----------------------|------------------------|
| **Instance Creation** | At class loading | On first call to `getInstance()` | On enum loading |
| **Thread Safety** | Guaranteed by ClassLoader | Needs `volatile` + `synchronized` | JVM Guaranteed |
| **Reflection Safe?** | Broken by Reflection | Broken by Reflection | Immune |
| **Serialization Safe?** | Needs `readResolve()` | Needs `readResolve()` | Built-in safe |

---

### D. Upcasting vs Downcasting

| Feature | Upcasting | Downcasting |
|---------|-----------|-------------|
| **Direction** | Child ➔ Parent (`Animal a = new Dog();`) | Parent ➔ Child (`Dog d = (Dog) a;`) |
| **Safety** | 100% Type-safe | Risky — may throw `ClassCastException` |
| **Syntax** | Implicit / Automatic | Explicit cast required |
| **Access** | Parent class methods only | Child-specific methods restored |

---

## 3. Top 15 Interview Golden Rules

1. **HashCode-Equals Contract:** Agar `o1.equals(o2)` is `true`, toh unka `hashCode()` MUST BE identical. Reverse zaroori nahi hai (Hash Collision).
2. **String Immutability:** String ka koi bhi method original string ko modify nahi karta; hamesha new String return karta hai.
3. **Switch Expression (Java 14+):** Arrow syntax `->` me `break` ki zaroorat nahi hoti; no fall-through!
4. **Cloneable is Empty:** `Cloneable` me 0 methods hain — ye sirf ek JVM marker interface hai.
5. **Runtime Class:** `Runtime.getRuntime()` is a classic standard library Singleton example.
6. **File EOF:** `InputStream.read()` returns `int`, aur stream end hone par **`-1`** return karta hai.
7. **Append in FileOutputStream:** Append mode enable karne ke liye `new FileOutputStream("file.txt", true)` pass karo.
8. **GC is not Instant:** Object unreachable hote hi GC execute hona guaranteed nahi hai.
9. **No Island of Isolation Escape:** Agar do objects sirf ek dusre ko refer kar rahe hain aur root se unattached hain, toh dono GC eligible hain.
10. **Finalize is Deprecated:** Modern Java me resources clean karne ke liye `AutoCloseable` + `try-with-resources` use karo.
11. **instanceof with null:** `null instanceof AnyClass` hamesha **`false`** evaluate hota hai bina exception ke.
12. **BigInteger is Immutable:** `a.add(b)` call karne par `a` change nahi hota; result new `BigInteger` hota hai (`a = a.add(b)`).
13. **Class.forName():** Dynamically loads a class at runtime without needing compile-time dependency.
14. **Reflection Breaks Private:** `field.setAccessible(true)` private members ko bhi access de deta hai.
15. **Try-with-resources:** Bracket ke andar declared resources automatically close ho jaate hain.

---

[Previous: File Handling](./10-file-handling.md) · [Back to OOP Index](./README.md) · [Root README](../README.md)
