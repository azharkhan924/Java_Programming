# Synchronized Collections — Thread-Safe Wrappers

> **Summary:** Why ArrayList/LinkedList are not thread-safe, `Collections.synchronizedList/Set/Map()` wrapper methods, `CopyOnWriteArrayList`, and synchronized vs concurrent collections comparison.

---

## 1. The Problem — ArrayList is NOT Thread-Safe

ArrayList, LinkedList, HashSet, HashMap — none of these are **synchronized by default**.

If multiple threads simultaneously read/write to the same ArrayList:

```java
// DANGER: Multiple threads modifying same ArrayList!
ArrayList<String> list = new ArrayList<>();
// Thread 1: list.add("A");
// Thread 2: list.add("B");
// Thread 3: list.remove(0);
// Result: ConcurrentModificationException, data corruption, crashes! 
```

---

## 2. Solution 1: `Collections.synchronizedList()` Wrapper

The `Collections` utility class provides a **synchronized (thread-safe) wrapper** for every non-synchronized collection:

```java
// valid Thread-safe wrapper around ArrayList
List<String> syncList = Collections.synchronizedList(new ArrayList<>());
syncList.add("A");  // Each method call is locked by synchronized block
syncList.get(0);    // Thread-safe read
```

### Note: Important: Iteration is still NOT safe automatically!

```java
// WRONG — Another thread modifying during iteration causes ConcurrentModificationException!
for (String s : syncList) {
    System.out.println(s);
}

// valid CORRECT — Manual synchronization required during iteration!
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

`CopyOnWriteArrayList` (package: `java.util.concurrent`) is a better modern alternative:

```java
CopyOnWriteArrayList<String> cowList = new CopyOnWriteArrayList<>();
cowList.add("A");
cowList.add("B");
```

### How It Works:
```text
Read Operations:  Direct access on current internal array → NO LOCKING needed!  Fast!
Write Operations: A fresh copy of the entire array is created, modification is made on the copy,
                  then the internal reference is updated to point to the new array.
```

### When to Use CopyOnWriteArrayList?
- **Read operations are very frequent** and **write operations are rare** (e.g., config lists, listener registries)
- Safe iteration without `ConcurrentModificationException`

### When NOT to Use?
- Frequent writes → Copying the entire array on every write is expensive!

---

## 5. ⚖ Comparison: Synchronized Wrapper vs CopyOnWriteArrayList

| Feature | `Collections.synchronizedList()` | `CopyOnWriteArrayList` |
|---------|----------------------------------|------------------------|
| **Read Locking** |  Every read is locked | No lock on reads (fast!) |
| **Write Behavior** | In-place modification with lock | Creates a new array copy |
| **Iteration Safety** | Manual sync required around iterator |  Automatically safe (snapshot iterator) |
| **Write Performance** | Better for frequent writes | Worse for frequent writes (array copy each time) |
| **Read Performance** | Slower (lock on every read) |  Faster (no locking) |
| **Best For** | Balanced read/write workloads | Read-heavy, write-rare workloads |

---

## 6. Other Concurrent Collections (Java 5+)

The `java.util.concurrent` package provides more powerful thread-safe collections:

| Class | Replaces | Key Feature |
|-------|----------|-------------|
| `CopyOnWriteArrayList` | `ArrayList` | Copy-on-write, lock-free reads |
| `CopyOnWriteArraySet` | `HashSet` | Copy-on-write Set |
| `ConcurrentHashMap` | `HashMap` / `Hashtable` | Segment-level locking (much faster than full sync) |
| `ConcurrentLinkedQueue` | `LinkedList` as Queue | Lock-free non-blocking queue |
| `ConcurrentLinkedDeque` | `LinkedList` as Deque | Lock-free non-blocking deque |

---

## Interview Quick Traps

| Trap | Answer |
|------|--------|
| How to make ArrayList thread-safe? | `Collections.synchronizedList(new ArrayList<>())` or use `CopyOnWriteArrayList`. |
| Is iteration safe with `Collections.synchronizedList()`? | No! Must manually wrap iteration in a `synchronized(list) { ... }` block. |
| Does `CopyOnWriteArrayList` lock on every operation? | No locking on reads! Only writes create a new internal array copy. |
| Difference between `Hashtable` and `ConcurrentHashMap`? | `Hashtable` locks the full object (slow). `ConcurrentHashMap` uses segment-level locking (fast). |
| Should `Vector` still be used in modern code? |  Generally not. Prefer `CopyOnWriteArrayList` or `Collections.synchronizedList()`. |

---

[Previous: LinkedList](./04-linkedlist.md) · [Back to Collections Index](./README.md) · [Next: Cursors](./06-cursors.md)
