# Variable Arguments (Varargs)

---

## 1. What is Varargs?

**Variable Arguments (Varargs)** — introduced in **Java 5 (JDK 1.5)**.

Jab method me pass hone wale arguments ki count fix nahi ho → varargs use karo.

### Problem Without Varargs

```java
void sum(int a, int b) { }
void sum(int a, int b, int c) { }
void sum(int a, int b, int c, int d) { }
// ... har count ke liye alag method
```

### Solution With Varargs

```java
void sum(int... x) {
 // x is internally an array
}
```

> **Zero ya more values** pass kar sakte hain ek hi method me.

---

## 2. Syntax

```java
returnType methodName(dataType... variableName)
```

Valid styles (spaces around `...`):

```java
void sum(int... a) // 
void sum(int ...a) // 
void sum(int...a) // 
```

> Must have exactly **three dots (`...`)**.

---

## 3. Varargs Rules

### Rule 1 — Last Parameter Hona Chahiye

```java
void display(int x, int... y) // valid
void display(int... x, int y) // invalid
```

### Rule 2 — Only ONE Varargs Parameter

```java
void display(int x, int... y) // valid
void display(int... x, int... y) // invalid
```

### Rule 3 — Varargs & Array Cannot Coexist as Overloads

```java
void sum(int... a) // same effective signature
void sum(int[] a) // cannot coexist
```

---

## 4. Varargs vs Array Parameter

| Feature | Varargs `int... a` | Array `int[] a` |
|---------|-------------------|-----------------|
| Zero args | `sum()` | Not possible |
| Direct values | `sum(10, 20)` | Not possible |
| Array reference | `sum(arr)` | `sum(arr)` |
| Overloading priority | **Lowest** | — |

---

## 5. Example

```java
class A {
 void sum(int... a) {
 int s = 0;
 for (int i : a) {
 s = s + i;
 }
 System.out.println(s);
 }

 public static void main(String[] args) {
 A obj = new A();

 obj.sum(); // 0
 obj.sum(10); // 10
 obj.sum(10, 20); // 30
 obj.sum(100, 200, 300); // 600

 int[] x = {10, 20, 30, 40};
 obj.sum(x); // 100
 }
}
```

---

## 6. Varargs Overloading Resolution

Varargs ko **lowest priority** milti hai overload resolution me:

```text
1. Exact match
2. Widening
3. Boxing/unboxing
4. Varargs ← lowest
```

---

## 7. `printf()` and Varargs

`printf()` internally varargs use karta hai:

```java
System.out.printf("%d", x);
System.out.printf("%d %d %d", x, y, z);
```

Variable number of arguments accept karne ki capability varargs se aati hai.

---

## Interview Traps

| Trap | Answer |
|------|--------|
| `void sum(int... a)` ko zero args ke saath call kar sakte hain? | Yes |
| `void sum(int[] a)` ko zero args ke saath call kar sakte hain? | No |
| `int... a` aur `int[] a` dono overloaded methods ho sakte hain? | No — same signature |
| Varargs method me argument ki position? | **Last parameter** hona chahiye |
| Ek method me kitne varargs parameters? | **Sirf ek** |
| Varargs ki overloading priority? | **Lowest / least** |

---

## Quick Revision

```text
Varargs
→ Introduced in Java 5
→ Syntax: dataType... name
→ Zero or more arguments
→ Only ONE varargs parameter per method
→ Must be LAST parameter
→ Array reference bhi pass ho sakta hai
→ Lowest priority in overload resolution
→ Cannot coexist with array parameter as overload
```

---

[Previous: Access Modifiers](./08-access-modifiers.md) · [Back to Core Java Index](./README.md) · [Next: Control Statements](./10-control-statements.md)
