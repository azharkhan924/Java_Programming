# Advanced Java --- Object Class, toString(), hashCode(), equals(), Type Casting & instanceof

## 1. Object Class

`Object` Java ki **root / top-most superclass** hai.

-   Package: `java.lang`
-   Har Java class directly ya indirectly `Object` class ko inherit
    karti hai.
-   Isliye `Object` ke methods har Java object ko available hote hain.

``` java
class A {
}
```

Conceptually:

``` text
Object
  ↑
  A
```

Agar hum explicitly `extends` nahi likhte, tab bhi class indirectly
`Object` ko inherit karti hai.

------------------------------------------------------------------------

# 2. Object Class ke Methods

Object class mein commonly discussed **12 methods** hain:

  No.   Method                            Important Modifier
  ----- --------------------------------- ----------------------------
  1     `getClass()`                      `public final native`
  2     `hashCode()`                      `public native`
  3     `equals(Object obj)`              `public`
  4     `clone()`                         `protected native`
  5     `toString()`                      `public`
  6     `notify()`                        `public final native`
  7     `notifyAll()`                     `public final native`
  8     `wait(long timeout)`              `public final`
  9     `wait(long timeout, int nanos)`   `public final`
  10    `wait()`                          `public final`
  11    `finalize()`                      `protected` --- deprecated
  12    `registerNatives()`               `private static native`

### Modifier-based revision

-   **Public:** 9 methods
-   **Protected:** 2 methods
-   **Private:** 1 method
-   **Final:** 6 methods
-   **Normally overridable:** 5 methods

The 5 overridable methods are:

``` text
hashCode()
equals()
clone()
toString()
finalize()
```

> `finalize()` is deprecated and should not be used in new code. It has
> been deprecated for removal in modern Java.

### Important correction

`wait(long, int)` Java-level declaration `native` nahi hai; it is a
`public final` method that performs argument checking and ultimately
coordinates with the JVM's waiting mechanism.

------------------------------------------------------------------------

# 3. getClass()

Syntax:

``` java
public final native Class<?> getClass()
```

`getClass()` runtime par object ki actual class ka `Class` object return
karta hai.

Example:

``` java
class A {
}

A a1 = new A();

System.out.println(a1.getClass());
```

Output conceptually:

``` text
class A
```

### Method Chaining

``` java
a1.getClass().getName();
```

Yahan:

``` text
a1
 ↓
getClass()
 ↓
Class object
 ↓
getName()
 ↓
"A"
```

Isse **method chaining** kehte hain.

------------------------------------------------------------------------

# 4. Printing an Object

Suppose:

``` java
class A {
}

A a1 = new A();

System.out.println(a1);
```

Humne `a1` ko directly print kiya.

Internally:

``` java
System.out.println(a1);
```

roughly object ko `String` representation mein convert karta hai, aur
object ke case mein `toString()` use hota hai.

Conceptually:

``` java
System.out.println(a1.toString());
```

Object class ki default `toString()` implementation:

``` text
ClassName@hexadecimalHashCode
```

Example:

``` text
A@5e2de80c
```

Yahan:

``` text
A          → class name
@          → separator
5e2de80c   → hashCode ka hexadecimal representation
```

------------------------------------------------------------------------

# 5. Object Class ki toString() ki Internal Working

Object class ki default implementation conceptually:

``` java
public String toString() {
    return getClass().getName() + "@" +
           Integer.toHexString(hashCode());
}
```

Isliye:

``` java
A a1 = new A();

System.out.println(a1);
```

Output:

``` text
A@someHexValue
```

### Important point

Ye output generally object ke meaningful data ko represent nahi karta.

Agar hume meaningful output chahiye, to `toString()` ko override kar
sakte hain.

------------------------------------------------------------------------

# 6. toString() Override

Example:

``` java
class Employee {
    int id;
    String name;

    Employee(int id, String name) {
        this.id = id;
        this.name = name;
    }

    @Override
    public String toString() {
        return "Employee{id=" + id + ", name='" + name + "'}";
    }
}
```

Now:

``` java
Employee e = new Employee(101, "Azhar");

System.out.println(e);
```

Output:

``` text
Employee{id=101, name='Azhar'}
```

Instead of:

``` text
Employee@6d03e736
```

### Why override toString()?

