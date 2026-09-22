# 🔗 LinkedList — Deep Dive

> **Summary:** LinkedList internal structure (Doubly Linked List), constructors, List + Deque/Queue operations, special first/last methods, aur ArrayList vs LinkedList comparison.

---

## 1. LinkedList Kya Hai?

**LinkedList** ek **Doubly Linked List** data structure hai jahan har element (node) apne **previous aur next node ka reference** maintain karta hai.

```text
LinkedList Internal Structure:
null ← [prev | A | next] ⟷ [prev | B | next] ⟷ [prev | C | next] → null
          ↑ head                                         ↑ tail
```

### Key Properties:
```text
✅ Maintains insertion order
✅ Allows duplicate elements
✅ Allows null values (multiple nulls bhi)
✅ Implements List + Deque interfaces simultaneously
❌ Does NOT implement RandomAccess (index access is O(n), not O(1)!)
❌ NOT synchronized (Not thread-safe)
```

### Class Declaration:
```java
public class LinkedList<E> extends AbstractSequentialList<E>
        implements List<E>, Deque<E>, Cloneable, Serializable
```

---

## 2. LinkedList Constructors

### Constructor 1: Default — `LinkedList()`
```java
LinkedList<String> list = new LinkedList<>();
```
- Koi initial capacity nahi hoti (ArrayList jaise 10 wala concept nahi hai)
- Nodes dynamically create hote hain jab elements add hote hain

### Constructor 2: From Collection — `LinkedList(Collection c)`
```java
LinkedList<String> list = new LinkedList<>(existingArrayList);
```

---

## 3. Why No `RandomAccess`?

ArrayList me elements contiguous memory me store hote hain → `get(5)` directly 5th slot access kar leta hai → **O(1)**.

LinkedList me koi contiguous memory nahi — elements scattered nodes me hain. `get(5)` ke liye head se traverse karke 5th node tak pohchna padta hai → **O(n)**.

```text
ArrayList:  [A][B][C][D][E] → get(3) = Direct jump to D ✅ O(1)
LinkedList: A → B → C → D → E → get(3) = A se start hokar 3 hops ❌ O(n)
```

Isliye LinkedList `RandomAccess` marker interface implement **nahi** karta!

---

## 4. LinkedList — Best & Worst Use Cases

### ✅ Best Choice (Use LinkedList When):
- **Frequent insertion/deletion at beginning or middle** — Koi shifting nahi hoti, sirf pointers (prev/next references) update hote hain → O(1) for first/last
- **Stack ya Queue behavior** chahiye — LinkedList `Deque` implement karta hai
- **Elements ka order baar-baar change** ho raha ho (insert, remove, reorder)

### ❌ Worst Choice (Avoid LinkedList When):
- **Frequent random access by index** (`get(i)`) — O(n) traversal lagegi
- **Memory-sensitive application** — Har node me extra 2 pointers (prev + next) ka overhead hota hai

---

## 5. LinkedList — Special First/Last Methods

LinkedList ke paas `Deque` interface se kuch special methods aate hain jo ArrayList me nahi milte:

```java
LinkedList<String> list = new LinkedList<>();
list.add("A"); list.add("B"); list.add("C");

// ─── Adding at ends ───
list.addFirst("Z");    // [Z, A, B, C] → Beginning me add
list.addLast("D");     // [Z, A, B, C, D] → End me add
list.offerFirst("Y");  // [Y, Z, A, B, C, D] → Same as addFirst but returns boolean
list.offerLast("E");   // [Y, Z, A, B, C, D, E]

// ─── Accessing (without removing) ───
list.getFirst();       // "Y" (throws NoSuchElementException if empty)
list.getLast();        // "E" (throws NoSuchElementException if empty)
list.peekFirst();      // "Y" (returns null if empty — safer!)
list.peekLast();       // "E" (returns null if empty — safer!)

// ─── Removing from ends ───
list.removeFirst();    // Removes "Y" (throws if empty)
list.removeLast();     // Removes "E" (throws if empty)
list.pollFirst();      // Removes first (returns null if empty — safer!)
list.pollLast();       // Removes last (returns null if empty — safer!)
```

