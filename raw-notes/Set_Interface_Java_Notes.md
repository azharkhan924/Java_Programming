# Set Interface — Java Collections Framework

## Set (I)

→ **Set is the child interface of Collection.**

### Collection → Set hierarchy

```text
Collection (1.2)
      |
     Set (1.2)
    /    \
HashSet  SortedSet (1.2)
  |          |
LinkedHashSet NavigableSet (1.6)
(1.4)         |
             TreeSet (1.2)
```

> `LinkedHashSet` is a direct subclass of `HashSet`.
>
> `NavigableSet` is a child interface of `SortedSet`.
>
> `TreeSet` implements `NavigableSet`.

---

## Set

→ If we want to represent a **group of individual objects as a single entity** where:

- duplicates are not allowed
- insertion order is not preserved

then we should go for **Set**.

→ Set interface doesn't add new element-manipulation methods of its own, so we mainly use the methods inherited from `Collection`.

### Set implementations

- HashSet
- LinkedHashSet
- TreeSet

---

# 1. HashSet

→ **Since Java 1.2**

→ Underlying data structure is a **Hash Table**.  
→ Internally, `HashSet` is backed by a `HashMap`.

→ Duplicates are not allowed.

→ If we try to add a duplicate object, `add()` simply returns `false`.

→ Insertion order is **not preserved**.

→ Objects are placed according to their **hash value / hash code**, not according to insertion order.

→ Heterogeneous objects are allowed.

→ `null` insertion is possible; one `null` element can be stored.

→ Implements `Serializable` and `Cloneable`.

→ Does not implement `RandomAccess`.

→ **HashSet is a good choice when frequent operations are search (`contains`) / add / remove.**

---

# Internal Working of HashSet — IMPORTANT

### HashSet internally uses HashMap

```java
HashSet<String> hs = new HashSet<>();

hs.add("A");
```

Internally, conceptually:

```text
HashSet
   |
   ↓
HashMap
   |
   ↓
Hash Table / Buckets
```

A `HashSet` stores the element as a key in the backing `HashMap`.

Conceptually:

```java
map.put(element, PRESENT);
```

where `PRESENT` is an internal dummy value.

---

## How does HashSet decide whether an object is duplicate?

Mainly two things are important:

```text
hashCode()
   ↓
bucket selection
   ↓
equals()
   ↓
duplicate or new object
```

### Simple flow

```text
hs.add(obj)
     |
     ↓
obj.hashCode()
     |
     ↓
Find appropriate bucket
     |
     ↓
Any existing object with matching hash?
     |
   Yes / No
     |
     ↓
If required → equals()
     |
     ├── true  → duplicate → add() returns false
     |
     └── false → new object → object is inserted
```

### VERY IMPORTANT

→ `hashCode()` is mainly used to **find the bucket quickly**.

→ `equals()` is used to **check logical equality** when candidates are in the same hash location.

So:

```text
hashCode() → WHERE to search
equals()   → WHETHER it is the same object logically
```

---

# equals() and hashCode() Contract

If two objects are equal according to `equals()`:

```java
obj1.equals(obj2) == true
```

then their hash codes **must be the same**:

```java
obj1.hashCode() == obj2.hashCode()
```

But reverse is NOT compulsory:

```text
same hashCode
     ↓
does NOT mean
     ↓
objects are equal
```

This is called a **hash collision**.

---

# Example 1 — String duplicate

```java
HashSet<String> hs = new HashSet<>();

System.out.println(hs.add("A"));  // true
System.out.println(hs.add("A"));  // false
```

Why?

```text
First "A"
   ↓
hashCode()
   ↓
bucket
   ↓
no equal object
   ↓
inserted

Second "A"
   ↓
same hashCode
   ↓
same bucket
   ↓
equals() → true
   ↓
duplicate
   ↓
false
```

Output:

```text
true
false
```

---

# Example 2 — Different objects but logically equal

```java
String s1 = new String("Java");
String s2 = new String("Java");

System.out.println(s1 == s2);       // false
System.out.println(s1.equals(s2));  // true
```

→ `s1` and `s2` are two different objects.

→ But String overrides `equals()` for content comparison.

Therefore:

```java
HashSet<String> hs = new HashSet<>();

hs.add(s1);
hs.add(s2);

System.out.println(hs.size()); // 1
```

Because content is equal.

---

# Example 3 — `==` vs `equals()`

