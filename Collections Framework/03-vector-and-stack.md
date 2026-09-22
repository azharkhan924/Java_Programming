# 🛡️ Vector & Stack

> **Summary:** Vector constructors, capacity vs size, Vector-specific methods, ArrayList vs Vector comparison, Stack class (LIFO), push/pop/peek methods, aur Stack usage example.

---

## 1. Vector Kya Hai?

**Vector** ArrayList jaise hi ek **resizable array** hai, lekin ek major difference ke sath:

> 🔒 **Vector ke saare methods `synchronized` hain — yani Thread-Safe hai by default!**

```text
Vector Key Properties:
✅ Maintains insertion order
✅ Allows duplicate elements
✅ Allows null values
✅ Implements RandomAccess → Fast index-based access O(1)
✅ Synchronized (Thread-safe) — Every method locked!
⚠️ Legacy class (Java 1.0 se hai, ab rarely direct use hota hai)
```

---

## 2. Vector Constructors

### Constructor 1: Default — `Vector()`
```java
Vector<String> v = new Vector<>();
```
- **Initial Capacity:** 10
- **Capacity Increment:** Previous capacity double hoti hai (x2)

### Constructor 2: Custom Capacity — `Vector(int initialCapacity)`
```java
Vector<String> v = new Vector<>(50);
```
- Custom starting capacity, increment still 2x

### Constructor 3: Custom Capacity + Custom Increment — `Vector(int capacity, int incrementBy)`
```java
Vector<String> v = new Vector<>(50, 10);
```
- Starting capacity 50, har resize par sirf 10 extra space badhega (instead of doubling)

### Constructor 4: From Collection — `Vector(Collection c)`
```java
Vector<String> v = new Vector<>(existingList);
```

---

## 3. Capacity Growth: ArrayList vs Vector

| Feature | ArrayList | Vector |
|---------|-----------|--------|
| **Growth Formula** | `(old * 3/2) + 1` | `old * 2` (default), ya custom increment |
| **Default Initial Capacity** | 10 | 10 |
| **Custom Increment?** | ❌ No | ✅ Yes (3rd constructor) |

---

## 4. Capacity vs Size — Critical Difference

```java
Vector<Integer> v = new Vector<>(); // capacity = 10
v.add(1);
v.add(2);
v.add(3);

System.out.println(v.capacity()); // 10 → Total allocated space
System.out.println(v.size());     //  3 → Actual elements added
```

```text
Capacity: [1] [2] [3] [ ] [ ] [ ] [ ] [ ] [ ] [ ]
                        ↑___ Empty slots (capacity - size = 7)
Size = 3 elements stored
Capacity = 10 total slots available
```

> ⚠️ **Note:** ArrayList me `capacity()` method **nahi** hota. Ye sirf Vector ka feature hai.

---

## 5. ⚖️ ArrayList vs Vector (Top Interview Question!)

| Feature | ArrayList | Vector |
|---------|-----------|--------|
| **Thread Safety** | ❌ NOT synchronized | ✅ Synchronized (every method par lock) |
| **Performance** | Faster (no locking overhead) | Slower (synchronization cost) |
| **Capacity Growth** | `(old * 3/2) + 1` | `old * 2` (or custom increment) |
| **`capacity()` Method** | ❌ Not available | ✅ Available |
| **Legacy?** | No (Java 1.2+) | Yes (Java 1.0, re-engineered in 1.2) |
| **Iteration** | `Iterator` aur `ListIterator` | `Enumeration` bhi support karta hai (plus Iterator/ListIterator) |
| **When to use?** | Single-threaded apps, high-performance reads | Multi-threaded apps (lekin modern code me `CopyOnWriteArrayList` prefer hota hai) |

---

## 6. Vector-Specific Methods

Vector ke paas kuch extra methods hain jo ArrayList me nahi milte:

