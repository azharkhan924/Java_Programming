# Loops in Java

---

## 1. Types of Loops

```text
1. while
2. do-while
3. for
4. for-each (Enhanced for)
```

---

## 2. `while` Loop

```java
while (condition) {
 // body
}
```

Condition must be **boolean** — same rule as `if`:

```java
while (0) { } // incompatible types: int cannot be converted to boolean
```

---

## 3. `for` Loop

```java
for (initialization; condition; update) {
 // body
}
```

### Example

```java
for (int i = 0; i < 5; i++) {
 System.out.println(i);
}
```

### Important: Exactly Two Semicolons

```java
for (;;) { } // infinite loop — valid
for (int i = 0; i < 5; i++) { } // valid

for (int i = 0; i < 5; i++;) { } // extra semicolon — compile error
```

---

## 4. `for-each` Loop (Enhanced For)

Arrays aur `Iterable` collections traverse karne ke liye:

```java
for (dataType variable : arrayOrCollection) {
 // body
}
```

### Example

```java
int[] x = {10, 20, 30, 40};

for (int i : x) {
 System.out.println(i);
}
```

Output:

```text
10
20
30
40
```

### Important Points

- Automatically traversal — index manage nahi karna padta
- Arrays aur `Iterable` collections ke saath kaam karta hai
- First se last element tak sequentially
- **Index-based manipulation** ke liye for-each suitable nahi — traditional `for` loop use karo

---

## 5. `break` and `continue`

### `break` — Exit Loop/Switch

```java
for (int i = 1; i <= 5; i++) {
 if (i == 3) break;
 System.out.println(i);
}
// Output: 1, 2
```

> `break` **nearest enclosing loop ya switch** se bahar nikalta hai.

### `continue` — Skip Current Iteration

```java
for (int i = 1; i <= 5; i++) {
 if (i == 3) continue;
 System.out.println(i);
}
// Output: 1, 2, 4, 5
```

> `continue` current iteration skip karke **next iteration** par jaata hai.

### Rules

- `continue` must be inside a **loop** (switch me nahi)
- `break` can target a **loop or switch**
- **Labeled break/continue** enclosing labeled statement ko target kar sakte hain

---

## 6. Constant Expression vs Variable — Unreachable Code

Java compiler **constant expressions** ke liye compile-time analysis karta hai.

### Constant Expression → Unreachable Error

```java
while (true) {
}
System.out.println("Hello"); // unreachable statement
```

Compiler jaanta hai ki `while(true)` kabhi terminate nahi hoga.

### Variable → No Error

```java
boolean x = true;

while (x) {
}
System.out.println("Hello"); // no compile error
```

`x` variable hai — compiler exactly prove nahi kar sakta ki loop infinite hai.

> **Exam point:** Constant conditions → compiler unreachable code detect kar sakta hai. Variable conditions → generally runtime par evaluate hoti hain.

---

## Interview Traps

| Trap | Answer |
|------|--------|
| `while (0)` valid hai? | No — must be boolean |
| `for` loop me kitne semicolons? | Exactly **2** |
| `for (;;)` valid hai? | Yes — infinite loop |
| `while(true) { } sop("hi")` compile hoga? | No — unreachable statement |
| `boolean x = true; while(x) { } sop("hi")` compile hoga? | Yes — variable condition |
| `continue` switch me allowed hai? | No — loop me hi allowed |
| `break` switch me allowed hai? | Yes |

---

## Quick Revision

```text
LOOPS
→ while, do-while, for, for-each

for-each
→ Arrays + Iterable collections
→ Automatic traversal, no index management

break → exits nearest loop/switch
continue → skips current iteration, continues next

UNREACHABLE CODE
→ Constant true condition + code after loop → compile error
→ Variable condition + code after loop → allowed
```

---

[Previous: Control Statements](./10-control-statements.md) · [Back to Core Java Index](./README.md) · [Next: I/O & Scanner](./12-io-and-scanner.md)
