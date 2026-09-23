# Java Collection Framework — Map

## 1. Map — Introduction

`Map` is **not a child interface of `Collection`**.

If we want to represent a group of objects in the form of **key-value pairs**, we should go for a `Map`.

### Basic Points

- A `Map` stores data in the form of **key-value pairs**.
- Both keys and values are objects (reference types).
- A key-value pair is called an **Entry**.
- **Duplicate keys are not allowed.**
- **Duplicate values are allowed.**
- Each key can be associated with only one value at a time.
- If an existing key is inserted again, its old value is replaced by the new value.

Example:

| Key | Value |
|---|---|
| 101 | aaa |
| 102 | bbb |
| 103 | ccc |
| 104 | ddd |

Here, each row represents one **Entry**.

> A `Map` can be viewed through its entries using `entrySet()`.

---

## 2. Map Hierarchy / Diagram

```text
                         Map (Interface)
                              |
        ---------------------------------------------------------
        |            |             |              |             |
     HashMap     LinkedHashMap  IdentityHashMap  WeakHashMap  SortedMap
        |                                         |              |
        |                                         |        NavigableMap
        |                                         |              |
        |                                         |           TreeMap
        |
        |
    Hashtable
       |
    Dictionary
   (legacy abstract class)

```

### Important Version Numbers

| Class / Interface | Introduced |
|---|---:|
| `Map` | Java 1.2 |
| `HashMap` | Java 1.2 |
| `LinkedHashMap` | Java 1.4 |
| `IdentityHashMap` | Java 1.4 |
| `WeakHashMap` | Java 1.2 |
| `SortedMap` | Java 1.2 |
| `NavigableMap` | Java 1.6 |
| `TreeMap` | Java 1.2 |
| `Hashtable` | Java 1.0 |
| `Dictionary` | Java 1.0 |

> **Note:** `Hashtable` is a legacy class. `Dictionary` is its older abstract superclass.

### More Accurate Relationship

```text
Map (Interface)
│
├── HashMap (Class) ─────────────── Java 1.2
│
├── LinkedHashMap (Class) ───────── Java 1.4
│
├── IdentityHashMap (Class) ─────── Java 1.4
│
├── WeakHashMap (Class) ─────────── Java 1.2
│
├── SortedMap (Interface) ───────── Java 1.2
│      │
│      └── NavigableMap (Interface) ─ Java 1.6
│             │
│             └── TreeMap (Class) ─── Java 1.2
│
└── Hashtable (Class) ───────────── Java 1.0
       │
       └── Dictionary (legacy abstract class)
```

---

# 3. Map Methods

## `put(Object key, Object value)`

```java
Object put(Object key, Object value);
```

Adds a key-value pair to the map.

### Important Behavior

If the key is **not already present**:

```text
new key → new value
return → null
```

If the key is **already present**:

```text
old value → replaced by new value
return → old value
```

### Example

```java
Map m = new HashMap();

System.out.println(m.put(101, "aaa"));  // null
System.out.println(m.put(102, "bbb"));  // null

System.out.println(m.put(101, "ccc"));  // aaa

System.out.println(m);
```

The final value for key `101` is `"ccc"`.

---

## `putAll(Map m)`

```java
void putAll(Map m);
```

Adds all key-value pairs of the specified map into the current map.

```java
Map m1 = new HashMap();
m1.put(101, "aaa");
m1.put(102, "bbb");

Map m2 = new HashMap();
m2.putAll(m1);
```

---

## `get(Object key)`

```java
Object get(Object key);
```

Returns the value associated with the specified key.

```java
System.out.println(m.get(101));
```

