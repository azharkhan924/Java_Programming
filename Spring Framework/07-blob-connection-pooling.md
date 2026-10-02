# Spring Framework -- BLOB Handling & Connection Pooling

> **Topics:** Storing/fetching images and videos with BLOB, Positional vs Named parameter BLOB, Connection Pooling concept, HikariCP, Apache DBCP2, Apache Tomcat JDBC Pool, c3p0, Pool configuration, Spring Boot default pool selection, `BeanPropertyRowMapper`, `queryForList()` patterns

---

# 71. Storing Videos and Images in Database Using BLOB

**BLOB = Binary Large Object**

BLOB columns binary data store karne ke liye use hoti hain, jaise:

-   Images
-   PDFs
-   Audio
-   Small/medium video files
-   Other binary files

MySQL mein four BLOB types hote hain:

  Type                          Maximum length
  -------------- -----------------------------
  `TINYBLOB`                         255 bytes
  `BLOB`                 65,535 bytes ≈ 64 KiB
  `MEDIUMBLOB`       16,777,215 bytes ≈ 16 MiB
  `LONGBLOB`       4,294,967,295 bytes ≈ 4 GiB

Actual transfer limit server/client `max_allowed_packet`, available
memory, etc. se further limited ho sakti hai.

---

# 72. MySQL Table for Image

```sql
CREATE TABLE InsImg (
    uname VARCHAR(50),
    UImg LONGBLOB
);
```

Agar image size small hai to `BLOB`/`MEDIUMBLOB` bhi use kar sakte hain.

For example:

```sql
CREATE TABLE InsImg (
    uname VARCHAR(50),
    UImg MEDIUMBLOB
);
```

---

# 73. BLOB --- Positional Parameter Insert

Yahan SQL mein `?` placeholders use honge.

```java
import java.io.FileInputStream;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.datasource.DriverManagerDataSource;

public class StoreImage {

    public static void main(String[] args)
            throws Exception {

        ApplicationContext app =
            new ClassPathXmlApplicationContext(
                "applicationContext.xml"
            );

        DriverManagerDataSource ds =
            (DriverManagerDataSource)
                app.getBean("dataSource");

        JdbcTemplate jt =
            new JdbcTemplate(ds);

        String q =
            "INSERT INTO InsImg VALUES (?, ?)";

        FileInputStream f1 =
            new FileInputStream(
                "D:/Basics/car.png"
            );

        byte[] imageBytes =
            f1.readAllBytes();

        int x =
            jt.update(
                q,
                "AAA",
                imageBytes
            );

        f1.close();

        System.out.println(
            "Data inserted = " + x
        );
    }
}
```

### Flow

```text
Image File
    ↓
FileInputStream
    ↓
byte[]
    ↓
JdbcTemplate.update()
    ↓
BLOB column
    ↓
MySQL
```

> `readAllBytes()` Java 9+ API hai. Older Java version mein
> `Files.readAllBytes(Path)` ya buffered stream use kar sakte hain.

---

# 74. BLOB Insert --- Cleaner `Files.readAllBytes()`

```java
Path path =
    Paths.get("D:/Basics/car.png");

byte[] imageBytes =
    Files.readAllBytes(path);

int x =
    jt.update(
        "INSERT INTO InsImg VALUES (?, ?)",
        "AAA",
        imageBytes
    );

System.out.println(
    "Data inserted = " + x
);
```

---

# 75. Fetching Image from Database --- Positional Parameter

Database se BLOB read karke file mein write karna:

```java
import java.io.FileOutputStream;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.datasource.DriverManagerDataSource;

public class FetchImage {

    public static void main(String[] args)
            throws Exception {

        ApplicationContext app =
            new ClassPathXmlApplicationContext(
                "applicationContext.xml"
            );

        DriverManagerDataSource ds =
            (DriverManagerDataSource)
                app.getBean("dataSource");

        JdbcTemplate jt =
            new JdbcTemplate(ds);

        String q =
            "SELECT UImg FROM InsImg " +
            "WHERE uname = ?";

        byte[] imageBytes =
            jt.queryForObject(
                q,
                (rs, rowNum) ->
                    rs.getBytes("UImg"),
                "AAA"
            );

        FileOutputStream fo =
            new FileOutputStream(
                "D:/Basics/S3.png"
            );

        fo.write(imageBytes);
        fo.close();

        System.out.println(
            "Image fetched"
        );
    }
}
```

### Alternative --- Column number

```java
byte[] imageBytes =
    jt.queryForObject(
        q,
        (rs, rowNum) ->
            rs.getBytes(1),
        "AAA"
    );
```

Yahan `1` first selected column hai.

---

# 76. Fetching BLOB with `SELECT *`

Agar query:

```java
String q =
    "SELECT * FROM InsImg WHERE uname = ?";
```

