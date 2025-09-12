# Operators in Java

Operators in Java are special symbols that perform operations on variables and values. Java provides a rich set of operators for different types of operations.

## Arithmetic Operators

Arithmetic operators are used to perform common mathematical operations.

| Operator | Description | Example |
|----------|-------------|--------|
| `+` | Addition | `a + b` |
| `-` | Subtraction | `a - b` |
| `*` | Multiplication | `a * b` |
| `/` | Division | `a / b` |
| `%` | Modulus (remainder) | `a % b` |
| `++` | Increment | `a++` or `++a` |
| `--` | Decrement | `a--` or `--a` |

```java
int a = 10;
int b = 3;

int sum = a + b;        // 13
int difference = a - b;  // 7
int product = a * b;     // 30
int quotient = a / b;    // 3 (integer division)
int remainder = a % b;   // 1

int c = 5;
c++;  // c is now 6 (post-increment)
++c;  // c is now 7 (pre-increment)
c--;  // c is now 6 (post-decrement)
--c;  // c is now 5 (pre-decrement)
```

### Pre vs. Post Increment/Decrement

- **Pre-increment/decrement**: The value is incremented/decremented before it is used in the expression
- **Post-increment/decrement**: The value is incremented/decremented after it is used in the expression

```java
int a = 5;
int b = 5;

int resultA = ++a;  // a is incremented to 6, then assigned to resultA (resultA = 6)
int resultB = b++;  // b is assigned to resultB, then incremented to 6 (resultB = 5)

System.out.println(a);       // 6
System.out.println(b);       // 6
System.out.println(resultA); // 6
System.out.println(resultB); // 5
```

## Assignment Operators

Assignment operators are used to assign values to variables.

| Operator | Description | Example | Equivalent |
|----------|-------------|---------|------------|
| `=` | Simple assignment | `a = b` | `a = b` |
| `+=` | Add and assign | `a += b` | `a = a + b` |
| `-=` | Subtract and assign | `a -= b` | `a = a - b` |
| `*=` | Multiply and assign | `a *= b` | `a = a * b` |
| `/=` | Divide and assign | `a /= b` | `a = a / b` |
| `%=` | Modulus and assign | `a %= b` | `a = a % b` |
| `&=` | Bitwise AND and assign | `a &= b` | `a = a & b` |
| `|=` | Bitwise OR and assign | `a |= b` | `a = a | b` |
| `^=` | Bitwise XOR and assign | `a ^= b` | `a = a ^ b` |
| `<<=` | Left shift and assign | `a <<= b` | `a = a << b` |
| `>>=` | Right shift and assign | `a >>= b` | `a = a >> b` |
| `>>>=` | Unsigned right shift and assign | `a >>>= b` | `a = a >>> b` |

```java
int a = 10;

a += 5;  // a = a + 5 (a is now 15)
a -= 3;  // a = a - 3 (a is now 12)
a *= 2;  // a = a * 2 (a is now 24)
a /= 4;  // a = a / 4 (a is now 6)
a %= 4;  // a = a % 4 (a is now 2)
```

## Comparison Operators

Comparison operators are used to compare two values. They return a boolean result (`true` or `false`).

| Operator | Description | Example |
|----------|-------------|--------|
| `==` | Equal to | `a == b` |
| `!=` | Not equal to | `a != b` |
| `>` | Greater than | `a > b` |
| `<` | Less than | `a < b` |
| `>=` | Greater than or equal to | `a >= b` |
| `<=` | Less than or equal to | `a <= b` |

```java
int a = 10;
int b = 20;

boolean isEqual = (a == b);      // false
boolean isNotEqual = (a != b);    // true
boolean isGreater = (a > b);      // false
boolean isLess = (a < b);         // true
boolean isGreaterOrEqual = (a >= b); // false
boolean isLessOrEqual = (a <= b);    // true
```

### Object Equality

