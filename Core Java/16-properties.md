# Java Properties

## 1. Why do we need a Properties File?

In a Java program, some values change frequently, such as:

-   Database URL
-   Username and password
-   Port number
-   Server address
-   File paths
-   Other application configuration values

Such values are **not recommended to be hardcoded** directly inside the
Java program.

### Problem with Hardcoding

Suppose we write:

```java
String url = "jdbc:mysql://localhost:3306/studentdb";
```

If the value changes, we may need to:

1.  Modify the Java source code.
2.  Compile the application again.
3.  Rebuild the application.
4.  Redeploy the application.
5.  Sometimes restart the server/application.

This can create unnecessary business impact, especially in production
applications.

---

## 2. Solution: Properties File

We can overcome this problem by using a **properties file**.

Frequently changing configuration values are stored in a separate
`.properties` file.

The Java program reads these values from the properties file and uses
them.

### Flow

```text
.properties file
       |
       | load()
       v
Properties object
       |
       | getProperty()
       v
Java Application
```

### Advantage

If a configuration value changes, we can generally update the properties
file instead of changing Java source code.

This reduces the need for recompilation and makes configuration
management easier.

> **Note:** Whether a running application immediately picks up a changed
> properties file depends on how the application loads and caches the
> configuration.

---

# 3. Properties Class

`Properties` is a class available in the `java.util` package.

```java
import java.util.Properties;
```

A `Properties` object is used to hold configuration data, generally as
**String key-value pairs**.

### Syntax

```java
Properties p = new Properties();
```

### Example

```java
Properties p = new Properties();

p.setProperty("username", "Azhar");
p.setProperty("city", "Indore");

System.out.println(p.getProperty("username"));
System.out.println(p.getProperty("city"));
```

### Output

```text
Azhar
Indore
```

---

# 4. Key and Value in Properties

In normal `Map` implementations such as:

-   `HashMap`
-   `Hashtable`
-   `TreeMap`

the key and value types can be defined according to the map being used.

In `Properties`, configuration data is normally represented as:

```text
String key = String value
```

For example:

```text
username=Azhar
city=Indore
```

### Important

The main APIs used for configuration are:

```java
setProperty(String key, String value)
getProperty(String key)
```

Although `Properties` extends `Hashtable<Object,Object>`, configuration
should be handled using String keys and String values.

---

# 5. Important Methods of Properties

| Method | Purpose |
|---|---|
| `setProperty(String key, String value)` | Adds/updates a String property |
| `getProperty(String key)` | Returns the value associated with the key |
| `propertyNames()` | Returns an enumeration of property names |
| `load(...)` | Loads properties from an input stream |
| `store(...)` | Stores properties into an output stream |

---

## 6. setProperty()

Used to add a new property or replace the existing value of a property.

### Syntax

```java
p.setProperty("key", "value");
```

### Example

```java
Properties p = new Properties();

p.setProperty("username", "Azhar");
p.setProperty("password", "12345");
```

If the property already exists, its old value is replaced by the new
value.

```java
p.setProperty("username", "Azhar");
p.setProperty("username", "Khan");
```

Now:

```text
username=Khan
```

---

# 7. getProperty()

Used to retrieve the value associated with a property name.

### Syntax

```java
p.getProperty("propertyName");
```

### Example

```java
Properties p = new Properties();

p.setProperty("name", "Azhar");

String name = p.getProperty("name");

System.out.println(name);
```

### Output

```text
Azhar
```

If the specified property does not exist, `getProperty()` returns
`null`.

```java
System.out.println(p.getProperty("age"));
```

Output:

```text
null
```

---

# 8. getProperty() with Default Value

`getProperty()` also has an overloaded version that accepts a default
value.

### Syntax

```java
p.getProperty("key", "defaultValue");
```

### Example

```java
Properties p = new Properties();

System.out.println(p.getProperty("city", "Indore"));
```

If `city` is not present, it returns:

```text
Indore
```

---

# 9. propertyNames()

Used to obtain the names of the properties.

### Example

```java
Properties p = new Properties();

p.setProperty("name", "Azhar");
p.setProperty("city", "Indore");
p.setProperty("course", "B.Tech");

Enumeration<?> e = p.propertyNames();

while (e.hasMoreElements()) {
    String name = (String) e.nextElement();
    System.out.println(name + " = " + p.getProperty(name));
}
```

---

# 10. Properties File

A properties file normally has the extension:

```text
.properties
```

Example:

```text
application.properties
```

### Example contents

```properties
username=Azhar
password=12345
city=Indore
```

The basic format is:

```text
key=value
```

---

# 11. Loading Properties from a File

We can load the contents of a properties file into a `Properties` object
using the `load()` method.

### Example properties file

**`application.properties`**

```properties
username=Azhar
password=12345
city=Indore
```

### Java Program

```java
import java.io.FileInputStream;
import java.io.IOException;
import java.util.Properties;

public class PropertiesDemo {
    public static void main(String[] args) throws IOException {

        Properties p = new Properties();

        FileInputStream fis =
                new FileInputStream("application.properties");

        p.load(fis);

        System.out.println(p.getProperty("username"));
        System.out.println(p.getProperty("password"));
        System.out.println(p.getProperty("city"));

        fis.close();
    }
}
```

### Output

```text
Azhar
12345
Indore
```

---

# 12. Better Way: try-with-resources

