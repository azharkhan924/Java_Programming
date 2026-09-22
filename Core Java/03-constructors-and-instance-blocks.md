# Constructors & Instance Blocks

---

## 1. What is a Constructor?

Constructor ek **special member** hai jo object creation ke time **initialization** ke liye use hota hai.

```java
class Student {

 Student() {
 System.out.println("Constructor called");
 }
}

Student s1 = new Student(); // "Constructor called"
```

### Constructor ki Properties

| Property | Detail |
|----------|--------|
| Return type | Nahi hota — not even `void` |
| Name | Class name ke **same** |
| Invocation | Object create karte waqt **automatically** |
| Purpose | Object ko **initialize** karna |
| `new` keyword | Constructor `new` ke through invoke hota hai |

---

## 2. Types of Constructors

### Default Constructor

Agar class me **koi constructor declare nahi kiya**, compiler ek no-arg constructor provide karta hai.

```java
class Student {
 // no constructor written
}
```

Compiler internally generate karta hai:

```java
Student() {
 super();
}
```

### No-Argument Constructor

Programmer khud bhi no-arg constructor define kar sakta hai:

```java
class Student {
 Student() {
 System.out.println("No-arg constructor");
 }
}
```

> Note: **Default constructor** aur **no-arg constructor** technically same nahi hain. Default constructor specifically **compiler-provided** constructor ko kehte hain.

### Parameterized Constructor

Parameters ke saath constructor:

```java
class Student {
 int age;

 Student(int age) {
 this.age = age;
 }
}

Student s1 = new Student(20);
```

### Note: Important Trap

Agar **koi bhi constructor explicitly likha**, compiler default constructor **nahi deta**:

```java
class A {
 A(int x) { }
}

new A(); // no default constructor available
new A(10); // 
```

---

## 3. Constructor vs Method

| Point | Constructor | Method |
|-------|------------|--------|
| Return type | Nahi hota | Hota hai (`void` bhi) |
| Name | Class name ke same | Freely choose kar sakte hain |
| Invocation | Object creation ke time auto | Manually invoke |
| Purpose | Initialization | Behaviour define karna |
| Re-call | Object creation ke saath | Same method ko baar baar call possible |

---

## 4. `this` Keyword

`this` **current object** ka reference hold karta hai.

```java
class Student {
 int age;

 void setAge(int age) {
 this.age = age; // this.age = instance variable
 // age = method parameter
 }
}
```

> **Definition:** `this` refers to the **current object**.

---

## 5. Constructor Chaining

Constructor chaining means **ek constructor dusre constructor ko call karta hai**.

### `this()` vs `super()`

| Call | Purpose |
|------|---------|
| `this()` | Same class ka dusra constructor call |
| `super()` | Parent class ka constructor call |

### Example

```java
class A {
 A() {
 System.out.println("A");
 }
}

class B extends A {
 B() {
 // super(); ← compiler implicitly insert karta hai
 System.out.println("B");
 }
}
```

Output:

```text
A
B
```

### Important Rules

| Rule | Detail |
|------|--------|
| **Rule 1** | `this()` ya `super()` constructor ka **first statement** hona chahiye |
| **Rule 2** | Dono ek saath ek constructor me **nahi use** kar sakte |
| **Rule 3** | Sirf **ek explicit constructor invocation** first statement ho sakta hai |
| **Rule 4** | `this()` → current class constructor call |
| **Rule 5** | `super()` → immediate parent class constructor call |

### Note: Parameterized Parent Constructor Trap

```java
class A {
 A(int x) { } // no default constructor
}

class B extends A {
 B() {
 // compiler tries: super()
 // but A has no no-arg constructor → ERROR
 }
}
```

Fix:

```java
class B extends A {
 B() {
 super(10); // explicitly call parameterized constructor
 }
}
```

---

## 6. Instance Initializer Block

Instance block `{ }` class ke andar likha jata hai (method ke bahar).

```java
class Student {

 {
 System.out.println("Instance Block");
 }

 Student() {
 System.out.println("Constructor");
 }
}
```

Output (on `new Student()`):

```text
Instance Block
Constructor
```

### Execution Order

```text
Object Creation
 ↓
Instance Initializer Block
 ↓
Constructor Body
```

> Instance block constructor body **se pehle** execute hota hai.

---

## 7. Instance Block — Uses

- Instance variables ki **common initialization**
- Code jo har object creation par execute karna ho
- Multiple constructors me repeated code avoid karna

---

## 8. Instance Block vs Constructor

| Point | Instance Block | Constructor |
|-------|---------------|-------------|
| Type | Initializer block | Special member |
| Execution | Constructor se **pehle** | Instance block ke **baad** |
| Parameters | Directly receive nahi karta | Parameters accept karta hai |
| Multiple | Multiple blocks possible | Multiple constructors (overloading) |
| Purpose | Common initialization | Object-specific initialization |

---

## 9. Inheritance me Execution Order

```text
Parent Instance Block
 ↓
Parent Constructor
 ↓
Child Instance Block
 ↓
Child Constructor
```

### Example

```java
class Parent {
 { System.out.println("Parent IB"); }
 Parent() { System.out.println("Parent Constructor"); }
}

class Child extends Parent {
 { System.out.println("Child IB"); }
 Child() { System.out.println("Child Constructor"); }
}

Child c = new Child();
```

Output:

```text
Parent IB
Parent Constructor
Child IB
Child Constructor
```

> **Reason:** Child object create karne se pehle parent part pehle initialize hota hai.

---

## Interview Quick Questions

| Question | Answer |
|----------|--------|
| Constructor ka return type? | No return type — not even `void` |
| Constructor aur method me main difference? | Constructor = initialization, Method = behaviour |
| `this` kya represent karta hai? | Current object ka reference |
| Instance block kab execute hota hai? | Constructor body se pehle |
| `this()` aur `super()` ek saath use ho sakte hain? | No — sirf ek first statement ho sakta hai |
| Default constructor kab milta hai? | Jab **koi constructor nahi likha** tab compiler deta hai |

---

## Quick Revision

```text
Constructor
│
├── Default Constructor (compiler-provided)
├── No-Argument Constructor (programmer-written)
└── Parameterized Constructor

Object Creation Flow:
Object → Instance Block → Constructor

Inheritance Flow:
Parent IB → Parent Constructor → Child IB → Child Constructor

this() → same class constructor
super() → parent class constructor
→ Must be FIRST statement
→ Only ONE allowed per constructor
```

---

[Previous: Variables & Type Casting](./02-variables-and-typecasting.md) · [Back to Core Java Index](./README.md) · [Next: Static Keyword](./04-static-keyword.md)
