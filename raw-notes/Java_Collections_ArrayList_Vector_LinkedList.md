# Java Collections Framework — ArrayList vs Vector, Synchronized Collections & LinkedList

## 1. ArrayList — Synchronization

Every method of `ArrayList` is **non-synchronized**.

Therefore:

- Multiple threads can operate on an `ArrayList` object at the same time.
- `ArrayList` is **not thread-safe** by default.
- Threads do not have to wait for a lock on the `ArrayList`.
- Therefore, compared with a synchronized collection, it generally provides better performance in single-threaded/non-concurrent use.

### Version

`ArrayList` was introduced in **Java 1.2** and is considered a **non-legacy collection class**.

---

# 2. Vector

`Vector` methods are **synchronized**.

Therefore:

- At a time, access to a Vector's synchronized methods is mutually exclusive.
- `Vector` is considered **thread-safe** for operations protected by its synchronization.
- Threads may have to wait for the object's monitor lock.
- Synchronization introduces overhead, so performance can be lower than an unsynchronized `ArrayList` in comparable use cases.

### Version

`Vector` was introduced in **Java 1.0** and is considered a **legacy collection class**.

---

# 3. ArrayList vs Vector

| Feature | ArrayList | Vector |
|---|---|---|
| Introduced | Java 1.2 | Java 1.0 |
| Legacy class | No | Yes |
| Synchronization | Non-synchronized | Synchronized methods |
| Thread safety | Not thread-safe by default | Synchronized operations are thread-safe |
| Performance | Generally faster in non-concurrent use | Generally slower due to synchronization overhead |
| RandomAccess | Yes | Yes |
| Serializable | Yes | Yes |
| Cloneable | Yes | Yes |

> **Important:** Thread safety depends on how the object is used. Synchronizing individual methods does not automatically make a multi-step sequence of operations atomic.

---

# 4. How to Get a Synchronized Version of ArrayList

By default:

```java
ArrayList
```

is non-synchronized.

We can obtain a synchronized List view by using:

```java
Collections.synchronizedList(List<T> list)
```

### Method Signature

```java
public static <T> List<T> synchronizedList(List<T> list)
```

### Example

```java
import java.util.*;

class Demo {
    public static void main(String[] args) {

        ArrayList<Integer> l1 = new ArrayList<>();

        List<Integer> l2 =
            Collections.synchronizedList(l1);
    }
}
```

Here:

```text
l1 → non-synchronized ArrayList
l2 → synchronized List view of l1
```

### Important

If iterating over a synchronized collection from multiple threads, external synchronization is still required during iteration:

```java
synchronized (l2) {
    Iterator<Integer> itr = l2.iterator();

    while (itr.hasNext()) {
        System.out.println(itr.next());
    }
}
```

---

# 5. Synchronized Set

We can obtain a synchronized Set using:

```java
Collections.synchronizedSet(Set<T> s)
```

### Method Signature

```java
public static <T> Set<T> synchronizedSet(Set<T> s)
```

### Example

```java
Set<Integer> s1 = new HashSet<>();

Set<Integer> s2 =
    Collections.synchronizedSet(s1);
```

---

# 6. Synchronized Map

We can obtain a synchronized Map using:

```java
Collections.synchronizedMap(Map<K,V> m)
```

### Method Signature

```java
public static <K,V> Map<K,V> synchronizedMap(Map<K,V> m)
```

### Example

```java
Map<Integer, String> m1 = new HashMap<>();

Map<Integer, String> m2 =
    Collections.synchronizedMap(m1);
```

---

# 7. LinkedList

The underlying data structure of `LinkedList` is a:

**Doubly Linked List**

Each node conceptually contains:

```text
Previous | Data | Next
```

Example:

```text
null ← [A] ⇄ [B] ⇄ [C] → null
```

---

## Properties of LinkedList

- Insertion order is preserved.
- Duplicates are allowed.
- `null` insertion is possible.
- Heterogeneous objects are possible when generics are not restricted.
- Implements `Serializable`.
- Implements `Cloneable`.
- Does **not** implement `RandomAccess`.

---

# 8. Why LinkedList Does Not Implement RandomAccess

`LinkedList` does not provide efficient direct index-based access.

For example:

```java
list.get(500);
```

The implementation has to traverse the linked list to reach the required node.

Therefore:

- Frequent retrieval → `LinkedList` is generally a poor choice.
- Frequent insertion/deletion at appropriate positions → `LinkedList` can be useful.

> The exact cost of insertion/deletion depends on whether the node/iterator position is already known. Finding an index itself can still require traversal.

---

# 9. LinkedList — Best and Worst Choice

### Best choice

If the frequent operation is **insertion or deletion**, especially when the required position/node can be reached efficiently, `LinkedList` can be a suitable choice.

### Worst choice

If the frequent operation is **retrieval by index**, `LinkedList` is generally a poor choice.

Example:

```java
list.get(index);
```

This requires traversal rather than direct array-style random access.

---

# 10. LinkedList Special Methods

`LinkedList` provides methods for operations at both ends.

### `addFirst()`

```java
public void addFirst(E e)
```

Adds an element at the beginning.

### `addLast()`

```java
public void addLast(E e)
```

Adds an element at the end.

### `getFirst()`

```java
public E getFirst()
```

Returns the first element.

### `getLast()`

```java
public E getLast()
```

Returns the last element.

### `removeFirst()`

```java
public E removeFirst()
```

Removes and returns the first element.

### `removeLast()`

```java
public E removeLast()
```

Removes and returns the last element.

---

# Quick Revision

| Feature | ArrayList | Vector | LinkedList |
|---|---|---|---|
| Underlying structure | Resizable array | Resizable array | Doubly linked list |
| Duplicates | Yes | Yes | Yes |
| Insertion order | Preserved | Preserved | Preserved |
| `null` | Allowed | Allowed | Allowed |
| Synchronized | No | Yes | No |
| Thread-safe by default | No | Yes for synchronized operations | No |
| RandomAccess | Yes | Yes | No |
| Serializable | Yes | Yes | Yes |
| Cloneable | Yes | Yes | Yes |
| Retrieval by index | Fast | Fast | Relatively slow |
| Middle insertion/deletion | Relatively slow | Relatively slow | Can be efficient when position/node is known |
| Introduced | Java 1.2 | Java 1.0 | Java 1.2 |

---

# One-Line Revision

```text
ArrayList → Best for retrieval/random access
Vector    → Synchronized/legacy version of dynamic array
LinkedList → Doubly linked list; useful for insertion/deletion when position is efficiently reached
```
