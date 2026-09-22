# Java Notes --- Anonymous Inner Class, Adapter Class, Lambda Expression & Inner Classes

## 1. Anonymous Inner Class

An **anonymous inner class** is a class that has **no name** and is
created at the same place where its object is required.

It is useful when we want to **override a method for a particular object
only**, without creating a separate named class.

### Example: Method overriding for one particular Employee

Suppose we have:

``` java
class Employee {
    void cName() {
        System.out.println("Employee Name");
    }

    void salary() {
        System.out.println(50000);
    }
}
```

Normally, every `Employee` object uses the same `salary()`
implementation:

``` java
Employee e1 = new Employee();
Employee e2 = new Employee();

e1.salary();
e2.salary();
```

Output:

``` text
50000
50000
```

But suppose we want to change `salary()` **only for one particular
object**.

We can use an anonymous inner class:

``` java
Employee e1 = new Employee() {
    @Override
    void salary() {
        System.out.println(80000);
    }
};
```

Now:

``` java
Employee e2 = new Employee();

e1.salary();   // 80000
e2.salary();   // 50000
```

### Important point

Here:

``` java
new Employee() {
    @Override
    void salary() {
        System.out.println(80000);
    }
}
```

we are creating an anonymous subclass of `Employee` and overriding
`salary()` for that particular object.

------------------------------------------------------------------------

# 2. Reference Variable vs Object vs Constructor

This is an important concept.

Consider:

``` java
A a1 = new B();
```

If `B` extends `A`:

``` java
class A {
}

class B extends A {
}
```

then:

``` java
A a1 = new B();
```

has three different things to understand:

### 1. `A`

`A` is the **reference type**.

So `a1` can directly access members available through the `A` reference.

### 2. `a1`

`a1` is a **reference variable**.

It stores a reference to the object.

### 3. `new B()`

`new B()` creates an **object of class `B`**.

The `B` constructor is called.

Conceptually:

``` text
Reference variable
       |
       v
      a1
       |
       v
   +---------+
   | B object |
   +---------+
```

The object is a `B` object, not an `A` object.

`A` is only the reference type.

### Memory concept

When:

``` java
A a1 = new B();
```

executes:

1.  `new B()` requests memory for a new `B` object.
2.  The `B` constructor is executed.
3.  The resulting object exists in heap memory.
4.  `a1` stores a reference to that object.
5.  Because the reference type is `A`, only members accessible through
    `A` can be directly accessed using `a1`.

Example:

``` java
class A {
    void showA() {
        System.out.println("A");
    }
}

class B extends A {
    void showB() {
        System.out.println("B");
    }
}
```

``` java
A a1 = new B();

a1.showA();   // valid
// a1.showB();   // compile-time error
```

The object is still a `B` object.

This is commonly called **upcasting**.

------------------------------------------------------------------------

# 3. Anonymous Inner Class Implementing an Interface

An interface cannot normally be instantiated directly:

``` java
// Inter1 i = new Inter1();   // invalid
```

But an anonymous class can implement the interface:

``` java
interface Inter1 {
    void show();
}

class Demo {
    public static void main(String[] args) {

        Inter1 in = new Inter1() {
            @Override
            public void show() {
                System.out.println("ABC");
            }
        };

        in.show();
    }
}
```

### What is happening here?

``` java
Inter1 in = new Inter1() {
    public void show() {
        System.out.println("ABC");
    }
};
```

The important point is:

-   `Inter1` → reference type
-   `in` → reference variable
-   `new Inter1() { ... }` → creates an **anonymous class object**
-   The anonymous class **implements `Inter1`**
-   The object is stored/referenced through the `Inter1` reference

Conceptually:

``` text
Inter1 in
   |
   v
+---------------------------+
| Anonymous class object    |
|                           |
| show() {                  |
|   System.out.println(...); |
| }                         |
+---------------------------+
```

### Does an object of the interface get created?

**No.**

An interface itself is not instantiated.

The object belongs to the **anonymous class**.

The interface reference simply refers to that object.

This is similar to:

``` java
A a = new B();
```

Here:

``` java
Inter1 in = new Inter1() { ... };
```

means:

> Create an object of an anonymous class that implements `Inter1`, and
> store its reference in an `Inter1` reference variable.

------------------------------------------------------------------------

# 4. Anonymous Inner Class Inside Method Argument

Sometimes we don't even need a separate reference variable.

For example:

``` java
addWindowListener(new MyAdapter() {
    @Override
    public void windowClosing(WindowEvent e) {
        System.exit(0);
    }
});
```

Here the anonymous object is directly passed as an argument.

------------------------------------------------------------------------

# 5. Adapter Class

