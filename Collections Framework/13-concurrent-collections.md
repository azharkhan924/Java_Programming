# Concurrent Collections — Java Notes

## 1. ConcurrentMap → ConcurrentHashMap

```text
Map (Interface)
      ↓
ConcurrentMap (Interface)
      ↓
ConcurrentHashMap (Implementation Class)
```

- `ConcurrentMap` is an interface that extends `Map`.
- `ConcurrentHashMap` is an implementation class of `ConcurrentMap`.
- Important concurrent collections are present in `java.util.concurrent`.

---

# 2. ConcurrentHashMap — Important Methods

## `put()` vs `putIfAbsent()`

### Normal `put()`

```java
map.put(key, newValue);
```

- If the key is **not present**, a new entry is added.
- If the key is **already present**, the old value is replaced by the new value.
- `put()` returns the **previous/old value** associated with the key, or `null` if there was no mapping.

Example:

```java
ConcurrentHashMap<Integer, String> map = new ConcurrentHashMap<>();

map.put(101, "A");
String old = map.put(101, "B");

System.out.println(old);       // A
System.out.println(map.get(101)); // B
```

### `putIfAbsent()`

```java
map.putIfAbsent(key, value);
```

- If the key is **already present**, the new entry is not added/replaced.
- It returns the **existing value**.
- If the key is **absent**, the new entry is added and `null` is returned.
- The check-and-insert operation is atomic.

Example:

```java
map.put(101, "A");

String old = map.putIfAbsent(101, "B");

System.out.println(old);          // A
System.out.println(map.get(101)); // A
```

For an absent key:

```java
String old = map.putIfAbsent(102, "B");

System.out.println(old);          // null
System.out.println(map.get(102)); // B
```

### Easy Difference

| `put()` | `putIfAbsent()` |
|---|---|
| Key absent → insert | Key absent → insert |
| Key present → replace old value | Key present → do not replace |
| Returns old value | Returns old/existing value; `null` if absent |

---

# 3. `remove(key)` vs `remove(key, value)`

## `remove(key)`

```java
V remove(Object key)
```

Removes the entry associated with the specified key and returns the previous value.

```java
map.put(101, "A");

String result = map.remove(101);

System.out.println(result); // A
```

After removal:

```text
101 → removed
```

## `remove(key, value)`

```java
boolean remove(Object key, Object value)
```

Removes the entry **only if both the key and its current value match**.

Example:

```java
map.put(101, "A");

boolean result = map.remove(101, "B");

System.out.println(result); // false
```

Because the current value is `"A"`, not `"B"`.

```java
boolean result = map.remove(101, "A");

System.out.println(result); // true
```

Now the entry is removed.

### Easy Difference

```text
remove(key)
→ Remove if key exists.

remove(key, value)
→ Remove only if key exists AND current value matches.
```

---

# 4. `replace(key, value)`

```java
V replace(K key, V value)
```

Replaces the current value **only if the key is already present**.

```java
map.put(101, "A");

String old = map.replace(101, "B");

System.out.println(old);          // A
System.out.println(map.get(101)); // B
```

If key `102` is absent:

```java
map.replace(102, "B");
```

No new entry is created.

### Difference from `put()`

```text
put()
→ Key absent: insert
→ Key present: replace

replace()
→ Key absent: do nothing
→ Key present: replace
```

---

# 5. `replace(key, oldValue, newValue)`

```java
boolean replace(K key, V oldValue, V newValue)
```

The value is replaced only when the current value matches `oldValue`.

Example:

```java
map.put(101, "A");

boolean result = map.replace(101, "A", "B");

System.out.println(result);          // true
System.out.println(map.get(101));    // B
```

If the old value does not match:

```java
map.put(101, "A");

boolean result = map.replace(101, "X", "B");

System.out.println(result);          // false
System.out.println(map.get(101));    // A
```

### Easy Difference

```text
replace(key, value)
→ Replace if key exists.

replace(key, oldValue, newValue)
→ Replace only if key exists AND oldValue matches.
```

The check-and-replace operation is atomic.

---

# 6. ConcurrentHashMap — Internal Working

`ConcurrentHashMap` is designed for concurrent access.

### Important points

