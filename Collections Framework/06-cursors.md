# 🔍 Cursors — Enumeration, Iterator & ListIterator

> **Summary:** Java me Collection elements traverse karne ke 3 cursor types, methods, forward vs bidirectional traversal, fail-fast behavior, aur kab kaunsa use karna chahiye.

---

## 1. Cursors Kya Hain?

Collection ke elements ko **ek-ek karke access karne (traverse/iterate)** ke liye Java me 3 types ke cursors hain:

```text
Cursors in Java:
1. Enumeration  → Oldest (Java 1.0), sirf Vector/legacy ke liye
2. Iterator     → Universal (Java 1.2), har Collection ke liye
3. ListIterator → Most Powerful (Java 1.2), sirf List ke liye
```

---

## 2. Enumeration (Legacy Cursor)

**Enumeration** sabse purana cursor hai (Java 1.0). Ye sirf **legacy classes (Vector, Stack, Hashtable)** ke sath kaam karta hai.

### How to Get Enumeration:
```java
Vector<String> v = new Vector<>();
v.add("A"); v.add("B"); v.add("C");

Enumeration<String> e = v.elements(); // Vector ka special method
```

### Methods (Only 2):
```java
while (e.hasMoreElements()) {       // Aage element hai ya nahi?
    String val = e.nextElement();   // Next element return karo
    System.out.println(val);
}
```

### Limitations:
- ❌ Sirf **forward direction** me traverse kar sakte ho
- ❌ Traverse karte waqt **elements remove nahi** kar sakte
- ❌ Sirf **legacy classes** ke sath kaam karta hai (ArrayList, HashSet ke sath nahi!)

---

## 3. Iterator (Universal Cursor)

**Iterator** Java 1.2 me introduce hua. Ye **har Collection type** (ArrayList, HashSet, LinkedList, TreeSet, etc.) ke sath kaam karta hai.

### How to Get Iterator:
```java
ArrayList<String> list = new ArrayList<>();
list.add("X"); list.add("Y"); list.add("Z");

Iterator<String> it = list.iterator(); // Collection interface ka method!
```

### Methods (3 Methods):
```java
while (it.hasNext()) {          // Aage element hai ya nahi?
    String val = it.next();     // Next element return karo
    
    if (val.equals("Y")) {
        it.remove();            // ✅ Safe removal during iteration!
    }
}
```

### Key Advantages over Enumeration:
- ✅ **Universal** — Har Collection ke sath kaam karta hai
- ✅ **Safe `remove()`** — Iterate karte hue safely element delete kar sakte ho

### Limitations:
- ❌ Sirf **forward direction** me traverse kar sakte ho
- ❌ Traverse karte waqt **add ya replace** nahi kar sakte (sirf remove)

---

## 4. ListIterator (Most Powerful Cursor)

**ListIterator** sirf **List** implementations (ArrayList, LinkedList, Vector) ke sath kaam karta hai. Ye **bidirectional traversal + modification** support karta hai.

### How to Get ListIterator:
```java
ArrayList<String> list = new ArrayList<>(List.of("A", "B", "C", "D"));

ListIterator<String> lit = list.listIterator();       // Start from index 0
ListIterator<String> lit2 = list.listIterator(2);     // Start from index 2
```

### Methods (9 Methods — Most Rich Cursor):

#### Forward Traversal:
```java
while (lit.hasNext()) {
    int index = lit.nextIndex();    // Next element ka index
    String val = lit.next();        // Next element return karo + move forward
    System.out.println(index + ": " + val);
}
```

#### Backward Traversal:
```java
while (lit.hasPrevious()) {
    int index = lit.previousIndex(); // Previous element ka index
    String val = lit.previous();     // Previous element return karo + move backward
    System.out.println(index + ": " + val);
}
```

#### Modification During Iteration:
```java
ListIterator<String> lit = list.listIterator();
while (lit.hasNext()) {
    String val = lit.next();
    
    if (val.equals("B")) {
        lit.remove();       // ✅ Remove current element
    }
    if (val.equals("C")) {
        lit.set("C-MODIFIED"); // ✅ Replace current element!
    }
    if (val.equals("D")) {
        lit.add("NEW");     // ✅ Insert new element at current position!
    }
}
```

---

## 5. ⚖️ The Ultimate Comparison: Enumeration vs Iterator vs ListIterator

| Feature | Enumeration | Iterator | ListIterator |
|---------|-------------|----------|--------------|
| **Introduced** | Java 1.0 | Java 1.2 | Java 1.2 |
| **Works With** | Legacy only (Vector, Stack, Hashtable) | **Any Collection** (Universal) | **List only** (ArrayList, LinkedList, Vector) |
| **Direction** | Forward only ➡️ | Forward only ➡️ | **Bidirectional** ⬅️➡️ |
| **Read** | ✅ `nextElement()` | ✅ `next()` | ✅ `next()` + `previous()` |
| **Remove** | ❌ Not possible | ✅ `remove()` | ✅ `remove()` |
| **Add** | ❌ | ❌ | ✅ `add()` |
| **Replace** | ❌ | ❌ | ✅ `set()` |
| **Method Count** | 2 | 3 | 9 |
| **How to Get** | `vector.elements()` | `collection.iterator()` | `list.listIterator()` |

---

## 6. Fail-Fast vs Fail-Safe Iterators

### Fail-Fast (Default Behavior):
Iterator agar detect karta hai ki collection **structurally modified** ho gayi hai iteration ke dauran (kisi aur thread ya direct `list.add()` se), toh **immediately `ConcurrentModificationException` throw** karta hai.

```java
ArrayList<String> list = new ArrayList<>(List.of("A", "B", "C"));
Iterator<String> it = list.iterator();

while (it.hasNext()) {
    String s = it.next();
    list.remove(s);  // ❌ Direct modification → ConcurrentModificationException!
    // it.remove();  // ✅ Iterator ke through remove karo toh safe hai
}
```

### Fail-Safe:
`CopyOnWriteArrayList` aur `ConcurrentHashMap` ke iterators **snapshot par kaam** karte hain, toh modification se exception nahi aata:

```java
CopyOnWriteArrayList<String> cowList = new CopyOnWriteArrayList<>(List.of("A", "B"));
for (String s : cowList) {
    cowList.add("NEW"); // ✅ No exception! Iterator snapshot par traverse karta hai
}
```

---

## 🧠 Interview Quick Traps

| Trap | Answer |
|------|--------|
| Enumeration kya universal hai? | ❌ Nahi! Sirf legacy classes (Vector, Stack, Hashtable) ke sath kaam karta hai. |
| Iterator me `add()` method hota hai? | ❌ Nahi! Sirf `hasNext()`, `next()`, `remove()`. Add ke liye ListIterator chahiye. |
| ListIterator HashSet ke sath kaam karega? | ❌ Nahi! ListIterator sirf `List` implementations ke sath kaam karta hai. |
| Iterate karte waqt direct `list.remove()` safe hai? | ❌ `ConcurrentModificationException` aa sakta hai. **`iterator.remove()`** use karo. |
| `ConcurrentModificationException` kaun throw karta hai? | **Fail-Fast** iterators (ArrayList, HashSet, HashMap ke iterators). |

---

[⬅️ Previous: Synchronized Collections](./05-synchronized-collections.md) · [📖 Back to Collections Index](./README.md) · [Next → Set & HashSet ➡️](./07-set-and-hashset.md)
