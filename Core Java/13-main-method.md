# The `main()` Method & `System.out.println()`

---

## 1. `public static void main(String[] args)`

Java application ka standard **entry point**:

```java
public static void main(String[] args)
```

### Each Keyword Explained

| Keyword | Reason |
|---------|--------|
| `public` | JVM/application launcher ko method access karna hota hai |
| `static` | Object create kiye bina call ho sake |
| `void` | JVM ko koi return value nahi deta |
| `main` | Standard entry-point method name |
| `String[] args` | Command-line arguments receive karne ke liye |

---

## 2. Why `main()` is Static?

Agar `main()` instance method hota:

```java
public void main(String[] args)   // non-static
```

to JVM ko pehle object create karna padta — **chicken-and-egg problem**.

`static` hone ki wajah se JVM class-level method ko **object create kiye bina** invoke kar sakta hai.

---

## 3. Command-Line Arguments

```java
class Demo {
    public static void main(String[] args) {
        System.out.println(args[0]);
        System.out.println(args[1]);
    }
}
```

Run:

```text
java Demo Java Developer
```

Output:

```text
Java
Developer
```

```text
args[0] = "Java"
args[1] = "Developer"
```

> Command-line arguments always **Strings** hote hain. Number chahiye to:
> ```java
> int x = Integer.parseInt(args[0]);
> ```

---

## 4. `System.out.println()` — Internal Breakdown

```text
System      → java.lang.System (class)
     ↓
out         → static field (type: PrintStream)
     ↓            conceptually: public static final PrintStream out
println()   → PrintStream ki instance method
```

### Step by Step

```java
System.out.println("Hello");
```

1. `System` → class (`java.lang` — automatically imported)
2. `out` → `System` ka static field → `PrintStream` object
3. `println()` → `PrintStream` class ki instance method

### Key Points

- `PrintStream` class: `java.io.PrintStream`
- `java.lang` package automatically imported → no explicit import needed
- `out` is `public static final PrintStream`

---

## 5. `System.out` with Static Import

```java
import static java.lang.System.out;

class Demo {
    public static void main(String[] args) {
        out.println("Hello");       // valid no System prefix
    }
}
```

Wildcard:

```java
import static java.lang.System.*;

out.println("Hello");              // valid
```

---

## Interview Quick Questions

| Question | Answer |
|----------|--------|
| `main()` kyu `static` hai? | Object create kiye bina JVM invoke kar sake |
| `main()` kyu `public` hai? | JVM launcher ko accessible hona chahiye |
| `main()` ka return type? | `void` |
| `System.out` ka type kya hai? | `PrintStream` |
| `println()` kaunsi class ki method hai? | `PrintStream` |
| Command-line args ka type? | `String[]` |
| `args[0]` me kya aata hai? | First command-line argument (as String) |

---

## ⚡ Quick Revision

```text
main() Method:
→ public + static + void + main + String[] args
→ JVM entry point
→ static = no object needed
→ args = command-line arguments (Strings)

System.out.println():
→ System = class (java.lang)
→ out = static PrintStream field
→ println() = PrintStream instance method
→ java.lang auto-imported — no import needed

Command-Line Args:
→ Always Strings
→ args[0] = first argument
→ parseInt() for number conversion
```

---

[Previous: I/O & Scanner](./12-io-and-scanner.md) · [Back to Core Java Index](./README.md) · [Next: Quick Revision](./14-quick-revision.md)