```java
String s1 = new String("A");
String s2 = new String("A");
```

```text
s1 == s2
   ↓
false
```

because references are different.

But:

```text
s1.equals(s2)
      ↓
    true
```

because String compares content.

→ HashSet uses logical equality (`equals()`), not simply `==`.

---

# Example 4 — Same hashCode but not equal

Hash collision can happen.

Suppose:

```java
class Student {
    int id;

    Student(int id) {
        this.id = id;
    }

    @Override
    public int hashCode() {
        return 10;
    }

    @Override
    public boolean equals(Object obj) {
        return false;
    }
}
```

Now:

```java
HashSet<Student> hs = new HashSet<>();

hs.add(new Student(1));
hs.add(new Student(2));
```

Both can be stored.

Why?

```text
hashCode same
     ↓
same bucket
     ↓
equals() → false
     ↓
not duplicate
     ↓
both inserted
```

So:

```text
same hashCode ≠ equal objects
```

---

# Example 5 — equals() true but hashCode different

Suppose:

```java
class Student {
    int id;

    Student(int id) {
        this.id = id;
    }

    @Override
    public boolean equals(Object obj) {
        Student s = (Student)obj;
        return this.id == s.id;
    }

    @Override
    public int hashCode() {
        return (int)(Math.random() * 100);
    }
}
```

This is a **wrong implementation**.

If:

```java
s1.equals(s2) == true
```

but:

```java
s1.hashCode() != s2.hashCode()
```

then HashSet may put them into different buckets.

Therefore HashSet may fail to detect the duplicate.

### Conclusion

→ If we override `equals()`, we should also override `hashCode()` consistently.

---

# Example 6 — Correct equals() + hashCode()

```java
class Student {
    int id;

    Student(int id) {
        this.id = id;
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj)
            return true;

        if (!(obj instanceof Student))
            return false;

        Student s = (Student)obj;
        return this.id == s.id;
    }

    @Override
    public int hashCode() {
        return Integer.hashCode(id);
    }
}
```

Now:

```java
HashSet<Student> hs = new HashSet<>();

hs.add(new Student(10));
hs.add(new Student(10));

System.out.println(hs.size()); // 1
```

Because:

```text
id same
 ↓
equals() → true
 ↓
hashCode() also same
 ↓
duplicate
```

---

# Example 7 — Override equals() only

```java
class Student {
    int id;

    Student(int id) {
        this.id = id;
    }

    @Override
    public boolean equals(Object obj) {
        Student s = (Student)obj;
        return id == s.id;
    }
}
```

Here `hashCode()` is inherited from `Object`.

Therefore two logically equal Student objects can have different hash codes.

```java
HashSet<Student> hs = new HashSet<>();

hs.add(new Student(10));
hs.add(new Student(10));
```

Expected logically:

```text
1 object
```

But because the `equals()` / `hashCode()` contract is broken, HashSet can contain both.

### Exam point

→ **Whenever we override `equals()`, we should override `hashCode()` also.**

---

# Example 8 — Override hashCode() only

```java
class Student {
    int id;

    @Override
    public int hashCode() {
        return id;
    }
}
```

Here `equals()` is still inherited from `Object`.

So two different Student objects with same id are still normally not equal.

Therefore:

```text
same hashCode
+
equals() false
=
both objects allowed
```

Again:

```text
same hashCode does not mean duplicate.
```

---

# Example 9 — Mutable object problem

```java
class Student {
    int id;

    Student(int id) {
        this.id = id;
    }

    @Override
    public int hashCode() {
        return id;
    }

    @Override
    public boolean equals(Object obj) {
        Student s = (Student)obj;
        return id == s.id;
    }
}
```

Now:

```java
Student s = new Student(10);

HashSet<Student> hs = new HashSet<>();
hs.add(s);

s.id = 20;

System.out.println(hs.contains(s));
```

This can return:

```text
false
```

Why?

```text
Insertion time:
id = 10
 ↓
bucket based on 10

After modification:
id = 20
 ↓
HashSet searches bucket based on 20
 ↓
old object is still sitting according to old hash location
```

### Conclusion

→ Do not modify fields used in `equals()` / `hashCode()` while the object is stored in HashSet.

---

# Example 10 — `Integer` duplicate

```java
HashSet<Integer> hs = new HashSet<>();

hs.add(10);
hs.add(20);
hs.add(10);

System.out.println(hs);
```

Result contains only:

```text
10, 20
```

