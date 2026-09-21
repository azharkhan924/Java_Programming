# 🔀 Control Statements & Switch

---

## 1. Control Statements in Java

```text
1. if
2. if-else
3. Nested if
4. else-if ladder
5. switch-case
6. Ternary operator
7. Loops
```

---

## 2. `if` Condition — Must Be Boolean

Java me `if` condition **boolean expression** chahti hai.

```java
if (true) { }              // ✅
if (x > 5) { }             // ✅

boolean flag = true;
if (flag) { }              // ✅
```

### ⚠️ C/C++ Wala Pattern Java Me Invalid Hai

```java
if (0) { }                 // ❌ incompatible types: int cannot be converted to boolean
if (1) { }                 // ❌

boolean x = 1;             // ❌ int is not boolean
```

> Java me `0` = false aur `1` = true **nahi hota** — C/C++ ki tarah nahi.

---

## 3. Switch Statement

```java
switch (x) {
    case 1:
        System.out.println("One");
        break;

    case 2:
        System.out.println("Two");
        break;

    default:
        System.out.println("Other");
}
```

### Execution Rules

| Scenario | Result |
|----------|--------|
| Matching case found | Us case ka code execute hota hai |
| No match + `default` exists | `default` block execute hota hai |
| No match + no default | Kuch nahi execute hota |

---

## 4. Switch — Important Rules

### `break` Terminates Switch

```java
case 1:
    System.out.println("One");
    break;       // switch se bahar
    break;       // ❌ unreachable statement — compile error
```

### No Duplicate Case Labels

```java
case 1:
    System.out.println("A");
case 1:                        // ❌ duplicate case label
    System.out.println("B");
```

### No Duplicate Default

```java
default:
    System.out.println("A");
default:                       // ❌ duplicate default label
    System.out.println("B");
```

### `case default:` is Invalid

```java
case default:                  // ❌ illegal start of expression
    System.out.println("X");

default:                       // ✅ correct syntax
    System.out.println("X");
```

---

## 5. Allowed Data Types in Switch

### ✅ Allowed

```text
byte, short, char, int
String (Java 7+)
enum
Corresponding wrapper types
```

### ❌ Not Allowed

```text
long, float, double, boolean
```

> **`String` in switch** — Java 7 se support hua. Before Java 7, `String` directly switch me use nahi kar sakte the.

### Byte Switch Range Check

Agar switch expression `byte` hai, to case constants **byte range** (-128 to 127) me hone chahiye.

---

## 🧠 Interview Traps

| Trap | Answer |
|------|--------|
| `if (0)` valid hai Java me? | ❌ No — must be boolean |
| `boolean x = 1;` valid hai? | ❌ No |
| `long` switch me allowed hai? | ❌ No |
| `String` kab se switch me allowed? | **Java 7** onwards |
| `case default:` valid hai? | ❌ No — `default:` alag label hai |
| Duplicate case label allowed? | ❌ No |
| Duplicate `default` allowed? | ❌ No |

---

## ⚡ Quick Revision

```text
if → condition must be boolean
switch → byte, short, char, int, String, enum
       → NOT: long, float, double, boolean
       → String support: Java 7+
       → No duplicate case/default
       → break terminates switch
```

---

[⬅️ Previous: Varargs](./09-varargs.md) · [📖 Back to Core Java Index](./README.md) · [Next → Loops ➡️](./11-loops.md)
