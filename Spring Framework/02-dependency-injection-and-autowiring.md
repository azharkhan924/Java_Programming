# Spring Framework – Advanced Dependency Injection & Autowiring Notes

> **Focus:** Lookup Method Injection, Circular Dependency, Constructor/Setter Injection, XML Namespaces, Java Configuration, Collection Injection, Autowiring, `@Autowired`, `@Qualifier`, `@Primary`, `@Component`, `@Nullable` and multiple-bean injection.

---

## 1. Lookup Method Injection

### Definition

**Lookup Method Injection** is a Spring feature in which Spring overrides a method of a bean and, whenever that method is called, returns another bean from the Spring container.

It is especially useful when:

- the main bean is a **singleton**
- but it needs a **prototype** object every time.

### Example

Suppose `Student` is a singleton and `Address` is prototype.

### `Address.java`

```java
package p1;

public class Address {
    public Address() {
        System.out.println("Address constructor");
    }
}
```

### `Student.java`

```java
package p1;

public abstract class Student {

    public Student() {
        System.out.println("Student constructor");
    }

    public void show() {
        Address address = get();
        System.out.println(address);
    }

    public abstract Address get();
}
```

### `ABC.xml`

```xml
<bean id="A1"
      class="p1.Address"
      scope="prototype"/>

<bean id="S1"
      class="p1.Student"
      scope="singleton">
    <lookup-method name="get" bean="A1"/>
</bean>
```

### Main class

```java
ApplicationContext ctx =
        new ClassPathXmlApplicationContext("ABC.xml");

Student s1 = (Student) ctx.getBean("S1");

s1.show();
s1.show();
```

### Important point

`Student` is abstract, but Spring can still create its bean using **method injection**. Spring generates a subclass and overrides the lookup method.

The important behavior is:

```text
S1 -> singleton Student object
        |
        +-- get() -> asks Spring for A1
                      |
                      +-- A1 is prototype
                          -> a new Address object each time
```

So multiple calls to:

```java
s1.get();
```

can return different `Address` instances.

### Can Lookup Method Injection be used with an abstract class?

**Yes.**

It can be used with:

- abstract classes
- concrete classes

provided the method can be overridden.

### Parameterized lookup method

Lookup method injection is **not used with parameterized lookup methods**.

For example, do not design it like:

```java
public abstract Address get(int id);
```

Use a no-argument lookup method.

### Final class and final method

Spring needs to override the lookup method in a generated subclass.

Therefore:

```java
public final class Student
```

cannot be used for subclass-based lookup injection.

Similarly:

```java
public final Address get()
```

cannot be overridden.

So avoid `final` on the class or lookup method when using lookup-method injection.

### CGLIB/internal working

Spring uses subclass-based method injection for this feature. In the traditional implementation this is associated with **CGLIB-style subclassing**.

Conceptually:

```text
Original class
     |
     v
Spring-generated subclass
     |
     +-- overrides get()
             |
             +-- asks BeanFactory for A1
```

The generated subclass intercepts the lookup method and obtains the required bean from the container.

---

# 2. Common Ways of Creating Objects in Java

Common mechanisms include:

### 1. `new` keyword

```java
Student s = new Student();
```

### 2. String literal

```java
String s = "Hello";
```

This creates/uses a `String` object through the String pool mechanism. It is not a general replacement for `new` for arbitrary classes.

### 3. Reflection

```java
Student s =
    Student.class.getDeclaredConstructor().newInstance();
```

### 4. Cloning

```java
Student s2 = (Student) s1.clone();
```

### 5. Deserialization

```java
ObjectInputStream in = ...;
Student s = (Student) in.readObject();
```

### 6. Factory method

```java
Student s = StudentFactory.createStudent();
```

A factory method can create/return an object without the caller directly using `new`.

> **Important:** A constructor initializes an object; technically, the constructor itself is not the mechanism that allocates the object. The `new` expression performs object creation and invokes the constructor.

---

# 3. Circular Dependency

## Definition

A **circular dependency** occurs when two or more beans depend on each other directly or indirectly.

Example:

```text
A -> B
B -> A
```

or:

```text
A -> B
B -> C
C -> A
```

This creates a dependency cycle.

---

## Constructor Injection + Circular Dependency