Instead of manually closing the stream, we can use try-with-resources.

```java
import java.io.FileInputStream;
import java.io.IOException;
import java.util.Properties;

public class PropertiesDemo {
    public static void main(String[] args) throws IOException {

        Properties p = new Properties();

        try (FileInputStream fis =
                     new FileInputStream("application.properties")) {

            p.load(fis);
        }

        System.out.println(p.getProperty("username"));
        System.out.println(p.getProperty("city"));
    }
}
```

The stream is automatically closed.

---

# 13. Storing Properties into a File

We can also create a `Properties` object and store its contents into a
properties file using the `store()` method.

### Example

```java
import java.io.FileOutputStream;
import java.io.IOException;
import java.util.Properties;

public class PropertiesStoreDemo {
    public static void main(String[] args) throws IOException {

        Properties p = new Properties();

        p.setProperty("name", "Azhar");
        p.setProperty("city", "Indore");
        p.setProperty("course", "B.Tech");

        try (FileOutputStream fos =
                     new FileOutputStream("student.properties")) {

            p.store(fos, "Student Details");
        }
    }
}
```

This creates:

**`student.properties`**

```properties
#Student Details
#Wed Sep 23 05:00:00 IST 2026
name=Azhar
city=Indore
course=B.Tech
```

> The timestamp in the generated comment will vary.

---

# 14. Loading and Storing --- Complete Flow

```text
                Properties File
                      |
                      | load()
                      v
               Properties Object
                 /           \
                /             \
       getProperty()       setProperty()
              |                 |
              v                 v
       Java Application     store()
                                |
                                v
                         Properties File
```

---

# 15. JDBC Example Using Properties File

Database configuration is a common use case for properties files.

Instead of hardcoding database details in Java:

```java
String url = "jdbc:mysql://localhost:3306/studentdb";
String username = "root";
String password = "root";
```

we can put them inside a properties file.

---

## 16. `db.properties`

```properties
url=jdbc:mysql://localhost:3306/studentdb
username=root
password=root
```

The values can then be read from Java.

---

## 17. Java JDBC Program Using Properties

```java
import java.io.FileInputStream;
import java.sql.Connection;
import java.sql.DriverManager;
import java.util.Properties;

public class JDBCPropertiesDemo {

    public static void main(String[] args) throws Exception {

        Properties p = new Properties();

        try (FileInputStream fis =
                     new FileInputStream("db.properties")) {

            p.load(fis);
        }

        String url = p.getProperty("url");
        String username = p.getProperty("username");
        String password = p.getProperty("password");

        Connection con =
                DriverManager.getConnection(url, username, password);

        System.out.println("Database connected successfully!");

        con.close();
    }
}
```

### How it works

```text
db.properties
      |
      | load()
      v
Properties object
      |
      | getProperty()
      v
url, username, password
      |
      v
DriverManager.getConnection()
      |
      v
Database Connection
```

---

# 18. JDBC Configuration Example with More Properties

We can also store the driver class and other configuration values.

### `db.properties`

```properties
driver=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/studentdb
username=root
password=root
```

### Java

```java
Properties p = new Properties();

try (FileInputStream fis =
             new FileInputStream("db.properties")) {

    p.load(fis);
}

String driver = p.getProperty("driver");
String url = p.getProperty("url");
String username = p.getProperty("username");
String password = p.getProperty("password");

Class.forName(driver);

Connection con =
        DriverManager.getConnection(url, username, password);
```

> With modern JDBC drivers, explicit `Class.forName()` is usually
> unnecessary when the JDBC driver is correctly registered through the
> JDBC 4+ service-provider mechanism. It is shown here because it is
> commonly used in traditional JDBC examples.

---

# 19. Advantages of Properties File

### 1. Avoids hardcoding configuration

Configuration values can be kept outside the Java source code.

### 2. Easy configuration changes

For configuration-only changes, the Java source code does not need to be
modified or recompiled.

### 3. Better maintainability

Application configuration is separated from business logic.

### 4. Reusability

The same Java program can work with different configuration files.

For example:

```text
development.properties
testing.properties
production.properties
```

### 5. Useful for database configuration

Database URL, username and other configuration values can be
externalized.

---

# 20. Important Points to Remember

-   `Properties` belongs to the `java.util` package.
-   `Properties` extends `Hashtable<Object,Object>`.
-   It is mainly designed for **String-based configuration data**.
-   `setProperty()` is used to add/update a property.
-   `getProperty()` is used to read a property.
-   `load()` loads properties from an input stream.
-   `store()` writes properties to an output stream.
-   `propertyNames()` returns property names.
-   A properties file commonly uses the format:

```text
key=value
```

-   Properties files are useful for keeping frequently changing
    configuration outside Java source code.
-   JDBC configuration is a common practical use case.

---

# 21. Quick Revision

```text
Properties p = new Properties();

p.setProperty("key", "value");
```

### Read

```java
p.getProperty("key");
```

### Load

```java
p.load(inputStream);
```

### Store

```java
p.store(outputStream, "comment");
```

### Property File

```properties
username=Azhar
password=12345
```

### Main Concept

```text
Hardcoded Configuration
        ↓
Java Source Code
        ↓
Change → Compile → Build → Deploy

Properties File
        ↓
External Configuration
        ↓
Change Configuration
        ↓
No Java source-code change required
```