Because `Integer` correctly implements `equals()` and `hashCode()`.

---

# Example 11 — null in HashSet

```java
HashSet<String> hs = new HashSet<>();

hs.add(null);
hs.add(null);

System.out.println(hs.size());
```

Output:

```text
1
```

→ HashSet allows `null`.

→ Duplicate `null` is not allowed.

---

# Example 12 — Heterogeneous objects in HashSet

```java
HashSet hs = new HashSet();

hs.add(10);
hs.add("A");
hs.add(10.5);
hs.add(true);
```

This is allowed.

Why?

```text
HashSet does not require natural sorting/comparison.
```

So heterogeneous objects can be stored.

---

# Example 13 — Insertion order is not guaranteed

```java
HashSet<Integer> hs = new HashSet<>();

hs.add(10);
hs.add(20);
hs.add(5);
hs.add(30);

System.out.println(hs);
```

Do not depend on:

```text
10, 20, 5, 30
```

as the iteration order.

→ HashSet makes **no guarantee about iteration order**.

---

# Example 14 — equals() decides logical duplicate

```java
class Employee {
    int id;

    Employee(int id) {
        this.id = id;
    }

    @Override
    public boolean equals(Object obj) {
        Employee e = (Employee)obj;
        return id == e.id;
    }

    @Override
    public int hashCode() {
        return id;
    }
}
```

```java
HashSet<Employee> hs = new HashSet<>();

hs.add(new Employee(101));
hs.add(new Employee(101));
hs.add(new Employee(102));
```

Result:

```text
101
102
```

Because employees with the same `id` are treated as logically equal.

---

# Example 15 — equals() can compare multiple fields

```java
class Student {
    int rollNo;
    String name;

    Student(int rollNo, String name) {
        this.rollNo = rollNo;
        this.name = name;
    }

    @Override
    public boolean equals(Object obj) {
        Student s = (Student)obj;

        return rollNo == s.rollNo &&
               name.equals(s.name);
    }

    @Override
    public int hashCode() {
        return 31 * rollNo + name.hashCode();
    }
}
```

Now duplicate means:

```text
rollNo same
AND
name same
```

If either is different:

```text
equals() → false
```

and both objects can exist.

---

# Example 16 — Different hash codes mean HashSet normally doesn't call equals between them

Suppose:

```text
Object A
hashCode = 10

Object B
hashCode = 20
```

HashSet places them in different hash locations.

Therefore there is no reason to compare A and B using `equals()` as duplicate candidates.

### Simple idea

```text
hashCode different
      ↓
different bucket
      ↓
not compared as same-bucket candidate
```

This is why `hashCode()` is important for performance.

---

# Example 17 — Same hash code requires equals() check

```text
Object A → hashCode 10
Object B → hashCode 10
```

Both may reach the same bucket.

Then HashSet checks:

```java
A.equals(B)
```

If:

```text
true  → duplicate
false → both allowed
```

---

# Example 18 — Why hashCode alone cannot remove duplicates

Suppose:

```text
A.hashCode() = 100
B.hashCode() = 100
```

If HashSet assumed same hash means duplicate, it would incorrectly remove B.

Therefore:

```text
hashCode → bucket/location
equals   → final logical equality check
```

Both are important.

---

# Example 19 — Real-life example

Suppose we create:

```java
class User {
    int userId;
    String name;
}
```

If our rule is:

```text
same userId = same user
```

then `equals()` and `hashCode()` should be based on `userId`.

```java
@Override
public boolean equals(Object obj) {
    User u = (User)obj;
    return userId == u.userId;
}

@Override
public int hashCode() {
    return Integer.hashCode(userId);
}
```

Now:

```java
User u1 = new User(101, "Azhar");
User u2 = new User(101, "Khan");
```

If equality is based only on `userId`:

```text
u1.equals(u2)
      ↓
    true
```

So HashSet stores only one of them.

---

# Example 20 — Summary of HashSet duplicate checking

```text
add(obj)
   ↓
hashCode()
   ↓
bucket selection
   ↓
compare candidate objects
   ↓
equals()
   |
   ├── true  → duplicate → add() returns false
   |
   └── false → insert → add() returns true
```

### Golden Rule

```text
If a.equals(b) == true
then a.hashCode() == b.hashCode() MUST be true.
```

But:

```text
a.hashCode() == b.hashCode()
does NOT guarantee
a.equals(b) == true.
```

---