Example:

```java
class A {
    B b;

    A(B b) {
        this.b = b;
    }
}
```

```java
class B {
    A a;

    B(A a) {
        this.a = a;
    }
}
```

Spring cannot complete construction:

```text
To create A -> B is required
To create B -> A is required
To create A -> B is required
...
```

Therefore constructor-based circular dependencies cannot be resolved in the normal singleton creation process.

A typical failure is a `BeanCurrentlyInCreationException` / dependency-creation failure.

---

# 4. Setter/Field Injection and Circular Dependency

Setter or field injection can allow Spring to resolve certain singleton circular dependencies because the object can be instantiated first and dependencies can be populated afterward.

Conceptually:

```text
Create A
  |
  v
early reference for A
  |
  v
Create B
  |
  v
inject A into B
  |
  v
inject B into A
```

Spring maintains singleton caches during bean creation.

---

# 5. Spring's Three-Level Singleton Cache

For singleton beans, Spring internally uses three important levels:

```text
1. singletonObjects
   -> fully initialized singleton objects

2. earlySingletonObjects
   -> early references to singleton objects

3. singletonFactories
   -> factories capable of creating an early reference
```

The third-level cache is important for exposing an early reference during circular dependency resolution.

### Important limitation

This mechanism is mainly relevant to **singleton circular dependencies** and is not a universal solution for every circular dependency.

Constructor injection cannot simply use an early object reference because the constructor requires the dependency **before the object is fully constructed**.

---

# 6. Constructor Injection

## Definition

Constructor injection supplies dependencies through the constructor.

```java
public class Student {

    private Address address;

    public Student(Address address) {
        this.address = address;
    }
}
```

### Advantages

- mandatory dependencies are explicit
- object can be created in a valid state
- fields can be `final`
- easier to reason about dependencies
- supports constructor-based immutability

---

# 7. Constructor-Based Immutability

Example:

```java
public class Student {

    private final Address address;

    public Student(Address address) {
        this.address = address;
    }
}
```

Once the constructor assigns:

```java
this.address = address;
```

the reference cannot be reassigned.

However:

> **Reference immutability is not the same as object immutability.**

For example:

```java
private final List<String> languages;
```

The reference cannot be changed:

```java
languages = anotherList; // not allowed
```

but the list itself may still be modified:

```java
languages.add("Java");
```

### Achieving an unmodifiable list

```java
this.languages =
        Collections.unmodifiableList(languages);
```

Or, where appropriate:

```java
this.languages =
        List.copyOf(languages);
```

This protects the list from normal external modification through that reference.

---

# 8. Setter Injection

Setter injection supplies dependencies using setter methods.

```java
public class Student {

    private Address address;

    public void setAddress(Address address) {
        this.address = address;
    }
}
```

### Constructor vs Setter Injection

| Constructor Injection | Setter Injection |
|---|---|
| Dependency supplied during object creation | Dependency supplied after object creation |
| Good for mandatory dependencies | Good for optional/configurable dependencies |
| Can support final fields | Usually requires mutable fields |
| Helps constructor-based immutability | Allows dependency replacement |
| Circular dependency cannot normally be resolved | Certain singleton circular dependencies can be resolved |

---

# 9. Constructor Argument Type Mismatch

Suppose:

```xml
<constructor-arg value="A"/>
<constructor-arg value="B"/>
```

but the constructor expects:

```java
Student(int id, String name)
```

Spring may fail because it cannot convert the supplied values to the required constructor parameters.

A dependency/configuration failure can result, commonly represented through `BeanCreationException` / `UnsatisfiedDependencyException` depending on the exact configuration path.

---

# 10. Constructor Argument `index`

If constructor arguments are in the wrong order, use `index`.

```xml
<bean id="S1" class="p1.Student">

    <constructor-arg index="0" value="101"/>

    <constructor-arg index="1" value="AAA"/>

</bean>
```

For:

```java
public Student(int id, String name) {
    this.id = id;
    this.name = name;
}
```

Here:

```text
index 0 -> int id
index 1 -> String name
```

This removes ambiguity caused by argument ordering.

---

# 11. XML `p` Namespace

The **p namespace** provides a shorter way to configure bean properties.

Instead of:

```xml
<bean id="A1" class="p1.Address">
    <property name="city" value="Indore"/>
</bean>
```

we can use:

```xml
<bean id="A1"
      class="p1.Address"
      p:city="Indore"/>
```

### Namespace declaration

```xml
<beans
    xmlns="http://www.springframework.org/schema/beans"
    xmlns:p="http://www.springframework.org/schema/p"
    ...>
```

`p:` is mainly used for property-based configuration.

---

# 12. XML `c` Namespace

The **c namespace** provides a shortcut for constructor arguments.

Example:

```xml
<bean id="S1"
      class="p1.Student"
      c:_0="101"
      c:_1="AAA"/>
```

Here:

```text
c:_0 -> constructor argument at index 0
c:_1 -> constructor argument at index 1
```

Namespace:

```xml
xmlns:c="http://www.springframework.org/schema/c"
```

### Example constructor

```java
public Student(int id, String name) {
    this.id = id;
    this.name = name;
}
```

---

# 13. Java-Based Configuration

From Spring 3.x, Java-based configuration can be used instead of XML configuration.

### `MyConfig.java`

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class MyConfig {

    @Bean
    public Student getStudent() {
        return new Student();
    }
}
```

### Main class

```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class Test {

    public static void main(String[] args) {

        ApplicationContext ctx =
            new AnnotationConfigApplicationContext(MyConfig.class);

        Student s = ctx.getBean(Student.class);
    }
}
```

---

# 14. `@Configuration`

`@Configuration` marks a class as a source of bean definitions.

Example:

```java
@Configuration
public class MyConfig {

    @Bean
    public Address address() {
        return new Address();
    }

    @Bean
    public Student student() {
        return new Student(address());
    }
}
```

In the normal/full `@Configuration` mode, Spring enhances the configuration class so that calls between `@Bean` methods can be intercepted and managed through the container.

Therefore:

```java
address()
```

can resolve to the container-managed bean rather than simply behaving like an ordinary Java method call.

### Bean inside Bean

Yes, one bean can depend on another bean.

```java
@Bean
public Address address() {
    return new Address();
}

@Bean
public Student student() {
    return new Student(address());
}
```

---

# 15. XML List Injection

Suppose:

### `Student.java`

```java
public class Student {

    private List<String> lang;

    public void setLang(List<String> lang) {
        this.lang = lang;
    }

    public void show() {
        System.out.println(lang);
    }
}
```

### `ABC.xml`

```xml
<bean id="S1" class="p1.Student">

    <property name="lang">
        <list>
            <value>C</value>
            <value>C++</value>
            <value>Java</value>
            <value>Python</value>
        </list>
    </property>

</bean>
```

Spring creates the list and injects it through:

```java
setLang(...)
```

Output:

```text
[C, C++, Java, Python]
```

---

# 16. XML Set Injection

### Java

```java
private Set<String> lang;

public void setLang(Set<String> lang) {
    this.lang = lang;
}
```

### XML

```xml
<property name="lang">
    <set>
        <value>C</value>
        <value>Java</value>
        <value>Python</value>
        <value>Java</value>
    </set>
</property>
```

A `Set` does not keep duplicate elements.

---

# 17. XML Map Injection

### Java

```java
private Map<Integer, String> lang;

public void setLang(Map<Integer, String> lang) {
    this.lang = lang;
}
```

### XML

```xml
<property name="lang">
    <map>
        <entry key="1" value="C"/>
        <entry key="2" value="C++"/>
        <entry key="3" value="Java"/>
        <entry key="4" value="Python"/>
    </map>
</property>
```

Output:

```text
{1=C, 2=C++, 3=Java, 4=Python}
```

---

# 18. Autowiring

## Definition

**Autowiring** is a Spring IoC feature that allows the container to automatically resolve and inject bean dependencies.

It can use mechanisms such as:

- XML autowiring
- `@Autowired`
- `@Qualifier`
- `@Primary`

The actual dependency resolution is performed by the Spring container.

---

# 19. XML `autowire="byType"`

Example:

### Address bean

```xml
<bean id="A1" class="p1.Address">
    <property name="city" value="Indore"/>