Some listener interfaces contain many methods.

For example, `WindowListener` contains several methods:

``` java
windowOpened()
windowClosing()
windowClosed()
windowIconified()
windowDeiconified()
windowActivated()
windowDeactivated()
```

If we implement `WindowListener` directly:

``` java
class MyAdapter implements WindowListener {

    public void windowOpened(WindowEvent e) {}
    public void windowClosing(WindowEvent e) {}
    public void windowClosed(WindowEvent e) {}
    public void windowIconified(WindowEvent e) {}
    public void windowDeiconified(WindowEvent e) {}
    public void windowActivated(WindowEvent e) {}
    public void windowDeactivated(WindowEvent e) {}
}
```

we have to provide implementations for all required methods.

Usually we may need only one method, such as `windowClosing()`.

That's why an **adapter class** is useful.

### Custom Adapter Class

``` java
class MyAdapter implements WindowListener {

    public void windowOpened(WindowEvent e) {}

    public void windowClosing(WindowEvent e) {}

    public void windowClosed(WindowEvent e) {}

    public void windowIconified(WindowEvent e) {}

    public void windowDeiconified(WindowEvent e) {}

    public void windowActivated(WindowEvent e) {}

    public void windowDeactivated(WindowEvent e) {}
}
```

All unwanted methods have empty bodies.

Now another class can extend this adapter:

``` java
class FDemo extends Frame {

    FDemo() {

        MyAdapter m = new MyAdapter() {
            @Override
            public void windowClosing(WindowEvent e) {
                System.exit(0);
            }
        };

        addWindowListener(m);
    }
}
```

------------------------------------------------------------------------

# 6. Anonymous Adapter Class --- Short Form

Instead of creating a separate object:

``` java
MyAdapter m = new MyAdapter() {
    @Override
    public void windowClosing(WindowEvent e) {
        System.exit(0);
    }
};

addWindowListener(m);
```

we can directly pass the anonymous object:

``` java
addWindowListener(new MyAdapter() {
    @Override
    public void windowClosing(WindowEvent e) {
        System.exit(0);
    }
});
```

### Breakdown

``` java
addWindowListener(
    new MyAdapter() {
        @Override
        public void windowClosing(WindowEvent e) {
            System.exit(0);
        }
    }
);
```

Read it as:

> Call `addWindowListener()` and pass an object of an anonymous subclass
> of `MyAdapter` as the argument.

This is called:

**Anonymous inner class inside the method argument.**

------------------------------------------------------------------------

# 7. ActionListener Using Anonymous Inner Class

`ActionListener` is commonly used with buttons.

``` java
Button b1 = new Button("Red");
Button b2 = new Button("Green");

add(b1);
add(b2);

ActionListener al1 = new ActionListener() {
    @Override
    public void actionPerformed(ActionEvent e) {
        b1.setBackground(Color.PINK);
    }
};

ActionListener al2 = new ActionListener() {
    @Override
    public void actionPerformed(ActionEvent e) {
        b2.setBackground(Color.GREEN);
    }
};

b1.addActionListener(al1);
b2.addActionListener(al2);
```

The anonymous class provides the implementation of `actionPerformed()`.

------------------------------------------------------------------------

# 8. Functional Interface

A **functional interface** is an interface that contains **exactly one
abstract method**.

Example:

``` java
interface Inter1 {
    void show();
}
```

`Inter1` is a functional interface because it contains only one abstract
method:

``` java
void show();
```

We can explicitly mark it using:

``` java
@FunctionalInterface
interface Inter1 {
    void show();
}
```

------------------------------------------------------------------------

# 9. Lambda Expression

A lambda expression is a concise way of providing the implementation of
the single abstract method of a functional interface.

Example using anonymous inner class:

``` java
Inter1 in = new Inter1() {
    @Override
    public void show() {
        System.out.println("ABC");
    }
};
```

The same thing using lambda:

``` java
Inter1 in = () -> {
    System.out.println("ABC");
};
```

And because the body contains only one statement:

``` java
Inter1 in = () -> System.out.println("ABC");
```

### Important idea

Lambda expressions work with **functional interfaces**.

They provide the implementation of the interface's single abstract
method.

------------------------------------------------------------------------

# 10. Building a Lambda Expression Step by Step

Suppose:

``` java
interface Inter1 {
    void show();
}
```

### Step 1 --- Anonymous class

``` java
Inter1 in = new Inter1() {
    @Override
    public void show() {
        System.out.println("SWT");
    }
};
```

### Step 2 --- Remove class/object boilerplate

Because `Inter1` has only one abstract method, Java knows which method
we are implementing.

``` java
Inter1 in = () -> {
    System.out.println("SWT");
};
```

