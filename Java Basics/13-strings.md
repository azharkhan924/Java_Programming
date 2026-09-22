# Strings in Java

---

## What is String?

`String` Java me ek **class** hai (reference type), primitive nahi.

```java
String name = "Azhar"; // String literal — double quotes
```

| Property | Detail |
|----------|--------|
| **Type** | Reference type (class `java.lang.String`) |
| **Quotes** | Double quotes `" "` |
| **Mutable?** | **Immutable** — once created, cannot be changed |
| **Default** | `null` |

> Note: `'A'` → char (single quotes), `"A"` → String (double quotes)

---

## String Concatenation with `+`

`+` operator String ke saath use karne par **concatenation** karta hai:

```java
String x = "Hello";
String y = "World";

System.out.println(x + y); // Output: HelloWorld
System.out.println(x + " " + y); // Output: Hello World
```

### String + Non-String

Jab `+` ke ek side String ho aur doosri side koi aur type, to non-String part **automatically String me convert** hota hai:

```java
System.out.println("Age: " + 20); // Output: Age: 20
System.out.println("Score: " + 95.5); // Output: Score: 95.5
System.out.println("Pass: " + true); // Output: Pass: true
```

### Note: Tricky Behavior — Order Matters!

```java
System.out.println(10 + 20 + "Java"); // Output: 30Java
// 10 + 20 = 30 (arithmetic), then 30 + "Java" = "30Java"

System.out.println("Java" + 10 + 20); // Output: Java1020
// "Java" + 10 = "Java10", then "Java10" + 20 = "Java1020"

System.out.println("Java" + (10 + 20)); // Output: Java30
// Parentheses force arithmetic first
```

> ** Rule:** Left-to-right evaluation hoti hai. Jab tak String nahi milta, arithmetic hota hai. String milne ke baad sab concatenation!

---

## `char` + `char` vs `String` + `String`

```java
// char + char → INTEGER ADDITION (not concatenation!)
System.out.println('A' + 'B'); // Output: 131 (65 + 66)

// String + String → CONCATENATION
System.out.println("A" + "B"); // Output: AB
```

| Expression | What Happens | Result |
|-----------|-------------|--------|
| `'A' + 'B'` | Numeric addition | `131` |
| `"A" + "B"` | String concatenation | `"AB"` |
| `"" + 'A' + 'B'` | Concatenation (empty string forces it) | `"AB"` |
| `'A' + 'B' + ""` | `131 + ""` → concatenation | `"131"` |

---

## String Comparison

### Don't use `==` for content comparison

```java
String a = new String("Java");
String b = new String("Java");

System.out.println(a == b); // false → compares REFERENCES
System.out.println(a.equals(b)); // true → compares CONTENT
```

### Always use `.equals()` for content

```java
String s1 = "Hello";
String s2 = "Hello";

s1.equals(s2); // true — content match
s1.equalsIgnoreCase("HELLO"); // true — case-insensitive
```

> ** Pro Tip:** NullPointerException avoid karne ke liye literal pehle likho:
> ```java
> "Hello".equals(str); // Safe even if str is null
> str.equals("Hello"); // Throws NPE if str is null
> ```

---

## String Pool (Important Concept)

```java
String a = "Java"; // Goes to String Pool
String b = "Java"; // Points to SAME object in pool
String c = new String("Java"); // Creates NEW object on heap

System.out.println(a == b); // true (same pool reference)
System.out.println(a == c); // false (different objects)
System.out.println(a.equals(c)); // true (same content)
```

```text
 String Pool (Heap) Heap
 ┌─────────────────┐ ┌──────────────┐
 │ "Java" ←── a │ │ "Java" ←── c│
 │ ←── b │ │ │
 └─────────────────┘ └──────────────┘
```

---

## Useful String Methods (Preview)

| Method | Description | Example |
|--------|-------------|---------|
| `length()` | String ki length | `"Java".length()` → `4` |
| `charAt(i)` | Index `i` par character | `"Java".charAt(0)` → `'J'` |
| `equals()` | Content comparison | `"abc".equals("abc")` → `true` |
| `toUpperCase()` | Uppercase convert | `"java".toUpperCase()` → `"JAVA"` |
| `toLowerCase()` | Lowercase convert | `"JAVA".toLowerCase()` → `"java"` |
| `trim()` | Leading/trailing spaces remove | `" hi ".trim()` → `"hi"` |
| `contains()` | Substring check | `"Hello".contains("ell")` → `true` |
| `substring(i,j)` | Extract part | `"Hello".substring(1,4)` → `"ell"` |
| `replace()` | Replace characters | `"abc".replace('a','x')` → `"xbc"` |

---

## StringBuilder & StringBuffer

`StringBuilder` ek **mutable character sequence** hai.
Matlab: iska content change kar sakte hain without creating a new `String` object every time.

### Why StringBuilder?

`String` immutable hai, isliye repeated modifications/concatenations me multiple String objects create ho sakte hain.
`StringBuilder` mutable hone ki wajah se frequent modifications ke liye useful hai.

### Important Points

- Methods **synchronized nahi hote**.
- Isliye ye `StringBuffer` se generally faster hota hai.
- Multiple threads same `StringBuilder` object ko concurrently access kar sakte hain, but required synchronization khud handle karni padegi.
- **Thread safety required nahi ho** to `StringBuilder` prefer kiya jata hai.
- `StringBuilder` **Java 5** me introduce hua.

```java
StringBuilder sb = new StringBuilder("Hello");
sb.append(" Java");
System.out.println(sb); // Hello Java
```

### Common Methods

```java
sb.append("Java");
sb.insert(0, "Hi ");
sb.replace(0, 2, "Hello");
sb.delete(0, 5);
sb.reverse();
```

### String vs StringBuffer vs StringBuilder

| Property | String | StringBuffer | StringBuilder |
|---|---|---|---|
| Mutability | Immutable | Mutable | Mutable |
| Thread Safe? | Yes | Yes (Synchronized) | No (Not Synchronized) |
| Performance | Fast for constants | Slower due to locks | Fastest for modifications |
| Introduced In | Java 1.0 | Java 1.0 | Java 5.0 |

---

[Back to Index](./README.md) · [Next: printf() Formatting](./14-printf-formatting.md)

