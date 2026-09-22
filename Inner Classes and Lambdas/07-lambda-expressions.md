# Functional Interfaces and Lambda Expressions

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


---

[Previous: Adapter Classes](./06-adapter-classes.md) · [Back to Index](./README.md) · [Next: Nested Interfaces and Classes](./08-nested-interfaces-and-classes.md)
