# BigInteger in Java

> **Package:** `java.math`\
> **Class:** `BigInteger`

`BigInteger` का use तब किया जाता है जब हमें **बहुत बड़े integer numbers** के
साथ काम करना हो, जो normal `int` या `long` की fixed range से बाहर हो सकते
हैं।

------------------------------------------------------------------------

## 1. Why BigInteger?

Normal integer types की fixed limit होती है:

``` java
int   → 32-bit
long  → 64-bit
```

अगर number बहुत बड़ा है:

``` java
long x = 999999999999999999999999L;
```

तो यह normal `long` की range से बाहर हो जाएगा।

ऐसे cases में:

``` java
BigInteger x = new BigInteger("999999999999999999999999");
```

use करते हैं।

### Important

`BigInteger` **integer values** के लिए है।

Decimal values के लिए:

``` java
BigDecimal
```

use किया जाता है।

------------------------------------------------------------------------

# 2. Import

``` java
import java.math.BigInteger;
```

------------------------------------------------------------------------

# 3. Creating BigInteger Object

## Method 1 --- `valueOf()`

छोटे `long` value से:

``` java
BigInteger x = BigInteger.valueOf(10);
BigInteger y = BigInteger.valueOf(20);
```

यह convenient तरीका है जब value `long` के अंदर represent हो सकती है।

------------------------------------------------------------------------

## Method 2 --- Constructor with String

बहुत बड़े number के लिए:

``` java
BigInteger x = new BigInteger("123456789123456789123456789");
```

यहाँ number को **String** में देना पड़ता है।

``` java
BigInteger x = new BigInteger("999999999999999999999999999999");
```

क्योंकि अगर हम पहले ही इसे `long` literal बना देंगे, तो number `long` की limit
से बाहर होने पर problem आ जाएगी।

### ❌ Wrong

``` java
BigInteger x = BigInteger.valueOf(999999999999999999999L);
```

अगर literal `long` की range से बाहर है तो compile-time problem होगी।

### ✅ Correct

``` java
BigInteger x =
    new BigInteger("999999999999999999999");
```

------------------------------------------------------------------------

# 4. BigInteger Arithmetic

BigInteger में normal operators directly use नहीं कर सकते।

### ❌ Wrong

``` java
x + y
x - y
x * y
x / y
```

### ✅ Correct Methods

``` java
x.add(y);
x.subtract(y);
x.multiply(y);
x.divide(y);
x.mod(y);
```

------------------------------------------------------------------------

## Example

``` java
BigInteger x = BigInteger.valueOf(10);
BigInteger y = BigInteger.valueOf(20);

BigInteger add = x.add(y);
BigInteger sub = x.subtract(y);
BigInteger mul = x.multiply(y);
BigInteger div = x.divide(y);
BigInteger mod = x.mod(y);
```

### Output

``` text
add = 30
sub = -10
mul = 200
div = 0
mod = 10
```

### Important --- BigInteger is Immutable

BigInteger objects **immutable** होते हैं।

इसलिए:

``` java
x.add(y);
```

से `x` change नहीं होता।

Result को store करना हो तो:

``` java
x = x.add(y);
```

या:

``` java
BigInteger z = x.add(y);
```

करना पड़ेगा।

------------------------------------------------------------------------

# 5. Important BigInteger Methods

  Method          Purpose
  --------------- -------------------------
  `add()`         Addition
  `subtract()`    Subtraction
  `multiply()`    Multiplication
  `divide()`      Division
  `mod()`         Remainder
  `abs()`         Absolute value
  `pow()`         Power
  `gcd()`         GCD
  `max()`         Maximum of two values
  `min()`         Minimum of two values
  `compareTo()`   Compare two BigIntegers
  `toString()`    Convert to String
  `intValue()`    Convert to int
  `longValue()`   Convert to long

------------------------------------------------------------------------

# 6. BigInteger Constants

Java में कुछ predefined constants भी होते हैं:

``` java
BigInteger.ZERO
BigInteger.ONE
BigInteger.TWO
BigInteger.TEN
```

Example:

``` java
BigInteger x = BigInteger.ZERO;
BigInteger y = BigInteger.ONE;
```

> `BigInteger.TWO` Java 9 से available है।

------------------------------------------------------------------------

# 7. BigInteger Comparison

BigInteger के साथ `==` use करके values compare नहीं करनी चाहिए।

### ❌ Wrong

