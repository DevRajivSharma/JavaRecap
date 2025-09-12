# Data Types in Java

Java is a statically-typed language, which means that all variables must be declared with their data type before they can be used. Java provides two categories of data types: primitive types and reference types.

## Primitive Data Types

Java has eight primitive data types that represent simple values. These are not objects and do not have methods.

### 1. Integer Types

| Type | Size | Range | Default Value |
|------|------|-------|---------------|
| `byte` | 8 bits | -128 to 127 | 0 |
| `short` | 16 bits | -32,768 to 32,767 | 0 |
| `int` | 32 bits | -2^31 to 2^31-1 | 0 |
| `long` | 64 bits | -2^63 to 2^63-1 | 0L |

```java
byte b = 100;
short s = 1000;
int i = 100000;
long l = 10000000000L;  // Note the 'L' suffix for long literals
```

### 2. Floating-Point Types

| Type | Size | Range | Default Value |
|------|------|-------|---------------|
| `float` | 32 bits | Approximately ±3.40282347E+38F | 0.0F |
| `double` | 64 bits | Approximately ±1.79769313486231570E+308 | 0.0D |

```java
float f = 3.14F;  // Note the 'F' suffix for float literals
double d = 3.14159265359;
```

### 3. Character Type

| Type | Size | Range | Default Value |
|------|------|-------|---------------|
| `char` | 16 bits | 0 to 65,535 (Unicode characters) | '\u0000' |

```java
char c1 = 'A';
char c2 = '\u0041';  // Unicode representation of 'A'
char c3 = 65;        // ASCII value of 'A'
```

### 4. Boolean Type

| Type | Size | Range | Default Value |
|------|------|-------|---------------|
| `boolean` | 1 bit | true or false | false |

```java
boolean isJavaFun = true;
boolean isBoring = false;
```

## Reference Data Types

Reference types are used to access objects. These include:

### 1. Class Types

Classes are user-defined types that serve as blueprints for objects.

```java
String name = "John Doe";
Person person = new Person("John", 30);
```

### 2. Interface Types

Interfaces define a contract that implementing classes must follow.

```java
List<String> names = new ArrayList<>();
Map<String, Integer> ages = new HashMap<>();
```

### 3. Array Types

Arrays store multiple values of the same type.

```java
int[] numbers = {1, 2, 3, 4, 5};
String[] fruits = new String[3];
fruits[0] = "Apple";
fruits[1] = "Banana";
fruits[2] = "Orange";
```

### 4. Enum Types

Enums define a set of constants.

```java
enum Day {
    MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY
}

Day today = Day.MONDAY;
```

## Type Conversion

Java supports two types of type conversion: implicit (automatic) and explicit (casting).

### 1. Implicit Conversion (Widening)

Automatic conversion happens when converting from a smaller type to a larger type.

```java
byte b = 100;
int i = b;    // Implicit conversion from byte to int
long l = i;   // Implicit conversion from int to long
float f = l;  // Implicit conversion from long to float
double d = f; // Implicit conversion from float to double
```

Implicit conversion follows this hierarchy:
```
byte → short → int → long → float → double
```

### 2. Explicit Conversion (Narrowing)

Explicit conversion is required when converting from a larger type to a smaller type, which might result in data loss.

```java
double d = 100.04;
long l = (long)d;    // Explicit conversion from double to long
int i = (int)l;      // Explicit conversion from long to int
short s = (short)i;  // Explicit conversion from int to short
byte b = (byte)s;    // Explicit conversion from short to byte
```

## Wrapper Classes

Java provides wrapper classes for each primitive type, allowing them to be used as objects.

| Primitive Type | Wrapper Class |
|----------------|---------------|
| `byte` | `Byte` |
| `short` | `Short` |
| `int` | `Integer` |
| `long` | `Long` |
| `float` | `Float` |
| `double` | `Double` |
| `char` | `Character` |
| `boolean` | `Boolean` |