```java
Vector<String> v = new Vector<>();
v.add("A"); v.add("B"); v.add("C");

// ─── Adding ───
v.addElement("D");       // Legacy add method (same as add())

// ─── Removing ───
v.removeElement("B");    // Removes first occurrence of "B"
v.removeElementAt(0);    // Removes element at index 0
v.removeAllElements();   // Clears entire vector

// ─── Accessing ───
v.elementAt(0);          // Legacy version of get(0)
v.firstElement();        // First element (throws exception if empty)
v.lastElement();         // Last element (throws exception if empty)

// ─── Capacity ───
v.capacity();            // Current internal array capacity
v.trimToSize();          // Capacity ko current size tak shrink karo
v.ensureCapacity(100);   // Minimum 100 capacity ensure karo
```

---

## 7. Stack — LIFO Data Structure

**Stack** class **Vector ko extend** karta hai aur **LIFO (Last-In-First-Out)** behavior provide karta hai.

```text
Stack extends Vector

    ┌─────┐
    │  C  │ ← Top (Last added, first removed)
    ├─────┤
    │  B  │
    ├─────┤
    │  A  │ ← Bottom (First added, last removed)
    └─────┘
```

### Stack Constructor:
```java
Stack<String> stack = new Stack<>(); // Only one constructor — no-arg
```

---

## 8. Stack Methods (5 Core Operations)

```java
Stack<Integer> stack = new Stack<>();

// 1. push() — Element ko top par add karna
stack.push(10);  // [10]
stack.push(20);  // [10, 20]
stack.push(30);  // [10, 20, 30]

// 2. pop() — Top element remove + return karna
int top = stack.pop(); // Returns 30, Stack: [10, 20]

// 3. peek() — Top element dekhna WITHOUT removing
int current = stack.peek(); // Returns 20, Stack: [10, 20] (unchanged!)

// 4. empty() — Stack khali hai ya nahi?
boolean isEmpty = stack.empty(); // false

// 5. search(element) — 1-based position from top return karta hai
int pos = stack.search(10); // Returns 2 (10 is 2nd from top)
// Agar element nahi mila toh -1 return karta hai
```

### Stack LIFO Example:
```java
Stack<String> stack = new Stack<>();
stack.push("A");
stack.push("B");
stack.push("C");

while (!stack.empty()) {
    System.out.println(stack.pop());
}
// Output: C, B, A → Reverse order (LIFO!)
```

---

## 9. ⚠️ Modern Alternative to Stack

> Stack class **legacy** hai (Java 1.0). Modern code me **`Deque`** interface use karna recommended hai:

```java
// ✅ Modern approach
Deque<Integer> stack = new ArrayDeque<>();
stack.push(10);
stack.push(20);
stack.pop(); // 20
```

**Why ArrayDeque over Stack?**
- `Stack` inherits `Vector` ka synchronization overhead (even jab zaroorat nahi ho)
- `ArrayDeque` faster hai aur no unnecessary synchronization

---

## 🧠 Interview Quick Traps

| Trap | Answer |
|------|--------|
| Vector thread-safe hai lekin ArrayList nahi, toh hamesha Vector use karna chahiye? | ❌ Nahi! Synchronization se performance slow hoti hai. Single-threaded apps me hamesha ArrayList use karo. |
| Stack konsi class extend karta hai? | `java.util.Vector` |
| `peek()` aur `pop()` me farq? | `peek()` sirf dekhta hai (stack unchanged). `pop()` remove + return karta hai. |
| Empty stack par `pop()` call karein toh kya hoga? | `EmptyStackException` throw hogi! |
| Vector ka default capacity growth kya hai? | Double (2x) hota hai. |
| `stack.search()` 0-based return karta hai? | ❌ **1-based** position return karta hai from top! |

---

[⬅️ Previous: ArrayList](./02-arraylist.md) · [📖 Back to Collections Index](./README.md) · [Next → LinkedList ➡️](./04-linkedlist.md)
