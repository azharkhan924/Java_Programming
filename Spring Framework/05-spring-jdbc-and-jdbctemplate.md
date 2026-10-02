# Spring Framework -- Spring JDBC & JdbcTemplate

> **Topics:** DriverManagerDataSource, JdbcTemplate `update()` method, Variable arguments, `query()`, RowMapper, JdbcTemplate as a Bean, Positional vs Named Parameters, NamedParameterJdbcTemplate, `queryForList()`, `queryForObject()`, `BeanPropertyRowMapper`, Alibaba Druid, File insert with Reader

---

# 8. DriverManagerDataSource

Package:

```java
org.springframework.jdbc.datasource.DriverManagerDataSource
```

`DriverManagerDataSource` Spring JDBC ka simple `DataSource`
implementation hai.

It configures a plain JDBC driver using properties such as:

-   Driver class
-   URL
-   Username
-   Password

### Important

`DriverManagerDataSource` connection pooling provide nahi karta.

Generally, `getConnection()` ke through new JDBC connection establish
kiya jaata hai.

### Flow

```text
Spring Container
      |
      v
DriverManagerDataSource
      |
      v
getConnection()
      |
      v
JDBC DriverManager
      |
      v
Database Connection
```

### Features

-   Simple
-   Lightweight
-   Easy setup
-   Direct JDBC usage
-   Connection pooling nahi

### Limitations

-   Connection pooling nahi
-   Repeated connection creation ka overhead
-   High-load production applications ke liye generally preferred choice
    nahi
-   Scalability/performance impact ho sakta hai

Production applications mein connection-pooling DataSource commonly
preferred hota hai.

---

# 9. DriverManagerDataSource --- Java Example

```java
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.datasource.DriverManagerDataSource;

public class MainDemo {

    public static void main(String[] args) {

        DriverManagerDataSource d =
            new DriverManagerDataSource();

        d.setDriverClassName("com.mysql.cj.jdbc.Driver");
        d.setUrl("jdbc:mysql://localhost/springdb");
        d.setUsername("root");
        d.setPassword("root");

        JdbcTemplate j =
            new JdbcTemplate(d);

        String q =
            "INSERT INTO login VALUES ('aaa', '111')";

        j.update(q);

        System.out.println("Data inserted");
    }
}
```

---

# 10. DriverManagerDataSource --- XML

```xml
<bean id="d1"
      class="org.springframework.jdbc.datasource.DriverManagerDataSource">

    <property name="driverClassName"
              value="com.mysql.cj.jdbc.Driver"/>

    <property name="url"
              value="jdbc:mysql://localhost/springdb"/>

    <property name="username"
              value="root"/>

    <property name="password"
              value="root"/>

</bean>
```

---

# 11. JdbcTemplate

Package:

```java
org.springframework.jdbc.core.JdbcTemplate
```

`JdbcTemplate` JDBC ke repetitive boilerplate code ko simplify karta
hai.

```java
JdbcTemplate j = new JdbcTemplate(d);
```

Yahan `d` DataSource hai.

---

# 12. `update()` Method

`update()` generally use hota hai:

-   INSERT
-   UPDATE
-   DELETE

Examples:

```java
j.update(sql);
```

or:

```java
j.update(sql, Object... args);
```

Example:

```java
String q =
    "INSERT INTO login VALUES (?, ?)";

int count =
    j.update(q, "aaa", "111");

System.out.println(count);
```

### Return type

```java
int
```

Usually affected rows ka count return hota hai.

---

# 13. Variable Arguments --- `Object...`

Example:

```java
public void test(Object... values) {

    for (Object value : values) {
        System.out.println(value);
    }
}
```

Call:

```java
test("Azhar", 101, 90.5);
```

JdbcTemplate:

```java
j.update(
    "INSERT INTO login VALUES (?, ?)",
    "Azhar",
    "123"
);
```

---

# 14. Retrieving Data --- `query()`

Simple String result:

```java
List<String> li = j.query(
    "SELECT name FROM login",
    (rs, rowNum) -> rs.getString("name")
);

for (String s : li) {
    System.out.println(s);
}
```

Yahan lambda RowMapper-style mapping provide kar raha hai.

