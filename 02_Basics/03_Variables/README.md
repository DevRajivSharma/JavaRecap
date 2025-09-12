# Variables in Java

Variables are containers for storing data values in Java. Each variable has a specific data type that determines what kind of values it can hold and what operations can be performed on it.

## Variable Declaration and Initialization

### Declaration

To declare a variable in Java, you specify its data type followed by the variable name:

```java
int age;
String name;
double salary;
boolean isEmployed;
```

### Initialization

Initialization is the process of assigning a value to a variable:

```java
int age = 25;
String name = "John Doe";
double salary = 50000.50;
boolean isEmployed = true;
```

### Declaration and Initialization in Separate Steps

```java
int count;       // Declaration
count = 10;      // Initialization

String message;  // Declaration
message = "Hello, Java!";  // Initialization
```

## Variable Naming Rules and Conventions

### Rules (Must Follow)

1. Variable names can contain letters, digits, underscores (`_`), and dollar signs (`$`)
2. Variable names must begin with a letter, underscore, or dollar sign
3. Variable names cannot be Java keywords (e.g., `int`, `class`, `public`)
4. Variable names are case-sensitive (`age` and `Age` are different variables)

### Conventions (Should Follow)

1. Use camelCase for variable names (e.g., `firstName`, `totalAmount`)
2. Start variable names with a lowercase letter
3. Use meaningful and descriptive names
4. For constants, use uppercase letters with underscores (e.g., `MAX_VALUE`)

```java
// Good variable names
int age;
String firstName;
double hourlyRate;
boolean isActive;
final int MAX_ATTEMPTS = 3;

// Poor variable names (avoid these)
int a;
String s;
double d;
boolean b;
```

## Types of Variables

Java has three types of variables based on their scope and lifetime:

### 1. Instance Variables (Non-Static Fields)

Instance variables are declared within a class but outside any method, constructor, or block. Each object of the class has its own copy of these variables.

```java
public class Student {
    // Instance variables
    private String name;
    private int age;
    private double gpa;
    
    // Constructor and methods
}
```

Characteristics:
- Declared within a class but outside methods
- Created when an object is instantiated
- Have default values if not explicitly initialized
- Accessible through object references

### 2. Class Variables (Static Fields)

Class variables are declared with the `static` keyword within a class but outside any method. They are shared among all instances of the class.

```java
public class School {
    // Instance variable
    private String name;
    
    // Class variable (static field)
    private static int totalStudents;
    
    // Static constant
    public static final int MAX_STUDENTS_PER_CLASS = 30;
    
    // Constructor and methods
}
```

Characteristics:
- Declared with the `static` keyword
- Shared among all instances of the class
- Created when the class is loaded
- Have default values if not explicitly initialized
- Accessible through the class name (e.g., `School.totalStudents`)

### 3. Local Variables

Local variables are declared within a method, constructor, or block. They are only accessible within the method, constructor, or block in which they are declared.

```java
public class Example {
    public void calculateSum(int a, int b) {
        // Local variable
        int sum = a + b;
        System.out.println("Sum: " + sum);
    }
    
    public void printMessage() {
        // Local variable
        String message = "Hello, World!";
        System.out.println(message);
    }
}
```

Characteristics:
- Declared within a method, constructor, or block
- Created when the method is called or block is entered
- Must be initialized before use (no default values)
- Destroyed when the method completes or block is exited
- Only accessible within the method, constructor, or block

### 4. Parameters

Parameters are variables used in method declarations to receive values passed to the method.

```java
public class Calculator {
    // Parameters: a and b
    public int add(int a, int b) {
        return a + b;
    }
    
    // Parameters: radius
    public double calculateArea(double radius) {
        return Math.PI * radius * radius;
    }
}
```

Characteristics:
- Declared in method signatures
- Receive values when the method is called
- Behave like local variables within the method
- Scope is limited to the method

## Default Values

Instance and class variables have default values if not explicitly initialized:

| Data Type | Default Value |
|-----------|---------------|
| `byte`, `short`, `int`, `long` | 0 |
| `float`, `double` | 0.0 |
| `char` | '\u0000' (null character) |
| `boolean` | false |
| Reference types | null |

Local variables do not have default values and must be initialized before use.

## Variable Scope

The scope of a variable determines where it can be accessed in the code:

### 1. Class Level Scope

Instance and class variables have class-level scope and are accessible throughout the class.