```java
// Creating wrapper objects
Integer num = Integer.valueOf(100);
Double pi = Double.valueOf(3.14159);

// Auto-boxing (automatic conversion from primitive to wrapper)
Integer autoBoxed = 100;

// Unboxing (automatic conversion from wrapper to primitive)
int unboxed = autoBoxed;
```

## Literals

Literals are fixed values that are directly written in the code.

### 1. Integer Literals

```java
// Decimal (base 10)
int decimal = 100;

// Octal (base 8) - prefix with 0
int octal = 0144;  // Equivalent to 100 in decimal

// Hexadecimal (base 16) - prefix with 0x or 0X
int hex = 0x64;    // Equivalent to 100 in decimal

// Binary (base 2) - prefix with 0b or 0B (Java 7+)
int binary = 0b1100100;  // Equivalent to 100 in decimal

// Integer literals with underscore (Java 7+)
int million = 1_000_000;  // Equivalent to 1000000
```

### 2. Floating-Point Literals

```java
// Double literal (default)
double d1 = 3.14;
double d2 = 3.14d;  // Explicit double suffix
double d3 = 3.14D;  // Explicit double suffix

// Float literal
float f1 = 3.14f;   // Explicit float suffix
float f2 = 3.14F;   // Explicit float suffix

// Scientific notation
double sci1 = 3.14e2;  // 3.14 * 10^2 = 314.0
double sci2 = 3.14E-2; // 3.14 * 10^-2 = 0.0314
```

### 3. Character Literals

```java
char c1 = 'A';         // Single character
char c2 = '\t';        // Tab character
char c3 = '\u0041';    // Unicode representation of 'A'
```

### 4. String Literals

```java
String s1 = "Hello, World!";
String s2 = "Java\nProgramming";  // With newline
String s3 = """                    // Text block (Java 15+)
           This is a
           multi-line
           string.
           """;
```

### 5. Boolean Literals

```java
boolean b1 = true;
boolean b2 = false;
```

### 6. Null Literal

```java
String str = null;
Object obj = null;
```

## Constants

Constants are variables whose values cannot be changed once assigned. In Java, constants are declared using the `final` keyword.

```java
final double PI = 3.14159;
final int MAX_USERS = 100;

// For class constants (static final)
public class Constants {
    public static final int MAX_CONNECTIONS = 100;
    public static final String APP_NAME = "MyApp";
}
```

## Type Inference with var (Java 10+)

Java 10 introduced the `var` keyword for local variable type inference, allowing the compiler to determine the type based on the initialization expression.

```java
// Before Java 10
String message = "Hello, World!";
ArrayList<String> names = new ArrayList<>();

// With var (Java 10+)
var message = "Hello, World!";  // Inferred as String
var names = new ArrayList<String>();  // Inferred as ArrayList<String>
var number = 42;  // Inferred as int
```

Restrictions on `var`:
- Can only be used for local variables
- Cannot be used for method parameters, return types, or fields
- Must be initialized at declaration
- Cannot be initialized with null without a type

## Best Practices

1. Choose the appropriate data type based on the expected range of values
2. Use `int` for most integer values unless you have a specific reason to use another type
3. Use `double` for most floating-point calculations unless memory is a concern
4. Use wrapper classes when working with collections or when null values are needed
5. Use meaningful names for variables that indicate their purpose and data type
6. Declare constants for values that should not change
7. Use `var` judiciously, only when the type is obvious from the context
8. Be careful with type conversions to avoid data loss

```java
// Good practice examples
public class DataTypeExample {
    // Constants
    private static final int MAX_USERS = 100;
    private static final double TAX_RATE = 0.08;
    
    public void processUserData(String username, int age) {
        // Local variables with appropriate types
        boolean isAdult = age >= 18;
        double discountRate = isAdult ? 0.1 : 0.05;
        
        // Using var where type is obvious (Java 10+)
        var message = "Welcome, " + username;
        var items = new ArrayList<String>();
        
        // Careful type conversion
        long userId = 1000000000L;
        int userIdInt = (int)userId;  // Potential data loss, be cautious
        
        System.out.println(message);
    }
}
```