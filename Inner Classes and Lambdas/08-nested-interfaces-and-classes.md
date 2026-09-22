# Nested Interfaces and Classes

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


---

[Previous: Lambda Expressions](./07-lambda-expressions.md) · [Back to Index](./README.md) · [Next: Quick Revision](./09-quick-revision.md)