Then:

```java
byte[] imageBytes =
    jt.queryForObject(
        q,
        (rs, rowNum) ->
            rs.getBytes(2),
        "AAA"
    );
```

Yahan:

```text
1 → uname
2 → UImg
```

Better practice: jab sirf image chahiye ho, use:

```sql
SELECT UImg
FROM InsImg
WHERE uname = ?
```

instead of:

```sql
SELECT *
```

---

# 77. BLOB Insert Using Named Parameter

Positional:

```sql
INSERT INTO InsImg VALUES (?, ?)
```

Named:

```sql
INSERT INTO InsImg
(uname, UImg)
VALUES
(:uname, :uimg)
```

Java:

```java
import java.io.FileInputStream;
import java.util.HashMap;
import java.util.Map;

import org.springframework.jdbc.core.namedparam.NamedParameterJdbcTemplate;

NamedParameterJdbcTemplate named =
    new NamedParameterJdbcTemplate(ds);

String q =
    "INSERT INTO InsImg " +
    "(uname, UImg) " +
    "VALUES (:uname, :uimg)";

FileInputStream f1 =
    new FileInputStream(
        "D:/Basics/car.png"
    );

byte[] imageBytes =
    f1.readAllBytes();

Map<String, Object> map =
    new HashMap<>();

map.put("uname", "AAA");
map.put("uimg", imageBytes);

int x =
    named.update(q, map);

f1.close();

System.out.println(
    "Data inserted = " + x
);
```

---

# 78. BLOB Insert Using `MapSqlParameterSource`

`MapSqlParameterSource` named parameters ke liye convenient class hai.

```java
String q =
    "INSERT INTO InsImg " +
    "(uname, UImg) " +
    "VALUES (:uname, :uimg)";

byte[] imageBytes =
    Files.readAllBytes(
        Paths.get("D:/Basics/car.png")
    );

SqlParameterSource source =
    new MapSqlParameterSource()
        .addValue("uname", "AAA")
        .addValue("uimg", imageBytes);

int x =
    named.update(q, source);

System.out.println(
    "Data inserted = " + x
);
```

---

# 79. Fetching BLOB Using Named Parameter

```java
String q =
    "SELECT UImg FROM InsImg " +
    "WHERE uname = :uname";

SqlParameterSource source =
    new MapSqlParameterSource()
        .addValue("uname", "AAA");

byte[] imageBytes =
    named.queryForObject(
        q,
        source,
        (rs, rowNum) ->
            rs.getBytes("UImg")
    );

FileOutputStream fo =
    new FileOutputStream(
        "D:/Basics/S3.png"
    );

fo.write(imageBytes);
fo.close();

System.out.println(
    "Image fetched"
);
```

---

# 80. BLOB --- Positional vs Named Parameters

  ------------------------------------------------------------------------------
  Point                   Positional              Named
  ----------------------- ----------------------- ------------------------------
  Placeholder             `?`                     `:name`

  Example                 `WHERE uname = ?`       `WHERE uname = :uname`

  Binding                 Order based             Name based

  Main class              `JdbcTemplate`          `NamedParameterJdbcTemplate`

  Readability             Lower for many          Better
                          parameters              

  Reordering              Can require argument    Parameter names remain
                          reordering              explicit

  Simple query            Very convenient         Also convenient

  Large/complex query     Can become difficult to Usually easier to read
                          maintain                
  ------------------------------------------------------------------------------

### Positional Example

```java
String q =
    "INSERT INTO InsImg VALUES (?, ?)";

jt.update(
    q,
    "AAA",
    imageBytes
);
```

### Named Example

```java
String q =
    "INSERT INTO InsImg " +
    "(uname, UImg) " +
    "VALUES (:uname, :uimg)";

named.update(
    q,
    new MapSqlParameterSource()
        .addValue("uname", "AAA")
        .addValue("uimg", imageBytes)
);
```

---

# 81. Connection Pooling

**Connection Pool = collection of pre-created/reusable database
connections.**

Instead of creating a brand-new physical database connection for every
operation:

```text
Application
     |
     | getConnection()
     v
 Connection Pool
     |
     +---- Connection 1
     +---- Connection 2
     +---- Connection 3
     +---- Connection 4
     |
     v
 Database
```

Typical lifecycle:

```text
Borrow connection
      ↓
Execute SQL
      ↓
Commit / rollback as required
      ↓
Close connection
      ↓
Connection returned to pool
```

Important:

```java
connection.close();
```

In a pool, closing the application-facing connection normally returns it
to the pool rather than necessarily closing the underlying physical
connection.

---

# 82. Why Connection Pooling?

Without pooling, repeated database access may involve:

```text
getConnection()
      ↓
Authentication / network setup
      ↓
Execute SQL
      ↓
Close physical connection
```

