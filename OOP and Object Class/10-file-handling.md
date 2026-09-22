# File Handling — Streams, FileOutputStream & FileInputStream

> **Summary:** Java File I/O basics, Byte Streams vs Character Streams, `FileOutputStream` (Write & Append mode), `FileInputStream` (`read()`, why `int` return type, EOF `-1`), aur modern `try-with-resources`.

---

## 1. Java I/O Streams Overview

Java me file reading aur writing **Streams** ke zariye hoti hai. Stream ek continuous sequence of data hota hai.

```text
 ┌─────────────────────── Java I/O ──────────────────────┐
 │ │
 Byte Streams (8-bit bytes) Character Streams (16-bit Unicode)
 (Images, Audio, PDF, Raw Binary) (Plain Text Files)
 ├── InputStream ├── Reader
 │ └── FileInputStream │ └── FileReader / BufferedReader
 └── OutputStream └── Writer
 └── FileOutputStream └── FileWriter / BufferedWriter
```

| Stream Type | Unit of Data | Base Classes | Best Used For |
|-------------|--------------|--------------|---------------|
| **Byte Streams** | 1 byte (8-bit) | `InputStream` / `OutputStream` | Images, videos, zip, binary data, audio |
| **Character Streams** | 1 char (16-bit) | `Reader` / `Writer` | Text files (`.txt`, `.json`, `.csv`), handles UTF-8 / UTF-16 encoding cleanly |

---

## 2. FileOutputStream (Writing to Files)

`FileOutputStream` ka use file me **raw bytes write** karne ke liye hota hai.

### Overwrite Mode (Default)
Agar file pehle se exist karti hai, toh purana content completely erase (overwrite) ho jayega:

```java
FileOutputStream fos = new FileOutputStream("notes.txt");
fos.write('A'); // Writes ASCII value of 'A' (65)
fos.close();
```

### Append Mode (`append = true`)
Agar aap chahte hain ki purana data delete na ho aur naya data file ke end me jud jaye:

```java
// Second parameter 'true' enables APPEND mode!
FileOutputStream fos = new FileOutputStream("notes.txt", true);
```

### Writing Strings to File:
String ko byte array me convert karne ke liye `getBytes()` method use kiya jata hai:

```java
String text = "Hello Java World!\n";
byte[] bytes = text.getBytes();
fos.write(bytes); // Writes entire byte array
```

---

## 3. FileInputStream (Reading from Files)

`FileInputStream` ka use file se **byte-by-byte data read** karne ke liye hota hai.

```java
FileInputStream fis = new FileInputStream("notes.txt");
int data = fis.read(); // Reads single byte
fis.close();
```

### Interview Trap: `read()` ka Return Type `int` kyu hota hai, `byte` kyu nahi?

Java ka `byte` signed hota hai (`-128` se `127`).
- `read()` valid bytes ko `0` se `255` (unsigned range) ke beech represent karta hai.
- **End of Stream / File (EOF)** ko signal karne ke liye `read()` **`-1`** return karta hai.
- Agar return type `byte` hota, toh genuine byte `(byte) 0xFF` (`-1`) aur EOF `-1` me farq nahi pata chalta!
- Isliye `read()` hamesha **`int`** return karta hai!

### Reading an Entire File with a Loop:

```java
int ch;
while ((ch = fis.read()) != -1) {
 System.out.print((char) ch); // Typecast int to char for display
}
```

---

## 4. Modern Best Practice: `try-with-resources`

Pehle streams ko manually `finally` block me `close()` karna padta tha, jo boilerplate aur error-prone tha.
Java 7+ me **`try-with-resources`** introduce hua. Koi bhi class jo `AutoCloseable` implement karti hai, use bracket me declare karo aur JVM use automatically close kar dega — chahe exception aaye ya na aaye!

```java
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;

public class FileIODemo {
 public static void main(String[] args) {
 String filename = "sample.txt";

 // 1. Writing with try-with-resources (Auto close!)
 try (FileOutputStream fos = new FileOutputStream(filename, true)) {
 String msg = "Learning Java File I/O\n";
 fos.write(msg.getBytes());
 System.out.println("Data written successfully!");
 } catch (IOException e) {
 e.printStackTrace();
 }

 // 2. Reading with try-with-resources
 try (FileInputStream fis = new FileInputStream(filename)) {
 int ch;
 System.out.println("File Content:");
 while ((ch = fis.read()) != -1) {
 System.out.print((char) ch);
 }
 } catch (IOException e) {
 e.printStackTrace();
 }
 }
}
```

---

## 5. Byte Stream vs Character Stream Example

```java
// Byte Stream — Raw bytes, might split multi-byte characters (e.g. Hindi, emojis)
FileInputStream fis = new FileInputStream("test.txt");

// Character Stream — Properly handles Unicode, encodings (UTF-8)
FileReader reader = new FileReader("test.txt");
BufferedReader br = new BufferedReader(reader);
String line = br.readLine(); // Reads line-by-line cleanly
```

---

## Interview Quick Traps

| Trap | Answer |
|------|--------|
| `FileInputStream.read()` file end par kya return karta hai? | **`-1`** |
| `read()` byte kyu nahi return karta? | Kyuki byte signed hota hai, aur `-1` EOF signal karne ke liye `int` return type zaroori hai. |
| `FileOutputStream` me bina purana content delete kiye likhne ke liye kya karte hain? | Constructor me `true` pass karte hain: `new FileOutputStream("file.txt", true)`. |
| Stream ko close karna kyu zaroori hai? | Operating System ke file handles leak ho sakte hain, jisse memory/resource leak hota hai. |
| Automatic closing ke liye stream ko kaunsa interface implement karna hota hai? | `java.lang.AutoCloseable`. |

---

[Previous: Singleton Pattern](./09-singleton-pattern.md) · [Back to OOP Index](./README.md) · [Next: Quick Revision](./11-quick-revision.md)
