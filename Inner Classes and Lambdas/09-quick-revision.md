# Inner Classes and Lambdas Quick Revision

# 25. Instance Inner vs Local Inner vs Anonymous Inner

| Feature | Instance Inner | Local Inner | Anonymous Inner |
|---|---|---|---|
| Has name? | Yes | Yes | No |
| Declared inside | Class | Method/block/constructor | Expression/object creation |
| Scope | Outer class | Declaring local scope | Usually one use |
| Common use | Encapsulation | Temporary local logic | Listeners/callbacks |

---

# 26. Graphics, Lambda Expressions and Listeners

Inner/anonymous classes are commonly encountered in Java GUI and event-driven programming.

Example using an event listener:

```java
button.addActionListener(new ActionListener() {

 public void actionPerformed(ActionEvent e) {
 System.out.println("Button clicked");
 }
});
```

This is an anonymous class implementing `ActionListener`.

With modern Java, a functional interface can often be written using a lambda:

```java
button.addActionListener(e -> {
 System.out.println("Button clicked");
});
```

Thus anonymous inner classes are closely related to **listeners, callbacks and lambda expressions**.

---

# 27. Quick Revision Map

```text
 NESTED CLASS
 │
 ┌───────────┴───────────┐
 │ │
 NON-STATIC STATIC
 │ NESTED CLASS
 ┌──────┼──────┐
 │ │ │
 Instance Local Anonymous
 Inner Inner Inner
 Class Class Class
```

### Instance Inner Class

```java
Outer o = new Outer();
Outer.Inner i = o.new Inner();
```

### Local Inner Class

```java
void show() {

 class B {
 }
}
```

### Anonymous Inner Class

```java
A a = new A() {
 // body
};
```

### Abstract Inner Class

```java
abstract class B {
}
```

### Private Inner Class

```java
private class B {
}
```

---

# 28. Exam-Oriented One-Liners

- **Nested class:** A class declared inside another class.
- **Instance inner class:** A non-static class declared inside another class.
- **Instance inner class object:** Created using the outer class object.
- **Syntax:** `Outer.Inner i = outer.new Inner();`
- **Private inner class:** Can be accessed only within its enclosing class.
- **Local inner class:** A class declared inside a method, constructor or local block.
- **Anonymous inner class:** A class without an explicit name.
- **Anonymous class can:** Extend a class or implement an interface.
- **Local variable accessed by inner class:** Must be `final` or effectively final.
- **Effectively final:** A variable whose value is not modified after initialization.
- **Superclass reference:** Can hold a subclass object, e.g. `A a = new B();`
- **Abstract class:** Cannot be instantiated directly but can have reference variables.
- **Abstract reference:** `A a = new B();` is valid when `B extends A`.
- **Inner class:** Can access members of its outer class, including private members.

# 26. Quick Comparison

 -------------------------------------------------------------------------
 Concept Object Creation
 ----------------------------------- -------------------------------------
 Normal class `A a = new A();`

 Upcasting `A a = new B();`

 Static nested class `A.B b = new A.B();`

 Instance inner class `A a = new A(); A.B b = a.new B();`

 Anonymous class `A a = new A() { ... };`

 Anonymous implementation of `Inter i = new Inter() { ... };`
 interface 

 Lambda `Inter i = () -> ...;`
 -------------------------------------------------------------------------

------------------------------------------------------------------------

# 27. Most Important Memory Concept

Remember this pattern:

``` java
A a = new B();
```

Think:

``` text
A → Reference Type
a → Reference Variable
new B() → Object Creation
B → Actual Object Type
```

Similarly:

``` java
Inter i = new Inter() {
 public void show() {
 System.out.println("Hello");
 }
};
```

Think:

``` text
Inter
 ↓
Reference Type

i
 ↓
Reference Variable

new Inter() { ... }
 ↓
Anonymous Class Object
```

**Interface ka object nahi banta.**

Anonymous class ka object banta hai aur uska reference `Inter` type ke
variable mein rakha jata hai.

------------------------------------------------------------------------

# 28. Key Points to Remember

1. Anonymous inner class has **no class name**.
2. It is useful when a method needs a **special implementation for one
 particular object**.
3. `A a = new B();` mein `a` reference variable hai aur `new B()` `B` ka
 object create karta hai.
4. Interface ka direct object nahi banaya ja sakta.
5. Anonymous class interface ko implement karke uska object create kar
 sakti hai.
6. Adapter classes are useful when a listener interface has many
 methods but we need only a few.
7. `WindowListener` ke liye adapter class unwanted methods ki empty
 implementations provide kar sakti hai.
8. Anonymous adapter can be passed directly as a method argument.
9. A functional interface has **exactly one abstract method**.
10. Lambda expression functional interface ke single abstract method ki
 implementation provide karta hai.
11. Lambda mein method name aur boilerplate likhne ki zaroorat nahi hoti.
12. One parameter mein parentheses optional hain: `x -> ...`
13. Multiple parameters mein parentheses required hain: `(a, b) -> ...`
14. Static nested class can be created without an object of the outer
 class.
15. Instance inner class requires an outer-class object.
16. Static nested class can contain static and non-static members.
17. Traditional instance inner-class rules do not allow ordinary static
 members; `static final` constants are the classic exception.
18. A class can contain another class/interface.
19. An interface can contain another interface/class.
20. The actual object type and reference type can be different,
 especially in polymorphism and anonymous classes.

---

[Previous: Nested Interfaces and Classes](./08-nested-interfaces-and-classes.md) · [Back to Index](./README.md) · [Root README](../README.md)