Repeated connection creation can add latency and resource overhead.

With pooling:

```text
Pool already has connections
          ↓
Borrow
          ↓
Execute
          ↓
Return
```

### Benefits

-   Connection creation overhead reduce hot path par
-   Lower latency in many workloads
-   Better resource utilization
-   Limits the number of simultaneous DB connections
-   Helps application scalability
-   Database connection lifecycle becomes centrally configurable

> Pool size blindly increase nahi karna chahiye. Database ki capacity,
> application concurrency aur workload ke according tune karna chahiye.

---

# 83. Connection Pooling --- Basic Flow

## Without Pool

```text
Request
  ↓
DriverManager.getConnection()
  ↓
Database
  ↓
SQL
  ↓
Connection close
```

Repeated requests ke liye ye process repeatedly hota hai.

## With Pool

```text
Application startup
        ↓
Create/prepare pool
        ↓
Request
        ↓
Borrow connection
        ↓
SQL
        ↓
Return connection
        ↓
Pool
```

---

# 84. HikariCP

**HikariCP** ek lightweight, high-performance JDBC connection pool hai.

Spring Boot currently HikariCP ko prefer karta hai when it is available.
Spring Boot ke current documentation ke according selection order
HikariCP → Tomcat JDBC pool → Commons DBCP2 hai.
`spring-boot-starter-jdbc` aur `spring-boot-starter-data-jpa` HikariCP
dependency automatically laate hain. citeturn2search3

### Common properties

```text
jdbcUrl
username
password
maximumPoolSize
minimumIdle
connectionTimeout
idleTimeout
maxLifetime
```

---

# 85. HikariCP --- Programmatic Insertion Example

```java
import com.zaxxer.hikari.HikariConfig;
import com.zaxxer.hikari.HikariDataSource;

import org.springframework.jdbc.core.JdbcTemplate;

public class HikariDemo {

    public static void main(String[] args) {

        HikariConfig config =
            new HikariConfig();

        config.setJdbcUrl(
            "jdbc:mysql://localhost:3306/springdb"
        );

        config.setUsername("root");
        config.setPassword("root");

        HikariDataSource ds =
            new HikariDataSource(config);

        JdbcTemplate jt =
            new JdbcTemplate(ds);

        String q =
            "INSERT INTO InsMarks " +
            "VALUES (?, ?, ?, ?, ?)";

        Object[] data = {
            "101",
            "AAA",
            "10",
            "20",
            "30"
        };

        int n =
            jt.update(q, data);

        System.out.println(
            "Inserted = " + n
        );

        ds.close();
    }
}
```

> Modern MySQL JDBC drivers normally do not require
> `setDriverClassName()` when the JDBC URL is enough for DriverManager
> to resolve the JDBC 4+ driver. HikariCP itself recommends omitting
> `driverClassName` unless an older driver requires it or driver
> resolution fails. citeturn0search2

---

# 86. HikariCP --- XML Configuration

```xml
<bean id="config"
      class="com.zaxxer.hikari.HikariConfig">

    <property name="jdbcUrl"
              value="jdbc:mysql://localhost:3306/springdb"/>

    <property name="username"
              value="root"/>

    <property name="password"
              value="root"/>

    <property name="maximumPoolSize"
              value="10"/>

    <property name="minimumIdle"
              value="3"/>

</bean>
```

Main:

```java
ApplicationContext app =
    new ClassPathXmlApplicationContext(
        "applicationContext.xml"
    );

HikariConfig config =
    (HikariConfig)
        app.getBean("config");

HikariDataSource ds =
    new HikariDataSource(config);

JdbcTemplate jt =
    new JdbcTemplate(ds);

String q =
    "INSERT INTO InsMarks " +
    "VALUES (?, ?, ?, ?, ?)";

Object[] data = {
    "101",
    "AAA",
    "10",
    "20",
    "30"
};

int n =
    jt.update(q, data);

System.out.println(
    "Inserted = " + n
);

ds.close();
```

---

# 87. HikariCP --- `minimumIdle`

`minimumIdle` = pool mein maintain karne ki koshish ki jaane wali
minimum number of **idle** connections.

`maximumPoolSize` = pool mein allowed maximum total connections,
including:

-   idle connections
-   in-use connections

HikariCP documentation ke according `maximumPoolSize` ka default 10 hai.
`minimumIdle` ka default `maximumPoolSize` ke equal hota hai; HikariCP
maximum performance/responsiveness ke liye often `minimumIdle`
explicitly set na karke fixed-size pool allow karne ki recommendation
deta hai. citeturn0search2

---

# 88. `minimumIdle = 3`, `maximumPoolSize = 10`

Suppose:

```java
config.setMinimumIdle(3);
config.setMaximumPoolSize(10);
```

Conceptually:

