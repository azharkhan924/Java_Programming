# Java Notes --- Variables, Type Casting, Constructors & Static

> **Style:** Hinglish + English, short-note/revision friendly\
> **Focus:** Java basics + interview-important concepts

------------------------------------------------------------------------

## 1. Variable Declaration & Initialization

### C++ vs Java

``` cpp
// C++
int x;
```

In C++, a local variable declared without initialization has an
**indeterminate value** (commonly called a garbage value). Using it
before initialization is unsafe/undefined behavior.

In Java:

``` java
int x;
System.out.println(x);
```

Compiler error:

``` text
variable x might not have been initialized
```

### Important Point

Java me **local variable ko use karne se pehle definitely initialize
karna compulsory hai.**

``` java
int x = 10;
System.out.println(x);   // valid
```

------------------------------------------------------------------------

## 2. Variable ko Same Scope me Re-declare Karna

``` java
int x = 10;
int x = 20;
```

Error:

``` text
variable x is already defined in method main
```

Same scope me same local variable ko dobara declare nahi kar sakte.

------------------------------------------------------------------------

# 3. Integer Data Types & Range

Java me integer types:

  Data Type        Size Range
  ----------- --------- -------------------
  `byte`         1 byte -128 to 127
  `short`       2 bytes -32,768 to 32,767
  `int`         4 bytes -2³¹ to 2³¹-1
  `long`        8 bytes -2⁶³ to 2⁶³-1

### Maximum / Minimum Constants

``` java
Long.MAX_VALUE
Long.MIN_VALUE

Byte.MAX_VALUE
Byte.MIN_VALUE
```

Example:

``` java
System.out.println(Long.MAX_VALUE);
System.out.println(Byte.MIN_VALUE);
```

### Why so many integer data types?

Main reason:

> **Memory ko efficiently use karna.**

Agar small range ka data hai, unnecessarily `long` use karne ki need
nahi hai.

------------------------------------------------------------------------

# 4. Integer Literal ka Important Rule

Java me **integer literal by default `int` hota hai.**

Example:

``` java
long x = 2147483648;
```

Ye error dega, even though `2147483648` `long` ki range ke andar hai.

### Why?

Compiler pehle `2147483648` ko **int literal** ke form me treat karne ki
koshish karta hai.

`int` ki maximum value:

``` text
2147483647
```

Isliye literal valid `int` nahi hai.

### Solution

Literal ke end me `L` ya `l` lagao:

``` java
long x = 2147483648L;
```

**Prefer capital `L`** because lowercase `l` visually `1` jaisa lag
sakta hai.

------------------------------------------------------------------------

# 5. Floating-Point Literal

Java me decimal/point wali floating-point literal by default
**`double`** hoti hai.

``` java
double x = 10.5;
```

Agar:

``` java
float x = 10.5;
```

to error aayegi because `10.5` is a `double` literal.

Correct:

``` java
float x = 10.5f;
```

### Example

``` java
float x = 10;
System.out.println(x);
```

Output:

``` text
10.0
```

`10` is an `int` literal, but assignment to `float` is allowed through
widening conversion.

------------------------------------------------------------------------

# 6. Widening Order of Numeric Types

General widening order:

``` text
byte → short → int → long → float → double
```

Iska matlab:

-   `byte` → `short` ✅
-   `short` → `int` ✅
-   `int` → `long` ✅
-   `long` → `float` ✅
-   `float` → `double` ✅

Reverse direction automatically allowed nahi hai:

-   `double` → `float` ❌
-   `float` → `long` ❌
-   `long` → `int` ❌
-   `int` → `short` ❌
-   `short` → `byte` ❌

Because narrowing conversion me **data loss ka possibility** hota hai.

> Note: `long → float` technically widening conversion hai, but
> floating-point representation ki wajah se precision loss possible ho
> sakta hai.

------------------------------------------------------------------------

# 7. Arithmetic Operation & Numeric Promotion

Example:

``` java
float x = 10;
int y = 3;

System.out.println(x / y);
```

Output:

``` text
3.3333333
```

Result `float` hoga.

If:

``` java
double x = 10;
int y = 3;

System.out.println(x / y);
```

Result `double` hoga.

### Rule --- Binary Numeric Promotion

Arithmetic operation me operands promote hote hain.

Simplified order:

``` text
double > float > long > int
```

Aur:

``` text
byte, short, char
```

generally arithmetic operation ke time `int` me promote ho jaate hain.