1. It is thread-safe.
2. Concurrent reads can proceed without requiring a lock in the normal retrieval path.
3. Updates use internal synchronization/atomic mechanisms.
4. It provides much more concurrency than a single-lock map such as `Hashtable` or a synchronized map wrapper.
5. `null` keys and `null` values are not allowed.
6. Its iterators do not throw `ConcurrentModificationException` merely because another thread modifies the map during iteration.

### Important Correction About "Bucket Lock / Segment Lock"

Older explanations often describe `ConcurrentHashMap` as using **16 segments/bucket locks**.

For modern Java, do **not** memorize:

> "There are exactly 16 buckets and exactly 16 locks."

Modern `ConcurrentHashMap` does not use the old Java 7 segmented-locking design. The `concurrencyLevel` constructor argument is retained mainly as a **sizing hint**, not as a fixed number of segments.

So the safe exam concept is:

```text
HashMap
→ Not thread-safe.

Hashtable / synchronizedMap
→ Thread-safe using synchronization around the map.

ConcurrentHashMap
→ Thread-safe with a highly concurrent internal implementation;
   reads generally do not require locking, while updates use
   internal synchronization/atomic mechanisms.
```

---

# 7. Concurrency Level

Older notes may say:

```text
Default concurrency level = 16
→ 16 threads can write simultaneously.
```

This is an **old simplified explanation**.

For modern Java, do not interpret `concurrencyLevel = 16` as:

> "Exactly 16 threads can perform updates."

It is a constructor sizing/concurrency hint. The actual implementation does not simply create 16 fixed segments.

Example constructor:

```java
ConcurrentHashMap(int initialCapacity,
                  float loadFactor,
                  int concurrencyLevel)
```

---

# 8. ConcurrentHashMap — Constructors

Common constructors include:

### 1. Default constructor

```java
ConcurrentHashMap()
```

Creates an empty map with default settings.

### 2. Initial capacity

```java
ConcurrentHashMap(int initialCapacity)
```

Creates a map with the specified initial capacity.

### 3. Initial capacity + load factor

```java
ConcurrentHashMap(int initialCapacity,
                  float loadFactor)
```

Specifies initial capacity and load factor.

### 4. Initial capacity + load factor + concurrency level

```java
ConcurrentHashMap(int initialCapacity,
                  float loadFactor,
                  int concurrencyLevel)
```

Specifies initial capacity, load factor, and concurrency-level hint.

### 5. Copy from another map

```java
ConcurrentHashMap(Map<? extends K, ? extends V> m)
```

Creates a map containing the same mappings as the specified map.

---

# 9. ConcurrentHashMap — ConcurrentModification Example

## Example 1: `ConcurrentHashMap`

```java
import java.util.concurrent.*;

class MyThread extends Thread {

    static ConcurrentHashMap<Integer, String> map =
            new ConcurrentHashMap<>();

    public void run() {
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
        }

        System.out.println("Child thread updating map");
        map.put(103, "C");
    }
}

public class Demo {

    public static void main(String[] args)
            throws InterruptedException {

        ConcurrentHashMap<Integer, String> map =
                MyThread.map;

        map.put(101, "A");
        map.put(102, "B");

        MyThread t = new MyThread();
        t.start();

        for (Integer key : map.keySet()) {
            System.out.println(
                "Main thread: " + key + " = " + map.get(key)
            );
            Thread.sleep(3000);
        }
    }
}
```

### Result

The child thread can update the map while the main thread is iterating.

`ConcurrentHashMap` does **not** throw `ConcurrentModificationException`.

However:

> There is **no guarantee** that an update made during iteration will be seen by that iterator.

If the iterator has already passed the relevant position, that newly added mapping may not be returned by that particular traversal.

So:

```text
Iterator → moves in forward direction

Thread-1 → iterating
Thread-2 → updating

Possible:
update is visible to iterator
OR
update is not visible to that iterator

No CME is thrown.
```

---

# 10. Same Example with HashMap

If the same program uses:

```java
HashMap<Integer, String>
```

instead of:

```java
ConcurrentHashMap<Integer, String>
```

and the map is structurally modified while an iterator is active, a `ConcurrentModificationException` can be thrown.

Example:

```java
import java.util.*;

public class Demo {

    public static void main(String[] args) {

        HashMap<Integer, String> map = new HashMap<>();

        map.put(101, "A");
        map.put(102, "B");
        map.put(103, "C");

        for (Integer key : map.keySet()) {

            if (key == 102) {
                map.put(104, "D");
            }

            System.out.println(key);
        }
    }
}
```

Possible result:

```text
ConcurrentModificationException
```

### Why?

The `HashMap` iterator is **fail-fast** on structural modification.

---

# 11. Fail-Fast vs Fail-Safe

## Fail-Fast Iterator

A fail-fast iterator detects structural modification outside the iterator's own supported modification mechanism and attempts to throw:

```text
ConcurrentModificationException
```

Example:

```text
HashMap
ArrayList
```

Important:

> Fail-fast behavior is best-effort. It should not be relied upon for program correctness.

## "Fail-Safe" — Important Terminology

"Fail-safe" is a commonly used teaching term, but it is **not the official Java Collections API category**.

For concurrent collections, it is more accurate to describe the iterator by its actual behavior.

For example:

### `ConcurrentHashMap`

Its iterators are **weakly consistent**:

- They do not throw `ConcurrentModificationException`.
- They may reflect some, all, or none of the modifications made after iteration begins.
- They do not freeze the entire map for iteration.

### `CopyOnWriteArrayList`

Its iterator uses the **snapshot of the array that existed when the iterator was created**.

Therefore modifications made after iterator creation are not reflected in that iterator.

---

# 12. HashMap vs Hashtable vs SynchronizedMap vs ConcurrentHashMap

| Feature | HashMap | Synchronized Map | Hashtable | ConcurrentHashMap |
|---|---|---|---|---|
| Thread-safe | No | Yes | Yes | Yes |
| Synchronization | None | Synchronized wrapper | Synchronized methods | Concurrent internal mechanisms |
| Read operation | No lock | Requires synchronization on map for wrapper operations | Synchronized | Generally non-blocking reads |
| Update operation | No synchronization | Map-level synchronization | Map-level synchronization | Concurrent internal synchronization |
| Concurrent iteration + update | Can result in CME | Iterator is fail-fast; external synchronization needed during iteration | Iterator is fail-fast | No CME; weakly consistent iterator |
| Null key | One allowed | Depends on wrapped map | No | No |
| Null values | Allowed | Depends on wrapped map | No | No |
| Introduced | Java 1.2 | Java 1.2 | Java 1.0 | Java 1.5 |
| Suitable for high concurrency | No | Limited | Limited | Yes |

### Synchronized Map

Created using:

```java
Map<K,V> map =
    Collections.synchronizedMap(new HashMap<>());
```

Thread safety is obtained through synchronization around the map.

For iteration, the documentation requires external synchronization on the returned map:

```java
synchronized (map) {
    for (K key : map.keySet()) {
        // iteration
    }
}
```

---

# 13. Three-Way Difference: ConcurrentHashMap vs SynchronizedMap vs Hashtable

### 1. Locking / Concurrency

```text
ConcurrentHashMap
→ Thread-safe without one single global lock for every operation.
→ Designed for high concurrency.

SynchronizedMap
→ Operations are synchronized on the wrapper/map.

Hashtable
→ Legacy synchronized map; its methods are synchronized.
```

### 2. Multiple Threads

```text
ConcurrentHashMap
→ Multiple threads can concurrently access the map.

SynchronizedMap
→ Concurrent operations are serialized through synchronization.

Hashtable
→ Concurrent operations are serialized through synchronization.
```

### 3. Read Operations

```text
ConcurrentHashMap
→ Retrievals generally do not require locking.

SynchronizedMap
→ Access through the synchronized wrapper is synchronized.

Hashtable
→ Access through synchronized methods is synchronized.
```

### 4. Iteration

```text
ConcurrentHashMap
→ Weakly consistent iterator.
→ No CME merely because another thread modifies the map.

SynchronizedMap
→ Iterator is fail-fast.
→ Synchronize on the map during iteration.

Hashtable
→ Iterator is fail-fast.
→ Enumeration is legacy and has different behavior.
```

### 5. Null

