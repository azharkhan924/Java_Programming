# 📥 I/O, Scanner & BufferedReader

---

## 1. Standard I/O Streams

| Stream | Purpose | Object |
|--------|---------|--------|
| Standard Output | Screen/Console | `System.out` |
| Standard Input | Keyboard | `System.in` |

### `System.out.println()` — Deep Dive

```text
System   → class (java.lang.System)
out      → static field (type: PrintStream)
println()→ PrintStream ki instance method
```

```java
System.out.println("Hello");
// System → out (PrintStream object) → println() method
```

> `println()` ka return type **`void`** hai — kuch return nahi karta.

### `%n` in printf

```java
System.out.printf("Hello%nWorld");
```

Output:

```text
Hello
World
```

> `%n` = platform-independent line separator.

---

## 2. InputStreamReader (ISR)

`System.in` ek **byte-oriented** input stream hai. `InputStreamReader` byte → character conversion karta hai.

```text
Keyboard → System.in → InputStreamReader → Character data
```

```java
import java.io.InputStreamReader;

InputStreamReader isr = new InputStreamReader(System.in);
```

---

## 3. BufferedReader (BR)

`BufferedReader` efficient character input ke liye buffer use karta hai.

```text
System.in → InputStreamReader → BufferedReader
```

```java
import java.io.*;

BufferedReader br = new BufferedReader(
    new InputStreamReader(System.in)
);
```

### `read()` — Single Character

```java
int x = br.read();
```

- Returns character ka **integer value** (Unicode code unit)
- End of stream par **`-1`** return karta hai
- Return type: `int` (not `char`)

### `readLine()` — Entire Line

```java
String s = br.readLine();
```

- Puri line read karke **`String`** return karta hai

Input: `10 20 30` → Result: `"10 20 30"` (as one String)

---

## 4. IOException

I/O operations `IOException` throw kar sakti hain:

```java
import java.io.*;

class Demo {
    public static void main(String[] args) throws IOException {

        BufferedReader br = new BufferedReader(
            new InputStreamReader(System.in)
        );

        String s = br.readLine();
        System.out.println(s);
    }
}
```

> `java.lang` automatically imported hota hai. I/O classes (`InputStreamReader`, `BufferedReader`, `IOException`) `java.io` me hain.

---

## 5. StringTokenizer

String ko **tokens** (smaller parts) me todne ke liye.

```java
import java.util.StringTokenizer;

String s = "10 20 30";
StringTokenizer st = new StringTokenizer(s);
```

By default **whitespace** delimiter hota hai.

### Custom Delimiters

```java
String s = "10,20 30";
StringTokenizer st = new StringTokenizer(s, ", ");
// Delimiters: comma AND space
```

### Important Methods

| Method | Purpose |
|--------|---------|
| `nextToken()` | Next token return + position advance |
| `countTokens()` | Remaining tokens count |
| `hasMoreTokens()` | More tokens available? (boolean) |

```java
while (st.hasMoreTokens()) {
    System.out.println(st.nextToken());
}
```

### ⚠️ NoSuchElementException

Agar `nextToken()` call kiya jab koi token nahi bacha → `NoSuchElementException`.

---

## 6. Scanner Class

```java
import java.util.Scanner;

Scanner sc = new Scanner(System.in);
```

### `next()` — Single Token

```java
String s = sc.next();
```

Input: `Hello World` → Result: `"Hello"` (whitespace par ruk jaata hai)

### `nextInt()` — Integer Token

```java
int x = sc.nextInt();
```

> Agar next token valid integer nahi hai → **`InputMismatchException`**.

### `nextLine()` — Full Line

```java
String line = sc.nextLine();
```

Input: `Hello World` → Result: `"Hello World"` (puri line)

---

## 7. Enter Key — `\r` and `\n`

| Character | Name | Purpose |
|-----------|------|---------|
| `\r` | Carriage Return | Cursor line start par |
| `\n` | Line Feed | New line |

Windows me Enter key produces: `\r\n`

> Line ending **platform-dependent** hai. Java me `System.lineSeparator()` use karo platform-appropriate separator ke liye.

---

## 8. Scanner vs BufferedReader

| Feature | Scanner | BufferedReader |
|---------|---------|---------------|
| Package | `java.util` | `java.io` |
| Parsing | ✅ Built-in (`nextInt()`, `nextDouble()`) | ❌ Manual parsing needed |
| Speed | Slower (parsing overhead) | Faster (buffered) |
| Exception | `InputMismatchException` | `IOException` |
| Token reading | `next()` — one token | `readLine()` — full line |
| Best for | Simple inputs, competitive coding | Large inputs, performance-critical |

---

## 🧠 Interview Traps

| Trap | Answer |
|------|--------|
| `System.out.println()` ka return type? | `void` |
| `BufferedReader.read()` return type? | `int` (not `char`) |
| `BufferedReader.readLine()` return type? | `String` |
| `Scanner.next()` pura line read karta hai? | ❌ No — sirf ek token |
| `nextToken()` jab token nahi bacha to? | `NoSuchElementException` |
| `Scanner.nextInt()` invalid input par? | `InputMismatchException` |
| `%n` kya karta hai? | Platform-independent line separator |

---

## ⚡ Quick Revision

```text
I/O
→ System.in  = standard input (keyboard)
→ System.out = standard output (console)

Input Hierarchy:
System.in → InputStreamReader → BufferedReader

BufferedReader
→ read() returns int
→ readLine() returns String

StringTokenizer (java.util)
→ nextToken(), countTokens(), hasMoreTokens()
→ NoSuchElementException if no token left

Scanner (java.util)
→ next() = one token
→ nextInt() = integer
→ nextLine() = full line
→ InputMismatchException on invalid input
```

---

[⬅️ Previous: Loops](./11-loops.md) · [📖 Back to Core Java Index](./README.md) · [Next → main() Method ➡️](./13-main-method.md)