</bean>
```

### LocalAddress bean

```xml
<bean id="L1" class="p1.LocalAddress"/>
```

### Student

```xml
<bean id="S1"
      class="p1.Student"
      autowire="byType">

    <property name="id" value="101"/>
    <property name="name" value="AAA"/>

</bean>
```

If `Student` has:

```java
private Address address;
private LocalAddress localAddress;
```

Spring searches for beans matching the required property types.

### Important

`autowire="byType"` matches **type**, not bean ID.

---

# 20. Multiple Beans of Same Type + `byType`

Suppose:

```xml
<bean id="A1" class="p1.Address"/>
<bean id="A2" class="p1.Address"/>
```

There are two beans of type `Address`.

If Spring has to inject:

```java
Address address;
```

using by-type autowiring, it cannot choose between `A1` and `A2`.

This can lead to a dependency-resolution failure, commonly involving:

```text
NoUniqueBeanDefinitionException
```

wrapped in a higher-level dependency/bean creation exception.

---

# 21. XML `autowire="byName"`

By-name autowiring matches the property name with a bean name.

Suppose:

```java
private Address address;

public void setAddress(Address address) {
    this.address = address;
}
```

Then the bean should be named:

```xml
<bean id="address" class="p1.Address"/>
```

and:

```xml
<bean id="S1"
      class="p1.Student"
      autowire="byName"/>
```

Spring searches for a bean whose name matches:

```text
property name = address
bean id       = address
```

---

# 22. XML Autowiring Modes

Common XML values are:

```xml
autowire="no"
autowire="byName"
autowire="byType"
autowire="constructor"
```

### `no`

No automatic wiring.

Dependencies must be configured manually.

### `byName`

Matches property name with bean name.

### `byType`

Matches property type with bean type.

### `constructor`

Matches constructor arguments by compatible type.

### Important

The default XML autowiring mode is:

```text
no
```

It is **not** `byType`.

---

# 23. `@Autowired`

`@Autowired` asks Spring to resolve and inject a dependency.

Example:

```java
@Autowired
private Address address;
```

By default, `@Autowired` resolves dependencies primarily **by type**.

If multiple candidates exist, Spring needs additional information such as:

- `@Qualifier`
- `@Primary`
- another supported resolution mechanism

---

# 24. `context:annotation-config`

In XML configuration:

```xml
<context:annotation-config/>
```

enables processing of common annotation-based dependency injection annotations for beans managed by that context.

Example:

```xml
<beans
    xmlns:context="http://www.springframework.org/schema/context">

    <context:annotation-config/>

</beans>
```

### Important

`annotation-config` does **not** scan the classpath for all components.

---

# 25. `context:component-scan`

Example:

```xml
<context:component-scan base-package="p1"/>
```

Component scanning finds classes annotated with stereotype annotations such as:

```java
@Component
@Service
@Repository
@Controller
```

It also registers them as Spring beans and enables the relevant annotation configuration processing associated with component scanning.

### Difference

| `annotation-config` | `component-scan` |
|---|---|
| Enables annotation processing for registered beans | Scans packages for component classes |
| Does not perform component scanning | Finds and registers components |
| Useful with explicitly declared XML beans | Useful with annotation-based bean definitions |

---

# 26. `@Autowired(required = false)`

Example:

```java
@Autowired(required = false)
private Address address;
```

If a suitable dependency is available, Spring injects it.

If no suitable dependency is available, the dependency can remain unset.

For a field this commonly means:

```text
address == null
```

### Method injection

```java
@Autowired(required = false)
public void setAddress(Address address) {
    this.address = address;
}
```

If the dependency is absent, Spring can skip the method invocation.

> `required=false` is not a general solution for making a constructor dependency optional. For constructors, use an appropriate optional-dependency design such as `Optional<T>` or `@Nullable` where supported.

---

# 27. `@Qualifier`

When multiple beans have the same type, use `@Qualifier` to select a specific bean.

### XML

```xml
<bean id="A1" class="p1.Address"/>
<bean id="A2" class="p1.Address"/>
```

### Student

```java
@Autowired
@Qualifier("A2")
private Address address;
```

Now Spring specifically selects:

```text
A2
```

instead of trying to choose between `A1` and `A2`.

---

# 28. Autowiring Multiple Beans at Once – List

Suppose:

```java
public class Address {