# HashSet Constructors

## 1. HashSet()

```java
HashSet h = new HashSet();
```

→ Creates an empty HashSet object.

→ Default initial capacity = **16**

→ Default load factor / fill ratio = **0.75**

---

## 2. HashSet(int initialCapacity)

```java
HashSet h = new HashSet(20);
```

→ Creates an empty HashSet with specified initial capacity.

→ Load factor remains default:

```text
0.75
```

---

## 3. HashSet(int initialCapacity, float loadFactor)

```java
HashSet h = new HashSet(20, 0.80f);
```

→ Creates an empty HashSet with:

```text
initial capacity = 20
load factor = 0.80
```

---

## 4. HashSet(Collection C)

```java
HashSet h = new HashSet(Collection c);
```

→ Creates a HashSet containing the elements of the given Collection.

→ Used for **conversion from one Collection type to another Collection type**.

Example:

```java
ArrayList<Integer> al = new ArrayList<>();

al.add(10);
al.add(20);
al.add(10);

HashSet<Integer> hs = new HashSet<>(al);
```

Result:

```text
10, 20
```

because HashSet removes duplicates.

---

# Load Factor / Fill Ratio

→ Load factor is the factor that decides **when the backing hash table should resize**.

Formula:

```text
Threshold = Capacity × Load Factor
```

Default:

```text
Capacity = 16
Load Factor = 0.75

Threshold = 16 × 0.75
          = 12
```

→ When the number of stored entries crosses the resize threshold, the backing HashMap resizes.

### Important correction

It is **not** that a new HashSet object is created.

Rather:

```text
HashSet
  ↓
backing HashMap
  ↓
hash table is resized
```

---

# 2. LinkedHashSet

→ **Since Java 1.4**

→ `LinkedHashSet` is a child class of `HashSet`.

→ It is almost same as HashSet, except for the following major difference:

| HashSet | LinkedHashSet |
|---|---|
| Hash table | Hash table + linked list |
| Insertion order not guaranteed | Insertion order preserved |
| Search/add/remove based on hashing | Hashing + linked ordering |
| Duplicates not allowed | Duplicates not allowed |

→ Underlying data structure:

```text
HashSet
→ Hash table

LinkedHashSet
→ Hash table + linked list
```

→ Heterogeneous objects are allowed.

→ `null` is allowed.

→ Implements `Serializable` and `Cloneable`.

### Example

```java
LinkedHashSet<String> lhs = new LinkedHashSet<>();

lhs.add("A");
lhs.add("B");
lhs.add("C");
lhs.add("A");

System.out.println(lhs);
```

Output:

```text
[A, B, C]
```

→ Duplicate `"A"` is ignored.

→ Insertion order is preserved.

### Best use

→ `LinkedHashSet` is a good choice when:

```text
duplicates are not allowed
        +
insertion order must be preserved
```

Example: maintaining unique recently processed items / ordered unique values.

---

# 3. SortedSet

→ **Since Java 1.2**

→ SortedSet is the **child interface of Set**.

→ If we want to represent a group of individual objects according to **some sorting order** and duplicates are not allowed, then we should go for `SortedSet`.

```text
Set
 |
SortedSet
```

→ SortedSet provides ordering.

→ Elements are arranged according to:

```text
1. Natural sorting order
       OR
2. Customized sorting order using Comparator
```

---

# Important SortedSet Methods

Suppose:

```java
SortedSet<Integer> s =
    new TreeSet<>();

s.add(10);
s.add(20);
s.add(30);
s.add(40);
s.add(50);
```

Set:

```text
[10, 20, 30, 40, 50]
```

---

## 1. first()

```java
s.first();
```

→ Returns the **first / lowest element**.

```text
[10, 20, 30, 40, 50]
 ↑
first()
```

Result:

```text
10
```

---

## 2. last()

```java
s.last();
```

→ Returns the **last / highest element**.

```text
[10, 20, 30, 40, 50]
                ↑
              last()
```

Result:

```text
50
```

---

## 3. headSet(Object obj)

```java
s.headSet(30);
```

→ Returns elements **strictly less than 30**.

Result:

```text
[10, 20]
```

30 is not included.

---

## 4. tailSet(Object obj)

```java
s.tailSet(30);
```

→ Returns elements **greater than or equal to 30**.

Result:

```text
[30, 40, 50]
```

---

## 5. subSet(Object obj1, Object obj2)

```java
s.subSet(20, 50);
```