```text
Minimum idle = 3
Maximum total = 10
```

### Startup

Pool available workload ke hisaab se connections maintain karta hai and
minimum idle target ko maintain karne ki koshish karta hai.

```text
Idle target ≈ 3
Total limit = 10
```

### Increased Traffic

Suppose 7 connections in use:

```text
In-use = 7
Idle = 3
Total = 10
```

Agar aur request aaye aur idle connection available nahi hai, pool
`maximumPoolSize` tak new connections establish kar sakta hai.

### At Maximum

```text
Total = 10
Idle = 0
In-use = 10
```

Ab additional `getConnection()` calls available connection ka wait
karenge.

HikariCP mein `connectionTimeout` determine karta hai ki connection
available hone tak caller maximum kitna wait karega; timeout exceed hone
par exception aa sakti hai. citeturn0search2

### Traffic Decreases

Agar pool mein idle connections `minimumIdle` se above hain aur
`minimumIdle < maximumPoolSize`, idle timeout rules ke according extra
idle connections retire ho sakte hain.

```text
10 total
   ↓
traffic decreases
   ↓
extra idle connections
   ↓
idle timeout / pool maintenance
   ↓
pool shrinks toward minimum idle
```

### Stable State

Low/moderate traffic mein pool configured minimum idle ke around
maintain karne ki koshish karega.

---

# 89. HikariCP Pool Stages

  -----------------------------------------------------------------------
  Stage                               Typical behavior
  ----------------------------------- -----------------------------------
  Startup                             Pool initializes/maintains
                                      connections according to
                                      configuration

  Traffic increases                   Borrow idle connections; create
                                      more up to maximum

  Maximum reached                     New borrowers wait for a connection

  Traffic decreases                   Extra idle connections may be
                                      retired when eligible

  Stable state                        Pool maintains configured
                                      target/limits
  -----------------------------------------------------------------------

---

# 90. `minimumIdle == maximumPoolSize`

Example:

```text
minimumIdle = 10
maximumPoolSize = 10
```

Is configuration mein pool effectively fixed-size behavior ke close hota
hai:

```text
10 connections total
10 idle when unused
```

HikariCP specifically notes that leaving `minimumIdle` unset lets it act
as a fixed-size pool because the default minimum idle equals maximum
pool size. citeturn0search2

---

# 91. HikariCP --- Why Driver Class Name Is Often Not Needed

Old-style JDBC examples commonly show:

```java
Class.forName(
    "com.mysql.cj.jdbc.Driver"
);
```

or:

```java
config.setDriverClassName(
    "com.mysql.cj.jdbc.Driver"
);
```

Modern JDBC 4+ drivers can register/load themselves through the JDBC
driver mechanism.

HikariCP can resolve a driver through `DriverManager` using the
`jdbcUrl`; its documentation says `driverClassName` is generally
unnecessary unless an older driver requires it or automatic resolution
fails. citeturn0search2

So:

```java
config.setJdbcUrl(
    "jdbc:mysql://localhost:3306/springdb"
);
```

is often enough when the MySQL JDBC driver is correctly present on the
classpath.

---

# 92. HikariCP --- Manual JAR Note

For a manual, non-Maven setup, the exact supporting JARs depend on the
HikariCP version and logging setup.

Typical project dependencies are:

```text
HikariCP
SLF4J API
SLF4J binding/provider if required by the selected logging setup
MySQL Connector/J
```

With Maven/Gradle, prefer declaring dependencies rather than manually
copying arbitrary JAR versions.

Example Maven:

```xml
<dependency>
    <groupId>com.zaxxer</groupId>
    <artifactId>HikariCP</artifactId>
</dependency>

<dependency>
    <groupId>com.mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
    <scope>runtime</scope>
</dependency>
```

---

# 93. Apache Commons DBCP

**Apache Commons DBCP** = Apache Commons Database Connection Pool.

DBCP2 provides `BasicDataSource` and uses Commons Pool2 for the
underlying pooling mechanism. citeturn1search6turn1search0

Common configuration:

```text
url
username
password
maxTotal
maxIdle
minIdle
maxWait
```

---

# 94. Apache DBCP --- Programmatic Example

