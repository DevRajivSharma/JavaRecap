# Java Syntax

## Basic Structure of a Java Program

```java
// This is a single-line comment

/*
   This is a
   multi-line comment
*/

/**
 * This is a documentation comment (Javadoc)
 * @author Your Name
 */

// Package declaration (must be the first statement)
package com.example;

// Import statements
import java.util.ArrayList;
import java.util.*; // Imports all classes in the java.util package

// Class declaration
public class MyClass {
    // Class variables (static fields)
    private static int staticVariable = 10;
    
    // Instance variables (non-static fields)
    private int instanceVariable;
    private String name;
    
    // Constructor
    public MyClass(String name) {
        this.name = name;
        this.instanceVariable = 0;
    }
    
    // Main method - entry point of the program
    public static void main(String[] args) {
        // Create an object of MyClass
        MyClass obj = new MyClass("Example");
        
        // Call instance method
        obj.displayInfo();
        
        // Call static method
        printMessage("Hello, World!");
    }
    
    // Instance method
    public void displayInfo() {
        System.out.println("Name: " + name);
        System.out.println("Instance Variable: " + instanceVariable);
    }
    
    // Static method
    public static void printMessage(String message) {
        System.out.println("Message: " + message);
        System.out.println("Static Variable: " + staticVariable);
    }
}

// Another class in the same file (not public)
class AnotherClass {
    // Class members
}
```

## Naming Conventions

- **Classes and Interfaces**: PascalCase (e.g., `MyClass`, `Runnable`)
- **Methods and Variables**: camelCase (e.g., `displayInfo`, `firstName`)
- **Constants**: UPPER_CASE_WITH_UNDERSCORES (e.g., `MAX_VALUE`)
- **Packages**: all lowercase, with dots as separators (e.g., `com.example.project`)

## File Structure

- Each Java source file can contain only one public class.
- The file name must match the public class name (e.g., `MyClass.java` for `public class MyClass`).
- A source file can contain multiple non-public classes.

## Access Modifiers

- **public**: Accessible from any class.
- **protected**: Accessible within the same package and by subclasses.
- **default** (no modifier): Accessible only within the same package.
- **private**: Accessible only within the same class.

## Method Structure

```java
[access_modifier] [static] [final] [abstract] [synchronized] return_type method_name([parameters]) [throws exceptions] {
    // Method body
    return value; // If return type is not void
}
```

## Class Structure

```java
[access_modifier] [static] [final] [abstract] class ClassName [extends SuperClass] [implements Interface1, Interface2, ...] {
    // Class body
}
```

## Interface Structure

```java
[access_modifier] interface InterfaceName [extends Interface1, Interface2, ...] {
    // Constants (implicitly public, static, final)
    int CONSTANT_VALUE = 100;
    
    // Abstract methods (implicitly public and abstract)
    void methodName(int parameter);
    
    // Default methods (Java 8+)
    default void defaultMethod() {
        // Implementation
    }
    
    // Static methods (Java 8+)
    static void staticMethod() {
        // Implementation
    }
    
    // Private methods (Java 9+)
    private void privateMethod() {
        // Implementation
    }
}
```

## Enum Structure

```java
[access_modifier] enum EnumName {
    VALUE1, VALUE2, VALUE3;
    
    // Fields, constructors, methods can be added
}
```

## Package and Import Statements

```java
// Package declaration (must be the first statement)
package com.example.project;

// Import statements
import java.util.List;
import java.util.ArrayList;
import static java.lang.Math.PI; // Static import
```

## Statements and Blocks

```java
// Expression statement
x = 10;

// Declaration statement
int y;

// Block statement
{
    int z = 20;
    System.out.println(z);
}
```

## Semicolons and Braces

- Statements end with semicolons (`;`).
- Blocks are enclosed in braces (`{` and `}`).
- Method and class declarations do not end with semicolons.