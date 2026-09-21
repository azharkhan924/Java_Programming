# ⚡ Core Java — Quick Revision Cheat Sheet

---

## 📦 Arrays

```text
→ Indexed collection of same type
→ Invalid index = ArrayIndexOutOfBoundsException
→ Default values: int=0, boolean=false, references=null
→ int[] println = [I@hashcode | char[] println = characters
→ new int[3][] = valid (jagged array)
→ NPE only when uninitialized row is dereferenced
→ Anonymous: new int[]{10,20} (no size!)
```

---

## 🔢 Variables

```text
Local Variable → must initialize before use
Instance Variable → default value (0, false, null, '\u0000')
Static Variable → class-level, shared, ClassName.variable
```

```text
byte → short → int → long → float → double
(Widening order — implicit conversion allowed →)
(← Narrowing — explicit cast required)
```

```text
Integer literal default = int (use L for long)
Floating literal default = double (use f for float)
```

---

## 🏗️ Constructors

```text
→ No return type (not even void)
→ Same name as class
→ Invoked during object creation

Default Constructor → compiler-provided (when none written)
No-Arg Constructor → programmer-written
Parameterized Constructor → with parameters

this() → same class constructor | super() → parent constructor
→ Must be FIRST statement | Only ONE per constructor
```

```text
Execution Order:
Instance Block → Constructor
Parent IB → Parent Ctor → Child IB → Child Ctor
```

---

## ⚡ Static

```text
static variable → class-level / shared
static method → no object needed, cannot access instance directly
static block → class initialization time, multiple OK
static import → use static members without class name
static nested class → nested only, no outer object needed
→ this ❌ | super ❌ in static context
```

---

## 🧬 Inheritance & Final

```text
Supported: Single, Multilevel, Hierarchical
Not Supported: Multiple class inheritance (use interfaces)

final variable → cannot reassign
final method → cannot override
final class → cannot extend (e.g., String)
blank final → must be explicitly assigned (not auto 0)
```

---

## 🧩 Abstract & Interface

```text
Abstract Class:
→ Cannot instantiate
→ May have ZERO abstract methods
→ Class with abstract method MUST be abstract

Interface:
→ Cannot instantiate
→ Normal methods = public abstract (implicit)
→ Java 8: default + static methods allowed
→ Variables = public static final (implicit)
→ Implementation cannot reduce access
```

---

## 🎭 Polymorphism

```text
OVERLOADING (Compile-time)
→ Same class + same name + different params
→ Return type alone ≠ overloading
→ Priority: exact > widening > boxing > varargs

OVERRIDING (Runtime)
→ Parent-Child + same signature + instance method
→ Actual object decides implementation
→ Cannot reduce access

HIDING
→ Static methods → reference type decides
→ Compile-time dispatch
```

---

## 🔐 Access Modifiers

```text
private   → same class
default   → same package
protected → same package + subclass
public    → everywhere

Overriding: can maintain/increase access, CANNOT reduce
```

---

## 📢 Varargs

```text
Syntax: dataType... name (Java 5+)
→ Zero or more arguments
→ Must be LAST parameter
→ Only ONE per method
→ Lowest overloading priority
→ Cannot coexist with array overload
```

---

## 🔀 Control Statements

```text
if → condition MUST be boolean (0 ≠ false in Java)
switch → byte, short, char, int, String(7+), enum
       → NOT: long, float, double, boolean
       → No duplicate case/default
```

---

## 🔁 Loops

```text
while, do-while, for, for-each
for → exactly 2 semicolons | for(;;) = infinite
for-each → for(type x : array/collection)
break → exits loop/switch
continue → skips iteration (loop only)
Constant true + code after = unreachable ❌
Variable true + code after = allowed ✅
```

---

## 📥 I/O

```text
System.in → InputStreamReader → BufferedReader
BufferedReader: read()=int, readLine()=String
StringTokenizer: nextToken(), countTokens(), hasMoreTokens()
Scanner: next()=token, nextInt()=int, nextLine()=line
→ InputMismatchException on invalid nextInt()
```

---

## 🚀 main() Method

```text
public static void main(String[] args)
public  → JVM accessible
static  → no object needed
void    → no return
args    → String[] command-line arguments
```

```text
System.out.println():
System(class) → out(static PrintStream) → println()(method)
```

---

## 🧠 40 Must-Remember Facts

1. Local variable → no default value, must initialize
2. Instance variable → default value automatically
3. `char` default = `'\u0000'` (not space)
4. `static` variable cannot be local
5. Integer literal = `int`, floating literal = `double`
6. `long` needs `L` suffix, `float` needs `f` suffix
7. `long → float` widening but precision loss possible
8. Constructor has no return type
9. Default constructor only when none is written
10. `this()` / `super()` must be first statement
11. Instance block executes before constructor
12. Static block at class initialization
13. `this` / `super` not available in static context
14. Multiple class inheritance ❌, multiple interfaces ✅
15. `final` variable cannot reassign
16. `final` method cannot override
17. `final` class cannot extend
18. Blank final ≠ automatic 0
19. `private` methods are not inherited
20. Abstract class may have zero abstract methods
21. Interface methods are implicitly `public abstract`
22. `default` methods in interfaces — Java 8
23. Cannot reduce access when overriding
24. Overloading = compile-time = parameter list
25. Overriding = runtime = actual object
26. Static methods hide, not override
27. Varargs = last parameter, only one
28. `if(0)` invalid in Java — boolean required
29. `String` in switch — Java 7
30. `long/float/double/boolean` not in switch
31. `for` loop = exactly 2 semicolons
32. `for-each` = arrays + Iterable
33. `break` exits loop/switch
34. `continue` skips iteration (loop only)
35. `BufferedReader.read()` returns `int`
36. `Scanner.next()` reads one token
37. `main()` is static → no object needed
38. `System.out` is `PrintStream`
39. Command-line args are always `String`
40. `%n` = platform-independent line separator

---

[📖 Back to Core Java Index](./README.md)