### Step 3 --- Remove braces for a single statement

``` java
Inter1 in = () -> System.out.println("SWT");
```

This is the final concise lambda expression.

------------------------------------------------------------------------

# 11. Lambda With Parameters

Suppose the interface is:

``` java
interface Inter1 {
    void show(int x);
}
```

Anonymous class:

``` java
Inter1 in = new Inter1() {
    @Override
    public void show(int x) {
        System.out.println(x);
    }
};
```

Lambda:

``` java
Inter1 in = (int x) -> {
    System.out.println(x);
};
```

Because the parameter type can be inferred:

``` java
Inter1 in = (x) -> {
    System.out.println(x);
};
```

Parentheses can be removed for a single parameter:

``` java
Inter1 in = x -> {
    System.out.println(x);
};
```

And finally:

``` java
Inter1 in = x -> System.out.println(x);
```

------------------------------------------------------------------------

# 12. Lambda With Two Parameters

Suppose:

``` java
interface Inter1 {
    void show(int a, int b);
}
```

Lambda:

``` java
Inter1 in = (a, b) -> {
    System.out.println(a + b);
};
```

For two parameters, parentheses are required:

``` java
(a, b)
```

They cannot be written as:

``` java
a, b -> ...
```

------------------------------------------------------------------------

# 13. Lambda Returning a Value

Suppose:

``` java
interface Inter1 {
    int show(int a, int b);
}
```

Lambda:

``` java
Inter1 in = (a, b) -> {
    return a + b;
};
```

For a single expression, `return` and braces can be removed:

``` java
Inter1 in = (a, b) -> a + b;
```

This is the shortest form.

------------------------------------------------------------------------

# 14. ActionListener Using Lambda Expression

`ActionListener` is a functional interface because it has one abstract
method:

``` java
actionPerformed(ActionEvent e)
```

Therefore, we can write:

``` java
Button b = new Button("Click");

b.addActionListener(e -> {
    setBackground(Color.RED);
});
```

Or, because there is only one statement:

``` java
b.addActionListener(e -> setBackground(Color.RED));
```

Compare:

### Anonymous Inner Class

``` java
b.addActionListener(new ActionListener() {
    @Override
    public void actionPerformed(ActionEvent e) {
        setBackground(Color.RED);
    }
});
```

### Lambda

``` java
b.addActionListener(e -> setBackground(Color.RED));
```

Lambda removes a lot of boilerplate.

------------------------------------------------------------------------

# 15. Static Inner Class

A class declared inside another class is called an **inner/nested
class**.

A class declared using the `static` keyword is called a **static nested
class**.

Example:

``` java
class A {

    static class B {

        void show() {
            System.out.println("Show");
        }
    }
}
```

Here:

``` text
A
└── static class B
      └── show()
```

------------------------------------------------------------------------

# 16. Creating Object of Static Inner Class

A static nested class can be accessed using the outer class name.

``` java
class A {

    static class B {

        void show() {
            System.out.println("Show");
        }
    }
}
```

Another class:

``` java
class D {

    public static void main(String[] args) {

        A.B b = new A.B();

        b.show();
    }
}
```

### Important syntax

``` java
A.B b = new A.B();
```

Here:

-   `A.B` → type of reference variable
-   `b` → reference variable
-   `new A.B()` → creates object of static nested class `B`

No object of outer class `A` is required.

------------------------------------------------------------------------

# 17. Why Static Inner Class Can Be Created Without Outer Object

For a normal instance inner class, an outer object is required:

``` java
A a = new A();
A.B b = a.new B();
```

But for a static nested class:

``` java
A.B b = new A.B();
```

is sufficient.

Reason:

> A static nested class belongs to the outer class itself, not to a
> particular object of the outer class.

------------------------------------------------------------------------

# 18. Static Members in Static Nested Class

A static nested class can contain:

-   static variables
-   static methods
-   non-static variables
-   non-static methods
-   `main()` method

Example:

``` java
class A {

    static class B {

        static int x = 100;

        int y = 200;

        static void staticShow() {
            System.out.println("Static method");
        }

        void instanceShow() {
            System.out.println("Instance method");
        }

        public static void main(String[] args) {
            System.out.println("Main method inside static nested class");
        }
    }
}
```

------------------------------------------------------------------------

# 19. Instance Inner Class

A non-static class declared inside another class is an **instance inner
class**.

Example:

``` java
class A {

    class B {

        void show() {
            System.out.println("Show");
        }
    }
}
```

To create its object:

``` java
A a = new A();

A.B b = a.new B();

b.show();
```

An instance inner class is associated with an object of the outer class.

------------------------------------------------------------------------