---

## 6. LinkedList as Queue (FIFO) & Stack (LIFO)

```java
// ─── As Queue (FIFO: First In First Out) ───
LinkedList<String> queue = new LinkedList<>();
queue.offer("Task1");    // Add at rear
queue.offer("Task2");
queue.offer("Task3");
queue.poll();            // Remove from front → "Task1"

// ─── As Stack (LIFO: Last In First Out) ───
LinkedList<String> stack = new LinkedList<>();
stack.push("A");         // Internally addFirst()
stack.push("B");
stack.push("C");
stack.pop();             // Internally removeFirst() → "C"
```

---

## 7. ⚖️ ArrayList vs LinkedList (The Big Interview Table!)

| Feature | ArrayList | LinkedList |
|---------|-----------|------------|
| **Internal Structure** | Resizable Array (`Object[]`) | Doubly Linked List (Nodes with prev/next) |
| **`RandomAccess`** | ✅ Implements | ❌ Does not implement |
| **`get(index)` Time** | **O(1)** ✅ Direct jump | **O(n)** ❌ Node traversal |
| **`add(end)` Time** | O(1) amortized | O(1) |
| **`add(0, elem)` — Insert at beginning** | **O(n)** ❌ Shift all elements | **O(1)** ✅ Update head pointers |
| **`remove(0)` — Remove from beginning** | **O(n)** ❌ Shift all elements | **O(1)** ✅ Update head pointers |
| **`add(mid, elem)` — Insert at middle** | O(n) shift | O(n) traversal to find + O(1) insert |
| **Memory Overhead per Element** | Low (just the element reference) | High (element + prev pointer + next pointer) |
| **Implements Deque?** | ❌ No | ✅ Yes (Stack + Queue operations) |
| **Thread Safety** | ❌ Not synchronized | ❌ Not synchronized |
| **Best For** | Random access, bulk reads | Frequent insert/delete at ends, Queue/Stack behavior |

---

## 8. Important LinkedList Methods — Quick Table

| Method | Description | Time |
|--------|-------------|------|
| `add(E e)` | End me element add karta hai | O(1) |
| `add(int i, E e)` | Index `i` par insert karta hai | O(n) |
| `get(int i)` | Index `i` ka element return karta hai | O(n) |
| `remove(int i)` | Index `i` ka element remove karta hai | O(n) |
| `addFirst(E e)` | Beginning me add | O(1) |
| `addLast(E e)` | End me add | O(1) |
| `removeFirst()` | First element remove | O(1) |
| `removeLast()` | Last element remove | O(1) |
| `peekFirst()` / `peekLast()` | First/Last dekhna (null if empty) | O(1) |
| `pollFirst()` / `pollLast()` | Remove first/last (null if empty) | O(1) |
| `push(E e)` / `pop()` | Stack operations (LIFO) | O(1) |
| `offer(E e)` / `poll()` | Queue operations (FIFO) | O(1) |

---

## 🧠 Interview Quick Traps

| Trap | Answer |
|------|--------|
| LinkedList me `RandomAccess` implement hota hai? | ❌ **Nahi!** Index-based access O(n) hai, O(1) nahi. |
| LinkedList singly linked list hai? | ❌ **Doubly Linked List** hai — prev aur next dono pointers hain. |
| ArrayList vs LinkedList me insertion ke liye konsa better hai? | **Beginning/End par frequent insertion** → LinkedList better. **End par occasional add + frequent reads** → ArrayList better. |
| LinkedList me koi initial capacity concept hai? | ❌ Nahi! Nodes on-demand dynamically create hote hain. |
| LinkedList `List` ke sath aur kaunsa major interface implement karta hai? | **`Deque`** (Double-Ended Queue) interface. |

---

[⬅️ Previous: Vector & Stack](./03-vector-and-stack.md) · [📖 Back to Collections Index](./README.md) · [Next → Synchronized Collections ➡️](./05-synchronized-collections.md)