For objects, `==` compares references (whether they point to the same object), while `.equals()` compares content (depending on how it's implemented).

```java
String str1 = new String("Hello");
String str2 = new String("Hello");
String str3 = str1;

boolean refEqual1 = (str1 == str2);  // false (different objects)
boolean refEqual2 = (str1 == str3);  // true (same object)

boolean contentEqual = str1.equals(str2);  // true (same content)
```

## Logical Operators

Logical operators are used to determine the logic between variables or values.

| Operator | Description | Example |
|----------|-------------|--------|
| `&&` | Logical AND | `a && b` |
| `\|\|` | Logical OR | `a \|\| b` |
| `!` | Logical NOT | `!a` |

```java
boolean a = true;
boolean b = false;

boolean andResult = a && b;  // false
boolean orResult = a || b;   // true
boolean notResult = !a;      // false
```

### Short-Circuit Evaluation

Logical operators use short-circuit evaluation:
- For `&&`, if the first operand is `false`, the second operand is not evaluated
- For `||`, if the first operand is `true`, the second operand is not evaluated

```java
int x = 10;
int y = 20;

// Short-circuit AND
if (x > 15 && y++ > 15) {
    System.out.println("Both conditions are true");
}
System.out.println(y);  // 20 (y++ was not evaluated)

// Short-circuit OR
if (x < 15 || y++ < 15) {
    System.out.println("At least one condition is true");
}
System.out.println(y);  // 20 (y++ was not evaluated)
```

## Bitwise Operators

Bitwise operators perform operations on individual bits of integer types.

| Operator | Description | Example |
|----------|-------------|--------|
| `&` | Bitwise AND | `a & b` |
| `\|` | Bitwise OR | `a \| b` |
| `^` | Bitwise XOR | `a ^ b` |
| `~` | Bitwise complement (NOT) | `~a` |
| `<<` | Left shift | `a << b` |
| `>>` | Right shift with sign extension | `a >> b` |
| `>>>` | Right shift with zero extension | `a >>> b` |

```java
int a = 5;   // Binary: 0000 0101
int b = 3;   // Binary: 0000 0011

int andResult = a & b;    // 1 (Binary: 0000 0001)
int orResult = a | b;     // 7 (Binary: 0000 0111)
int xorResult = a ^ b;    // 6 (Binary: 0000 0110)
int notResult = ~a;       // -6 (Binary: 1111 1010)

int leftShift = a << 1;   // 10 (Binary: 0000 1010)
int rightShift = a >> 1;  // 2 (Binary: 0000 0010)

int c = -5;  // Binary: 1111 1011
int rightShiftSigned = c >> 1;    // -3 (Binary: 1111 1101)
int rightShiftUnsigned = c >>> 1; // 2147483645 (Binary: 0111 1111 ... 1101)
```

## Ternary Operator

The ternary operator (`? :`) is a shorthand for the if-then-else statement.

```java
// Syntax: condition ? expression1 : expression2

int a = 10;
int b = 20;

// If a is greater than b, max is a; otherwise, max is b
int max = (a > b) ? a : b;  // 20

// Nested ternary operator
int x = 5;
String result = (x > 10) ? "Greater than 10" :
                (x > 5) ? "Between 6 and 10" :
                (x == 5) ? "Equal to 5" : "Less than 5";
// result is "Equal to 5"
```

## instanceof Operator

The `instanceof` operator checks whether an object is an instance of a specific class or implements an interface.

```java
String str = "Hello";
Integer num = 10;

boolean isString = str instanceof String;  // true
boolean isObject = str instanceof Object;  // true
boolean isInteger = num instanceof Integer;  // true
boolean isNumber = num instanceof Number;    // true (Integer extends Number)
```

## Operator Precedence

Operator precedence determines the order in which operators are evaluated in an expression. Operators with higher precedence are evaluated before operators with lower precedence.

| Precedence | Operators | Associativity |
|------------|-----------|---------------|
| Highest | `()`, `[]`, `.` | Left to right |
| | `++`, `--`, `+` (unary), `-` (unary), `~`, `!`, `(type)` | Right to left |
| | `*`, `/`, `%` | Left to right |
| | `+`, `-` | Left to right |
| | `<<`, `>>`, `>>>` | Left to right |
| | `<`, `<=`, `>`, `>=`, `instanceof` | Left to right |
| | `==`, `!=` | Left to right |
| | `&` | Left to right |
| | `^` | Left to right |
| | `\|` | Left to right |
| | `&&` | Left to right |
| | `\|\|` | Left to right |
| | `? :` | Right to left |
| Lowest | `=`, `+=`, `-=`, `*=`, `/=`, `%=`, `&=`, `^=`, `\|=`, `<<=`, `>>=`, `>>>=` | Right to left |

```java
int result = 10 + 5 * 2;  // 20 (not 30, because * has higher precedence than +)

int a = 10;
int b = 5;
int c = 2;

int result2 = a + b * c;  // 20
int result3 = (a + b) * c;  // 30 (parentheses change the precedence)

boolean logicalResult = a > b && b > c;  // true (a > b is evaluated first, then && operation)
```

## String Concatenation

The `+` operator is also used for string concatenation when at least one of the operands is a string.

```java
String firstName = "John";
String lastName = "Doe";

String fullName = firstName + " " + lastName;  // "John Doe"

int age = 30;
String message = "Age: " + age;  // "Age: 30" (age is converted to a string)

String result = "Result: " + 10 + 20;  // "Result: 1020" (not "Result: 30")
String result2 = "Result: " + (10 + 20);  // "Result: 30"
```

## Best Practices

1. **Use parentheses for clarity**: Even when not strictly necessary, parentheses can make your code more readable
2. **Be careful with integer division**: Remember that dividing two integers results in an integer
3. **Avoid complex expressions**: Break complex expressions into simpler ones for better readability
4. **Be cautious with increment/decrement operators**: Understand the difference between pre and post increment/decrement
5. **Use `.equals()` for object comparison**: Use `.equals()` instead of `==` when comparing objects
6. **Leverage short-circuit evaluation**: Use `&&` and `||` to optimize conditional expressions
7. **Use the ternary operator judiciously**: It's great for simple conditions but can reduce readability for complex ones

```java
// Good practices
public class OperatorExample {
    public void calculateTax(double income) {
        // Use parentheses for clarity
        double taxRate = (income > 50000) ? 0.2 : 0.15;
        
        // Break complex expressions into simpler ones
        boolean isEligible = income > 30000;
        boolean hasHighCredit = checkCreditScore() > 700;
        boolean qualifiesForLoan = isEligible && hasHighCredit;
        
        // Leverage short-circuit evaluation
        if (income > 0 && calculateRatio(income) > 1.5) {
            // The calculateRatio method won't be called if income <= 0
            processApplication();
        }
    }
    
    private double calculateRatio(double income) {
        // Avoid integer division issues
        return income / 12.0;  // Use 12.0 instead of 12 to ensure floating-point division
    }
    
    private int checkCreditScore() {
        return 750;
    }
    
    private void processApplication() {
        // Implementation
    }
}
```