# 20. Static Members in Instance Inner Class

An instance inner class cannot generally declare static members.

For example, this is not allowed in the traditional Java rule:

``` java
class A {

    class B {

        static void show() {
            System.out.println("Show");
        }
    }
}
```

Similarly, a static variable is not allowed in an ordinary inner class.

### Exception

A `static final` constant is allowed:

``` java
class A {

    class B {

        static final int X = 100;
    }
}
```

### Modern Java note

Recent Java versions have relaxed the old restriction for static members
in inner classes in some cases. For basic/core-Java notes and
traditional syllabus questions, remember the conventional rule above
unless your course specifically covers the newer Java-version behavior.

------------------------------------------------------------------------

# 21. Main Method in Static Nested Class

A `main()` method is static.

Therefore, a static nested class can contain:

``` java
class A {

    static class B {

        public static void main(String[] args) {
            System.out.println("Hello");
        }
    }
}
```

This is valid.

An ordinary instance inner class cannot be used in the same simple way
to declare a traditional static `main()` method under the old
inner-class restriction.

------------------------------------------------------------------------

# 22. Class Inside Class

A class can be declared inside another class.

``` java
class Outer {

    class Inner {

        void show() {
            System.out.println("Inside Inner");
        }
    }
}
```

This is a class inside a class.

It can be:

``` java
class Outer {

    static class Inner {
    }
}
```

or:

``` java
class Outer {

    class Inner {
    }
}
```

depending on whether it should be static or associated with an outer
object.

------------------------------------------------------------------------

# 23. Interface Inside Class

An interface can be declared inside a class.

``` java
class A {

    interface Inter1 {
        void show();
    }
}
```

It can be accessed using:

``` java
class Demo implements A.Inter1 {

    public void show() {
        System.out.println("Show");
    }
}
```

Or:

``` java
A.Inter1 obj = new A.Inter1() {
    public void show() {
        System.out.println("Anonymous implementation");
    }
};
```

------------------------------------------------------------------------

# 24. Interface Inside Interface

An interface can contain another interface.

``` java
interface OuterInter {

    interface InnerInter {
        void show();
    }
}
```

Implementation:

``` java
class Demo implements OuterInter.InnerInter {

    public void show() {
        System.out.println("Show");
    }
}
```

------------------------------------------------------------------------

# 25. Class Inside Interface

An interface can also contain a class.

``` java
interface Inter1 {

    class A {

        void show() {
            System.out.println("Show");
        }
    }
}
```

The nested class can be accessed as:

``` java
Inter1.A obj = new Inter1.A();

obj.show();
```

Nested types declared in an interface are implicitly `public static`, so
they do not require an object of the interface.

------------------------------------------------------------------------

# 26. Quick Comparison

  -------------------------------------------------------------------------
  Concept                             Object Creation
  ----------------------------------- -------------------------------------
  Normal class                        `A a = new A();`

  Upcasting                           `A a = new B();`

  Static nested class                 `A.B b = new A.B();`

  Instance inner class                `A a = new A(); A.B b = a.new B();`

  Anonymous class                     `A a = new A() { ... };`

  Anonymous implementation of         `Inter i = new Inter() { ... };`
  interface                           

  Lambda                              `Inter i = () -> ...;`
  -------------------------------------------------------------------------

------------------------------------------------------------------------

# 27. Most Important Memory Concept

Remember this pattern:

``` java
A a = new B();
```

Think:

``` text
A       → Reference Type
a       → Reference Variable
new B() → Object Creation
B       → Actual Object Type
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

**Interface का object नहीं बनता।**

Anonymous class का object बनता है और उसका reference `Inter` type के
variable में रखा जाता है.

------------------------------------------------------------------------

# 28. Key Points to Remember

1.  Anonymous inner class has **no class name**.
2.  It is useful when a method needs a **special implementation for one
    particular object**.
3.  `A a = new B();` में `a` reference variable है और `new B()` `B` का
    object create करता है.
4.  Interface का direct object नहीं बनाया जा सकता.
5.  Anonymous class interface को implement करके उसका object create कर
    सकती है.
6.  Adapter classes are useful when a listener interface has many
    methods but we need only a few.
7.  `WindowListener` के लिए adapter class unwanted methods की empty
    implementations provide कर सकती है.
8.  Anonymous adapter can be passed directly as a method argument.
9.  A functional interface has **exactly one abstract method**.
10. Lambda expression functional interface के single abstract method की
    implementation provide करता है.
11. Lambda में method name और boilerplate लिखने की जरूरत नहीं होती.
12. One parameter में parentheses optional हैं: `x -> ...`
13. Multiple parameters में parentheses required हैं: `(a, b) -> ...`
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