    private String city;

    public Address(String city) {
        this.city = city;
    }

    public String getCity() {
        return city;
    }
}
```

### Student

```java
public class Student {

    private List<Address> addresses;

    @Autowired
    public void setAddresses(List<Address> addresses) {
        this.addresses = addresses;
    }

    public void show() {
        for (Address address : addresses) {
            System.out.println(address.getCity());
        }
    }
}
```

### XML

```xml
<bean id="A1" class="p1.Address">
    <constructor-arg value="Indore"/>
</bean>

<bean id="A2" class="p1.Address">
    <constructor-arg value="Bhopal"/>
</bean>

<bean id="A3" class="p1.Address">
    <constructor-arg value="Mandsaur"/>
</bean>

<bean id="S1" class="p1.Student"/>
```

Spring injects all matching `Address` beans into the list.

Conceptually:

```text
A1
A2  ---> List<Address> ---> Student
A3
```

The setter is called once with the collection containing the matching beans.

---

# 29. Autowiring Multiple Beans – Array

```java
@Autowired
private Address[] addresses;
```

Spring can inject all matching `Address` beans into the array.

Example:

```java
@Autowired
public void setAddresses(Address[] addresses) {
    this.addresses = addresses;
}
```

---

# 30. Autowiring Multiple Beans – Map

Spring can also inject multiple beans into a map.

```java
@Autowired
private Map<String, Address> addresses;
```

The map keys are bean names and the values are the corresponding bean instances.

Example:

```text
A1 -> Address object
A2 -> Address object
A3 -> Address object
```

---

# 31. Optional Dependency with `@Nullable`

Spring supports `@Nullable` for optional dependencies.

Example:

```java
@Autowired
public Student(
        int id,
        String name,
        @Nullable Address address,
        @Nullable LocalAddress localAddress) {

    this.id = id;
    this.name = name;
    this.address = address;
    this.localAddress = localAddress;
}
```

If a dependency is available:

```text
Address -> injected
```

If it is unavailable:

```text
Address -> null
```

This allows the object to be created without that optional dependency.

---

# 32. `@Nullable` with Fields

```java
@Autowired
@Nullable
private Address address;
```

If no suitable `Address` bean exists, the field can remain `null`.

---

# 33. `@Nullable` with Setter

```java
@Autowired
public void setAddress(@Nullable Address address) {
    this.address = address;
}
```

The dependency can be absent without causing the same required-dependency failure.

---

# 34. Constructor Injection without `@Autowired`

Since **Spring 4.3**, if a class has a **single constructor**, `@Autowired` is not required on that constructor.

Example:

```java
public class Student {

    private int id;
    private String name;
    private Address address;
    private LocalAddress localAddress;

    public Student(
            int id,
            String name,
            Address address,
            LocalAddress localAddress) {

        this.id = id;
        this.name = name;
        this.address = address;
        this.localAddress = localAddress;
    }
}
```

If this is the only constructor, Spring can use it automatically.

---

# 35. Multiple Constructors

If a class has multiple constructors, Spring needs enough information to determine which constructor to use.

Example:

```java
public Student(int id, String name) {
    ...
}

@Autowired
public Student(
        int id,
        String name,
        Address address,
        LocalAddress localAddress) {
    ...
}
```

Here `@Autowired` identifies the constructor intended for dependency injection.

---

# 36. Autowiring Primitive/Wrapper Values

Consider:

```java
@Autowired
private int id;
```

Spring searches for a suitable bean of type `int` / corresponding wrapper resolution.

If no such bean/value is configured, normal dependency resolution can fail.

You can explicitly define a bean:

```xml
<bean id="I1"
      class="java.lang.Integer">
    <constructor-arg value="101"/>
</bean>
```

Then Spring can use that bean where a compatible dependency is required.

In real applications, configuration values are more commonly supplied using mechanisms such as `@Value` or configuration properties rather than defining simple integers as beans.

---

# 37. Field Injection

`@Autowired` can be used directly on a field.

```java
public class Student {