``` java
if (x == y)
```

### ✅ Using `equals()`

``` java
if (x.equals(y))
```

या numerical comparison के लिए:

``` java
x.compareTo(y)
```

### `compareTo()`

``` java
x.compareTo(y)
```

returns:

``` text
0   → x == y
< 0 → x < y
> 0 → x > y
```

Example:

``` java
BigInteger x = BigInteger.valueOf(10);
BigInteger y = BigInteger.valueOf(20);

if (x.compareTo(y) < 0)
    System.out.println("x is smaller");
```

------------------------------------------------------------------------

# 8. Power

``` java
BigInteger x = BigInteger.valueOf(2);

BigInteger result = x.pow(10);

System.out.println(result);
```

Output:

``` text
1024
```

------------------------------------------------------------------------

# 9. GCD

GCD निकालने के लिए:

``` java
BigInteger x = BigInteger.valueOf(48);
BigInteger y = BigInteger.valueOf(18);

BigInteger result = x.gcd(y);

System.out.println(result);
```

Output:

``` text
6
```

------------------------------------------------------------------------

# 10. Converting BigInteger to String

``` java
BigInteger x = new BigInteger("123456789123456789");

String s = x.toString();
```

अब `s` में String representation होगी।

------------------------------------------------------------------------

# 11. BigInteger vs int/long

  Feature              `int`         `long`        `BigInteger`
  -------------------- ------------- ------------- ---------------------
  Size                 Fixed         Fixed         Arbitrary precision
  Primitive/Object     Primitive     Primitive     Class/Object
  Operators            `+ - * /`     `+ - * /`     Methods
  Very large numbers   ❌            Limited       ✅
  Package              `java.lang`   `java.lang`   `java.math`
  Immutable            ---           ---           ✅

------------------------------------------------------------------------

# 12. BigInteger Object

``` java
BigInteger x = new BigInteger("100000000000000000000");
```

यहाँ:

``` text
BigInteger
    ↓
class

x
    ↓
reference variable

new BigInteger(...)
    ↓
object
```

मतलब BigInteger **class है और object create करके use करते हैं**।

------------------------------------------------------------------------

# 13. Why BigInteger Instead of long?

Suppose:

``` java
long x = 9223372036854775807L;
```

यह `long` की maximum value है।

लेकिन अगर हमें इससे भी बड़ा integer चाहिए:

``` text
123456789123456789123456789123456789
```

तो `long` sufficient नहीं है।

इस situation में:

``` java
BigInteger x =
    new BigInteger("123456789123456789123456789123456789");
```

use करेंगे।

------------------------------------------------------------------------

# 14. Important Use Cases

BigInteger का use mainly:

-   बहुत बड़े mathematical calculations
-   Competitive Programming
-   Cryptography
-   RSA जैसी cryptographic calculations
-   Large factorials
-   Large Fibonacci numbers
-   Large combinations/permutations
-   Exact integer calculations

में किया जाता है।

------------------------------------------------------------------------

# 15. Small Complete Example

``` java
import java.math.BigInteger;

class Demo {
    public static void main(String[] args) {

        BigInteger x =
            new BigInteger("100000000000000000000");

        BigInteger y =
            new BigInteger("200000000000000000000");

        System.out.println(x.add(y));

        System.out.println(y.subtract(x));

        System.out.println(x.multiply(y));

        System.out.println(y.divide(x));

        System.out.println(y.mod(x));
    }
}
```

------------------------------------------------------------------------

# 16. Quick Revision

``` text
BigInteger
    ↓
Very Large Integer
    ↓
java.math
    ↓
Object based
    ↓
Operators नहीं
    ↓
Methods use करो

+  → add()
-  → subtract()
*  → multiply()
/  → divide()
%  → mod()
```

------------------------------------------------------------------------

# 17. Exam के लिए Most Important

``` java
import java.math.BigInteger;

BigInteger x = BigInteger.valueOf(10);

BigInteger y = new BigInteger("999999999999999999999");

x.add(y);
x.subtract(y);
x.multiply(y);
x.divide(y);
x.mod(y);

BigInteger.ZERO;
BigInteger.ONE;
BigInteger.TWO;
BigInteger.TEN;
```

### One-line Definition

> **BigInteger is an immutable class of `java.math` package used to
> perform arithmetic operations on arbitrarily large integer values
> without the fixed-size limitation of primitive integer types.**