If the key does not exist, it returns `null` (subject to the map's null-value behavior).

---

## `remove(Object key)`

```java
Object remove(Object key);
```

Removes the entry associated with the specified key and returns the previous value.

```java
m.remove(101);
```

---

## `containsKey(Object key)`

```java
boolean containsKey(Object key);
```

Checks whether the specified key is present.

```java
m.containsKey(101);
```

---

## `containsValue(Object value)`

```java
boolean containsValue(Object value);
```

Checks whether the specified value is present.

```java
m.containsValue("aaa");
```

---

## `isEmpty()`

```java
boolean isEmpty();
```

Returns `true` if the map contains no mappings.

---

## `size()`

```java
int size();
```

Returns the number of key-value mappings.

---

## `clear()`

```java
void clear();
```

Removes all mappings from the map.

---

## `keySet()`

```java
Set keySet();
```

Returns a `Set` containing all keys.

```java
Set s = m.keySet();
System.out.println(s);
```

Since duplicate keys are not allowed, keys naturally form a `Set`.

---

## `values()`

```java
Collection values();
```

Returns a `Collection` containing all values.

```java
Collection c = m.values();
System.out.println(c);
```

Duplicate values are allowed, therefore the return type is `Collection`, not `Set`.

---

## `entrySet()`

```java
Set entrySet();
```

Returns a `Set` containing all entries of the map.

```java
Set s = m.entrySet();
System.out.println(s);
```

---

# 4. Map.Entry Interface

`Map.Entry` is a **nested interface inside the `Map` interface**.

```java
Map
 └── Entry
```

### What is an Entry?

A key-value pair is called an **Entry**.

```text
Key        Value
101   →    aaa
```

The complete pair:

```text
101 = aaa
```

is one `Map.Entry` object.

### Why is `Entry` inside `Map`?

A `Map` represents a collection of key-value mappings.

Without an existing map, there is normally no entry belonging to that map.

Therefore, the `Entry` interface is defined inside `Map`.

### Entry-specific methods

```java
Object getKey();
Object getValue();
Object setValue(Object value);
```

| Method | Purpose |
|---|---|
| `getKey()` | Returns the key of the entry |
| `getValue()` | Returns the value of the entry |
| `setValue(value)` | Replaces the value of the entry |

These methods are applied to an **Entry object**.

### Example

```java
Map.Entry entry = ...;

System.out.println(entry.getKey());
System.out.println(entry.getValue());

entry.setValue("newValue");
```

---

# 5. First Implementation of Map — HashMap

The first commonly introduced implementation class of `Map` is `HashMap`.

```java
HashMap m = new HashMap();
```

## HashMap — Important Properties

- Introduced in **Java 1.2**.
- Underlying data structure is based on a **hash table**.
- Insertion order is **not guaranteed/preserved**.
- Placement is based on the hash code of keys.
- Duplicate keys are not allowed.
- Duplicate values are allowed.
- Heterogeneous objects are allowed for keys and values.
- `null` key is allowed **only once**.
- `null` values can be stored multiple times.
- `HashMap` is **not synchronized**.
- `HashMap` implements `Serializable` and `Cloneable`.
- `HashMap` does **not** implement `RandomAccess`.
- It is suitable when frequent operations are **search/retrieval by key**.

> Modern Java implementations may use tree bins inside heavily-colliding buckets, but `HashMap` is traditionally described as being based on a hash table.

---

# 6. HashMap Constructors

## 1. Default Constructor

```java
HashMap m = new HashMap();
```

Creates an empty `HashMap`.

Traditional/default configuration:

```text
Initial Capacity = 16
Load Factor = 0.75
```

> The table itself is allocated lazily in modern Java; `16` is the default initial capacity used when the table is first initialized.

---

## 2. Initial Capacity Constructor

```java
HashMap m = new HashMap(int initialCapacity);
```

Example:

```java
HashMap m = new HashMap(20);
```

Creates a `HashMap` with the specified initial capacity.

---

## 3. Initial Capacity + Load Factor Constructor

```java
HashMap m = new HashMap(int initialCapacity, float loadFactor);
```

Example:

```java
HashMap m = new HashMap(20, 0.80f);
```

---

## 4. Map Constructor

```java
HashMap m = new HashMap(Map m);
```

Creates a new `HashMap` containing the mappings of the specified map.

Example:

```java
Map m1 = new HashMap();
m1.put(101, "aaa");
m1.put(102, "bbb");

Map m2 = new HashMap(m1);
```

---

# 7. HashMap Example

```java
import java.util.*;

class HashMapDemo {
    public static void main(String[] args) {

        HashMap m = new HashMap();

        m.put("chiranjeevi", 700);
        m.put("mahesh", 800);
        m.put("venkatesh", 900);
        m.put("nagarjuna", 500);

        System.out.println(m);
    }
}
```

Possible output order:

```text
{mahesh=800, nagarjuna=500, chiranjeevi=700, venkatesh=900}
```

> The order is only illustrative. `HashMap` does **not guarantee insertion order**.

---

# 8. Fetching Map Data Using `keySet()`

```java
Set s = m.keySet();

System.out.println(s);
```

Example output:

```text
[nagarjuna, venkatesh, mahesh, chiranjeevi]
```

---

# 9. Fetching Map Data Using `values()`

```java
Collection c = m.values();

System.out.println(c);
```

Example output:

```text
[500, 900, 800, 700]
```

---

# 10. Fetching Map Data Using `entrySet()`

```java
Set s1 = m.entrySet();

System.out.println(s1);
```

Example output:

```text
[nagarjuna=500, venkatesh=900, mahesh=800, chiranjeevi=700]
```

`entrySet()` is especially useful when we need **both key and value together**.

---

# 11. Traversing Map Using Iterator + Entry

```java
Iterator itr = m.entrySet().iterator();

while (itr.hasNext()) {

    Map.Entry entry = (Map.Entry) itr.next();

    System.out.println(
        entry.getKey() + "..." + entry.getValue()
    );
}
```

### Output

```text
nagarjuna...500
venkatesh...900
mahesh...800
chiranjeevi...700
```

### Updating a Value Through Entry

```java
Iterator itr = m.entrySet().iterator();

while (itr.hasNext()) {

    Map.Entry entry = (Map.Entry) itr.next();

    System.out.println(
        entry.getKey() + "..." + entry.getValue()
    );

    if (entry.getKey().equals("nagarjuna")) {
        entry.setValue(10000);
    }
}

System.out.println(m);
```

Possible output:

```text
{nagarjuna=10000, venkatesh=900, mahesh=800, chiranjeevi=700}
```

> `Map.Entry#setValue()` can be used to replace the value associated with that entry.

---

# 12. HashMap vs Hashtable

| Feature | HashMap | Hashtable |
|---|---|---|
| Introduced | Java 1.2 | Java 1.0 |
| Legacy | No | Yes |
| Synchronization | Not synchronized | Synchronized |
| Thread safety | Not thread-safe by default | Thread-safe through synchronization |
| Concurrent access | Multiple threads can operate without built-in synchronization | Methods are synchronized |
| Performance | Generally better in single-threaded/non-synchronized use | Generally lower due to synchronization overhead |
| `null` key | One `null` key allowed | Not allowed |
| `null` value | Multiple `null` values allowed | Not allowed |
| Duplicate keys | Not allowed | Not allowed |
| Duplicate values | Allowed | Allowed |
| Insertion order | Not guaranteed | Not guaranteed |
| Modern usage | Commonly preferred | Legacy; generally avoided in new code |

### Synchronization Concept

For `HashMap`:

```text
Thread 1 ─┐
Thread 2 ─┼──> HashMap
Thread 3 ─┘
```

There is no built-in synchronization.

For `Hashtable`, its methods are synchronized:

```text
Thread 1 ──> Hashtable
             ↑
Thread 2 ──> waits
             ↑
Thread 3 ──> waits
```

Only one thread can enter a synchronized method on the same object at a time.

> "Thread-safe" does not mean that every possible multi-step operation is automatically atomic; synchronization details still matter.

---

# 13. Getting a Synchronized Version of HashMap

By default:

```java
HashMap m = new HashMap();
```

is not synchronized.

A synchronized map wrapper can be obtained using:

```java
Map m1 = Collections.synchronizedMap(m);
```

Complete example:

```java
HashMap m = new HashMap();

m.put(101, "aaa");
m.put(102, "bbb");

Map m1 = Collections.synchronizedMap(m);
```

### Important

```text
HashMap
   ↓
Collections.synchronizedMap()
   ↓
Synchronized Map Wrapper
```

`Collections` is a utility class.

---

# 14. LinkedHashMap

`LinkedHashMap` is a **child class/subclass of `HashMap`**.

It provides almost the same methods and constructors as `HashMap`, but differs mainly in its internal organization and ordering behavior.

## Important Differences

| Feature | HashMap | LinkedHashMap |
|---|---|---|
| Parent relationship | — | Child class of `HashMap` |
| Underlying structure | Hash table | Hash table + linked list |
| Insertion order | Not guaranteed | Preserved |
| Introduced | Java 1.2 | Java 1.4 |

### Internal Structure

```text
HashMap
   ↓
Hash Table

LinkedHashMap
   ↓
Hash Table + Linked List
        ↓
     Hybrid structure
```

The linked structure maintains the predictable iteration order.

---

# 15. LinkedHashMap Example

```java
LinkedHashMap m = new LinkedHashMap();

m.put("A", 100);
m.put("B", 200);
m.put("C", 300);
m.put("D", 400);

System.out.println(m);
```

Output:

```text
{A=100, B=200, C=300, D=400}
```

The insertion order is preserved.

### Cache-Based Applications

`LinkedHashMap` is commonly useful for cache-related applications because it can maintain a predictable order.

It also supports **access-order** mode through an appropriate constructor, which is useful when implementing LRU-style caches.

---

# 16. `==` vs `equals()`

This concept is important before understanding `IdentityHashMap`.

Consider:

```java
Integer x = new Integer(10);
Integer y = new Integer(10);
```

`x` and `y` are two different objects.

### Using `==`

```java
System.out.println(x == y);
```

Output:

```text
false
```

Because `==` compares **object references** for objects.

### Using `equals()`

```java
System.out.println(x.equals(y));
```

Output:

```text
true
```

Because `Integer.equals()` compares the **integer value/content**.

```text
x ───────> Integer object (10)
y ───────> Integer object (10)

x == y       → false
x.equals(y)  → true
```

---

# 17. HashMap Uses `equals()` for Duplicate Keys

Example:

```java
HashMap m = new HashMap();

Integer x = new Integer(10);
Integer y = new Integer(10);

m.put(x, "A");
m.put(y, "B");

System.out.println(m);
```

`x` and `y` are different objects:

```java
x == y          // false
```

But:

```java
x.equals(y)     // true
```

`HashMap` uses the key's `hashCode()` and `equals()` semantics to identify logically duplicate keys.

Therefore, the second insertion replaces the value of the first mapping.

Result:

```text
{10=B}
```

> The exact printed formatting/order is implementation-dependent, but there is only **one mapping** for these two equal keys.

---

# 18. IdentityHashMap

`IdentityHashMap` is another implementation of the `Map` interface.

It is similar to `HashMap` in many operations, but has a major difference in how it identifies keys.

## Main Difference

### HashMap

Uses normal object equality semantics:

```java
equals()
```

along with hash codes.

### IdentityHashMap

Uses **reference identity**:

```java
==
```

to distinguish keys.

Therefore:

```text
HashMap
→ content/logical equality

IdentityHashMap
→ reference identity
```

---

# 19. IdentityHashMap Example

```java
IdentityHashMap m = new IdentityHashMap();

Integer x = new Integer(10);
Integer y = new Integer(10);

m.put(x, "A");
m.put(y, "B");

System.out.println(m);
```

Here:

```java
x == y
```

is:

```text
false
```

Although:

```java
x.equals(y)
```

is:

```text
true
```

`IdentityHashMap` treats them as different keys because they are different object references.

Therefore, two mappings can exist:

```text
{10=A, 10=B}
```

The printed order may vary.

---

# 20. HashMap vs IdentityHashMap

| Feature | HashMap | IdentityHashMap |
|---|---|---|
| Duplicate-key test | `hashCode()` + `equals()` semantics | Reference identity (`==`) |
| Equality concept | Logical/content equality | Object identity |
| `new Integer(10)` vs `new Integer(10)` | Same logical key | Different keys |
| `x == y` | `false` | Treated as different |
| `x.equals(y)` | `true` | Does not make them the same key |

### Example Summary

```java
Integer x = new Integer(10);
Integer y = new Integer(10);
```

```java
x == y
// false

x.equals(y)
// true
```

#### HashMap

```java
HashMap m = new HashMap();

m.put(x, "A");
m.put(y, "B");

System.out.println(m);
```

Conceptually:

```text
Only one mapping remains
10 → B
```

#### IdentityHashMap

```java
IdentityHashMap m = new IdentityHashMap();

m.put(x, "A");
m.put(y, "B");

System.out.println(m);
```

Conceptually:

```text
Two mappings remain
x → A
y → B
```

---

# 21. Key Revision Points

```text
Map
│
├── Stores key-value pairs
├── Duplicate keys not allowed
├── Duplicate values allowed
├── Key-value pair = Entry
├── Map does NOT extend Collection
└── Map.Entry is nested inside Map
```

```text
HashMap
│
├── Java 1.2
├── Not synchronized
├── Not thread-safe by default
├── One null key
├── Multiple null values
├── Insertion order not guaranteed
└── Uses hashCode() + equals() semantics
```

```text
LinkedHashMap
│
├── Java 1.4
├── Child of HashMap
├── Hash table + linked structure
└── Preserves insertion order
```

```text
IdentityHashMap
│
├── Uses reference identity
├── Uses == semantics for key identity
└── Two different but equals() objects can be separate keys
```

```text
Hashtable
│
├── Java 1.0
├── Legacy class
├── Synchronized
├── Thread-safe through synchronized methods
├── No null key
└── No null values
```

---

## Quick Comparison

| Property | HashMap | LinkedHashMap | IdentityHashMap | Hashtable |
|---|---|---|---|---|
| Version | 1.2 | 1.4 | 1.4 | 1.0 |
| Synchronized | No | No | No | Yes |
| Null key | One | One | Yes* | No |
| Null values | Multiple | Multiple | Yes* | No |
| Insertion order | No | Yes | No | No |
| Key identity | `equals()` + `hashCode()` | `equals()` + `hashCode()` | `==` | `equals()` + `hashCode()` |
| Legacy | No | No | No | Yes |

> `IdentityHashMap` has its own documented null/reference-identity behavior; the key point for this chapter is that key identity is based on `==`, not normal `equals()` semantics.