    @Autowired
    private Address address;
}
```

Spring uses reflection to inject the dependency.

### Private field

This works:

```java
@Autowired
private Address address;
```

The field does not need to be public.

### Static field

Avoid:

```java
@Autowired
private static Address address;
```

`@Autowired` is not designed to perform normal dependency injection into static fields. Static members belong to the class rather than an individual bean instance.

### Final field

Avoid:

```java
@Autowired
private final Address address;
```

Field injection cannot normally initialize a Java `final` field after construction.

For final dependencies, use constructor injection:

```java
private final Address address;

public Student(Address address) {
    this.address = address;
}
```

---

# 38. Interface as a Dependency

Suppose:

### `Address.java`

```java
package p1;

public interface Address {

    String getAddress();
}
```

### `OfficeAddress.java`

```java
package p1;

public class OfficeAddress implements Address {

    @Override
    public String getAddress() {
        return "Office Address";
    }
}
```

The field can use the interface:

```java
private Address address;
```

while the actual object is:

```text
OfficeAddress
```

This is normal polymorphism:

```text
Address reference
       |
       v
OfficeAddress object
```

---

# 39. XML Bean with Interface

Do not use an interface itself as the bean implementation class:

```xml
<bean id="A1" class="p1.Address"/>
```

if `Address` is an interface.

An interface cannot be instantiated directly.

Instead:

```xml
<bean id="A1"
      class="p1.OfficeAddress"/>
```

Now:

```text
Address -> interface/reference type
OfficeAddress -> actual object type
```

---

# 40. Two Implementations of the Same Interface

Suppose:

```java
public class OfficeAddress implements Address {
    public String getAddress() {
        return "Office Address";
    }
}
```

```java
public class LocalAddress implements Address {
    public String getAddress() {
        return "Local Address";
    }
}
```

XML:

```xml
<bean id="A1" class="p1.OfficeAddress"/>
<bean id="L1" class="p1.LocalAddress"/>
```

Student:

```java
@Autowired
private Address address;
```

Now Spring finds two candidates:

```text
A1 -> OfficeAddress
L1 -> LocalAddress
```

Spring cannot determine which one to inject.

Typical exception:

```text
NoUniqueBeanDefinitionException
```

often wrapped in:

```text
UnsatisfiedDependencyException
```

---

# 41. Resolving Multiple Beans with `@Qualifier`

```java
@Autowired
@Qualifier("A1")
private Address address;
```

or with a setter:

```java
@Autowired
@Qualifier("A1")
public void setAddress(Address address) {
    this.address = address;
}
```

Now Spring chooses:

```text
A1 -> OfficeAddress
```

---

# 42. `@Primary`

`@Primary` marks a bean as the preferred candidate when multiple beans of the same type are available.

Java configuration:

```java
@Bean
@Primary
public Address officeAddress() {
    return new OfficeAddress();
}
```

XML equivalent:

```xml
<bean id="A1"
      class="p1.OfficeAddress"
      primary="true"/>
```

If multiple `Address` beans exist and no qualifier is specified, the primary candidate can be selected.

### If two candidates are both primary

If multiple matching beans are marked primary, `@Primary` no longer gives a unique answer and dependency resolution can still fail.

---

# 43. `@Qualifier` vs `@Primary`

### `@Primary`

Says:

> "Use this bean as the default/preferred candidate."

### `@Qualifier`

Says:

> "Use this specific bean."

Example:

```java
@Autowired
@Qualifier("localAddress")
private Address address;
```

The qualifier provides a specific selection and therefore is used to narrow the candidate set.

---

# 44. `@Component`

`@Component` is a **stereotype annotation** used to mark a class as a Spring-managed component.

Example:

```java
@Component
public class OfficeAddress implements Address {

    @Override
    public String getAddress() {
        return "Office Address";
    }
}
```

With:

```xml
<context:component-scan base-package="p1"/>
```

Spring detects the class and registers it as a bean.

---

# 45. Default Bean Name with `@Component`

If:

```java
@Component
public class OfficeAddress {
}
```

the default bean name is typically:

```text
officeAddress
```

That is, the simple class name with its first character lowercased under Spring's default naming convention.

You can explicitly provide a name:

```java
@Component("office")
public class OfficeAddress {
}
```

Now the bean name is:

```text
office
```

Then:

```java
@Autowired
@Qualifier("office")
private Address address;
```

can select it.

---

# 46. Complete `@Qualifier` Example with Two Implementations

### `Address.java`

```java
package p1;

