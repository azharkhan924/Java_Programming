# Java Collections Framework — Collection, List & ArrayList

## 1. Collection Interface

If we want to represent a group of individual objects as a single entity, we should go for a **Collection**.

- `Collection` is the root interface of the Collection Framework (more precisely, the root interface for the collection hierarchy).
- It defines common methods applicable to Collection objects.
- The `Collection` interface does **not** provide index-based retrieval methods such as `get()`.
- There is no concrete class that directly implements `Collection`; interfaces such as `List`, `Set`, and `Queue` extend it.

### Common Collection Methods

```java
boolean add(E e);
boolean addAll(Collection<? extends E> c);

boolean remove(Object o);
boolean removeAll(Collection<?> c);
boolean retainAll(Collection<?> c);

void clear();

boolean contains(Object o);
boolean containsAll(Collection<?> c);

boolean isEmpty();
int size();

Object[] toArray();
<T> T[] toArray(T[] a);

Iterator<E> iterator();
```

> **Note:** The method sometimes written as `returnAll` in rough notes is actually **`retainAll()`**.

### Important Point

The `Collection` interface does not contain a method such as:

```java
E get(int index);
```

Index-based retrieval is provided by the `List` interface.

---

# 2. List Interface

`List` is a child interface of `Collection`.

If we want to represent a group of individual objects as a single entity where:

- **Duplicates are allowed**, and
- **Insertion order is preserved**

then we should go for **List**.

### Why is index important?

A List is index-based. We can identify/access elements using their index.

Example:

```java
List<String> list = new ArrayList<>();

list.add("A");
list.add("B");
list.add("C");
```

Logical representation:

```text
Index:    0    1    2
          ↓    ↓    ↓
List:    [A]  [B]  [C]
```

### List Methods

```java
boolean add(E e);
void add(int index, E element);

boolean addAll(Collection<? extends E> c);
boolean addAll(int index, Collection<? extends E> c);

E get(int index);

E set(int index, E element);

int indexOf(Object o);
int lastIndexOf(Object o);

Iterator<E> iterator();
```

---

# 3. ArrayList

The underlying data structure of `ArrayList` is a:

**Resizable Array / Growable Array**

### Important Properties

- Duplicates are allowed.
- Insertion order is preserved.
- Heterogeneous objects are allowed when generics are not restricted.
- `null` insertion is possible.
- `ArrayList` implements `Serializable`.
- `ArrayList` implements `Cloneable`.
- `ArrayList` implements `RandomAccess`.

---

## Constructors of ArrayList

### 1. No-argument constructor

```java
ArrayList<E> l = new ArrayList<>();
```

Creates an empty `ArrayList`.

> The commonly taught concept is that its capacity grows automatically as elements are added. In modern Java implementations, the no-arg constructor does not necessarily allocate an actual backing array of length 10 immediately; the backing array is allocated when the first element is added.

### 2. Initial-capacity constructor

```java
ArrayList<E> l = new ArrayList<>(int initialCapacity);
```

We can specify the initial capacity in advance.

Example:

```java
ArrayList<Integer> l = new ArrayList<>(20);
```

### 3. Collection constructor

```java
ArrayList<E> l = new ArrayList<>(Collection<? extends E> c);
```

Creates an `ArrayList` containing the elements of the given Collection.

Example:

```java
ArrayList<Integer> l1 = new ArrayList<>();

l1.add(10);
l1.add(20);
l1.add(30);

ArrayList<Integer> l2 = new ArrayList<>(l1);
```

---

## ArrayList Capacity Growth

When the backing array becomes full, `ArrayList` creates a larger backing array and copies the existing elements into it.

For modern OpenJDK implementations, the new capacity is approximately:

```text
new capacity = old capacity + (old capacity / 2)
```

i.e. approximately **1.5 × old capacity**.

> The exact implementation can vary between Java versions. Do not treat a fixed capacity-growth formula as part of the `ArrayList` API contract.

---

# 4. `toString()` and Collections

When we print a Collection object directly:

```java
System.out.println(l);
```

Java effectively uses the object's `toString()` representation.

For standard collection implementations such as `ArrayList`, the inherited/overridden `toString()` representation displays the elements in collection form.

Example:

```java
ArrayList<Integer> l = new ArrayList<>();

l.add(10);
l.add(20);
l.add(30);

System.out.println(l);
```

Output:

```text
[10, 20, 30]
```

---

# 5. Serializable and Cloneable

Collection implementations such as `ArrayList` support serialization and cloning.

### Serializable

Used when an object needs to be converted into a byte stream.

### Cloneable

Used to indicate that an object supports cloning through `Object.clone()`.

For `ArrayList`:

```java
l instanceof Serializable
l instanceof Cloneable
```

returns:

```text
true
true
```

> **Important:** Not every class implementing the `Collection` interface is automatically required by the interface contract to implement `Serializable` or `Cloneable`. These are properties of particular collection implementation classes.

---

# 6. RandomAccess Interface

`RandomAccess` is present in:

```java
java.util
```

It is a **marker interface**.

A marker interface does not contain methods. It is used to provide metadata/type information.

### RandomAccess

```java
public interface RandomAccess
```

It indicates that a List supports efficient random/index-based access.

Common Java collection classes implementing it include:

- `ArrayList`
- `Vector`

### Why is it important?

If retrieval/index access is a frequent operation, `ArrayList` is generally a suitable choice because random access is efficient.

Example:

```java
ArrayList<Integer> list = new ArrayList<>();

list.add(10);
list.add(20);
list.add(30);

System.out.println(list.get(2));
```

Output:

```text
30
```

---

# 7. `instanceof` Example

```java
import java.io.Serializable;
import java.util.*;

class Demo {
    public static void main(String[] args) {

        ArrayList<Integer> l1 = new ArrayList<>();
        LinkedList<Integer> l2 = new LinkedList<>();

        System.out.println(l1 instanceof Serializable);
        System.out.println(l1 instanceof Cloneable);

        System.out.println(l1 instanceof RandomAccess);
        System.out.println(l2 instanceof RandomAccess);
    }
}
```

Output:

```text
true
true
true
false
```

### Conclusion

- `ArrayList` → `Serializable` → `true`
- `ArrayList` → `Cloneable` → `true`
- `ArrayList` → `RandomAccess` → `true`
- `LinkedList` → `RandomAccess` → `false`

---

# 8. ArrayList — Best and Worst Choice

### Best choice

If the frequent operation is **retrieval**, `ArrayList` is generally a good choice.

```java
list.get(index);
```

Random/index access is efficient.

### Worst choice

If frequent operations are **insertion/deletion in the middle**, `ArrayList` can be inefficient because elements may need to be shifted.

Example:

```text
Before:
[A, B, C, D, E]

Insert X at index 2:

[A, B, X, C, D, E]
       ↑
   elements shifted
```

---

# Quick Revision

| Feature | ArrayList |
|---|---|
| Data structure | Resizable/Growable Array |
| Duplicates | Allowed |
| Insertion order | Preserved |
| `null` | Allowed |
| Heterogeneous objects | Possible without restrictive generics |
| Serializable | Yes |
| Cloneable | Yes |
| RandomAccess | Yes |
| Retrieval | Fast |
| Middle insertion/deletion | Relatively slower |
