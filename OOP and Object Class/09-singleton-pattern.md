# Singleton Design Pattern — Complete Guide

> **Summary:** Singleton Design Pattern ka concept, implementation steps (Lazy vs Eager), Thread-safety (DCL, Bill Pugh), `Runtime` class example, Singleton break hone ke tarike (Reflection, Serialization, Cloning) aur unke solutions, tatha Enum Singleton.

---

## 1. Singleton Pattern Kya Hai?

**Singleton Pattern** ek **Creational Design Pattern** hai jiska maksad ye ensure karna hota hai ki **puri application me ek class ka sirf aur sirf EK hi instance (object)** bane, aur us instance ko access karne ka ek global point ho.

### Real-World Use Cases
- **Database Connection Pool** — Har query ke liye naya pool banane ki bajaye single pool share hota hai.
- **Logging Service** — Sabhi modules ek hi Logger instance me logs append karte hain.
- **Configuration Manager** — `application.properties` ya app settings ek hi instance me load hoti hain.
- **Hardware / OS Drivers** — Printer spooler ya JVM Runtime environment.

---

## 2. Singleton Banane Ke 3 Golden Steps

Ek class ko Singleton banane ke liye ye 3 cheezein mandatory hain:

```text
1. Private Constructor ───► Bahar se 'new' keyword se object banana BLOCK kar do.
2. Private Static Field ──► Usi class ka single static reference store karo.
3. Public Static Method ──► Bahar ki duniya ko wahi single object provide karne ke liye factory method.
```

```java
public class MySingleton {
 // Step 2: Private static instance
 private static MySingleton instance;

 // Step 1: Private constructor
 private MySingleton() {
 System.out.println("Singleton Instance Created!");
 }

 // Step 3: Public static factory method
 public static MySingleton getInstance() {
 if (instance == null) {
 instance = new MySingleton(); // First time call par create hoga
 }
 return instance; // Har subsequent call par wahi object return hoga
 }
}
```

### Verification:
```java
public class Test {
 public static void main(String[] args) {
 MySingleton s1 = MySingleton.getInstance();
 MySingleton s2 = MySingleton.getInstance();

 System.out.println(s1 == s2); // true (Same memory address!)
 }
}
```

---

## 3. Eager vs Lazy Initialization

| Feature | Eager Initialization | Lazy Initialization |
|---------|----------------------|---------------------|
| **Kab banta hai?** | Class loading ke time par | Jab pehli baar `getInstance()` call ho |
| **Code syntax** | `private static final A INSTANCE = new A();` | `if (instance == null) instance = new A();` |
| **Memory usage** | Chahe use ho ya na ho, memory pehle se consume hoti hai | Memory efficient (on-demand creation) |
| **Startup time** | App startup thoda slow ho sakta hai | Startup fast hota hai |

---

## 4. Multithreading & Thread Safety

Lazy initialization me multi-threaded environment me **Race Condition** ho sakti hai:
- Agar Thread-A aur Thread-B ek hi waqt par `if (instance == null)` check karein, toh dono ko `null` milega aur **do alag objects** ban jayenge! 

### Approaches for Thread Safety:

#### Approach 1: Synchronized Method (Simple but Slow)
```java
public static synchronized MySingleton getInstance() {
 if (instance == null) {
 instance = new MySingleton();
 }
 return instance;
}
```
*Disadvantage:* Har call par lock acquire hota hai, performance degrade hoti hai.

#### Approach 2: Double-Checked Locking (DCL) — Industry Standard ⭐
```java
public class DCLSingleton {
 // 'volatile' ensures visibility across threads & prevents instruction reordering
 private static volatile DCLSingleton instance;

 private DCLSingleton() {}

 public static DCLSingleton getInstance() {
 if (instance == null) { // First check (no locking)
 synchronized (DCLSingleton.class) {
 if (instance == null) { // Second check (with locking)
 instance = new DCLSingleton();
 }
 }
 }
 return instance;
 }
}
```