public interface Address {

    String getAddress();
}
```

### `OfficeAddress.java`

```java
package p1;

import org.springframework.stereotype.Component;

@Component("officeAddress")
public class OfficeAddress implements Address {

    @Override
    public String getAddress() {
        return "Office Address";
    }
}
```

### `LocalAddress.java`

```java
package p1;

import org.springframework.stereotype.Component;

@Component("localAddress")
public class LocalAddress implements Address {

    @Override
    public String getAddress() {
        return "Local Address";
    }
}
```

### `Student.java`

```java
package p1;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;

@Component
public class Student {

    private Address address;

    @Autowired
    public void setAddress(
            @Qualifier("officeAddress") Address address) {

        this.address = address;
    }

    public void show() {
        System.out.println(address.getAddress());
    }
}
```

Spring sees two `Address` implementations but the qualifier tells it exactly which bean is required.

---

# 47. `@Autowired` on a Method

A method can be used for dependency injection.

```java
@Autowired
public void setAddress(Address address) {
    this.address = address;
}
```

The method does not have to be named `setAddress`; Spring can inject through an arbitrary method.

Example:

```java
@Autowired
public void injectAddress(Address address) {
    this.address = address;
}
```

The important part is the annotation and the parameter type.

---

# 48. One Method Can Inject Multiple Dependencies

```java
@Autowired
public void configure(
        Address address,
        LocalAddress localAddress) {

    this.address = address;
    this.localAddress = localAddress;
}
```

Spring resolves both method parameters and calls the method with the required beans.

If the method is made optional using:

```java
@Autowired(required = false)
```

the method can be skipped when the required dependency set cannot be satisfied.

---

# 49. Complete Multiple-Bean Collection Example

### `Address.java`

```java
public class Address {

    private String city;

    public Address(String city) {
        this.city = city;
    }

    public String getCity() {
        return city;
    }
}
```

### `Student.java`

```java
import org.springframework.beans.factory.annotation.Autowired;
import java.util.List;
import java.util.Map;

public class Student {

    private List<Address> addresses;

    private Map<String, Address> addressMap;

    @Autowired
    public void setAddresses(List<Address> addresses) {
        this.addresses = addresses;
    }

    @Autowired
    public void setAddressMap(Map<String, Address> addressMap) {
        this.addressMap = addressMap;
    }

    public void show() {

        System.out.println("List:");
        for (Address address : addresses) {
            System.out.println(address.getCity());
        }

        System.out.println("Map:");
        addressMap.forEach(
            (name, address) ->
                System.out.println(name + " = " + address.getCity())
        );
    }
}
```

### XML

```xml
<bean id="A1" class="p1.Address">
    <constructor-arg value="Indore"/>
</bean>

<bean id="A2" class="p1.Address">
    <constructor-arg value="Bhopal"/>
</bean>

<bean id="A3" class="p1.Address">
    <constructor-arg value="Mandsaur"/>
</bean>

<bean id="S1" class="p1.Student"/>
```

Spring can collect all matching `Address` beans.

---

# 50. Important Exception Mapping

## `NoUniqueBeanDefinitionException`

Used when Spring finds multiple candidates where one was required.

Example:

```text
Address
 |
 +-- A1
 +-- A2
```

and:

```java
@Autowired
Address address;
```

without a qualifier/primary resolution.

---

## `NoSuchBeanDefinitionException`

Used when Spring cannot find a required bean definition.

Example:

```text
Required: Address
Available: no Address bean
```

---

## `UnsatisfiedDependencyException`

Indicates that Spring could not satisfy a dependency required by a bean.

It can wrap a more specific underlying exception such as:

```text
NoSuchBeanDefinitionException
NoUniqueBeanDefinitionException
```

---

## `BeanCurrentlyInCreationException`

Commonly associated with a circular dependency where Spring cannot complete bean creation.

---

# 51. Quick Revision – Autowiring

```text
XML autowire
     |
     +-- no
     +-- byName
     +-- byType
     +-- constructor

Annotation-based
     |
     +-- @Autowired
     +-- @Qualifier
     +-- @Primary
     +-- @Nullable

Component scanning
     |
     +-- @Component
     +-- @Service
     +-- @Repository
     +-- @Controller