**Meaningful object representation display karne ke liye.**

------------------------------------------------------------------------

# 7. hashCode()

Object class mein:

``` java
public native int hashCode()
```

`hashCode()` object ke liye ek `int` value return karta hai.

Example:

``` java
A a1 = new A();

System.out.println(a1.hashCode());
```

Output:

``` text
1577213552
```

Actual number JVM/runtime ke according different ho sakta hai.

### Important correction

Ye kehna ki:

> "JVM har object ko ek unique number assign karti hai aur wahi hashCode
> hai."

**Strictly correct nahi hai.**

Correct concept:

-   `hashCode()` ek `int` value return karta hai.
-   Different objects ka same hash code **possible** hai.
-   Same object ka hash code, ek execution ke dauran, consistent rehna
    chahiye jab tak equality-relevant information change na ho.
-   Hash code ko object ki guaranteed unique identity nahi samajhna
    chahiye.

------------------------------------------------------------------------

# 8. toString() ka Hexadecimal aur hashCode() ka Decimal

Suppose:

``` java
A a1 = new A();

System.out.println(a1);
System.out.println(a1.hashCode());
```

Output example:

``` text
A@5e2de80c
1580066828
```

Yahan:

``` text
1580066828
```

hash code ka decimal representation hai.

Aur:

``` text
5e2de80c
```

usi hash code ka hexadecimal representation ho sakta hai.

Object ki default `toString()` implementation internally:

``` java
Integer.toHexString(hashCode())
```

use karti hai.

### Therefore

Same object ke liye:

``` java
a1.hashCode()
```

aur default:

``` java
a1.toString()
```

mein jo hexadecimal part hota hai, woh corresponding hash code ka
hexadecimal form hota hai.

------------------------------------------------------------------------

# 9. toString(), hashCode() aur equals() ka Relationship

In teen methods ko ek saath samjho:

``` text
Object
 ├── toString()
 ├── hashCode()
 └── equals()
```

-   `toString()` → object ki string representation
-   `hashCode()` → hash value
-   `equals()` → logical equality check

Agar hum `equals()` override karte hain, to normally `hashCode()` ko bhi
override karna chahiye.

------------------------------------------------------------------------

# 10. equals() Method

Object class:

``` java
public boolean equals(Object obj)
```

Object class ki default `equals()` method essentially **reference
identity** compare karti hai.

Conceptually:

``` java
this == obj
```

Example:

``` java
A a1 = new A();
A a2 = new A();

System.out.println(a1.equals(a2));
```

Output:

``` text
false
```

Because:

``` text
a1 → Object 1
a2 → Object 2
```

Dono alag objects hain.

But:

``` java
A a1 = new A();
A a2 = a1;

System.out.println(a1.equals(a2));
```

Output:

``` text
true
```

Because both references same object ko point kar rahe hain.

------------------------------------------------------------------------

# 11. == vs equals()

## `==` Operator

Objects ke case mein `==` **reference comparison** karta hai.

``` java
String s1 = new String("Java");
String s2 = new String("Java");

System.out.println(s1 == s2);
```

Output:

``` text
false
```

Because references different objects ko point kar rahe hain.

------------------------------------------------------------------------

## String ka equals()

`String` class ne `equals()` ko override kiya hai.

Isliye:

``` java
System.out.println(s1.equals(s2));
```

Output:

``` text
true
```

Because `String.equals()` content compare karta hai.

### Quick Table

  -----------------------------------------------------------------------
  Expression                          Comparison
  ----------------------------------- -----------------------------------
  `s1 == s2`                          Reference comparison

  `s1.equals(s2)`                     String content comparison

  `obj1.equals(obj2)` using Object's  Reference/identity comparison
  implementation                      
  -----------------------------------------------------------------------

------------------------------------------------------------------------

# 12. Meaningful equals() --- Employee Example

Suppose:

``` java
class Employee {
    int id;
    String name;

    Employee(int id, String name) {
        this.id = id;
        this.name = name;
    }
}
```

Agar hum chahte hain ki same `id` wale Employees ko equal maana jaye, to
`equals()` override kar sakte hain.

``` java
@Override
public boolean equals(Object o) {

    if (this == o)
        return true;

    if (!(o instanceof Employee))
        return false;

    Employee e = (Employee) o;

    return this.id == e.id;
}
```

