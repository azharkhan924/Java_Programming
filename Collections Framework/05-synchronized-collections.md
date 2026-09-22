# 🔄 Synchronized Collections — Thread-Safe Wrappers

> **Summary:** ArrayList/LinkedList ki thread-safety problem, `Collections.synchronizedList/Set/Map()` wrapper methods, `CopyOnWriteArrayList`, aur synchronized vs concurrent collections ka farq.

---

## 1. The Problem — ArrayList is NOT Thread-Safe

ArrayList, LinkedList, HashSet, HashMap — ye sab **default me synchronized nahi** hain.

Agar multiple threads ek hi ArrayList par simultaneously read/write karein:

```java
// ❌ DANGER: Multiple threads modifying same ArrayList!
ArrayList<String> list = new ArrayList<>();

// Thread 1: list.add("A");
// Thread 2: list.add("B");
// Thread 3: list.remove(0);
// Result: ConcurrentModificationException, data corruption, crashes! 💥
```

---

## 2. Solution 1: `Collections.synchronizedList()` Wrapper

`Collections` utility class har non-synchronized collection ka **synchronized (thread-safe) wrapper** provide karti hai:

```java
import java.util.Collections;

// ✅ Thread-safe wrapper around ArrayList
List<String> syncList = Collections.synchronizedList(new ArrayList<>());

syncList.add("A");  // Each method call is locked by synchronized block
syncList.add("B");
syncList.get(0);    // Thread-safe read
```

### ⚠️ Important: Iteration still NOT safe automatically!

```java
// ❌ WRONG — Iteration ke dauran koi dusra thread modify kare toh ConcurrentModificationException!
for (String s : syncList) {
    System.out.println(s);
}

// ✅ CORRECT — Manual synchronization required during iteration!
synchronized (syncList) {
    for (String s : syncList) {
        System.out.println(s);
    }
}
```

---

## 3. Synchronized Wrappers — Complete List

| Non-Synchronized | Synchronized Wrapper Method |
|-------------------|-----------------------------|
| `ArrayList` | `Collections.synchronizedList(new ArrayList<>())` |
| `LinkedList` | `Collections.synchronizedList(new LinkedList<>())` |
| `HashSet` | `Collections.synchronizedSet(new HashSet<>())` |
| `TreeSet` | `Collections.synchronizedSortedSet(new TreeSet<>())` |
| `HashMap` | `Collections.synchronizedMap(new HashMap<>())` |
| `TreeMap` | `Collections.synchronizedSortedMap(new TreeMap<>())` |

---

## 4. Solution 2: `CopyOnWriteArrayList` (Modern Approach)

`CopyOnWriteArrayList` (package: `java.util.concurrent`) ek better modern alternative hai:

```java
import java.util.concurrent.CopyOnWriteArrayList;

CopyOnWriteArrayList<String> cowList = new CopyOnWriteArrayList<>();
cowList.add("A");
cowList.add("B");
```

### How It Works:
```text
Read Operations:  Direct access on current internal array → NO LOCKING needed! ✅ Fast!
Write Operations: Puura array ka fresh copy create hota hai, usme modification hoti hai,
                  phir internal reference naye array ko point karne lagta hai.
```

### When to Use CopyOnWriteArrayList?
- **Read operations bahut zyada** hongi aur **write operations rare** hongi (e.g., config lists, listener registries)
- Iteration ke dauran safe rehna hai bina `ConcurrentModificationException` ke

### When NOT to Use?
- Frequent writes → Har write par array copy karna expensive hai!

---

## 5. ⚖️ Comparison: Synchronized Wrapper vs CopyOnWriteArrayList

| Feature | `Collections.synchronizedList()` | `CopyOnWriteArrayList` |
|---------|----------------------------------|------------------------|
| **Read Locking** | ✅ Every read locked | ❌ No lock on reads (fast!) |
| **Write Behavior** | In-place modification with lock | Creates new array copy |
| **Iteration Safety** | Manual sync required around iterator | ✅ Automatically safe (snapshot iterator) |
| **Write Performance** | Better for frequent writes | Worse for frequent writes (array copy each time) |
| **Read Performance** | Slower (lock on every read) | ✅ Faster (no locking) |
| **Best For** | Balanced read/write workloads | Read-heavy, write-rare workloads |

---

## 6. Other Concurrent Collections (Java 5+)

Java `java.util.concurrent` package me aur bhi powerful thread-safe collections hain:

| Class | Replaces | Key Feature |
|-------|----------|-------------|
| `CopyOnWriteArrayList` | `ArrayList` | Copy-on-write, lock-free reads |
| `CopyOnWriteArraySet` | `HashSet` | Copy-on-write Set |
| `ConcurrentHashMap` | `HashMap` / `Hashtable` | Segment-level locking (much faster than full sync) |
| `ConcurrentLinkedQueue` | `LinkedList` as Queue | Lock-free non-blocking queue |
| `ConcurrentLinkedDeque` | `LinkedList` as Deque | Lock-free non-blocking deque |

---

## 🧠 Interview Quick Traps

| Trap | Answer |
|------|--------|
| `ArrayList` ko thread-safe kaise banayein? | `Collections.synchronizedList(new ArrayList<>())` ya `CopyOnWriteArrayList` use karo. |
| `Collections.synchronizedList()` me iteration safe hai? | ❌ Nahi! Manually `synchronized(list) { ... }` block me iterate karna padta hai. |
| `CopyOnWriteArrayList` har operation me lock lagata hai? | ❌ Reads par koi lock nahi! Sirf writes par internal array copy hota hai. |
| `Hashtable` aur `ConcurrentHashMap` me farq? | `Hashtable` full object lock karta hai (slow). `ConcurrentHashMap` segment-level locking karta hai (fast). |
| Kya `Vector` ab bhi use karna chahiye? | ❌ Modern code me generally nahi. `CopyOnWriteArrayList` ya `Collections.synchronizedList()` prefer karo. |

---

[⬅️ Previous: LinkedList](./04-linkedlist.md) · [📖 Back to Collections Index](./README.md) · [Next → Cursors ➡️](./06-cursors.md)