```java
import org.apache.commons.dbcp2.BasicDataSource;
import org.springframework.jdbc.core.JdbcTemplate;

public class DBCPDemo {

    public static void main(String[] args) {

        BasicDataSource ds =
            new BasicDataSource();

        ds.setUrl(
            "jdbc:mysql://localhost:3306/springdb"
        );

        ds.setUsername("root");
        ds.setPassword("root");

        ds.setMaxTotal(10);
        ds.setMaxIdle(5);

        JdbcTemplate jt =
            new JdbcTemplate(ds);

        String q =
            "INSERT INTO InsMarks " +
            "VALUES (?, ?, ?, ?, ?)";

        Object[] data = {
            "101",
            "AAA",
            "10",
            "20",
            "30"
        };

        int x =
            jt.update(q, data);

        System.out.println(
            "Inserted = " + x
        );

        try {
            ds.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

DBCP2's `maxTotal` limits the total number of connections that can be
active/allocated, while `maxIdle` limits how many can remain idle.
citeturn1search0

---

# 95. Apache DBCP --- Required Dependencies

For DBCP2:

```text
commons-dbcp2
commons-pool2
MySQL Connector/J
Spring JDBC
```

Apache's documentation explicitly states that the `commons-dbcp2`
artifact relies on `commons-pool2` for the underlying object-pool
mechanisms. citeturn1search6

---

# 96. Apache Tomcat JDBC Connection Pool

Tomcat JDBC Pool is a JDBC connection pool developed as part of Apache
Tomcat.

Main DataSource:

```java
org.apache.tomcat.jdbc.pool.DataSource
```

Common properties:

```text
driverClassName
url
username
password
maxActive
maxIdle
minIdle
maxWait
```

Current Tomcat documentation describes:

-   `maxActive` = maximum active/allocated connections
-   `maxIdle` = maximum idle connections retained
-   `minIdle` = minimum established connections to keep
-   `maxWait` = maximum wait time when pool is exhausted
    citeturn0search3turn0search6

---

# 97. Apache Tomcat JDBC Pool --- Complete Example

```java
import org.apache.tomcat.jdbc.pool.DataSource;
import org.springframework.jdbc.core.JdbcTemplate;

public class TomcatPoolDemo {

    public static void main(String[] args) {

        DataSource ds =
            new DataSource();

        ds.setDriverClassName(
            "com.mysql.cj.jdbc.Driver"
        );

        ds.setUrl(
            "jdbc:mysql://localhost:3306/springdb"
        );

        ds.setUsername("root");
        ds.setPassword("root");

        ds.setMaxActive(10);
        ds.setMaxIdle(5);
        ds.setMinIdle(3);

        JdbcTemplate jt =
            new JdbcTemplate(ds);

        String q =
            "INSERT INTO InsMarks " +
            "VALUES (?, ?, ?, ?, ?)";

        Object[] data = {
            "101",
            "AAA",
            "10",
            "20",
            "30"
        };

        int x =
            jt.update(q, data);

        System.out.println(
            "Inserted = " + x
        );

        ds.close();
    }
}
```

---

# 98. Apache Tomcat JDBC Pool --- JARs

Manual setup commonly needs:

```text
tomcat-jdbc
tomcat-juli
MySQL Connector/J
Spring JDBC
Spring Core/context dependencies
```

Exact JAR versions should be kept compatible with the Tomcat/JDK/Spring
stack being used.

---

# 99. c3p0

**c3p0** is a JDBC connection pooling library.

A common pooled DataSource is:

```java
ComboPooledDataSource
```

It supports pooling-related configuration and can also support
prepared-statement pooling when configured. citeturn3search0

---

# 100. c3p0 --- Complete Example

```java
import com.mchange.v2.c3p0.ComboPooledDataSource;
import org.springframework.jdbc.core.JdbcTemplate;

public class C3P0Demo {