Ab:

``` java
Employee e1 = new Employee(101, "Azhar");
Employee e2 = new Employee(101, "Rahul");

System.out.println(e1.equals(e2));
```

Output:

``` text
true
```

Because comparison `id` ke basis par ho rahi hai.

------------------------------------------------------------------------

# 13. equals() mein Object Parameter kyun?

Method declaration:

``` java
public boolean equals(Object o)
```

Parameter `Object` hai because `Object` Java ki root class hai.

Therefore kisi bhi object ko argument ke roop mein receive kiya ja sakta
hai.

Example:

``` java
e1.equals(e2);
```

Internally:

``` text
Employee object
      ↓
Object reference parameter
      ↓
Object ko Employee mein cast
```

------------------------------------------------------------------------

# 14. Object Reference aur Employee Object

Suppose:

``` java
class Employee {
    int id;
}
```

We can write:

``` java
Object o = new Employee();
```

Ye valid hai because:

``` text
Object
  ↑
Employee
```

**Parent class ka reference child class ke object ko hold kar sakta
hai.**

But:

``` java
Employee e = new Object();
```

Ye directly valid nahi hai.

Because every `Object` necessarily `Employee` nahi hota.

------------------------------------------------------------------------

# 15. Object Reference se Employee-specific Member Access

Suppose:

``` java
Object o = new Employee();
```

Now:

``` java
System.out.println(o.id);
```

Compile-time error:

``` text
cannot find symbol
```

### Why?

Compile-time checking **reference variable ke type** ke according hoti
hai.

Reference variable:

``` java
Object o
```

`Object` class mein `id` variable nahi hai.

Compiler sirf reference type ki accessible members ko dekhta hai.

------------------------------------------------------------------------

# 16. Downcasting

Agar actual object `Employee` hai:

``` java
Object o = new Employee();
```

To hum cast kar sakte hain:

``` java
Employee e = (Employee) o;

System.out.println(e.id);
```

Ab compiler ko pata hai:

``` text
e → Employee reference
```

Isliye:

``` java
e.id
```

valid hai.

### Important

Casting reference ka **declared/reference type** change karti hai;
object ko physically convert nahi karti.

------------------------------------------------------------------------

# 17. Compile Time vs Runtime --- Very Important

Java mein object casting samajhne ke liye ye rule yaad rakho:

### Compile Time

Compiler mainly dekhta hai:

``` text
Reference variable ka type
```

### Runtime

JVM dekhti hai:

``` text
Actual object ka type
```

Therefore:

``` text
Compile time → Reference type
Runtime      → Actual object
```

------------------------------------------------------------------------

# 18. Upcasting

Parent reference → Child object:

``` java
A a1 = new B();
```

Agar:

``` text
B extends A
```

to ye valid hai.

Isse **upcasting** kehte hain.

``` text
A
↑
B
```

Child object ko parent reference mein store kiya gaya.

Upcasting generally implicit hoti hai:

``` java
A a1 = new B();
```

No explicit cast required.

------------------------------------------------------------------------

# 19. Downcasting

Parent reference → Child reference:

``` java
A a1 = new B();

B b1 = (B) a1;
```

Ye explicit cast hai.

Agar actual object genuinely `B` hai, to casting successful hogi.

------------------------------------------------------------------------

# 20. Class Hierarchy Example

Assume:

``` java
class A {
}

class B extends A {
}

class C extends A {
}
```

Diagram:

``` text
          A
         / \
        B   C
```

Yahan:

-   A → parent
-   B → child of A
-   C → child of A
-   B and C → sibling classes

------------------------------------------------------------------------

# 21. Type Casting ke Important Cases

## Case 1

``` java
B b1 = new A();
```

❌ Compile-time error.

Reason:

``` text
Parent object → Child reference
```

without explicit cast allowed nahi hai.

------------------------------------------------------------------------

## Case 2

``` java
C c1 = new A();
```

❌ Compile-time error.

Same reason.

------------------------------------------------------------------------

## Case 3

``` java
C c1 = new B();
```

❌ Compile-time error.

B aur C sibling classes hain.

``` text
       A
      / \
     B   C
```

B ko directly C nahi maana ja sakta.

------------------------------------------------------------------------

## Case 4

