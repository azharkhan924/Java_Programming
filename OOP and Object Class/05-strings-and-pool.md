# Strings & String Pool

---

## 1. String Pool / String Constant Pool

String literals JVM ke **String Pool** me maintain hote hain.

```java
String s3 = "abc";
String s4 = "abc";
```

Same pooled literal reuse hota hai:

```java
System.out.println(s3 == s4);          // true  — same pooled object
System.out.println(s3.equals(s4));     // true  — same content
```

### `new String()` — Separate Object

```java
String s1 = new String("abc");
String s2 = new String("abc");
```

`new` explicitly distinct objects create karta hai:

```java
System.out.println(s1 == s2);          // false — different objects
System.out.println(s1.equals(s2));     // true  — same content
```

### Memory Visualization

```text
String Pool:
┌─────────┐
│  "abc"   │ ◄── s3, s4 point here
└─────────┘

Heap:
┌──────────┐    ┌──────────┐
│ String   │    │ String   │
│ "abc"    │    │ "abc"    │
└──────────┘    └──────────┘
     ↑               ↑
     s1              s2
```

> String Pool modern JVMs me **heap memory** me hi hota hai. Sirf literals aur `intern()` ke through pooled strings main pool me aate hain.

---

## 2. String Immutability

`String` objects **immutable** hain — ek baar create hone ke baad content change nahi hota.

```java
String s = "Hello";
s.concat(" World");
System.out.println(s);     // "Hello" — original unchanged!
```

`concat()` returns a **new String** — original modify nahi hota.

```java
s = s.concat(" World");    // re-assign karna padega
System.out.println(s);     // "Hello World"
```

---

## 3. Mutable Strings — StringBuilder & StringBuffer

| Class | Mutable? | Thread-safe? | Performance |
|-------|----------|-------------|-------------|
| `String` |  Immutable |  (immutable = inherently safe) | Slow for many modifications |
| `StringBuilder` |  Mutable | Not synchronized | ⚡ Fastest — single-threaded preferred |
| `StringBuffer` |  Mutable |  Synchronized | Slower than StringBuilder |

```java
StringBuilder sb = new StringBuilder("Hello");
sb.append(" World");
System.out.println(sb);    // "Hello World"
```

> **Rule of thumb:** Use `StringBuilder` for single-threaded string building. Use `StringBuffer` only when thread safety is needed.

---

## 4. Switch Arrow Labels — Modern Java

### Traditional Switch (Java 1+)

```java
switch (x) {
    case 1:
        System.out.println("A");
        break;
    case 2:
        System.out.println("B");
        break;
    default:
        System.out.println("C");
}
```

### Arrow Switch (Java 14+)

```java
switch (x) {
    case 1 -> System.out.println("A");
    case 2 -> System.out.println("B");
    default -> System.out.println("C");
}
```

> Arrow rules **do NOT fall through** — separate `break` ki zaroorat nahi.

### Multiple Case Labels

```java
switch (x) {
    case 1, 3, 5, 7, 9  -> System.out.println("Odd");
    case 2, 4, 6, 8, 10 -> System.out.println("Even");
    default              -> System.out.println("Invalid");
}
```

### Multiple Statements — Use Block

```java
case 1 -> {
    System.out.println("A");
    System.out.println("B");
}
```

### Note: Duplicate Labels Still Invalid

```java
case 1, 2 -> System.out.println("A");
case 2, 3 -> System.out.println("B");   // duplicate case label: 2
```

---

## 5. File Handling — FileOutputStream

### Write to File

```java
FileOutputStream fi = new FileOutputStream("abc.txt");
fi.write('a');
fi.close();
```

### Append Mode

```java
FileOutputStream fi = new FileOutputStream("abc.txt", true);
```

`true` → **append mode**: existing content retained, new data added at end.

### Read from File

```java
int x = fi.read();
```

- Returns byte value as `int`
- Returns `-1` at **end of stream**

---

## Interview Quick Questions

| Question | Answer |
|----------|--------|
| `"abc" == "abc"` ka result? |  `true` — same pooled literal |
| `new String("abc") == new String("abc")` ka result? | `false` — different objects |
| `String` immutable kyu hai? | Security, caching, thread-safety, String Pool |
| `StringBuilder` vs `StringBuffer` ka difference? | StringBuilder = not synchronized, StringBuffer = synchronized |
| Arrow switch me `break` chahiye? | No — no fall-through |
| Multiple case labels allowed hain? | Yes — `case 1, 2 ->` |
| `FileOutputStream("file", true)` kya karta hai? | Append mode — existing content preserved |
| `InputStream.read()` kya return karta hai? | `int` — `-1` at end of stream |

---

[Previous: Type Casting & instanceof](./04-typecasting-and-instanceof.md) · [Back to OOP Index](./README.md) · [Next: BigInteger](./06-biginteger.md)