So result ka type operand promotion ke according decide hota hai.

------------------------------------------------------------------------

# 8. Negative Numbers --- Two's Complement

Example:

``` text
x = -5
y = -6
```

Agar:

``` java
System.out.println(x & y);
```

to result:

``` text
-6
```

### Process

Negative number ko binary me represent karne ke liye commonly **two's
complement representation** use hoti hai.

Steps:

1.  Positive number ka binary form likho.
2.  Fixed bit size use karo (example: 8-bit).
3.  **1's complement** karo.
4.  Usme `1` add karo → **2's complement**.
5.  MSB = **sign bit**.
    -   `0` → positive
    -   `1` → negative

### Example: -5 in 8-bit

``` text
+5
00000101

1's complement
11111010

+1
11111011
```

So:

``` text
-5 = 11111011
```

### -6

``` text
+6
00000110

1's complement
11111001

+1
11111010
```

So:

``` text
-6 = 11111010
```

### AND

``` text
  11111011   (-5)
& 11111010   (-6)
------------
  11111010
```

`11111010` = `-6`

Therefore:

``` text
-5 & -6 = -6
```

------------------------------------------------------------------------

# 9. ASCII & Unicode

## ASCII

**ASCII = American Standard Code for Information Interchange**

Original ASCII uses **7 bits** and represents **128 characters**:

``` text
0 to 127
```

It mainly covers English letters, digits, punctuation and control
characters.

------------------------------------------------------------------------

## Unicode

Unicode is a universal character standard designed to represent
characters from many writing systems.

Java uses **Unicode** for its character/text representation.

### Important Java point

Java's `char` is:

``` text
16-bit
2 bytes
```

and represents a **UTF-16 code unit** with values:

``` text
0 to 65535
```

> Unicode itself is much larger than `0–65535`; supplementary Unicode
> characters are represented in Java using **surrogate pairs**.

------------------------------------------------------------------------

# 10. Type Casting / Type Conversion

### Definition

**Type casting** means converting a value from one data type to another
compatible data type.

Example:

``` java
int x = 10;
double y = x;
```

Here:

``` text
int → double
```

------------------------------------------------------------------------

## Type Casting ke 2 Main Types

1.  **Implicit Type Casting**
2.  **Explicit Type Casting**

------------------------------------------------------------------------

# 11. Implicit Type Casting

Jab source type ki values/range destination type me safely represent ho
sakti hain, compiler automatically conversion kar deta hai.

Example:

``` java
int x = 10;
long y = x;
```

Here:

``` text
int → long
```

No explicit cast required.

Another example:

``` java
byte b = 10;
int x = b;
```

------------------------------------------------------------------------

# 12. Explicit Type Casting

Jab conversion automatic nahi hota, hume explicitly cast karna padta
hai.

Syntax:

``` java
destinationType variable =
    (destinationType) value;
```

Example:

``` java
double x = 10.5;
int y = (int) x;
```

Output/value:

``` text
y = 10
```

Decimal part lost ho gaya.

### Important

Explicit/narrowing conversion me:

> **Data loss ka possibility hota hai.**

------------------------------------------------------------------------

# 13. Data Type ↔ Object

Java me primitive data types aur objects alag concepts hain.

Primitive:

``` java
int x = 10;
```

Here `int` = primitive data type\
`x` = variable

Reference type:

``` java
String s1 = "10";
```

Here `String` = class/reference type\
`s1` = reference variable\
`"10"` = String object/value

Primitive ko corresponding wrapper object me convert karne ke liye
**boxing/autoboxing** use hota hai.

Example:

``` java
int x = 10;
Integer obj = x;   // autoboxing
```

Object ko primitive me:

``` java
Integer obj = 10;
int x = obj;       // unboxing
```

So primitive ↔ wrapper conversion ko sirf normal primitive type casting
samajhna correct nahi hai; yahan **boxing/unboxing** concepts involved
hain.

------------------------------------------------------------------------

# 14. String → int Conversion

Ye valid nahi hai:

``` java
String s1 = "10";
int x = s1;       // ERROR
```

String ko integer me convert karne ke liye:

``` java
int x = Integer.parseInt(s1);
```

### `Integer.parseInt()`

`Integer` = `int` ki corresponding wrapper class.

`parseInt()` ek static method hai jo numeric String ko primitive `int`
me convert karta hai.

Example:

``` java
String s1 = "100";

int x = Integer.parseInt(s1);

System.out.println(x);
```