---

# 15. RowMapper

`RowMapper<T>` ka purpose:

```text
Database Row → Java Object
```

Example:

```java
List<Student> list = j.query(
    "SELECT * FROM login",
    new RowMapper<Student>() {

        @Override
        public Student mapRow(
                ResultSet rs,
                int rowNum) throws SQLException {

            Student s = new Student();

            s.setId(rs.getInt("id"));
            s.setName(rs.getString("name"));

            return s;
        }
    }
);
```

---

# 16. MyRowMapper

```java
public class MyRowMapper
        implements RowMapper<Student> {

    @Override
    public Student mapRow(
            ResultSet rs,
            int rowNum) throws SQLException {

        Student s = new Student();

        s.setUname(rs.getString("uname"));
        s.setUpass(rs.getString("upass"));

        return s;
    }
}
```

XML:

```xml
<bean id="m1"
      class="pack1.MyRowMapper"/>
```

Main:

```java
MyRowMapper m =
    (MyRowMapper) app.getBean("m1");

List<Student> li =
    j.query("SELECT * FROM login", m);

for (Student s : li) {
    System.out.println(s);
}
```

---

# 17. JdbcTemplate as a Bean

```xml
<bean id="jdbcTemplate"
      class="org.springframework.jdbc.core.JdbcTemplate">

    <constructor-arg ref="d1"/>

</bean>
```

Then:

```java
JdbcTemplate j =
    (JdbcTemplate) app.getBean("jdbcTemplate");
```

---

# 18. Positional Parameters

Positional parameters mein `?` placeholder use hota hai.

```java
String q =
    "INSERT INTO marks VALUES (?, ?, ?, ?, ?)";
```

Values order ke according bind hongi:

```java
j.update(
    q,
    student.getUrno(),
    student.getUname(),
    student.getUphy(),
    student.getUche(),
    student.getUmath()
);
```

### Important

**Order matters.**

```text
1st ? → 1st argument
2nd ? → 2nd argument
3rd ? → 3rd argument
...
```

---

# 19. Named Parameters

Named parameters mein:

```text
:name
```

format use hota hai.

Example:

```sql
INSERT INTO marks
VALUES (:urno, :uname, :uphy, :uche, :umath)
```

For this, use:

```java
NamedParameterJdbcTemplate
```

### Benefits

-   Readability improve hoti hai
-   Parameter names meaningful hote hain
-   Complex queries samajhna easier
-   Positional ordering dependency reduce hoti hai

---

# 20. NamedParameterJdbcTemplate --- HashMap Example

Student:

```java
Student s1 =
    (Student) app.getBean("s1");
```

Create template:

```java
NamedParameterJdbcTemplate j =
    new NamedParameterJdbcTemplate(d);
```

Query:

```java
String q =
    "INSERT INTO ins_marks " +
    "(urno, uname, physics, chemistry, maths) " +
    "VALUES (:urno, :uname, :physics, :chemistry, :maths)";
```

Map:

```java
Map<String, Object> m =
    new HashMap<>();

m.put("urno", s1.getUrno());
m.put("uname", s1.getUname());
m.put("physics", s1.getPhysics());
m.put("chemistry", s1.getChemistry());
m.put("maths", s1.getMaths());
```

Execute:

```java
int x = j.update(q, m);

if (x > 0) {
    System.out.println("Inserted");
} else {
    System.out.println("Not inserted");
}
```

---

---

# Advanced Query Methods, Druid & File Insert

---

# 1. `queryForList()` --- Simple Values

`queryForList()` ka use tab kar sakte hain jab query se multiple rows
aayein aur humein kisi **single column/value type** ki list chahiye.

### Example

```java
String Q = "SELECT user_name FROM ins_marks WHERE user_roll_number = ?";

List<String> LI =
        jdbcTemplate.queryForList(Q, String.class, 101);

System.out.println(LI);
```

### Important correction

Agar query hai:

```sql
SELECT * FROM ins_marks WHERE user_roll_number = ? AND user_name = ?
```

toh:

```java
jdbcTemplate.queryForList(Q, String.class, 101, "AAA");
```

