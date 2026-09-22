# 📋 ArrayList — Deep Dive

> **Summary:** ArrayList constructors, initial capacity & growth formula, `RandomAccess` / `Serializable` / `Cloneable` marker interfaces, `toString()` behavior, aur best/worst use case scenarios.

---

## 1. ArrayList Kya Hai?

**ArrayList** ek **resizable array** (dynamic array) hai jo internally ordinary array use karta hai lekin size automatically grow hota hai jab elements add karte ho.

```text
ArrayList Key Properties:
✅ Maintains insertion order
✅ Allows duplicate elements
✅ Allows null values (multiple nulls bhi)
✅ Implements RandomAccess → Fast index-based retrieval O(1)
❌ NOT synchronized (Not thread-safe by default)
```

### Class Declaration:
```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, Serializable
```

---

## 2. ArrayList Constructors

### Constructor 1: Default — `ArrayList()`
```java
ArrayList<String> list = new ArrayList<>();
```
- **Initial Capacity:** 10 (internally size 10 ka array create hota hai)
- Jab 10 elements fill ho jaayein, toh automatically naya bada array banta hai

### Constructor 2: Custom Capacity — `ArrayList(int initialCapacity)`
```java
ArrayList<String> list = new ArrayList<>(100);
```
- Agar tumhe advance me pata hai ki roughly kitne elements aayenge, toh ye use karo
- **Performance Benefit:** Baar-baar resize/copy operations avoid hote hain

### Constructor 3: From Existing Collection — `ArrayList(Collection c)`
```java
List<String> original = List.of("A", "B", "C");
ArrayList<String> copy = new ArrayList<>(original);
```
- Kisi bhi existing Collection ka data ek ArrayList me copy kar deta hai

---

## 3. Capacity Growth Formula

Jab ArrayList ka internal array full ho jaata hai aur ek aur element add karna hota hai:

```text
New Capacity = (Old Capacity * 3 / 2) + 1

Example:
Initial Capacity = 10
After 1st growth  = (10 * 3/2) + 1 = 16
After 2nd growth  = (16 * 3/2) + 1 = 25
After 3rd growth  = (25 * 3/2) + 1 = 38
```

### Internally Kya Hota Hai?
```text
1. Naya bada array create hota hai (new capacity ke sath)
2. Purane array ke saare elements naye array me copy hote hain (System.arraycopy)
3. Purana array GC eligible ho jaata hai
4. ArrayList ka internal reference naye array ko point karne lagta hai
```

> ⚠️ **Performance Warning:** Agar starting capacity bahut chhoti rakhi aur bahut saare elements add kiye, toh baar-baar resizing + copying hogi jisse performance degrade hogi. Isliye agar approximate size pata ho toh initial capacity specify karo!

---

## 4. Marker Interfaces Implemented by ArrayList

ArrayList 3 important marker interfaces implement karta hai:

| Marker Interface | Purpose |
|------------------|---------|
| **`RandomAccess`** | JVM ko signal karta hai ki ye data structure **O(1) time me index-based access** support karta hai (`get(i)` operation fast hai) |
| **`Serializable`** | Object ko **byte stream me convert** (serialize) karke network ya file me bheja ja sakta hai |
| **`Cloneable`** | `clone()` method se **shallow copy** banayi ja sakti hai bina `CloneNotSupportedException` ke |

### `instanceof` Check Example:
```java
ArrayList<String> list = new ArrayList<>();

System.out.println(list instanceof RandomAccess);  // true
System.out.println(list instanceof Serializable);  // true
System.out.println(list instanceof Cloneable);      // true
System.out.println(list instanceof List);           // true
System.out.println(list instanceof Collection);     // true
```

---

## 5. `toString()` Behavior in Collections

ArrayList ka `toString()` method automatically override kiya hua hota hai (`AbstractCollection` class me):

```java
ArrayList<Integer> nums = new ArrayList<>();
nums.add(10);
nums.add(20);
nums.add(30);

System.out.println(nums);           // [10, 20, 30] ← Clean readable output!
System.out.println(nums.toString()); // [10, 20, 30] ← Same result
```

> **Comparison:** Plain array me `System.out.println(arr)` ugly output deta hai jaise `[I@1a2b3c`. Collections me `toString()` already overridden hai taaki human-readable `[elem1, elem2, ...]` format aaye.

---

## 6. ArrayList — Best & Worst Use Cases

### ✅ Best Choice (Use ArrayList When):
- **Frequent retrieval / read operations** chahiye (index-based access O(1) time me)
- Data mostly **sequential read** hota hai (e.g., display list of products, render table rows)
- Elements insert/remove mostly **end se** hote hain (`add()` amortized O(1) hai)

### ❌ Worst Choice (Avoid ArrayList When):
- **Frequent insertion/deletion middle me** karni ho → Har baar elements shift hone padte hain → O(n) time!
- **Thread-safety** required ho → ArrayList synchronized nahi hai, race conditions aa sakte hain
- Elements **beginning se baar-baar add/remove** karne hon → LinkedList better rahegi

```text
Operation Performance (ArrayList):
┌────────────────────────┬──────────┐
│ Operation              │ Time     │
├────────────────────────┼──────────┤
│ get(index)             │ O(1) ✅  │
│ add(element) at end    │ O(1)* ✅ │  (* amortized, resize excluded)
│ add(index, element)    │ O(n) ❌  │  (shift elements right)
│ remove(index)          │ O(n) ❌  │  (shift elements left)
│ contains(element)      │ O(n)     │  (linear search)
│ size()                 │ O(1)     │
└────────────────────────┴──────────┘
```

---

## 🧠 Interview Quick Traps

| Trap | Answer |
|------|--------|
| ArrayList ka default initial capacity kitni hoti hai? | **10** |
| Capacity growth formula kya hai? | `(OldCapacity * 3/2) + 1` |
| `RandomAccess` interface me kitne methods hain? | **0** (Marker Interface hai!) |
| Kya ArrayList me `null` dal sakte hain? | ✅ Haan, multiple nulls bhi allowed hain. |
| ArrayList thread-safe hai? | ❌ Nahi. Thread-safety ke liye `Collections.synchronizedList()` ya `CopyOnWriteArrayList` use karo. |
| ArrayList internally konsa data structure use karta hai? | Ordinary **resizable array** (Object[]) |

---

[⬅️ Previous: Framework Intro](./01-collection-framework-intro.md) · [📖 Back to Collections Index](./README.md) · [Next → Vector & Stack ➡️](./03-vector-and-stack.md)