Output:

``` text
100
```

### Invalid String

``` java
String s1 = "10abc";

int x = Integer.parseInt(s1);
```

Exception:

``` text
NumberFormatException
```

because String me valid integer representation nahi hai.

------------------------------------------------------------------------

# 15. Primitive Data Types & Wrapper Classes

  Primitive   Wrapper Class
  ----------- ---------------
  `byte`      `Byte`
  `short`     `Short`
  `int`       `Integer`
  `long`      `Long`
  `float`     `Float`
  `double`    `Double`
  `char`      `Character`
  `boolean`   `Boolean`

### Easy Trick

``` text
byte     → Byte
short    → Short
int      → Integer
long     → Long
float    → Float
double   → Double
char     → Character
boolean  → Boolean
```

------------------------------------------------------------------------

# 16. Local Variable vs Instance Variable

## Local Variable

Method/block ke andar declared variable.

``` java
void show() {
    int x;
}
```

Local variable ko use karne se pehle initialize karna compulsory hai.

``` java
int x;
System.out.println(x);  // ERROR
```

------------------------------------------------------------------------

## Instance Variable

Class ke andar but method/block ke bahar declared variable.

``` java
class Student {
    int age;
}
```

Agar instance variable ko explicitly initialize nahi karte, Java usko
**default value** deta hai.

### Default Values

  Type              Default Value
  ----------------- ---------------
  `byte`            `0`
  `short`           `0`
  `int`             `0`
  `long`            `0L`
  `float`           `0.0f`
  `double`          `0.0d`
  `char`            `'\u0000'`
  `boolean`         `false`
  Reference types   `null`

> `char` ki default value actual space character nahi hoti. It is the
> null character `'\u0000'`.

------------------------------------------------------------------------

# 17. Instance Variable Initialization

Instance variable ko initialize karne ke common ways:

### 1. Directly using object

``` java
Student s1 = new Student();

s1.x = 10;
s1.y = 20;
```

### 2. Through a method

``` java
s1.setData(10, 20);
```

### 3. Through Constructor

``` java
Student s1 = new Student(10, 20);
```

Constructor object creation ke time initial values set kar sakta hai.

------------------------------------------------------------------------

# 18. `this` Keyword

`this` current object ka reference hold karta hai.

Example:

``` java
class Student {
    int age;

    void setAge(int age) {
        this.age = age;
    }
}
```

Here:

``` java
this.age
```

= current object ka instance variable

and:

``` java
age
```

= method parameter

### Definition for Revision

> **`this` refers to the current object.**

------------------------------------------------------------------------

# 19. Instance Method

Instance method ko access karne ke liye object required hota hai.

``` java
class Student {

    void display() {
        System.out.println("Hello");
    }
}

Student s1 = new Student();
s1.display();
```

Here:

``` text
s1.display()
```

object ke through method access kiya gaya.

------------------------------------------------------------------------

# 20. Constructor

Constructor ek special member hota hai jo object creation ke time
initialization ke liye use hota hai.

Example:

``` java
class Student {

    Student() {
        System.out.println("Constructor called");
    }
}

Student s1 = new Student();
```

### Constructor ki Properties

-   Constructor ka **return type nahi hota** --- not even `void`.
-   Constructor ka name **class name ke same** hota hai.
-   Object create karte waqt constructor automatically invoke hota hai.
-   Constructor object ko initialize karne ke liye commonly use hota
    hai.
-   Constructor ko `new` ke through object creation ke time invoke kiya
    jata hai.

------------------------------------------------------------------------

# 21. Types of Constructors

### 1. Default Constructor

Agar class me programmer ne **koi constructor declare nahi kiya**,
compiler ek no-argument constructor provide kar sakta hai.

``` java
class Student {
    // no constructor written
}
```

Compiler-generated constructor ko commonly **default constructor** kehte
hain.

------------------------------------------------------------------------

### 2. No-Argument Constructor

Programmer khud bhi no-argument constructor define kar sakta hai:

``` java
class Student {

    Student() {
        System.out.println("No-arg constructor");
    }
}
```

> Important: **Default constructor** aur **no-argument constructor**
> technically same term nahi hain. Default constructor specifically
> compiler-provided constructor ko refer karta hai.

------------------------------------------------------------------------

### 3. Parameterized Constructor

Constructor me parameters pass kiye jaate hain:

``` java
class Student {

    int age;

    Student(int age) {
        this.age = age;
    }
}

Student s1 = new Student(20);
```