**sahi nahi hoga**, kyunki `SELECT *` multiple columns return karta hai,
jabki `String.class` ek single column/value ko String mein map karne ke
liye hai.

Agar multiple columns chahiye, `List<Map<String, Object>>` wala form use
karna better hai.

---

# 2. `queryForList()` --- Multiple Columns / Full Rows

Agar query:

```sql
SELECT * FROM ins_marks
```

hai, toh har row ko Spring ek `Map<String, Object>` mein represent kar
sakta hai.

-   Map ka **key** = column name
-   Map ki **value** = us column ki value
-   Saare row-maps = `List` ke andar

### Example

```java
String Q = "SELECT * FROM ins_marks";

List<Map<String, Object>> L =
        jdbcTemplate.queryForList(Q);

System.out.println(L);
```

Conceptually result kuch aisa ho sakta hai:

```text
[
    {user_roll_number=101, user_name=AAA, marks=85},
    {user_roll_number=102, user_name=BBB, marks=90}
]
```

Yaani:

```text
List
 ├── Map (Row 1)
 │    ├── column → value
 │    ├── column → value
 │    └── column → value
 │
 ├── Map (Row 2)
 │    ├── column → value
 │    ├── column → value
 │    └── column → value
```

### Map se value nikalna

```java
for (Map<String, Object> M : L) {
    System.out.println(
        M.get("user_roll_number") + "\t" +
        M.get("user_name")
    );
}
```

> `M.get(...)` ka return type `Object` hota hai, isliye zarurat ke
> according casting/conversion ki ja sakti hai.

### Output

```text
101    AAA
102    BBB
```

---

# 3. `queryForList()` --- Complete Example

```java
import java.util.List;
import java.util.Map;
import org.springframework.jdbc.core.JdbcTemplate;

public class Demo {

    public static void main(String[] args) {

        JdbcTemplate jt = ...;

        String Q = "SELECT * FROM ins_marks";

        List<Map<String, Object>> L =
                jt.queryForList(Q);

        System.out.println(L);

        for (Map<String, Object> M : L) {
            System.out.println(
                M.get("user_roll_number") + "\t" +
                M.get("user_name")
            );
        }
    }
}
```

---

# 4. `queryForList()` with WHERE Condition

Agar specific condition lagani ho:

```java
String Q =
    "SELECT * FROM ins_marks " +
    "WHERE user_roll_number = ? AND user_name = ?";

List<Map<String, Object>> L =
        jdbcTemplate.queryForList(Q, 101, "AAA");

System.out.println(L);
```

Yahaan:

```text
Q              → SQL query
101            → first ?
"AAA"          → second ?
L              → List<Map<String,Object>>
```

---

# 5. `queryForList()` --- Expected Result / Data Type / Behavior / Usage

### Expected Result

**Zero or more rows**, where each row is represented according to the
selected overload.

### Common Return Types

#### Single-column value list

```java
List<String>
```

Example:

```java
String Q = "SELECT user_name FROM ins_marks";

List<String> L =
        jdbcTemplate.queryForList(Q, String.class);
```

#### Multiple-column/full-row result

```java
List<Map<String, Object>>
```

Example:

```java
List<Map<String, Object>> L =
        jdbcTemplate.queryForList("SELECT * FROM ins_marks");
```

### Behavior

-   Agar koi row nahi milti, normally **empty List** return hoti hai.
-   Multiple rows aane par saari rows list mein aa sakti hain.
-   Multiple columns ke liye `List<Map<String,Object>>` useful hai.
-   Single-column result ke liye
    `queryForList(sql, ElementType.class, args...)` use kiya ja sakta
    hai.
-   Query ke selected columns aur requested Java type compatible hone
    chahiye.

### Usage

`queryForList()` tab useful hai jab:

-   multiple rows chahiye;
-   simple values ki list chahiye; ya
-   rows ko `Map<String,Object>` ke form mein quickly access karna ho.

---

# 6. `queryForObject()`

`queryForObject()` ka use generally tab hota hai jab humein **exactly
one result** expected ho.

## Expected Result

Do common cases:

### Case 1 --- Single value

```java
String Q =
    "SELECT user_name FROM ins_marks WHERE user_roll_number = ?";

String name =
    jdbcTemplate.queryForObject(Q, String.class, 101);
```