``` java
A a1 = new A();

B b1 = (B) a1;
```

Compilation:

``` text
✓
```

Runtime:

``` text
ClassCastException
```

Reason:

Actual object:

``` text
new A()
```

Hai, `B` object nahi hai.

------------------------------------------------------------------------

## Case 5

``` java
A a1 = new B();

B b1 = (B) a1;
```

Compilation:

``` text
✓
```

Runtime:

``` text
✓
```

Because actual object `B` hai.

------------------------------------------------------------------------

## Case 6

``` java
A a1 = new C();

B b1 = (B) a1;
```

Compilation:

``` text
✓
```

Runtime:

``` text
ClassCastException
```

Why?

Reference type:

``` text
A
```

Casting relationship ko compile-time par possible banata hai.

But actual object:

``` text
C
```

hai.

C ko B mein cast nahi kar sakte.

------------------------------------------------------------------------

# 22. ClassCastException

`ClassCastException` ek:

``` text
RuntimeException
```

hai.

Isliye ye **unchecked exception** hai, checked exception nahi.

Example:

``` java
A a1 = new A();

B b1 = (B) a1;
```

Runtime par:

``` text
ClassCastException
```

because actual object `A` hai aur hum usko `B` samajhne ki koshish kar
rahe hain.

### Important

ClassCastException tab aa sakta hai jab runtime object requested target
type ke compatible nahi hota.

Sirf "superclass ka object subclass reference mein store kar diya" kehna
incomplete explanation hai; actual runtime type compatibility important
hai.

------------------------------------------------------------------------

# 23. Type Casting ka Golden Rule

``` text
Casting generally parent-child relationship ke context mein possible hoti hai.
```

But successful downcasting ke liye:

``` text
Actual object must be an instance of
the target child type.
```

Example:

``` java
A a = new B();
B b = (B) a;   // ✓
```

But:

``` java
A a = new C();
B b = (B) a;   // ✗ runtime
```

------------------------------------------------------------------------

# 24. Method Overriding --- Reference Type vs Object Type

Assume:

``` java
class A {
    void show() {
        System.out.println("A");
    }
}

class B extends A {
    @Override
    void show() {
        System.out.println("B");
    }
}
```

Now:

``` java
A a1 = new B();

a1.show();
```

Output:

``` text
B
```

### Why?

Method overriding mein:

``` text
Compile time → reference type determines whether method is accessible
Runtime      → actual object determines overridden implementation
```

Therefore:

``` text
A a1 = new B();

a1.show();
```

Reference `A` hai, but actual object `B` hai.

So `B.show()` execute hota hai.

This is **runtime polymorphism / dynamic method dispatch**.

------------------------------------------------------------------------

# 25. Agar Parent Class mein Method hi Nahi Hai?

Suppose:

``` java
class A {
}

class B extends A {
    void show() {
        System.out.println("B");
    }
}
```

Now:

``` java
A a1 = new B();

a1.show();
```

❌ Compile-time error:

``` text
cannot find symbol
```

### Why?

Compiler reference type dekhta hai:

``` java
A a1
```

A mein `show()` available nahi hai.

Even though actual object `B` hai, compiler direct `B` ke extra methods
ko parent reference ke through access nahi karne deta.

------------------------------------------------------------------------

# 26. Parent mein Method Hai, Child mein Override Nahi Kiya

``` java
class A {
    void show() {
        System.out.println("A");
    }
}

class B extends A {
}
```

Now:

``` java
A a1 = new B();

a1.show();
```

Output:

``` text
A
```

Because B ne `show()` override nahi kiya; B inherited implementation use
karega.

------------------------------------------------------------------------

# 27. Method Name Different Hone par

Suppose:

``` java
class A {
    void show() {
        System.out.println("A");
    }
}

class B extends A {
    void display() {
        System.out.println("B");
    }
}
```

Now:

``` java
A a1 = new B();

a1.display();
```

❌ Compile-time error.

Because `A` reference se `display()` accessible nahi hai.

------------------------------------------------------------------------

# 28. instanceof Operator

`instanceof` ka use check karne ke liye hota hai ki koi object/reference
runtime par kisi given type ka instance hai ya nahi.

Syntax:

``` java
object instanceof ClassName
```

Result:

``` text
true / false
```