→ Returns elements:

```text
>= obj1
AND
< obj2
```

Result:

```text
[20, 30, 40]
```

So:

```text
subSet(from, to)

from → inclusive
to   → exclusive
```

---

# Comparator method

```java
s.comparator();
```

→ Returns the `Comparator` object used to describe the sorting technique.

If natural/default sorting order is used:

```java
s.comparator()
```

returns:

```text
null
```

because no custom Comparator was supplied.

---

# Natural Sorting Order

### For numbers

Natural sorting order:

```text
Ascending order

10, 20, 30, 40, 50
```

### For Strings

Natural sorting order is based on String's natural ordering:

```text
A, B, C
```

and for lowercase/uppercase combinations, ordering follows String's `compareTo()` rules, so it is better to think of it as **lexicographic/natural String order**, not simply "human alphabetical order".

---

# 4. TreeSet

→ **Since Java 1.2**

→ `TreeSet` implements:

```text
NavigableSet
     |
SortedSet
     |
Set
     |
Collection
```

→ Underlying data structure is a **TreeMap**, which is based on a balanced search tree.

→ Duplicates are not allowed.

→ Insertion order is not preserved.

→ Objects are inserted according to some sorting order.

→ Heterogeneous objects are generally not allowed when natural ordering is used.

→ If objects cannot be compared, we get:

```text
ClassCastException
```

at runtime.

→ Basic `add`, `remove`, and `contains` operations have guaranteed `O(log n)` time.

---

# TreeSet Constructors

## 1. TreeSet()

```java
TreeSet t = new TreeSet();
```

→ Creates an empty TreeSet.

→ Elements are inserted according to **default natural sorting order**.

---

## 2. TreeSet(Comparator c)

```java
TreeSet t = new TreeSet(Comparator c);
```

→ Creates an empty TreeSet.

→ Elements are inserted according to **customized sorting order** defined by the Comparator.

---

## 3. TreeSet(SortedSet s)

```java
TreeSet t = new TreeSet(SortedSet s);
```

→ Creates a TreeSet containing the elements of the given SortedSet.

→ The elements are ordered according to the ordering of the given SortedSet.

---

## 4. TreeSet(Collection c)

```java
TreeSet t = new TreeSet(Collection c);
```

→ Creates a TreeSet containing the elements of the specified Collection.

→ Elements must be mutually comparable under the TreeSet's ordering.

---

# TreeSet Example

```java
TreeSet<String> t = new TreeSet<>();

t.add("A");
t.add("a");
t.add("B");
t.add("Z");
t.add("L");

System.out.println(t);
```

Output follows String natural ordering.

Example:

```text
[A, B, L, Z, a]
```

→ It is not insertion order.

→ It is natural sorting order.

---

# TreeSet — Heterogeneous Objects

```java
TreeSet t = new TreeSet();

t.add(10);
t.add("A");
```

→ First object can be inserted.

→ When TreeSet tries to compare `String` with `Integer`, they cannot be naturally compared.

Therefore:

```text
ClassCastException
```

at runtime.

### Important

For natural sorting:

```text
Objects should be mutually comparable.
```

Usually this means homogeneous objects of a class/type whose natural ordering is defined.

---

# Comparable

→ An object is said to be **Comparable** when its class implements:

```java
java.lang.Comparable
```

Example:

```java
public final class String
implements Serializable, Comparable<String>, CharSequence
```

So String objects have natural ordering.

Wrapper classes such as:

```text
Integer
Double
Float
Long
Character
String
```

provide natural ordering through `Comparable`.

---

# StringBuffer Example

```java
TreeSet t = new TreeSet();

t.add(new StringBuffer("A"));
```

→ `TreeSet()` uses natural ordering.

→ Natural ordering requires the object to be comparable.

→ `StringBuffer` does not implement `Comparable`.

Therefore:

```text
ClassCastException
```

at runtime.

### Simple flow

```text
TreeSet()
   ↓
Natural sorting
   ↓
Object should be Comparable
   ↓
StringBuffer is not Comparable
   ↓
ClassCastException
```

---

# Important Note — TreeSet and null

For a TreeSet using **natural ordering**, do not write:

```java
TreeSet t = new TreeSet();

t.add(null);
```

This results in:

```text
NullPointerException
```

Natural ordering needs to compare the element, and `null` cannot be naturally compared.

So the safe exam point is:

```text
TreeSet with natural ordering does NOT permit null.
```