------------------------------------------------------------------------

# 22. Constructor vs Method

  -----------------------------------------------------------------------
  Constructor                         Method
  ----------------------------------- -----------------------------------
  Return type nahi hota               Return type hota hai, including
                                      `void`

  Class name ke same name             Method ka name freely choose kar
                                      sakte hain

  Object creation ke time             Generally manually invoke
  automatically invoke                

  Object initialization ke liye       Object/class behaviour define karta
  commonly used                       hai

  `new` ke through object creation se Object/class reference ke through
  associated                          invoke

  Object creation ke liye constructor Same method ko repeatedly call kar
  invocation occurs                   sakte hain
  -----------------------------------------------------------------------

Example:

``` java
class Student {

    Student() {              // Constructor
        System.out.println("Created");
    }

    void display() {         // Method
        System.out.println("Hello");
    }
}

Student s1 = new Student();  // Constructor
s1.display();               // Method
s1.display();               // Method again
```

------------------------------------------------------------------------

# 23. Static Keyword

`static` ka main idea:

> Member ko **class-level** banana, instead of making a separate copy
> for every object.

Static members class se associated hote hain.

------------------------------------------------------------------------

## Static Variable

``` java
class Student {
    static String college = "AITR";
}
```

Agar variable ko all objects ke liye common rakhna hai, `static` use kar
sakte hain.

``` java
Student s1 = new Student();
Student s2 = new Student();

System.out.println(Student.college);
```

Direct class name se access:

``` java
Student.college
```

Object create karna required nahi hai.

------------------------------------------------------------------------

# 24. Static Method

Example:

``` java
class Test {

    static void display() {
        System.out.println("Hello");
    }
}

Test.display();
```

Static method ko direct class name se access kar sakte hain.

``` text
ClassName.methodName()
```

Object create karna required nahi hai.

### Important Rule

Static method directly **static members** ko access kar sakti hai.

It cannot directly access instance members because instance members
object-specific hote hain.

Example:

``` java
class Test {

    int x = 10;
    static int y = 20;

    static void show() {
        System.out.println(y); // valid
        // System.out.println(x); // ERROR
    }
}
```

------------------------------------------------------------------------

# 25. Instance Variable vs Static Variable

## Instance Variable

``` text
Every object → separate instance data
```

Example:

``` java
class Student {
    int age;
}
```

If:

``` java
Student s1 = new Student();
Student s2 = new Student();
```

then `s1.age` and `s2.age` are separate instance variables belonging to
different objects.

### Points

-   Object-specific
-   Each object has its own instance state
-   Accessed through an object
-   Instance fields are part of the object

------------------------------------------------------------------------

## Static Variable

``` text
Class → one shared static member
```

Example:

``` java
class Student {
    static String college = "AITR";
}
```

All objects refer to the same class-level variable.

### Points

-   Class-level
-   Shared by objects
-   Directly accessible using class name
-   Useful when one value should be common for all objects

------------------------------------------------------------------------

# 26. When to Use Static?

Suppose:

``` text
Student 1 → college = AITR
Student 2 → college = AITR
Student 3 → college = AITR
```

College name sabke liye same hai.

To separate copy rakhne ki need nahi.

Use:

``` java
static String college = "AITR";
```

But:

``` text
Student 1 → age = 20
Student 2 → age = 21
Student 3 → age = 19
```

Age object-specific hai.

So:

``` java
int age;
```

not static.

### Shortcut

``` text
Object-specific data → Instance variable
Common/class-level data → Static variable
```

------------------------------------------------------------------------

# 27. Instance Block

Instance block ko **instance initializer block** bhi kehte hain.

Syntax:

``` java
class Student {

    {
        System.out.println("Instance block");
    }

}
```

Jab object create hota hai, instance initializer block execute hota hai.

``` java
Student s1 = new Student();
```

### Important

For a normal object construction flow:

``` text
Instance Initializer Block
        ↓
Constructor
```

Instance initializer block constructor body se pehle execute hota hai.

------------------------------------------------------------------------

# 28. Instance Block --- Uses

Instance block commonly use ho sakta hai:

-   Instance variables ki initialization ke liye
-   Common initialization code ke liye
-   Jo code har object creation par execute karna ho

Example:

``` java
class Student {

    int age;

    {
        age = 20;
        System.out.println("Instance block");
    }

    Student() {
        System.out.println("Constructor");
    }
}
```