```java
public class ScopeExample {
    // Class-level scope
    private int instanceVar = 10;
    private static int classVar = 20;
    
    public void method1() {
        // Can access instanceVar and classVar
        System.out.println(instanceVar);
        System.out.println(classVar);
    }
    
    public void method2() {
        // Can also access instanceVar and classVar
        instanceVar = 30;
        classVar = 40;
    }
}
```

### 2. Method Level Scope

Local variables have method-level scope and are only accessible within the method where they are declared.

```java
public class ScopeExample {
    public void method1() {
        // Method-level scope
        int localVar = 10;
        System.out.println(localVar);
    }
    
    public void method2() {
        // Cannot access localVar from method1
        // System.out.println(localVar); // Compilation error
        
        // Different localVar in method2
        int localVar = 20;
        System.out.println(localVar);
    }
}
```

### 3. Block Level Scope

Variables declared within a block (enclosed by curly braces) have block-level scope and are only accessible within that block.

```java
public class ScopeExample {
    public void blockScopeExample() {
        // Method-level scope
        int outer = 10;
        
        if (outer > 5) {
            // Block-level scope
            int inner = 20;
            System.out.println(outer);  // Can access outer
            System.out.println(inner);  // Can access inner
        }
        
        System.out.println(outer);  // Can access outer
        // System.out.println(inner);  // Compilation error - inner not accessible here
    }
}
```

## Variable Shadowing

Variable shadowing occurs when a variable in a local scope has the same name as a variable in an outer scope, effectively hiding the outer variable.

```java
public class ShadowExample {
    // Instance variable
    private int value = 10;
    
    public void printValue() {
        // Local variable shadows instance variable
        int value = 20;
        System.out.println("Local value: " + value);  // Prints 20
        System.out.println("Instance value: " + this.value);  // Prints 10
    }
    
    public void anotherMethod(int value) {
        // Parameter shadows instance variable
        System.out.println("Parameter value: " + value);
        System.out.println("Instance value: " + this.value);
    }
}
```

## Final Variables

Final variables cannot be reassigned after initialization. They are often used to create constants.

```java
public class FinalExample {
    // Final instance variable
    private final int MAX_SIZE = 100;
    
    // Final static variable (constant)
    public static final double PI = 3.14159;
    
    public void example() {
        // Final local variable
        final String message = "Hello";
        
        // Cannot reassign final variables
        // MAX_SIZE = 200;  // Compilation error
        // PI = 3.14;       // Compilation error
        // message = "Hi";  // Compilation error
    }
    
    // Final parameter
    public void process(final int value) {
        // Cannot modify final parameter
        // value++;  // Compilation error
        System.out.println(value);
    }
}
```

## Type Inference with var (Java 10+)

Java 10 introduced the `var` keyword for local variable type inference, allowing the compiler to determine the type based on the initialization expression.

```java
public class VarExample {
    public void example() {
        // Without var
        String name = "John";
        ArrayList<String> names = new ArrayList<>();
        
        // With var
        var age = 30;  // Inferred as int
        var message = "Hello";  // Inferred as String
        var list = new ArrayList<String>();  // Inferred as ArrayList<String>
    }
}
```

Restrictions on `var`:
- Can only be used for local variables
- Cannot be used for method parameters, return types, or fields
- Must be initialized at declaration
- Cannot be initialized with null without a type

## Best Practices

1. **Use descriptive names**: Choose variable names that clearly indicate their purpose
2. **Initialize variables**: Always initialize variables before using them
3. **Minimize scope**: Declare variables in the smallest scope possible
4. **Use final when appropriate**: Mark variables as final if they should not be changed
5. **Avoid shadowing**: Avoid using the same name for variables in different scopes
6. **Follow naming conventions**: Use camelCase for variables and UPPER_CASE for constants
7. **Limit the number of instance variables**: Keep the number of instance variables manageable
8. **Use var judiciously**: Only use var when the type is obvious from the context

```java
// Example of good variable practices
public class UserAccount {
    // Constants
    private static final int MAX_LOGIN_ATTEMPTS = 3;
    private static final long LOCK_DURATION_MS = 30_000;  // 30 seconds
    
    // Instance variables with descriptive names
    private String username;
    private String email;
    private boolean isActive;
    private int loginAttempts;
    
    public void resetPassword(String currentPassword, String newPassword) {
        // Local variables with clear purpose
        final int minPasswordLength = 8;
        
        // Use var when type is obvious (Java 10+)
        var isValidLength = newPassword.length() >= minPasswordLength;
        var containsSpecialChar = newPassword.matches(".*[^a-zA-Z0-9].*");
        
        if (isValidLength && containsSpecialChar) {
            // Implementation
        }
    }
}
```