A custom Comparator may be designed to handle null, but that is a separate case.

---

# Comparable Interface

Package:

```text
java.lang
```

→ `Comparable` contains one main comparison method:

```java
public int compareTo(Object obj);
```

For generic form:

```java
public int compareTo(T obj);
```

### Return value

```text
negative value
→ obj1 should come before obj2

positive value
→ obj1 should come after obj2

0
→ obj1 and obj2 are considered equal in ordering
```

Example:

```java
Integer a = 10;
Integer b = 20;

a.compareTo(b);
```

Result:

```text
negative
```

because 10 should come before 20.

---

# Comparator

`Comparator` is used when we want **customized sorting order**.

Example:

```java
TreeSet<Integer> t =
    new TreeSet<>(Comparator.reverseOrder());
```

Now:

```text
50, 40, 30, 20, 10
```

---

# SortedSet vs TreeSet

```text
SortedSet
   ↓
Interface

TreeSet
   ↓
Class / implementation
```

We can write:

```java
SortedSet<Integer> s = new TreeSet<>();
```

Here:

```text
Reference type → SortedSet
Object type    → TreeSet
```

---

# NavigableSet

→ **Since Java 1.6**

→ NavigableSet is the child interface of SortedSet.

```text
Set
 ↓
SortedSet
 ↓
NavigableSet
 ↓
TreeSet
```

→ It provides navigation methods such as:

```text
lower()
floor()
ceiling()
higher()
pollFirst()
pollLast()
descendingSet()
descendingIterator()
```

### Example

```java
NavigableSet<Integer> ns = new TreeSet<>();

ns.add(10);
ns.add(20);
ns.add(30);
ns.add(40);
ns.add(50);
```

For:

```java
ns.lower(30)
```

result:

```text
20
```

For:

```java
ns.floor(30)
```

result:

```text
30
```

For:

```java
ns.ceiling(30)
```

result:

```text
30
```

For:

```java
ns.higher(30)
```

result:

```text
40
```

### Easy trick

```text
lower   → <
floor   → <=
ceiling → >=
higher  → >
```

---

# Complete Set Hierarchy — Version

```text
Collection                         Java 1.2
    |
    └── Set                         Java 1.2
         |
         ├── HashSet                Java 1.2
         |
         │    └── LinkedHashSet     Java 1.4
         |
         └── SortedSet              Java 1.2
               |
               └── NavigableSet     Java 1.6
                     |
                     └── TreeSet    Java 1.2
```

### Remember

```text
HashSet       → Hashing
LinkedHashSet → Hashing + Linked List
TreeSet       → TreeMap / Balanced Tree + Sorting
```

---

# Quick Comparison

| Feature | HashSet | LinkedHashSet | TreeSet |
|---|---|---|---|
| Since | 1.2 | 1.4 | 1.2 |
| Duplicate | Not allowed | Not allowed | Not allowed |
| Order | No guaranteed order | Insertion order | Sorted order |
| Data structure | Hash table / HashMap | Hash table + linked list | TreeMap / balanced tree |
| Heterogeneous | Allowed | Allowed | Not with natural ordering |
| Null | One null allowed | One null allowed | Not with natural ordering |
| Search | Very fast average | Very fast average | O(log n) |
| Sorting | No | No | Yes |
| RandomAccess | No | No | No |

---

# Final Exam Points

→ **Set** = duplicates not allowed.

→ **HashSet** = no guaranteed order + hashing.

→ **LinkedHashSet** = insertion order preserved + hashing.

→ **TreeSet** = sorted order + tree.

→ HashSet duplicate checking:

```text
hashCode() → bucket
equals()   → equality
```

→ If:

```java
a.equals(b) == true
```

then:

```java
a.hashCode() == b.hashCode()
```

must be true.

→ Same hash code does **not** mean objects are equal.

→ If we override `equals()`, we should override `hashCode()` consistently.

→ Natural sorting requires objects to be mutually comparable.

→ `Comparable` → natural/default sorting.

→ `Comparator` → customized sorting.

→ `SortedSet.comparator()` returns the Comparator used for ordering; for natural ordering it returns `null`.

→ `TreeSet` with natural ordering does not permit `null`.

→ `TreeSet` with non-comparable objects can produce `ClassCastException`.

### One-line memory trick

```text
HashSet       → No order
LinkedHashSet → Insertion order
TreeSet       → Sorted order
```