Expected result:

```text
One String value
```

### Case 2 --- One row mapped to an object

```java
String Q =
    "SELECT user_roll_number, user_name " +
    "FROM ins_marks WHERE user_roll_number = ?";

Student student = jdbcTemplate.queryForObject(
    Q,
    (rs, rowNum) -> {
        Student s = new Student();
        s.setRoll(rs.getInt("user_roll_number"));
        s.setName(rs.getString("user_name"));
        return s;
    },
    101
);
```

Expected result:

```text
One Student object
```

### Return Type

Return type overload par depend karta hai:

```java
String
Integer
Student
Any custom Java object
```

Simple value ke case mein specified type use hota hai:

```java
queryForObject(Q, String.class, ...)
```

Row-mapping ke case mein `RowMapper<T>` ke according:

```java
queryForObject(Q, rowMapper, ...)
```

Return type:

```java
T
```

---

# 7. `queryForObject()` --- Behavior

### Zero rows

Agar query ko ek result expected hai aur **zero rows** milti hain,
Spring JDBC normally `DataAccessException` hierarchy mein
`EmptyResultDataAccessException` throw karta hai.

Example:

```java
String name =
    jdbcTemplate.queryForObject(Q, String.class, 999);
```

Agar roll number 999 ka record nahi hai, exception aa sakta hai.

### More than one row

Agar `queryForObject()` exactly one result expect kar raha hai aur
**more than one row** return ho jaati hain, Spring
`IncorrectResultSizeDataAccessException` hierarchy ki exception throw
karta hai.

Isliye `queryForObject()` ko tab use karo jab query logically **single
result** return kare.

### Best Usage

Aise queries ke liye useful hai jahan `WHERE` clause unique record
identify karta ho:

```sql
SELECT * FROM student WHERE roll = ?
```

Agar `roll` unique hai, toh one student expected hai.

---

# 8. `queryForObject()` vs `queryForList()`

  Feature           `queryForObject()`            `queryForList()`
  ----------------- ----------------------------- -------------------------
  Expected result   Exactly one result            Zero or more results
  Multiple rows     Exception                     List mein multiple rows
  Zero rows         Normally exception            Empty list
  Common return     Single value/object           List
  Use case          Unique record                 Multiple records
  Example           Find student by unique roll   Fetch all students

### Easy Trick

```text
ONE result  → queryForObject()
MANY/0..N   → queryForList()
```

---

# 9. `query()`

`query()` sabse flexible JDBC query methods mein se ek hai.

Iska use tab hota hai jab SQL query se **zero or more rows** fetch karke
har row ko kisi Java object mein map karna ho.

## Expected Result

```text
Zero or more rows
```

## Return Type

Usually:

```java
List<T>
```

Yahaan `T` ka type `RowMapper<T>` decide karta hai.

Example:

```java
List<Student>
```

---

# 10. `query()` with `RowMapper`

```java
String Q = "SELECT * FROM student";

List<Student> L = jdbcTemplate.query(
    Q,
    (rs, rowNum) -> {
        Student s = new Student();

        s.setRoll(rs.getInt("roll"));
        s.setName(rs.getString("name"));

        return s;
    }
);

for (Student s : L) {
    System.out.println(s);
}
```

Yahaan:

```text
SQL
 ↓
JdbcTemplate.query()
 ↓
Rows
 ↓
RowMapper
 ↓
Student objects
 ↓
List<Student>
```

---

# 11. `query()` --- Behavior

-   Agar rows milti hain, har row ko `RowMapper` map karta hai.
-   Agar koi row nahi milti, normally **empty List** return hoti hai.
-   `RowMapper` decide karta hai ki database row ko kis Java object mein
    convert karna hai.
-   Har row ke liye mapper execute hota hai.
-   Return type generally `List<T>` hota hai.

### Usage

`query()` tab useful hai jab:

-   custom Java objects banana ho;
-   multiple rows ko objects ki list mein convert karna ho;
-   custom mapping logic chahiye;
-   `BeanPropertyRowMapper` ya custom `RowMapper` use karna ho.

---