```text
ConcurrentHashMap
→ null key No
→ null value No

SynchronizedMap
→ Depends on the underlying map.
→ If backed by HashMap, null is allowed.

Hashtable
→ null key No
→ null value No
```

### 6. Version

```text
ConcurrentHashMap → Java 1.5
SynchronizedMap   → Java 1.2
Hashtable         → Java 1.0
```

### 7. Use Case

```text
ConcurrentHashMap
→ Highly concurrent applications.

SynchronizedMap
→ Existing map requiring synchronized access.

Hashtable
→ Legacy code; generally prefer modern alternatives.
```

---

# CopyOnWriteArrayList

## 14. Introduction

Hierarchy:

```text
List (Interface)
      ↓
CopyOnWriteArrayList (Class)
```

`CopyOnWriteArrayList` is a **thread-safe concurrent implementation of List**.

Package:

```java
java.util.concurrent
```

Introduced in:

```text
Java 1.5
```

---

# 15. How CopyOnWriteArrayList Works

The main idea:

> On every mutative operation, a fresh copy of the underlying array is created, the modification is performed on that new array, and then the new array becomes the current array.

Example:

```text
Original array
[A, B, C]

        ↓ add(D)

New copy
[A, B, C, D]

        ↓

New array becomes current
```

A reader can continue using the old array while the writer prepares the new array.

### Important Correction

Do **not** write:

> "JVM later synchronizes both copies."

That is not the correct mechanism.

Instead write:

> "The modified copy is published as the current internal array after the write operation."

Also, it is not accurate to say that every operation creates a copy.

```text
Read operation → no copy
Write/mutative operation → new array copy
```

Therefore, if there are 1000 write operations, the implementation may perform 1000 array-copying operations, which can be expensive.

---

# 16. Why CopyOnWriteArrayList is Expensive for Writes

Example:

```text
Initial:
[A, B, C]

add(D)
→ copy → [A, B, C, D]

add(E)
→ copy → [A, B, C, D, E]

remove(B)
→ copy → [A, C, D, E]
```

Therefore:

```text
Many reads + very few writes
        ↓
CopyOnWriteArrayList is useful

Many writes
        ↓
CopyOnWriteArrayList may be inefficient
```

It is especially useful when the list is **read much more frequently than it is modified**.

---

# 17. Concurrent Modification Example — CopyOnWriteArrayList

```java
import java.util.concurrent.*;

public class Demo {

    public static void main(String[] args) {

        CopyOnWriteArrayList<Integer> list =
                new CopyOnWriteArrayList<>();

        list.add(10);
        list.add(20);
        list.add(30);

        for (Integer i : list) {

            if (i == 20) {
                list.add(40);
            }

            System.out.println(i);
        }

        System.out.println(list);
    }
}
```

Output can be:

```text
10
20
30

[10, 20, 30, 40]
```

No:

```text
ConcurrentModificationException
```

### Why?

The iterator works on the **snapshot/array that existed when the iterator was created**.

The `add(40)` operation creates/publishes a new array.

The existing iterator continues using its old snapshot.

Therefore the newly added element is not returned by that existing iterator.

---

# 18. Same Program with ArrayList

If:

```java
CopyOnWriteArrayList<Integer>
```

is replaced by:

```java
ArrayList<Integer>
```

then:

```java
for (Integer i : list) {

    if (i == 20) {
        list.add(40);
    }
}
```

can throw:

```text
ConcurrentModificationException
```

### Reason

`ArrayList` iterator is fail-fast and detects structural modification outside the iterator.

---

# 19. Why CopyOnWriteArrayList Does Not Throw CME

```text
Iterator created
       ↓
Snapshot/reference to current array
       ↓
Another thread performs write
       ↓
New array is created
       ↓
Existing iterator continues over old snapshot
       ↓
No CME
```

Therefore:

```text
CopyOnWriteArrayList
→ Reader can continue while another thread writes.
→ Existing iterator sees its snapshot.
→ No CME.
```

---

# 20. Iterator Remove Operation

## ArrayList

`ArrayList` iterator supports:

```java
iterator.remove();
```

Example:

```java
Iterator<Integer> itr = list.iterator();

while (itr.hasNext()) {
    Integer value = itr.next();

    if (value == 20) {
        itr.remove();
    }
}
```