Example:

``` java
A a1 = new B();

System.out.println(a1 instanceof A);
System.out.println(a1 instanceof B);
```

Output:

``` text
true
true
```

Because actual object `B` hai, aur `B` is also an `A`.

------------------------------------------------------------------------

# 29. instanceof --- A, B, C Example

Hierarchy:

``` text
        A
       / \
      B   C
```

Assume:

``` java
A a1 = new A();
B b1 = new B();
C c1 = new C();
```

### For `a1`

``` java
a1 instanceof A   // true
a1 instanceof B   // false
a1 instanceof C   // false
```

### For `b1`

``` java
b1 instanceof A   // true
b1 instanceof B   // true
b1 instanceof C   // compile-time error
```

Because B and C sibling classes hain.

### For `c1`

``` java
c1 instanceof A   // true
c1 instanceof B   // compile-time error
c1 instanceof C   // true
```

------------------------------------------------------------------------

# 30. instanceof ka Internal Logic

Conceptually JVM/runtime ye check karta hai:

``` text
Actual object ka runtime type
          ↓
Kya requested type ka instance hai?
          ↓
       true / false
```

Example:

``` java
A a1 = new B();

a1 instanceof B
```

Reference type `A` hai, but actual object `B` hai.

Therefore:

``` text
true
```

------------------------------------------------------------------------

# 31. null instanceof --- Very Important

``` java
Object o = null;

System.out.println(o instanceof Object);
```

Output:

``` text
false
```

Similarly:

``` java
null instanceof String
```

always:

``` text
false
```

### Why?

`instanceof` actual object ki type check karta hai.

But `null` kisi object ko refer nahi karta.

Therefore:

``` text
null instanceof AnyReferenceType
→ false
```

------------------------------------------------------------------------

# 32. instanceof aur equals() mein Null Problem

Suppose equals method:

``` java
@Override
public boolean equals(Object o) {

    Employee e = (Employee) o;

    return this.id == e.id;
}
```

Now:

``` java
Employee e1 = new Employee(101, "Azhar");

System.out.println(e1.equals(null));
```

Casting:

``` java
(Employee) null
```

technically allowed hai aur result `null` hota hai.

But next line:

``` java
e.id
```

access karne par:

``` text
NullPointerException
```

aa sakta hai.

------------------------------------------------------------------------

# 33. instanceof se Null Problem Solve Karna

Better implementation:

``` java
@Override
public boolean equals(Object o) {

    if (this == o)
        return true;

    if (!(o instanceof Employee))
        return false;

    Employee e = (Employee) o;

    return this.id == e.id;
}
```

Suppose:

``` java
e1.equals(null)
```

Then:

``` java
null instanceof Employee
```

returns:

``` text
false
```

So method:

``` java
return false;
```

kar dega.

No `NullPointerException`.

------------------------------------------------------------------------

# 34. equals() ki Step-by-Step Internal Working

Consider:

``` java
Employee e1 = new Employee(101, "Azhar");
Employee e2 = new Employee(101, "Rahul");

e1.equals(e2);
```

### Step 1 --- `this`

Calling object:

``` text
e1
```

So:

``` java
this
```

means `e1`.

### Step 2 --- Parameter

``` java
Object o
```

contains reference to:

``` text
e2
```

### Step 3 --- instanceof

``` java
o instanceof Employee
```

checks whether the actual object is an Employee.

### Step 4 --- Type Casting

``` java
Employee e = (Employee) o;
```

Now we can access Employee-specific fields.

### Step 5 --- Compare Data

``` java
return this.id == e.id;
```

If both IDs are same:

``` text
true
```

Otherwise:

``` text
false
```

------------------------------------------------------------------------

# 35. equals() ka Recommended Structure

A common safe pattern:

``` java
@Override
public boolean equals(Object o) {

    if (this == o)
        return true;

    if (!(o instanceof Employee))
        return false;

    Employee other = (Employee) o;

    return this.id == other.id;
}
```

For a `String` field:

``` java
return Objects.equals(this.name, other.name);
```

instead of using `==`.

------------------------------------------------------------------------

# 36. equals() Override karte waqt hashCode() bhi Override karo

Java ka important contract:

``` text
If two objects are equal according to equals(),
then their hashCode() values must be equal.
```

Example:

``` java
@Override
public int hashCode() {
    return Integer.hashCode(id);
}
```

Complete example:

``` java
class Employee {

    int id;
    String name;

    Employee(int id, String name) {
        this.id = id;
        this.name = name;
    }

    @Override
    public boolean equals(Object o) {

        if (this == o)
            return true;

        if (!(o instanceof Employee))
            return false;

        Employee other = (Employee) o;

        return this.id == other.id;
    }

    @Override
    public int hashCode() {
        return Integer.hashCode(id);
    }

    @Override
    public String toString() {
        return "Employee{id=" + id + ", name='" + name + "'}";
    }
}
```

------------------------------------------------------------------------

# 37. Object Class --- Quick Revision

``` text
Object
  ↓
Java ki root class
  ↓
java.lang package
  ↓
12 commonly discussed methods
```

### Important Methods

``` text
getClass()
hashCode()
equals()
clone()
toString()
notify()
notifyAll()
wait()
wait(long)
wait(long, int)
finalize()
registerNatives()
```

### Most Important for Interviews

``` text
toString()
equals()
hashCode()
getClass()
```

------------------------------------------------------------------------

# 38. Modifier Revision

``` text
Public    → 9
Protected → 2
Private   → 1
Final     → 6
```

The 5 methods that are not `final` are:

``` text
hashCode()
equals()
clone()
toString()
finalize()
```

`registerNatives()` is private, so subclass directly override nahi kar
sakti.

------------------------------------------------------------------------

# 39. One-Page Concept Map

``` text
                         Object
                           |
        ┌──────────────────┼──────────────────┐
        ↓                  ↓                  ↓
    toString()         hashCode()          equals()
        |                  |                  |
 meaningful          hash value         equality check
 representation                            |
        |                                   |
   override it                         Object's default
                                      → identity/reference
                                      String overrides it
                                      → content comparison
```

Casting:

``` text
             A
            / \
           B   C

A a = new B();       → Upcasting ✓

B b = (B) a;         → Downcasting ✓
                       if actual object is B

A a = new C();
B b = (B) a;         → Runtime ClassCastException
```

------------------------------------------------------------------------

# 40. Final Rules to Remember

### Rule 1

``` text
Every class directly/indirectly extends Object.
```

### Rule 2

``` text
Object reference can hold any object.
```

Example:

``` java
Object o = new Employee();
```

### Rule 3

``` text
Reference type controls compile-time accessibility.
```

### Rule 4

``` text
Actual object controls overridden method execution at runtime.
```

### Rule 5

``` text
Parent reference → Child object = Upcasting
```

### Rule 6

``` text
Parent reference → Child reference = Downcasting
```

Downcasting successful tabhi hoga jab actual object target child type ka
ho.

### Rule 7

``` text
ClassCastException = RuntimeException = unchecked exception
```

### Rule 8

``` text
null instanceof AnyType → false
```

### Rule 9

``` text
If equals() is overridden,
hashCode() should also be overridden.
```

### Rule 10

``` text
== for objects → reference comparison
Object.equals() → identity/reference comparison
String.equals() → content comparison
```

------------------------------------------------------------------------

# 41. Interview-Friendly Short Summary

**Object class** Java ki root class hai aur `java.lang` package mein
hoti hai.

Default `toString()`:

``` text
ClassName@hexadecimalHashCode
```

return karti hai.

`hashCode()` integer hash value return karta hai. Hash code guaranteed
unique nahi hota.

Object class ki default `equals()` identity/reference equality check
karti hai, while `String` apni `equals()` implementation se content
compare karta hai.

``` text
==          → reference comparison
equals()    → depends on class's implementation
```

Compile-time par compiler reference type ko dekhta hai, while overridden
method ka actual implementation runtime object decide karta hai.

``` java
A a = new B();
a.show();
```

Agar `B` ne `show()` override kiya hai, to:

``` text
B.show()
```

execute hoga.

Casting mein:

``` java
A a = new B();
B b = (B) a;
```

valid hai.

But:

``` java
A a = new C();
B b = (B) a;
```

runtime par:

``` text
ClassCastException
```

dega.

`instanceof` runtime object ki type compatibility check karne mein
useful hai, aur:

``` java
null instanceof AnyClass
```

always `false` hota hai.