    public static void main(String[] args)
            throws Exception {

        ComboPooledDataSource ds =
            new ComboPooledDataSource();

        ds.setDriverClass(
            "com.mysql.cj.jdbc.Driver"
        );

        ds.setJdbcUrl(
            "jdbc:mysql://localhost:3306/springdb"
        );

        ds.setUser("root");
        ds.setPassword("root");

        ds.setMinPoolSize(3);
        ds.setMaxPoolSize(10);

        JdbcTemplate jt =
            new JdbcTemplate(ds);

        String q =
            "INSERT INTO InsMarks " +
            "VALUES (?, ?, ?, ?, ?)";

        Object[] data = {
            "101",
            "AAA",
            "10",
            "20",
            "30"
        };

        int x =
            jt.update(q, data);

        System.out.println(
            "Inserted = " + x
        );

        ds.close();
    }
}
```

c3p0's current documentation uses `ComboPooledDataSource`, `setJdbcUrl`,
`setUser`, `setPassword`, and pool-size settings such as
`setMinPoolSize` and `setMaxPoolSize`. citeturn3search0

---

# 101. c3p0 --- Basic Pool Configuration

Important properties:

```text
initialPoolSize
minPoolSize
maxPoolSize
acquireIncrement
maxIdleTime
```

For example:

```java
ds.setMinPoolSize(3);
ds.setAcquireIncrement(3);
ds.setMaxPoolSize(10);
```

c3p0 documentation explains that the pool size varies according to usage
between its configured minimum and maximum, and `acquireIncrement`
controls how many connections are acquired when the pool needs more
connections. citeturn3search0

---

# 102. Four Connection Pools --- Comparison

  -----------------------------------------------------------------------------------------------------------
  Pool              Main DataSource                            Strength            Typical limitation /
                                                                                   consideration
  ----------------- ------------------------------------------ ------------------- --------------------------
  c3p0              `ComboPooledDataSource`                    Mature,             Larger configuration
                                                               feature-rich,       surface; often more than
                                                               configurable        needed for a simple modern
                                                                                   Spring Boot app

  Apache DBCP2      `BasicDataSource`                          Mature Apache       More
                                                               ecosystem; Commons  configuration/components
                                                               Pool integration    than a minimal pool

  Tomcat JDBC Pool  `org.apache.tomcat.jdbc.pool.DataSource`   Good Tomcat         Strongest fit when Tomcat
                                                               integration and     ecosystem/integration is
                                                               configurable        relevant
                                                               pooling             

  HikariCP          `HikariDataSource`                         Lightweight,        Requires sensible pool
                                                               high-performance,   sizing and configuration;
                                                               strong concurrency  not a substitute for DB
                                                               characteristics     capacity planning
  -----------------------------------------------------------------------------------------------------------

These are not "good → bad" rankings. They are different implementations
with different trade-offs.

---

# 103. Why Move from One Pool to Another?

Important:

> Ye table actual historical migration timeline nahi hai. Ye **practical
> selection rationale** hai --- agar project mein one pool se another
> pool choose karna ho to kya factors consider kar sakte hain.

## c3p0 → DBCP2

Possible reason:

-   Apache Commons ecosystem prefer karna
-   Existing Apache infrastructure ke saath integration
-   Commons Pool based configuration
-   Existing application mein DBCP standardization

But c3p0 itself remains a valid mature pooling library.
citeturn3search0turn1search6

---

## DBCP2 → Tomcat JDBC Pool

Possible reason:

-   Application Tomcat ecosystem ke close ho
-   Tomcat-specific pooling features/configuration useful ho
-   Existing Tomcat deployment mein same ecosystem use karna

Tomcat JDBC pool has configurable active/idle/min-idle/wait behavior.
citeturn0search3

---

## Tomcat JDBC Pool → HikariCP

Possible reason:

-   Modern Spring Boot application
-   High concurrency
-   Lightweight pool preference
-   HikariCP's performance/concurrency focus
-   Spring Boot's built-in preference for HikariCP when available

Spring Boot's current documentation explicitly says it prefers HikariCP,
then Tomcat pooling, then Commons DBCP2. citeturn2search3

---

# 104. Spring Boot Connection Pool Selection

Current Spring Boot selection logic:

```text
HikariCP available?
      |
     Yes
      ↓
   HikariCP

No
 ↓
Tomcat JDBC pool available?
      |
     Yes
      ↓
Tomcat JDBC Pool

No
 ↓
DBCP2 available?
      |
     Yes
      ↓
DBCP2
```

Spring Boot can also be explicitly configured with:

```properties
spring.datasource.type=...
```

and additional pools can be configured manually. citeturn2search3

---

# 105. Why HikariCP Is Common in Modern Spring Boot

Current Spring Boot documentation says HikariCP is preferred for
performance and concurrency when available, and the JDBC/JPA starters
bring HikariCP automatically. citeturn2search3

Conceptually:

```text
Modern Spring Boot
        ↓
Spring Boot DataSource
        ↓
HikariCP
        ↓
MySQL/PostgreSQL/etc.
```

This does not mean other pools cannot be used.

---

# 106. Fetching Data with `JdbcTemplate.query()`

Suppose:

```sql
SELECT username
FROM InsMarks
```

Code:

```java
String q =
    "SELECT username FROM InsMarks";

List<String> li =
    jt.query(
        q,
        (rs, rowNum) ->
            rs.getString(1)
    );

for (String name : li) {
    System.out.println(name);
}
```

### Concept

```text
ResultSet row
     ↓
rs.getString(1)
     ↓
String
     ↓
List<String>
```

---

# 107. Fetching Name Using Roll Number --- Lambda

```java
String q =
    "SELECT username " +
    "FROM InsMarks " +
    "WHERE userRollNumber = ?";

List<String> li =
    jt.query(
        q,
        (rs, rowNum) ->
            rs.getString("username"),
        "101"
    );

for (String name : li) {
    System.out.println(name);
}
```

If your actual column is `urno`, use:

```sql
WHERE urno = ?
```

and:

```java
rs.getString("uname");
```

according to the actual table schema.

---

# 108. `BeanPropertyRowMapper`

`BeanPropertyRowMapper<T>` database columns ko JavaBean properties se
map karne ke liye use hota hai.

Example:

```java
BeanPropertyRowMapper<Student> rowMapper =
    new BeanPropertyRowMapper<>(
        Student.class
    );
