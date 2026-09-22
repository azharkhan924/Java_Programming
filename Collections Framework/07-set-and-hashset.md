# 🎯 Set Interface — HashSet & LinkedHashSet

> **Summary:** Set interface properties, HashSet internal working (HashMap-backed), `equals()` + `hashCode()` contract for duplicate detection, HashSet constructors aur Load Factor, LinkedHashSet (insertion order preserved), aur 6 critical examples.

---

## 1. Set Interface Kya Hai?

> **Set** = A collection that contains **NO duplicate elements** aur generally **no guaranteed insertion order** (implementation-dependent).

```text
Set Properties:
❌ Duplicates NOT allowed (add karne ki koshish karo toh silently ignore — no error)
❌ Insertion order NOT guaranteed (HashSet me)
✅ At most ONE null element allowed (HashSet me)
✅ Based on mathematical "Set" concept
```

### Set Hierarchy:
```text
Set (I)
 ├── HashSet (C)           → Hashing-based, fastest, no order guarantee
 │    └── LinkedHashSet (C) → Hashing-based + Insertion order preserved
 ├── SortedSet (I)
 │    └── NavigableSet (I)
 │         └── TreeSet (C)  → Sorted order (natural/custom), no nulls
 └── EnumSet (C)           → Optimized for enum types
```

---

## 2. HashSet — How It Works Internally (MOST IMPORTANT!)

> **Secret:** HashSet internally ek **`HashMap`** use karta hai!

```java
// HashSet ka source code (simplified):
public class HashSet<E> {
    private HashMap<E, Object> map;
    private static final Object PRESENT = new Object(); // Dummy value

    public boolean add(E e) {
        return map.put(e, PRESENT) == null; // Element as KEY, dummy as VALUE!
    }
}
```

Jab aap `hashSet.add("Azhar")` karte ho:
```text
Internally: hashMap.put("Azhar", DUMMY_OBJECT)
```

### Duplicate Detection — 2-Step Process:

```text
Step 1: hashCode() call hota hai → Bucket number decide hota hai
Step 2: Agar uss bucket me koi element pehle se hai →
        equals() call hota hai → Agar true → DUPLICATE! Element add nahi hoga.
```

```text
hashSet.add("Hello")
    ↓
hashCode("Hello") → Bucket #5
    ↓
Bucket #5 empty hai? → YES → Element stored! ✅
    
hashSet.add("Hello")  // Duplicate attempt
    ↓
hashCode("Hello") → Bucket #5
    ↓
Bucket #5 me already "Hello" hai → equals("Hello", "Hello") → true → REJECTED! ❌
```

---

## 3. `equals()` aur `hashCode()` Contract (THE Golden Rule!)

### The Contract:
1. **Agar `o1.equals(o2)` true hai → toh `o1.hashCode() == o2.hashCode()` MUST be true!**
2. Agar `hashCode()` same hai → `equals()` true bhi ho sakta hai, false bhi (Hash Collision).
3. Agar `equals()` false hai → `hashCode()` same ya different kuch bhi ho sakta hai.

### What Happens If You Break This Contract?

| Scenario | `equals()` | `hashCode()` | HashSet Behavior |
|----------|------------|---------------|------------------|
| ✅ Both overridden correctly | Content match → true | Same value for equal objects | Duplicates correctly detected! ✅ |
| ❌ Only `equals()` overridden | Content match → true | Different (default Object hashCode) | **Duplicate stored!** Different bucket → `equals()` never called! ❌ |
| ❌ Only `hashCode()` overridden | Reference check (default) | Same value | Same bucket par jaayega, `equals()` false return karega → **Duplicate stored!** ❌ |
| ❌ Neither overridden | Reference check | Random memory address | Logical duplicates both stored! ❌ |

---

## 4. Critical Examples

### Example 1: String Duplicates (Works Perfectly)
```java
HashSet<String> set = new HashSet<>();
set.add("Java");
set.add("Python");
set.add("Java");    // Duplicate — silently ignored!

System.out.println(set.size()); // 2
System.out.println(set);        // [Java, Python] (order not guaranteed)
```
> **Why it works:** `String` class already `equals()` aur `hashCode()` both override karti hai content-based comparison ke liye.

