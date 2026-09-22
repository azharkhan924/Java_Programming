# ⚖️ equals() Method & == Operator

---

## 1. `==` Operator for Objects

Objects ke case mein `==` **reference comparison** karta hai — kya dono references **same object** ko point kar rahe hain?

```java
String s1 = new String("Java");
String s2 = new String("Java");

System.out.println(s1 == s2);    // false
```

Because:

```text
s1 ─────► [String "Java"]  ← Object 1
s2 ─────► [String "Java"]  ← Object 2
```

Different objects hain — content same hone se farak nahi padta.

---

## 2. Object Class ka Default equals()

```java
public boolean equals(Object obj) {
    return this == obj;
}
```

Default `equals()` essentially **reference identity** compare karti hai — same as `==`.

```java
class A { }

A a1 = new A();
A a2 = new A();

System.out.println(a1.equals(a2));   // false — different objects
System.out.println(a1.equals(a1));   // true  — same object
```

---

## 3. String ka equals() — Content Comparison

`String` class ne `equals()` ko **override** kiya hai — ye content compare karta hai:

```java
String s1 = new String("Java");
String s2 = new String("Java");

System.out.println(s1 == s2);          // false — references different
System.out.println(s1.equals(s2));     // true  — content same
```

### Quick Reference

| Expression | Comparison Type | Result |
|-----------|----------------|--------|
| `s1 == s2` | Reference comparison | `false` |
| `s1.equals(s2)` | String content comparison | `true` |
| `obj1.equals(obj2)` (using Object's default) | Reference/identity | Depends |

---

## 4. Custom equals() — Employee Example

Agar same `id` wale Employees ko equal maanna ho:

```java
class Employee {
    int id;
    String name;

    Employee(int id, String name) {
        this.id = id;
        this.name = name;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Employee)) return false;
        Employee e = (Employee) o;
        return this.id == e.id;
    }
}
```

```java
Employee e1 = new Employee(101, "Azhar");
Employee e2 = new Employee(101, "Rahul");

System.out.println(e1.equals(e2));   // true — same id
```

---

## 5. Why Parameter is `Object`?

```java
public boolean equals(Object o)
```

Parameter `Object` hai because `Object` Java ki root class hai — **kisi bhi type ka object** argument me aa sakta hai.

```text
Employee object
      ↓
Object reference parameter
      ↓
instanceof check
      ↓
Cast to Employee
      ↓
Compare data
```

---

## 6. Null Safety with instanceof

### ❌ Unsafe equals()

```java
@Override
public boolean equals(Object o) {
    Employee e = (Employee) o;
    return this.id == e.id;
}
```

```java
e1.equals(null);
// (Employee) null → e = null
// e.id → NullPointerException ❌
```

### ✅ Safe equals() with instanceof

```java
@Override
public boolean equals(Object o) {
    if (this == o) return true;
    if (!(o instanceof Employee)) return false;
    Employee e = (Employee) o;
    return this.id == e.id;
}
```

```java
e1.equals(null);
// null instanceof Employee → false
// return false ✅ — no NullPointerException
```

---

## 7. Recommended equals() Structure

```java
@Override
public boolean equals(Object o) {

    // Step 1: Same reference check
    if (this == o) return true;

    // Step 2: Type check (also handles null)
    if (!(o instanceof Employee)) return false;

    // Step 3: Cast
    Employee other = (Employee) o;

    // Step 4: Compare relevant fields
    return this.id == other.id;
}
```

For `String` fields:

```java
return Objects.equals(this.name, other.name);
```

> `Objects.equals()` null-safe hai — `==` se compare nahi karna `String` ko.

---

## 8. equals() Override → hashCode() bhi Override Karo

### The Contract

```text
If   a.equals(b) == true
Then a.hashCode() == b.hashCode()   (MUST)
```

```java
@Override
public int hashCode() {
    return Integer.hashCode(id);
}
```

> Ye contract `HashMap`, `HashSet` jaise collections ke correct functioning ke liye **mandatory** hai.

---

## 9. == vs equals() vs String Pool — Summary

```java
String s1 = new String("abc");
String s2 = new String("abc");
String s3 = "abc";
String s4 = "abc";

System.out.println(s1 == s2);          // false — different new objects
System.out.println(s1.equals(s2));     // true  — same content
System.out.println(s1 == s3);          // false — heap vs pool
System.out.println(s1.equals(s3));     // true  — same content
System.out.println(s3 == s4);          // true  — same pooled literal
System.out.println(s3.equals(s4));     // true  — same content
```

---

## 10. == Compile-Time Check

```java
String s1 = new String("aaa");
StringBuffer s2 = new StringBuffer("aaa");

System.out.println(s1 == s2);   // ❌ Compile-time error
```

`String` aur `StringBuffer` unrelated final classes hain — compiler jaanta hai ki dono kabhi same object nahi ho sakte.

> `==` requires reference types to be **compatible** by casting rules.

---

## 🧠 Interview Quick Traps

| Trap | Answer |
|------|--------|
| `==` objects me kya compare karta hai? | References (same object?) |
| Default `Object.equals()` kya karta hai? | `this == obj` (reference check) |
| `String.equals()` kya karta hai? | Content comparison |
| `null instanceof AnyType` ka result? | `false` |
| `equals()` override kiya to `hashCode()` bhi override karna chahiye? | ✅ Mandatory |
| `Objects.equals(a, b)` kyu prefer karte hain? | Null-safe comparison |
| `new String("abc") == "abc"` ka result? | `false` |

---

[⬅️ Previous: toString() & hashCode()](./02-tostring-and-hashcode.md) · [📖 Back to OOP Index](./README.md) · [Next → Type Casting & instanceof ➡️](./04-typecasting-and-instanceof.md)