```

---

# 52. Quick Revision – Dependency Injection Types

```text
Constructor Injection
        |
        +-- mandatory dependencies
        +-- immutable/final references
        +-- constructor circular dependency not supported

Setter Injection
        |
        +-- optional/configurable dependencies
        +-- dependencies supplied after construction
        +-- certain singleton circular dependencies possible

Field Injection
        |
        +-- @Autowired field
        +-- reflection-based injection
        +-- not suitable for final/static fields
```

---

# 53. Important Corrections to Remember

### 1. XML autowiring default

```text
autowire="no"
```

is the default.

### 2. `@Autowired`

Primarily resolves by type.

### 3. Multiple same-type beans

Use:

```text
@Qualifier
```

or:

```text
@Primary
```

when appropriate.

### 4. `@Component`

Does not work by itself unless the class is registered/discovered, commonly through component scanning.

### 5. `context:annotation-config`

Enables annotation processing for registered beans; it does not perform component scanning.

### 6. `context:component-scan`

Scans packages and registers stereotype-annotated components.

### 7. `final` dependency

Prefer:

```java
private final Address address;

public Student(Address address) {
    this.address = address;
}
```

rather than field injection.

### 8. `@Nullable`

Means the dependency is allowed to be absent and can be supplied as `null`; it does not mean Spring creates a default object.

### 9. `@Autowired(required = false)`

For a field, an absent dependency can leave the field unset. For an optional constructor dependency, use a suitable optional type/design rather than relying on `required=false`.

### 10. Lookup method injection

The lookup method needs to be overridable; avoid `final` class/method declarations.

---

# 54. One-Page Mental Model

```text
                    SPRING IOC CONTAINER
                            |
              +-------------+-------------+
              |                           |
        Dependency Injection         Bean Creation
              |                           |
      +-------+-------+              +----+----+
      |       |       |              |         |
 Constructor Setter  Field        Singleton  Prototype
      |       |       |
      +-------+-------+
              |
          Autowiring
              |
      +-------+---------+
      |       |         |
   byType  Qualifier  Primary
      |
 Multiple candidates?
      |
      +---- Yes ----> Qualifier / Primary
      |
      +---- No -----> Inject

Circular Dependency
      |
      +-- Constructor -> normally fails
      |
      +-- Setter/Field -> certain singleton cycles
                         may be resolved using
                         early references/caches

Lookup Method Injection
      |
 Singleton bean
      |
      +-- lookup method
              |
              v
       Prototype bean
       new instance on lookup
```

---

# 55. Exam-Oriented Short Answers

### What is Lookup Method Injection?

It is a Spring dependency-injection technique in which Spring overrides a method of a bean and obtains another bean from the container whenever that method is called.

### Can lookup method injection be used with an abstract class?

Yes. Spring can generate a subclass and implement/override the lookup method.

### Why does constructor-based circular dependency fail?

Because each bean must be completely constructed with its constructor dependencies before the construction can finish, creating a cycle.

### Why can setter injection resolve some circular dependencies?

Because Spring can instantiate the objects first and populate dependencies afterward, using early singleton references where supported.

### What does `@Qualifier` do?

It selects a specific bean when multiple beans match the required type.

### What does `@Primary` do?

It marks one candidate as the preferred bean when multiple candidates of the same type exist.

### What does `@Component` do?

It marks a class as a Spring component that can be discovered and registered as a bean through component scanning.

### What does `context:annotation-config` do?

It enables processing of annotation-based injection/configuration for registered beans.

### What does `context:component-scan` do?

It scans a package for component classes and registers them as Spring beans.

### Can Spring inject multiple beans at once?

Yes. It can inject matching beans into:

```java
List<T>
Set<T>
Map<String, T>
T[]
```

### What happens if two beans of the same type exist?

Without a resolution mechanism, Spring can throw:

```text
NoUniqueBeanDefinitionException
```

### What happens if a required bean is missing?

Dependency resolution can fail with:

```text
NoSuchBeanDefinitionException
```

often wrapped by:

```text
UnsatisfiedDependencyException
```

### Is `@Autowired` required on a single constructor?

Since Spring 4.3, no. A single constructor can be used automatically.