# 12. `query()` vs `queryForObject()` vs `queryForList()`

```text
queryForObject()
    ↓
Exactly one result expected
    ↓
T

queryForList()
    ↓
Zero or more results
    ↓
List<T> / List<Map<String,Object>>

query()
    ↓
Zero or more rows
    ↓
List<T>
    ↓
Mapping controlled by RowMapper
```

---

# 13. `BeanPropertyRowMapper` with `query()`

Agar database column names aur Java bean property names compatible hain,
toh `BeanPropertyRowMapper` use kar sakte hain.

```java
String Q = "SELECT roll, name FROM student";

List<Student> L = jdbcTemplate.query(
    Q,
    new BeanPropertyRowMapper<>(Student.class)
);
```

Student:

```java
public class Student {

    private int roll;
    private String name;

    public int getRoll() {
        return roll;
    }

    public void setRoll(int roll) {
        this.roll = roll;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return roll + " " + name;
    }
}
```

---

# 14. Alibaba Druid

**Alibaba Druid** ek Java database connection pool / DataSource library
hai jo connection pooling ke saath monitoring aur SQL-related
capabilities provide karta hai.

Druid ka important class:

```java
DruidDataSource
```

Package:

```java
com.alibaba.druid.pool.DruidDataSource
```

## Main Features

### 1. Connection Pooling

Database connections ko pool mein maintain karta hai.

```text
Application
    ↓
Druid DataSource
    ↓
Connection Pool
    ↓
MySQL
```

Repeatedly new connection create karne ki cost kam ho sakti hai.

### 2. Monitoring

Druid database activity aur connection usage ko monitor karne ke
features provide karta hai.

### 3. SQL Monitoring / Statistics

SQL execution-related statistics aur monitoring capabilities available
hain.

### 4. SQL Parser / Firewall Features

Druid ecosystem mein SQL parsing aur SQL security/firewall related
capabilities bhi available hain.

---

# 15. Druid with `JdbcTemplate`

Basic structure:

```java
import com.alibaba.druid.pool.DruidDataSource;
import org.springframework.jdbc.core.JdbcTemplate;

public class App {

    public static void main(String[] args) {

        DruidDataSource ds = new DruidDataSource();

        ds.setUrl("jdbc:mysql://localhost:3306/testdb");
        ds.setUsername("root");
        ds.setPassword("root");

        JdbcTemplate jt = new JdbcTemplate(ds);

        String Q =
            "INSERT INTO student VALUES (?, ?)";

        int n = jt.update(Q, 101, "AAA");

        System.out.println(n);
    }
}
```

### Basic Flow

```text
DruidDataSource
      ↓
Connection Pool
      ↓
JdbcTemplate
      ↓
SQL operation
      ↓
Database
```

---

# 16. Inserting a File into Database using `Reader`

Agar database column character/text data store karta hai, toh `Reader` /
character stream approach useful ho sakti hai.

Notebook-style example:

```java
import java.io.FileReader;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.jdbc.core.JdbcTemplate;

public class App {

    public static void main(String[] args) throws Exception {

        ApplicationContext ac =
            new ClassPathXmlApplicationContext("applicationContext.xml");

        JdbcTemplate jt = ac.getBean(JdbcTemplate.class);

        String Q =
            "INSERT INTO ins_file VALUES (?)";

        FileReader fr =
            new FileReader("D:\\basics\\demo.java");

        int n = jt.update(Q, fr);

        System.out.println(n);

        fr.close();
    }
}
```

### Important

`FileReader` character data read karta hai.

Isliye database column ko appropriate character/text type ka hona
chahiye.

Agar **binary file** (image, PDF, ZIP, etc.) store karni ho, toh
`FileReader` nahi; normally `InputStream` / BLOB approach use ki jaati
hai.

Example:

```java
FileInputStream fis =
    new FileInputStream("D:\\images\\photo.jpg");
```

---

# 17. Better Resource Handling --- `try-with-resources`

Modern Java mein resource automatically close karne ke liye:

```java
try (FileReader fr =
         new FileReader("D:\\basics\\demo.java")) {

    int n = jt.update(Q, fr);

    System.out.println(n);
}
```

Isse `FileReader` automatically close ho jaata hai.