#### Approach 3: Bill Pugh Solution (Static Inner Helper Class) — Elegant & Best ⭐
```java
public class BillPughSingleton {
 private BillPughSingleton() {}

 // Inner static class is NOT loaded until getInstance() is called
 private static class Helper {
 private static final BillPughSingleton INSTANCE = new BillPughSingleton();
 }

 public static BillPughSingleton getInstance() {
 return Helper.INSTANCE; // ClassLoader guarantees thread-safety automatically!
 }
}
```

---

## 5. Java Standard Library Example: `java.lang.Runtime`

Java API me `Runtime` class Singleton pattern ka best standard example hai:
- `Runtime` class ka constructor private hai.
- Aap `new Runtime()` nahi kar sakte.
- Aapko `Runtime.getRuntime()` factory method use karna padta hai:

```java
Runtime r1 = Runtime.getRuntime();
Runtime r2 = Runtime.getRuntime();

System.out.println(r1 == r2); // true — hamesha same JVM runtime object
System.out.println("Total Memory: " + r1.totalMemory());
System.out.println("Free Memory: " + r1.freeMemory());
```

---

## 6. How Singleton Can Be Broken & Defenses

Interviewers aksar puchte hain: *"Singleton ko tod kar dikhao!"*

### 1⃣ Attack via Reflection
```java
// Reflection can access private constructor!
Constructor<MySingleton> cons = MySingleton.class.getDeclaredConstructor();
cons.setAccessible(true);
MySingleton s3 = cons.newInstance(); // Note: New instance created!
```
** Defense:** Constructor me check lagao:
```java
private MySingleton() {
 if (instance != null) {
 throw new RuntimeException("Instance already exists! Use getInstance().");
 }
}
```

### 2⃣ Attack via Serialization / Deserialization
Object serialize karke file me save karo aur deserialize karo — Java naya object instantiate kar deta hai!
** Defense:** `readResolve()` method implement karo:
```java
protected Object readResolve() {
 return getInstance(); // Returns the existing instance!
}
```

### 3⃣ Attack via Cloning
Agar Singleton class `Cloneable` implement karti hai, toh `clone()` se duplicate ban sakta hai.
** Defense:** `clone()` ko override karke exception throw karo:
```java
@Override
protected Object clone() throws CloneNotSupportedException {
 throw new CloneNotSupportedException("Singleton cannot be cloned!");
}
```

---

## 7. The Ultimate Singleton: Enum Singleton

Effective Java ke author **Joshua Bloch** ke mutabiq, Singleton banane ka sabse safe aur best tarika **Enum** hai:

```java
public enum EnumSingleton {
 INSTANCE;

 public void doSomething() {
 System.out.println("Working with Enum Singleton!");
 }
}
```

### Why Enum Singleton is Best?
1. **JVM Guaranteed Single Instance**
2. **Reflection Proof** — JVM internally reflection ke through Enum instantiate karne nahi deta (`Cannot reflectively create enum objects`).
3. **Serialization Safe** — JVM enum serialization khud handle karta hai, duplicates nahi bante.
4. **Thread Safe by default**.

---

## Interview Quick Traps

| Trap | Answer |
|------|--------|
| Singleton class ka constructor public ho sakta hai? | Nahi, `private` hona zaroori hai. |
| Double-Checked Locking me `volatile` keyword kyu zaroori hai? | Instruction reordering prevent karne aur visibility ensure karne ke liye. |
| Java standard library me Singleton ka example? | `java.lang.Runtime` (`Runtime.getRuntime()`). |
| Singleton todne ke 3 raste kaunse hain? | Reflection, Serialization, aur Cloning. |
| Sabse invincible Singleton implementation konsi hai? | **Enum Singleton**. |

---

[Previous: Cloning & Reflection](./08-cloning-and-reflection.md) · [Back to OOP Index](./README.md) · [Next: File Handling](./10-file-handling.md)