```

Then:

```java
List<Student> li =
    jt.query(
        q,
        rowMapper,
        "101"
    );
```

Column/property naming compatible honi chahiye.

---

# 109. Student Class for `BeanPropertyRowMapper`

```java
public class Student {

    private String userRollNumber;
    private String userName;
    private String userPhysics;
    private String userChemistry;
    private String userMaths;

    public String getUserRollNumber() {
        return userRollNumber;
    }

    public void setUserRollNumber(
            String userRollNumber) {

        this.userRollNumber =
            userRollNumber;
    }

    public String getUserName() {
        return userName;
    }

    public void setUserName(
            String userName) {

        this.userName = userName;
    }

    public String getUserPhysics() {
        return userPhysics;
    }

    public void setUserPhysics(
            String userPhysics) {

        this.userPhysics = userPhysics;
    }

    public String getUserChemistry() {
        return userChemistry;
    }

    public void setUserChemistry(
            String userChemistry) {

        this.userChemistry = userChemistry;
    }

    public String getUserMaths() {
        return userMaths;
    }

    public void setUserMaths(
            String userMaths) {

        this.userMaths = userMaths;
    }

    @Override
    public String toString() {

        return "Student{" +
            "userRollNumber='" +
            userRollNumber + '\'' +
            ", userName='" +
            userName + '\'' +
            ", userPhysics='" +
            userPhysics + '\'' +
            ", userChemistry='" +
            userChemistry + '\'' +
            ", userMaths='" +
            userMaths + '\'' +
            '}';
    }
}
```

---

# 110. Fetching Student Using `BeanPropertyRowMapper`

```java
String q =
    "SELECT " +
    "userRollNumber, " +
    "userName, " +
    "userPhysics, " +
    "userChemistry, " +
    "userMaths " +
    "FROM InsMarks " +
    "WHERE userRollNumber = ?";

BeanPropertyRowMapper<Student> rowMapper =
    new BeanPropertyRowMapper<>(
        Student.class
    );

List<Student> li =
    jt.query(
        q,
        rowMapper,
        "101"
    );

for (Student std : li) {

    System.out.println(
        std.getUserRollNumber()
        + "\t"
        + std.getUserName()
    );
}
```

### Output concept

```text
101    AAA
```

---

# 111. Fetching Name and Roll Number from List of Student Objects

```java
for (Student std : li) {

    System.out.println(
        std.getUserRollNumber()
        + "\t"
        + std.getUserName()
    );
}
```

Yeh:

```text
Student objects ki List
        ↓
each Student object
        ↓
getUserRollNumber()
getUserName()
        ↓
display
```

---

# 112. Fetching Only a List of Names --- `queryForList()`

Suppose:

```sql
SELECT username
FROM InsMarks
```

Use:

```java
String q =
    "SELECT username FROM InsMarks";

List<String> li =
    jt.queryForList(
        q,
        String.class
    );

for (String name : li) {
    System.out.println(name);
}
```

This is useful when you only need one column as a simple Java type.

---

# 113. `queryForList()` with Roll Number

```java
String q =
    "SELECT username " +
    "FROM InsMarks " +
    "WHERE userRollNumber = ?";

List<String> li =
    jt.queryForList(
        q,
        String.class,
        "101"
    );

for (String name : li) {
    System.out.println(name);
}
```

---

# 114. `queryForList()` vs `query()`

### `query()`

Use when you want custom row mapping:

```java
List<Student> li =
    jt.query(
        q,
        rowMapper,
        "101"
    );
```

### `queryForList()`

Use when you want a simple list of a single column type:

```java
List<String> li =
    jt.queryForList(
        q,
        String.class
    );
```

---

# 115. Returning a List of Maps

If multiple columns chahiye and you don't want to create a Java class:

```java
String q =
    "SELECT userRollNumber, username " +
    "FROM InsMarks";

List<Map<String, Object>> li =
    jt.queryForList(q);

for (Map<String, Object> row : li) {

    System.out.println(
        row.get("userRollNumber")
        + "\t"
        + row.get("username")
    );
}
```

Concept:

```text
Database Row
     ↓
Map<String,Object>
     ↓
List<Map<String,Object>>
```

---

# 116. Important JdbcTemplate Retrieval Methods

  Method               Typical result
  -------------------- -----------------------------------
  `query()`            Multiple rows with custom mapping
  `queryForObject()`   One result
  `queryForList()`     List of rows or simple values
  `queryForMap()`      One row as Map
  `update()`           Number of affected rows
  `batchUpdate()`      Array of affected-row counts
  `call()`             Stored procedure result Map

---

# 117. BLOB + Named Parameter + Bean --- Complete Pattern

Agar `Student`/file model mein:

```java
private String uname;
private byte[] uimg;
```

then:

```java
public class ImageData {