---

# 18. Named Parameter --- File Insert

Positional parameter:

```java
String Q =
    "INSERT INTO ins_file VALUES (?)";

FileReader fr =
    new FileReader("D:\\basics\\demo.java");

int n = jt.update(Q, fr);
```

Named parameter ke liye:

```java
NamedParameterJdbcTemplate named =
    new NamedParameterJdbcTemplate(jt.getDataSource());
```

Query:

```java
String Q =
    "INSERT INTO ins_file(file_data) VALUES (:file_data)";
```

Parameter:

```java
MapSqlParameterSource source =
    new MapSqlParameterSource();

source.addValue("file_data", fr);
```

Update:

```java
int n = named.update(Q, source);

System.out.println(n);
```

---

# 19. Complete Named-Parameter File Insert Example

```java
import java.io.FileReader;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.core.namedparam.MapSqlParameterSource;
import org.springframework.jdbc.core.namedparam.NamedParameterJdbcTemplate;

public class App {

    public static void main(String[] args) throws Exception {

        ApplicationContext ac =
            new ClassPathXmlApplicationContext(
                "applicationContext.xml"
            );

        JdbcTemplate jt =
            ac.getBean(JdbcTemplate.class);

        NamedParameterJdbcTemplate named =
            new NamedParameterJdbcTemplate(
                jt.getDataSource()
            );

        String Q =
            "INSERT INTO ins_file(file_data) " +
            "VALUES (:file_data)";

        try (FileReader fr =
                 new FileReader("D:\\basics\\demo.java")) {

            MapSqlParameterSource source =
                new MapSqlParameterSource();

            source.addValue("file_data", fr);

            int n = named.update(Q, source);

            System.out.println(n);
        }
    }
}
```

---

# 20. Positional vs Named Parameter

## Positional

```sql
INSERT INTO ins_file(file_data)
VALUES (?)
```

Java:

```java
jt.update(Q, fr);
```

## Named

```sql
INSERT INTO ins_file(file_data)
VALUES (:file_data)
```

Java:

```java
source.addValue("file_data", fr);
named.update(Q, source);
```

### Difference

```text
?            → positional parameter
:file_data   → named parameter
```

Named parameters large queries mein readability improve kar sakte hain,
especially jab multiple parameters hon.

---

# 21. Quick Revision

### `queryForObject()`

```text
Expected → exactly one result
Return   → T
0 rows   → normally exception
>1 rows  → result-size exception
Use      → unique/single record
```

### `queryForList()`

```text
Expected → zero or more results
Return   → List<T>
          or List<Map<String,Object>>
0 rows   → empty list
Use      → multiple/simple values or map-based rows
```

### `query()`

```text
Expected → zero or more rows
Return   → List<T>
Mapping  → RowMapper<T>
0 rows   → empty list
Use      → custom Java object mapping
```

### Druid

```text
Alibaba Druid
      ↓
DataSource / Connection Pool
      ↓
Pooling + Monitoring + SQL-related features
      ↓
JdbcTemplate
      ↓
Database
```

### File insert

```text
Text file
   ↓
FileReader
   ↓
JdbcTemplate / NamedParameterJdbcTemplate
   ↓
Character/Text column
```

Binary file:

```text
Image/PDF/etc.
   ↓
InputStream
   ↓
BLOB column
```

---

# 22. One-Line Exam Definitions

**`queryForObject()`**\
A JdbcTemplate method used when a query is expected to return exactly
one result; it returns a single value/object and throws a
result-size-related exception when the result count does not match the
expectation.

**`queryForList()`**\
A JdbcTemplate method used to retrieve zero or more results as a list,
commonly as `List<T>` for a single selected column or
`List<Map<String,Object>>` for rows with multiple columns.

**`query()`**\
A flexible JdbcTemplate method that retrieves zero or more rows and maps
each row to a Java object using a `RowMapper`.

**Alibaba Druid**\
A Java DataSource/connection-pooling library from Alibaba that provides
connection pooling along with database monitoring and SQL-related
capabilities.

**FileReader**\
A Java character-stream class used to read character data from a file;
it is appropriate for text-oriented database columns, not binary BLOB
data.