This is the iterator's supported removal mechanism.

## CopyOnWriteArrayList

Its iterator does **not** support modification operations.

```java
itr.remove();
```

throws:

```text
UnsupportedOperationException
```

Reason:

> The iterator is operating on a snapshot and is not designed to modify the underlying list through the iterator.

---

# 21. Similarities: ArrayList vs CopyOnWriteArrayList

Both:

1. Preserve insertion order.
2. Allow duplicate elements.
3. Allow heterogeneous objects if the generic type is broad enough, e.g. `Object`.
4. Implement `Serializable`.
5. Implement `RandomAccess`.
6. Implement `List`.
7. Support index-based access.

`ArrayList` is also `Cloneable`; `CopyOnWriteArrayList` provides cloning support as well.

---

# 22. ArrayList vs CopyOnWriteArrayList

| ArrayList | CopyOnWriteArrayList |
|---|---|
| Not thread-safe | Thread-safe |
| `java.util` | `java.util.concurrent` |
| Java 1.2 | Java 1.5 |
| Iterator is fail-fast | Iterator is snapshot-based / weakly consistent in the practical sense of COW iteration |
| Concurrent structural modification can cause CME | Concurrent modification does not cause CME |
| Iterator supports `remove()` | Iterator does not support `remove()` |
| Read/write on same underlying array | Writes create a new array |
| Good general-purpose list | Useful for read-heavy, write-rare scenarios |
| Frequent writes are relatively cheaper | Frequent writes can be expensive |

### Null

Both can contain `null` values.

---

# 23. CopyOnWriteArrayList Constructors

Common constructors:

### 1. Default

```java
CopyOnWriteArrayList()
```

Creates an empty list.

### 2. Collection

```java
CopyOnWriteArrayList(Collection<? extends E> c)
```

Creates a list containing the elements of the specified collection.

Example:

```java
ArrayList<Integer> list = new ArrayList<>();

list.add(10);
list.add(20);

CopyOnWriteArrayList<Integer> cowList =
        new CopyOnWriteArrayList<>(list);
```

### 3. Array

```java
CopyOnWriteArrayList(E[] toCopyIn)
```

Creates a list containing the elements of the specified array.

Example:

```java
Integer[] arr = {10, 20, 30};

CopyOnWriteArrayList<Integer> list =
        new CopyOnWriteArrayList<>(arr);
```

---

# 24. `addIfAbsent()`

Important method:

```java
boolean addIfAbsent(E e)
```

It adds the element **only if the element is not already present**.

Example:

```java
CopyOnWriteArrayList<Integer> list =
        new CopyOnWriteArrayList<>();

list.add(10);

System.out.println(list.addIfAbsent(20)); // true
System.out.println(list.addIfAbsent(10)); // false

System.out.println(list);
```

Output:

```text
true
false
[10, 20]
```

### Difference from `add()`

```text
add(10)
→ always attempts to add 10
→ duplicates are allowed

addIfAbsent(10)
→ adds only when 10 is absent
```

---

# 25. `addAllAbsent(Collection c)`

```java
int addAllAbsent(Collection<? extends E> c)
```

Adds only those elements from the supplied collection that are **not already present** in the list.

It returns the **number of elements actually added**.

Example:

```java
CopyOnWriteArrayList<Integer> list =
        new CopyOnWriteArrayList<>();

list.add(10);
list.add(20);

List<Integer> newList =
        Arrays.asList(20, 30, 40);

int count = list.addAllAbsent(newList);

System.out.println(count);
System.out.println(list);
```

Output:

```text
2
[10, 20, 30, 40]
```

Because:

```text
20 → already present → not added
30 → absent → added
40 → absent → added
```

### Difference

```text
addAll(c)
→ adds all elements; duplicates may be added.

addAllAbsent(c)
→ adds only elements that are not already present.
→ returns number of elements actually added.
```

---

# 26. Three-Way Difference: CopyOnWriteArrayList vs SynchronizedList vs Vector

## 1. Synchronization

```text
CopyOnWriteArrayList
→ Thread-safe using copy-on-write mechanism.

SynchronizedList
→ Thread-safe through synchronization.

Vector
→ Thread-safe through synchronized methods.
```