### Example 2: Custom Object WITHOUT Override (Bug!)
```java
class Student {
    int id;
    Student(int id) { this.id = id; }
}

HashSet<Student> set = new HashSet<>();
set.add(new Student(101));
set.add(new Student(101)); // Same logical data but DIFFERENT objects!

System.out.println(set.size()); // 2 ← ❌ WRONG! Both added because default equals() checks reference!
```

### Example 3: Custom Object WITH Correct Override (Fixed!)
```java
class Student {
    int id;
    Student(int id) { this.id = id; }

    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (obj == null || getClass() != obj.getClass()) return false;
        Student other = (Student) obj;
        return this.id == other.id;
    }

    @Override
    public int hashCode() {
        return Integer.hashCode(id); // Same id → Same hashCode → Same bucket → equals() called!
    }
}

HashSet<Student> set = new HashSet<>();
set.add(new Student(101));
set.add(new Student(101)); // Duplicate correctly detected!

System.out.println(set.size()); // 1 ← ✅ CORRECT!
```

---

## 5. HashSet Constructors

| Constructor | Default Capacity | Load Factor |
|-------------|------------------|-------------|
| `HashSet()` | 16 | 0.75 |
| `HashSet(int initialCapacity)` | Custom | 0.75 |
| `HashSet(int initialCapacity, float loadFactor)` | Custom | Custom |
| `HashSet(Collection c)` | Based on collection size | 0.75 |

### Load Factor / Fill Ratio Kya Hai?

```text
Load Factor = Threshold ratio jab rehashing (resize + rehash) trigger hoti hai

Default Load Factor = 0.75 (75%)
Meaning: Jab HashSet 75% filled ho jaaye → internally HashMap apni capacity DOUBLE kar deta hai
         aur saare existing elements ko naye buckets me rehash karta hai.

Example:
Default Capacity = 16 buckets
Threshold = 16 × 0.75 = 12 elements
Jab 13th element add hoga → Capacity doubled to 32 + rehashing!
```

> ⚠️ **High Load Factor** (e.g., 0.9) → Memory save, lekin hash collisions zyada → Slow search  
> ⚠️ **Low Load Factor** (e.g., 0.5) → Fast operations, lekin memory waste zyada

---

## 6. LinkedHashSet — Insertion Order Preserved!

**LinkedHashSet** = HashSet + **Doubly Linked List** jo insertion order maintain karti hai.

```java
LinkedHashSet<String> lhs = new LinkedHashSet<>();
lhs.add("C");
lhs.add("A");
lhs.add("B");
lhs.add("A"); // Duplicate — ignored!

System.out.println(lhs); // [C, A, B] ← Insertion order preserved! ✅
```

### HashSet vs LinkedHashSet:

| Feature | HashSet | LinkedHashSet |
|---------|---------|---------------|
| **Order** | ❌ No guaranteed order | ✅ Insertion order preserved |
| **Internal Structure** | HashMap | HashMap + Doubly Linked List |
| **Performance** | Slightly faster (no linked list overhead) | Slightly slower (extra pointers maintain karne padte hain) |
| **Memory** | Less | More (extra prev/next pointers per entry) |
| **Null Allowed?** | ✅ One null | ✅ One null |

---

## 🧠 Interview Quick Traps

| Trap | Answer |
|------|--------|
| HashSet internally kaunsa data structure use karta hai? | **HashMap** (elements as keys, dummy object as value). |
| HashSet me duplicate detection ka order kya hai? | **Pehle `hashCode()`** (bucket find karo), **phir `equals()`** (content match karo). |
| Sirf `equals()` override karein bina `hashCode()` ke — kya duplicates detect honge? | ❌ **Nahi!** Different hashCode → different bucket → `equals()` kabhi call hi nahi hoga! |
| HashSet me kitne null elements allowed hain? | Maximum **1 null** element. |
| LinkedHashSet me insertion order maintain hoti hai? | ✅ **Haan!** |
| HashSet ka default initial capacity kitni hai? | **16** (not 10 like ArrayList!) |

---

[⬅️ Previous: Cursors](./06-cursors.md) · [📖 Back to Collections Index](./README.md) · [Next → SortedSet & TreeSet ➡️](./08-sortedset-and-treeset.md)