    private String uname;
    private byte[] uimg;

    public String getUname() {
        return uname;
    }

    public void setUname(String uname) {
        this.uname = uname;
    }

    public byte[] getUimg() {
        return uimg;
    }

    public void setUimg(byte[] uimg) {
        this.uimg = uimg;
    }
}
```

Named parameter:

```java
String q =
    "INSERT INTO InsImg " +
    "(uname, UImg) " +
    "VALUES (:uname, :uimg)";
```

Parameter source:

```java
SqlParameterSource source =
    new BeanPropertySqlParameterSource(
        imageData
    );
```

Execute:

```java
named.update(q, source);
```

---

# 118. Connection Pool --- Quick Comparison Diagram

```text
                    DataSource
                       |
       +---------------+---------------+
       |               |               |
     c3p0            DBCP2       Tomcat JDBC
       |               |               |
       +---------------+---------------+
                       |
                    HikariCP
                       |
                 Modern Spring Boot
```

**Important:** diagram learning purpose ke liye hai; it does not
represent a literal migration chain.

---

# 119. Quick Revision --- BLOB

```text
BLOB
 ↓
Binary Large Object
 ↓
Binary data
 ↓
Image / PDF / Audio / etc.
```

Types:

```text
TINYBLOB   → 255 bytes
BLOB       → 65,535 bytes
MEDIUMBLOB → 16,777,215 bytes
LONGBLOB   → 4,294,967,295 bytes
```

---

# 120. Quick Revision --- Connection Pooling

```text
Connection Pool
      ↓
Pre-created/reusable DB connections
      ↓
Borrow
      ↓
Use
      ↓
Return
```

### Four pools

```text
c3p0
  ↓
Apache DBCP2
  ↓
Tomcat JDBC Pool
  ↓
HikariCP
```

Again, this is a conceptual comparison/learning sequence, not a claim
that the technologies literally replaced one another in this exact
historical order.

---

# 121. Quick Revision --- HikariCP

```text
HikariConfig
      ↓
HikariDataSource
      ↓
JdbcTemplate
      ↓
SQL
      ↓
Database
```

Important:

```text
minimumIdle
    ↓
minimum idle target

maximumPoolSize
    ↓
maximum total connections
```

---

# 122. Quick Revision --- Positional vs Named

### Positional

```java
String q =
    "SELECT * FROM InsMarks " +
    "WHERE urno = ?";

jt.query(q, rowMapper, "101");
```

### Named

```java
String q =
    "SELECT * FROM InsMarks " +
    "WHERE urno = :urno";

SqlParameterSource source =
    new MapSqlParameterSource()
        .addValue("urno", "101");

named.query(q, source, rowMapper);
```

---

# 123. Exam-Oriented One-Liners

### BLOB

**BLOB is a MySQL binary data type used to store binary large objects
such as images and files.**

### Connection Pool

**Connection pooling is the technique of maintaining reusable database
connections so applications can borrow and return connections instead of
creating a new physical connection for every operation.**

### HikariCP

**HikariCP is a lightweight JDBC connection pool focused on high
performance and concurrency and is preferred by current Spring Boot
auto-configuration when available.**

### `minimumIdle`

**`minimumIdle` specifies the minimum number of idle connections
HikariCP tries to maintain.**

### `maximumPoolSize`

**`maximumPoolSize` specifies the maximum total number of connections,
including idle and in-use connections.**

### DBCP2

**Apache Commons DBCP2 provides pooled JDBC DataSources such as
`BasicDataSource` and uses Commons Pool2 for underlying pooling
mechanisms.**

### Tomcat JDBC Pool

**Tomcat JDBC Pool is a JDBC connection pool provided by the Apache
Tomcat ecosystem.**

### c3p0

**c3p0 is a mature JDBC connection pooling library that provides pooled
DataSource implementations such as `ComboPooledDataSource`.**

### `BeanPropertyRowMapper`

**`BeanPropertyRowMapper` maps database columns to JavaBean
properties.**

### `queryForList()`

**`queryForList()` can be used to retrieve a list of simple values or
rows from a query.**

---

# 124. Official References Used for Verified Facts

-   MySQL BLOB types and maximum lengths: MySQL 8.4 Reference Manual ---
    BLOB and TEXT Types.
-   HikariCP configuration/defaults and driver resolution: HikariCP
    official documentation.
-   Spring Boot connection-pool selection: Spring Boot Reference
    Documentation.
-   Apache Commons DBCP2: Apache Commons DBCP documentation/API.
-   Tomcat JDBC Pool: Apache Tomcat JDBC Pool documentation.
-   c3p0: official c3p0 documentation.

For the examples, package/class names and method signatures should be
matched with the exact library versions in your project.