## 2. Read/Write Concurrency

```text
CopyOnWriteArrayList
→ Readers can continue while another thread performs a write.
→ Every mutative operation creates a new array.

SynchronizedList
→ Operations through the wrapper are synchronized.

Vector
→ Methods are synchronized.
```

## 3. Iteration

```text
CopyOnWriteArrayList
→ Iterator works on a snapshot.
→ No CME due to concurrent modification.
→ Iterator does not support remove().

SynchronizedList
→ Iterator is fail-fast.
→ External synchronization is required during iteration.

Vector
→ Iterator is fail-fast.
→ Enumeration is legacy and behaves differently from Iterator.
```

## 4. Performance / Use Case

```text
CopyOnWriteArrayList
→ Excellent when reads greatly outnumber writes.
→ Writes are expensive.

SynchronizedList
→ Suitable when synchronized access to an existing list is needed.

Vector
→ Legacy class; generally prefer modern collection alternatives.
```

## 5. Null / Duplicates / Order

All three can generally:

```text
→ Preserve insertion order
→ Allow duplicates
→ Allow null elements
```

---

# 27. Quick Revision Table

| Feature | HashMap | Hashtable | SynchronizedMap | ConcurrentHashMap |
|---|---|---|---|---|
| Thread-safe | No | Yes | Yes | Yes |
| Null key | 1 | No | Depends on backing map | No |
| Null value | Yes | No | Depends on backing map | No |
| Iterator | Fail-fast | Fail-fast | Fail-fast | Weakly consistent |
| Concurrent update during iteration | May cause CME | Iterator may cause CME | Iterator may cause CME | No CME |
| Concurrency | Low | Limited | Limited | High |
| Version | 1.2 | 1.0 | 1.2 | 1.5 |

---

# 28. Quick Revision — List Collections

| Feature | ArrayList | Vector | SynchronizedList | CopyOnWriteArrayList |
|---|---|---|---|---|
| Thread-safe | No | Yes | Yes | Yes |
| Package | `java.util` | `java.util` | `java.util` | `java.util.concurrent` |
| Version | 1.2 | 1.0 | 1.2 | 1.5 |
| Iterator | Fail-fast | Fail-fast | Fail-fast | Snapshot-based |
| Concurrent read/write | Unsafe | Synchronized | Synchronized | Supported |
| Iterator `remove()` | Yes | Yes | Yes through iterator with required synchronization | No |
| Write mechanism | Direct | Synchronized | Synchronized | Copy-on-write |
| Best suited for | General use | Legacy code | Synchronized list wrapper | Read-heavy, write-rare use cases |

---

# 29. Most Important Exam Points

```text
ConcurrentMap
→ Interface extending Map.

ConcurrentHashMap
→ Implementation of ConcurrentMap.

put()
→ Insert or replace.
→ Returns old value.

putIfAbsent()
→ Insert only if key is absent.
→ Returns existing value if key already exists.

remove(key)
→ Removes by key.
→ Returns old value.

remove(key, value)
→ Removes only when key + value match.
→ Returns boolean.

replace(key, value)
→ Replaces only when key exists.

replace(key, oldValue, newValue)
→ Replaces only when oldValue matches.
→ Returns boolean.

ConcurrentHashMap
→ Thread-safe.
→ null key/value not allowed.
→ Iterators do not throw CME because of concurrent updates.
→ Iterators are weakly consistent.
→ Designed for highly concurrent applications.

CopyOnWriteArrayList
→ Thread-safe version of List using copy-on-write.
→ Every mutative operation creates a new underlying array.
→ Excellent for read-heavy / write-rare use cases.
→ Iterator uses a snapshot.
→ Iterator does not support remove().
→ Concurrent modification does not cause CME.
→ null values are allowed.
```

## One-line memory trick

```text
HashMap
→ Fast but NOT thread-safe.

Hashtable / synchronizedMap
→ Thread-safe but synchronization can limit concurrency.

ConcurrentHashMap
→ Thread-safe + designed for high concurrency.

ArrayList
→ Normal List.

CopyOnWriteArrayList
→ Thread-safe List + snapshot iteration + expensive writes.
```