Output when object is created:

``` text
Instance block
Constructor
```

------------------------------------------------------------------------

# 29. Instance Block vs Constructor

  -----------------------------------------------------------------------
  Instance Block                      Constructor
  ----------------------------------- -----------------------------------
  Instance initializer block hai      Special constructor member hai

  Constructor body se pehle execute   Instance block ke baad constructor
  hota hai                            body execute hoti hai

  Common initialization code ke liye  Object-specific initialization ke
  useful                              liye useful

  Constructor call ke saath execute   Object creation ke time invoke hota
  hota hai                            hai

  Parameters directly receive nahi    Parameters receive kar sakta hai
  karta                               

  Class me multiple instance blocks   Multiple constructors possible
  ho sakte hain                       through overloading
  -----------------------------------------------------------------------

------------------------------------------------------------------------

# 30. Inheritance me Instance Block Order

Agar parent aur child class hain:

``` text
Parent Instance Initializer
        ↓
Parent Constructor
        ↓
Child Instance Initializer
        ↓
Child Constructor
```

Reason:

> Child object create karne se pehle parent part initialize hota hai.

Example flow:

``` java
class Parent {

    {
        System.out.println("Parent IB");
    }

    Parent() {
        System.out.println("Parent Constructor");
    }
}

class Child extends Parent {

    {
        System.out.println("Child IB");
    }

    Child() {
        System.out.println("Child Constructor");
    }
}
```

``` java
Child c = new Child();
```

Output:

``` text
Parent IB
Parent Constructor
Child IB
Child Constructor
```

------------------------------------------------------------------------

# 31. Quick Revision Map

``` text
Java Variables
│
├── Local Variable
│   └── Must initialize before use
│
├── Instance Variable
│   ├── Object-specific
│   ├── Default value available
│   └── Access through object
│
└── Static Variable
    ├── Class-level
    ├── Shared
    └── Access using class name
```

``` text
Type Conversion
│
├── Implicit / Widening
│   └── Automatic
│
└── Explicit / Narrowing
    ├── Cast required
    └── Data loss possible
```

``` text
Constructor
│
├── Default Constructor
├── No-Argument Constructor
└── Parameterized Constructor
```

``` text
Object Creation
      ↓
Instance Initializer Block
      ↓
Constructor
```

------------------------------------------------------------------------

# 32. Interview Quick Questions

### Q1. Java local variable ko default value kyu nahi milti?

Because Java requires **definite assignment** before a local variable is
used.

### Q2. Java me integer literal ka default type kya hai?

``` text
int
```

### Q3. `long x = 2147483648;` error kyu?

Because the literal is treated as an `int` literal unless suffixed with
`L`.

Correct:

``` java
long x = 2147483648L;
```

### Q4. Decimal literal ka default type kya hai?

``` text
double
```

### Q5. `float x = 10.5;` error kyu?

Because `10.5` is a `double` literal.

Correct:

``` java
float x = 10.5f;
```

### Q6. String `"100"` ko int me kaise convert karenge?

``` java
int x = Integer.parseInt("100");
```

### Q7. Invalid numeric String par kya exception?

``` text
NumberFormatException
```

### Q8. `this` kya represent karta hai?

``` text
Current object ka reference
```

### Q9. Static method ko object ke bina access kar sakte hain?

Yes.

``` java
ClassName.methodName();
```

### Q10. Instance variable aur static variable me main difference?

``` text
Instance → object-specific
Static   → class-level/shared
```

### Q11. Constructor ka return type?

``` text
No return type — not even void.
```

### Q12. Constructor aur method me main difference?

Constructor object initialization/creation flow se associated hota hai,
while method object/class behaviour perform karta hai.

------------------------------------------------------------------------

# One-Page Revision

``` text
byte → short → int → long → float → double

Integer literal → int
Long literal    → L/l
Floating literal → double
Float literal    → f/F

Local variable → initialize before use
Instance variable → default value
Reference default → null

this → current object

String → int
Integer.parseInt()

byte → Byte
short → Short
int → Integer
long → Long
float → Float
double → Double
char → Character
boolean → Boolean

Implicit → automatic / widening
Explicit → cast required / narrowing

Instance variable → object-specific
Static variable → class-level/shared

Static method → ClassName.method()

Constructor:
- no return type
- same name as class
- invoked during object creation

Instance Block → Constructor
Parent IB → Parent Constructor → Child IB → Child Constructor
```
