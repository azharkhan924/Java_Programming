# Adapter Classes and Event Listeners

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


---

[Previous: Static Nested Class](./05-static-nested-class.md) · [Back to Index](./README.md) · [Next: Lambda Expressions](./07-lambda-expressions.md)
