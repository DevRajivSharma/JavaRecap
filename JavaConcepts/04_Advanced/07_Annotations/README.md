# Java Annotations

Annotations are a form of metadata that provide data about a program that is not part of the program itself. They have no direct effect on the operation of the code they annotate. This document covers Java annotations, their usage, built-in annotations, custom annotations, and annotation processing.

## Introduction to Annotations

Annotations were introduced in Java 5 as a way to add metadata to Java code. They can be applied to declarations of classes, fields, methods, and other program elements.

### Annotation Syntax

Annotations are defined with the `@interface` keyword and used with the `@` symbol followed by the annotation name.

```java
// Using an annotation
@Override
public String toString() {
    return "This method overrides toString()";
}
```

## Built-in Annotations

Java provides several built-in annotations in the `java.lang` package.

### @Override

Indicates that a method is intended to override a method in a superclass.

```java
public class Parent {
    public void display() {
        System.out.println("Parent display");
    }
}

public class Child extends Parent {
    @Override
    public void display() {
        System.out.println("Child display");
    }
    
    // Compilation error - no method to override in Parent
    // @Override
    // public void displayInfo() {}
}
```

### @Deprecated

Indicates that a declaration is obsolete and should no longer be used.

```java
public class DeprecatedExample {
    @Deprecated
    public void oldMethod() {
        System.out.println("This method is deprecated");
    }
    
    // Replacement method
    public void newMethod() {
        System.out.println("Use this method instead");
    }
}

public class DeprecatedUsage {
    public static void main(String[] args) {
        DeprecatedExample example = new DeprecatedExample();
        
        // This will cause a compiler warning
        example.oldMethod();
        
        // Preferred method
        example.newMethod();
    }
}
```

You can provide additional information with `@Deprecated`:

```java
// Java 9+ enhanced @Deprecated annotation
@Deprecated(
    since = "2.0",
    forRemoval = true
)
public void veryOldMethod() {
    System.out.println("This method will be removed in future versions");
}
```

### @SuppressWarnings

Indicates that compiler warnings should be suppressed in the annotated element.

```java
public class SuppressWarningsExample {
    // Suppress specific warning
    @SuppressWarnings("deprecation")
    public void useDeprecatedMethod() {
        // Using a deprecated method without warnings
        new DeprecatedExample().oldMethod();
    }
    
    // Suppress multiple warnings
    @SuppressWarnings({"unchecked", "rawtypes"})
    public void useRawTypes() {
        List rawList = new ArrayList();
        rawList.add("String");
        rawList.add(42);
    }
}
```

Common warning types:
- `"deprecation"` - Usage of deprecated APIs
- `"unchecked"` - Unchecked type operations
- `"rawtypes"` - Usage of raw types
- `"unused"` - Unused code
- `"null"` - Potential null pointer dereference
- `"resource"` - Resource leaks
- `"all"` - All warnings

### @SafeVarargs

Asserts that the method does not perform potentially unsafe operations on its varargs parameter.

```java
public class SafeVarargsExample {
    // Without @SafeVarargs, this would generate a warning
    @SafeVarargs
    public final <T> List<T> asList(T... elements) {
        List<T> list = new ArrayList<>();
        for (T element : elements) {
            list.add(element);
        }
        return list;
    }
    
    public static void main(String[] args) {
        SafeVarargsExample example = new SafeVarargsExample();
        List<String> strings = example.asList("a", "b", "c");
        System.out.println(strings);
    }
}
```

### @FunctionalInterface

Indicates that an interface is intended to be a functional interface (an interface with a single abstract method).

```java
@FunctionalInterface
public interface Calculator {
    int calculate(int a, int b);
    
    // Allowed: default methods
    default void printInfo() {
        System.out.println("Calculator interface");
    }
    
    // Allowed: static methods
    static Calculator addition() {
        return (a, b) -> a + b;
    }
    
    // Allowed: methods from Object class
    @Override
    boolean equals(Object obj);
    
    // Compilation error: would make this not a functional interface
    // void anotherMethod();
}

public class FunctionalInterfaceExample {
    public static void main(String[] args) {
        // Using lambda expression with functional interface
        Calculator add = (a, b) -> a + b;
        Calculator subtract = (a, b) -> a - b;
        
        System.out.println("10 + 5 = " + add.calculate(10, 5));
        System.out.println("10 - 5 = " + subtract.calculate(10, 5));
    }
}
```

## Java 8+ Common Annotations

### @Repeatable

Indicates that the annotation can be applied multiple times to the same declaration.

```java
// Container annotation
@Retention(RetentionPolicy.RUNTIME)
public @interface Schedules {
    Schedule[] value();
}

// Repeatable annotation
@Retention(RetentionPolicy.RUNTIME)
@Repeatable(Schedules.class)
public @interface Schedule {
    String dayOfMonth() default "*";
    String dayOfWeek() default "*";
    String hour() default "*";
}

// Using repeatable annotations
public class RepeatableAnnotationExample {
    @Schedule(dayOfMonth = "1")
    @Schedule(dayOfMonth = "15")
    @Schedule(dayOfWeek = "Sunday", hour = "8")
    public void doPeriodicTask() {
        System.out.println("Performing periodic task");
    }
    
    public static void main(String[] args) {
        RepeatableAnnotationExample example = new RepeatableAnnotationExample();
        Method method = null;
        
        try {
            method = example.getClass().getMethod("doPeriodicTask");
        } catch (NoSuchMethodException e) {
            e.printStackTrace();
        }
        
        if (method != null) {
            // Getting repeatable annotations
            Schedule[] schedules = method.getAnnotationsByType(Schedule.class);
            System.out.println("Number of @Schedule annotations: " + schedules.length);
            
            for (Schedule schedule : schedules) {
                System.out.println("Day of month: " + schedule.dayOfMonth());
                System.out.println("Day of week: " + schedule.dayOfWeek());
                System.out.println("Hour: " + schedule.hour());
                System.out.println();
            }
        }
    }
}
```

## Creating Custom Annotations

You can create your own annotations to provide metadata for your code.

### Basic Annotation Definition

```java
// Simple annotation definition
public @interface Author {
    String name();
    String date();
}

// Using the custom annotation
@Author(name = "John Doe", date = "2023-01-01")
public class CustomAnnotationExample {
    // Class implementation
}
```

### Annotation Elements

Annotation elements can have default values and various types.

```java
public @interface DocumentationAnnotation {
    // Required element (no default value)
    String description();
    
    // Optional element (with default value)
    String author() default "Unknown";
    
    // Array element
    String[] reviewers() default {};
    
    // Primitive type element
    int version() default 1;
    
    // Enum element
    Priority priority() default Priority.MEDIUM;
    
    // Annotation element
    Reference reference() default @Reference(value = "");
    
    // Class element
    Class<?> relatedClass() default Void.class;
}

// Enum for use in annotation
enum Priority {
    LOW, MEDIUM, HIGH
}

// Annotation for use in annotation
public @interface Reference {
    String value();
}

// Using the annotation with various elements
@DocumentationAnnotation(
    description = "This is a sample class",
    author = "Jane Smith",
    reviewers = {"Alice", "Bob"},
    version = 2,
    priority = Priority.HIGH,
    reference = @Reference(value = "https://example.com/docs"),
    relatedClass = String.class
)
public class AnnotatedClass {
    // Class implementation
}
```

### Special Value Element

If an annotation has a single element named `value`, it can be used with a simplified syntax.

```java
public @interface Copyright {
    String value();
}

// Simplified usage when only the value element is needed
@Copyright("2023 Example Corp.")
public class SimplifiedAnnotationExample {
    // Class implementation
}
```

## Annotation Retention

The `@Retention` meta-annotation specifies how long annotations should be retained.

```java
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;

// Retained only in source code, discarded by compiler
@Retention(RetentionPolicy.SOURCE)
public @interface SourceRetention {}

// Retained in compiled class files, but not available at runtime
@Retention(RetentionPolicy.CLASS) // This is the default if not specified
public @interface ClassRetention {}

// Retained in compiled class files and available at runtime through reflection
@Retention(RetentionPolicy.RUNTIME)
public @interface RuntimeRetention {}
```

## Annotation Target

The `@Target` meta-annotation restricts the types of elements to which an annotation can be applied.

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Target;

// Can be applied to classes, interfaces, or enums
@Target(ElementType.TYPE)
public @interface TypeAnnotation {}

// Can be applied to fields
@Target(ElementType.FIELD)
public @interface FieldAnnotation {}

// Can be applied to methods
@Target(ElementType.METHOD)
public @interface MethodAnnotation {}

// Can be applied to method parameters
@Target(ElementType.PARAMETER)
public @interface ParameterAnnotation {}

// Can be applied to constructors
@Target(ElementType.CONSTRUCTOR)
public @interface ConstructorAnnotation {}

// Can be applied to local variables
@Target(ElementType.LOCAL_VARIABLE)
public @interface LocalVariableAnnotation {}

// Can be applied to annotation types
@Target(ElementType.ANNOTATION_TYPE)
public @interface AnnotationTypeAnnotation {}

// Can be applied to packages
@Target(ElementType.PACKAGE)
public @interface PackageAnnotation {}

// Can be applied to type parameters (Java 8+)
@Target(ElementType.TYPE_PARAMETER)
public @interface TypeParameterAnnotation {}

// Can be applied to any use of a type (Java 8+)
@Target(ElementType.TYPE_USE)
public @interface TypeUseAnnotation {}

// Can be applied to multiple element types
@Target({ElementType.METHOD, ElementType.FIELD})
public @interface MethodOrFieldAnnotation {}
```

Usage examples:

```java
@TypeAnnotation
public class TargetExampleClass {
    
    @FieldAnnotation
    private int field;
    
    @ConstructorAnnotation
    public TargetExampleClass(@ParameterAnnotation int param) {
        @LocalVariableAnnotation
        int localVar = param;
        this.field = localVar;
    }
    
    @MethodAnnotation
    public void method(@ParameterAnnotation String param) {
        // Method implementation
    }
    
    // Type parameter annotation (Java 8+)
    public <@TypeParameterAnnotation T> T genericMethod(T param) {
        return param;
    }
    
    // Type use annotation (Java 8+)
    public void typeUseExample() {
        String @TypeUseAnnotation [] array = new String @TypeUseAnnotation [10];
        @TypeUseAnnotation String str = "Hello";
    }
}
```

## Other Meta-Annotations

### @Documented

Indicates that annotations with this type should be documented by javadoc and similar tools.

```java
import java.lang.annotation.Documented;

@Documented
public @interface DocumentedAnnotation {
    String value();
}

// This annotation will appear in the generated javadoc
@DocumentedAnnotation("This class is documented")
public class DocumentedExample {
    // Class implementation
}
```

### @Inherited

Indicates that an annotation type is automatically inherited by subclasses.

```java
import java.lang.annotation.Inherited;

@Inherited
public @interface InheritedAnnotation {
    String value();
}

@InheritedAnnotation("This annotation is inherited by subclasses")
public class Parent {
    // Class implementation
}

// Child class inherits the @InheritedAnnotation from Parent
public class Child extends Parent {
    // Class implementation
}

public class InheritanceExample {
    public static void main(String[] args) {
        // Check if Child class has the annotation
        InheritedAnnotation annotation = Child.class.getAnnotation(InheritedAnnotation.class);
        if (annotation != null) {
            System.out.println("Child has inherited annotation: " + annotation.value());
        }
    }
}
```

## Annotation Processing at Runtime

You can access annotations at runtime using reflection.

```java
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.reflect.Method;

@Retention(RetentionPolicy.RUNTIME)
@interface TestAnnotation {
    String value() default "";
    int priority() default 0;
}

class RuntimeAnnotationExample {
    @TestAnnotation(value = "test1", priority = 1)
    public void testMethod1() {
        System.out.println("Running test method 1");
    }
    
    @TestAnnotation(value = "test2", priority = 2)
    public void testMethod2() {
        System.out.println("Running test method 2");
    }
    
    public void regularMethod() {
        System.out.println("Running regular method");
    }
}

public class AnnotationProcessingExample {
    public static void main(String[] args) {
        RuntimeAnnotationExample example = new RuntimeAnnotationExample();
        Class<?> clazz = example.getClass();
        
        // Get all methods
        Method[] methods = clazz.getDeclaredMethods();
        
        // Process methods with TestAnnotation
        for (Method method : methods) {
            // Check if method has the annotation
            if (method.isAnnotationPresent(TestAnnotation.class)) {
                // Get the annotation
                TestAnnotation annotation = method.getAnnotation(TestAnnotation.class);
                
                System.out.println("Method: " + method.getName());
                System.out.println("Annotation value: " + annotation.value());
                System.out.println("Annotation priority: " + annotation.priority());
                
                try {
                    // Invoke the method
                    method.invoke(example);
                } catch (Exception e) {
                    e.printStackTrace();
                }
                
                System.out.println();
            }
        }
    }
}
```

## Compile-Time Annotation Processing

Java provides the Annotation Processing Tool (apt) and later the `javax.annotation.processing` API for processing annotations at compile time.

### Creating an Annotation Processor

```java
import java.util.Set;
import javax.annotation.processing.*;
import javax.lang.model.SourceVersion;
import javax.lang.model.element.*;
import javax.tools.Diagnostic;

// Define an annotation to process
@Retention(RetentionPolicy.SOURCE)
@Target(ElementType.TYPE)
public @interface GenerateGetter {
    String[] fields() default {};
}

// Create an annotation processor
@SupportedAnnotationTypes("com.example.GenerateGetter")
@SupportedSourceVersion(SourceVersion.RELEASE_11)
public class GetterProcessor extends AbstractProcessor {
    
    @Override
    public boolean process(Set<? extends TypeElement> annotations, RoundEnvironment roundEnv) {
        // Process each element annotated with @GenerateGetter
        for (Element element : roundEnv.getElementsAnnotatedWith(GenerateGetter.class)) {
            // Only process classes
            if (element.getKind() != ElementKind.CLASS) {
                processingEnv.getMessager().printMessage(
                    Diagnostic.Kind.ERROR,
                    "@GenerateGetter can only be applied to classes",
                    element
                );
                continue;
            }
            
            // Get the annotation
            GenerateGetter annotation = element.getAnnotation(GenerateGetter.class);
            String[] fieldNames = annotation.fields();
            
            // Get all fields of the class
            for (Element enclosedElement : element.getEnclosedElements()) {
                if (enclosedElement.getKind() == ElementKind.FIELD) {
                    String fieldName = enclosedElement.getSimpleName().toString();
                    
                    // Check if this field should have a getter
                    if (fieldNames.length == 0 || contains(fieldNames, fieldName)) {
                        // Generate getter code
                        // In a real processor, we would generate Java source files here
                        processingEnv.getMessager().printMessage(
                            Diagnostic.Kind.NOTE,
                            "Would generate getter for field: " + fieldName,
                            enclosedElement
                        );
                    }
                }
            }
        }
        
        return true; // Claim the annotations
    }
    
    private boolean contains(String[] array, String value) {
        for (String item : array) {
            if (item.equals(value)) {
                return true;
            }
        }
        return false;
    }
}
```

### Registering an Annotation Processor

To register an annotation processor, you need to create a file named `javax.annotation.processing.Processor` in the `META-INF/services` directory of your JAR file with the fully qualified name of your processor:

```
com.example.GetterProcessor
```

Alternatively, you can use the `-processor` option with the Java compiler:

```bash
javac -processor com.example.GetterProcessor *.java
```

## Practical Examples

### Validation Annotations

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import java.lang.reflect.Field;

// Define validation annotations
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
@interface NotNull {
    String message() default "Field cannot be null";
}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
@interface Length {
    int min() default 0;
    int max() default Integer.MAX_VALUE;
    String message() default "Length must be between {min} and {max}";
}

// Class with annotated fields
class User {
    @NotNull(message = "Username cannot be null")
    @Length(min = 4, max = 20, message = "Username must be between {min} and {max} characters")
    private String username;
    
    @NotNull(message = "Email cannot be null")
    @Length(min = 5, max = 50)
    private String email;
    
    // Getters and setters
    public String getUsername() { return username; }
    public void setUsername(String username) { this.username = username; }
    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }
}

// Validator class
class Validator {
    public static <T> boolean validate(T object, StringBuilder errors) {
        boolean valid = true;
        Class<?> clazz = object.getClass();
        
        for (Field field : clazz.getDeclaredFields()) {
            field.setAccessible(true);
            
            try {
                Object value = field.get(object);
                
                // Check @NotNull
                if (field.isAnnotationPresent(NotNull.class) && value == null) {
                    NotNull annotation = field.getAnnotation(NotNull.class);
                    errors.append(field.getName()).append(": ").append(annotation.message()).append("\n");
                    valid = false;
                    continue; // Skip other validations if null
                }
                
                // Check @Length for String fields
                if (field.isAnnotationPresent(Length.class) && value instanceof String) {
                    Length annotation = field.getAnnotation(Length.class);
                    String strValue = (String) value;
                    int length = strValue.length();
                    
                    if (length < annotation.min() || length > annotation.max()) {
                        String message = annotation.message()
                            .replace("{min}", String.valueOf(annotation.min()))
                            .replace("{max}", String.valueOf(annotation.max()));
                        
                        errors.append(field.getName()).append(": ").append(message).append("\n");
                        valid = false;
                    }
                }
                
            } catch (IllegalAccessException e) {
                e.printStackTrace();
            }
        }
        
        return valid;
    }
}

// Usage example
public class ValidationExample {
    public static void main(String[] args) {
        User user = new User();
        user.setUsername("jo"); // Too short
        user.setEmail(null);    // Null email
        
        StringBuilder errors = new StringBuilder();
        boolean valid = Validator.validate(user, errors);
        
        if (!valid) {
            System.out.println("Validation errors:\n" + errors.toString());
        } else {
            System.out.println("User is valid");
        }
        
        // Fix the validation errors
        user.setUsername("john_doe");
        user.setEmail("john@example.com");
        
        errors = new StringBuilder();
        valid = Validator.validate(user, errors);
        
        if (!valid) {
            System.out.println("Validation errors:\n" + errors.toString());
        } else {
            System.out.println("User is valid");
        }
    }
}
```

### Custom Test Framework

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import java.lang.reflect.Method;

// Test annotations
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@interface Test {}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@interface Before {}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@interface After {}

// Class with test methods
class Calculator {
    public int add(int a, int b) {
        return a + b;
    }
    
    public int subtract(int a, int b) {
        return a - b;
    }
    
    public int multiply(int a, int b) {
        return a * b;
    }
    
    public int divide(int a, int b) {
        if (b == 0) {
            throw new ArithmeticException("Division by zero");
        }
        return a / b;
    }
}

// Test class
class CalculatorTest {
    private Calculator calculator;
    
    @Before
    public void setup() {
        System.out.println("Setting up test");
        calculator = new Calculator();
    }
    
    @Test
    public void testAdd() {
        int result = calculator.add(2, 3);
        assert result == 5 : "2 + 3 should equal 5";
    }
    
    @Test
    public void testSubtract() {
        int result = calculator.subtract(5, 2);
        assert result == 3 : "5 - 2 should equal 3";
    }
    
    @Test
    public void testMultiply() {
        int result = calculator.multiply(4, 5);
        assert result == 20 : "4 * 5 should equal 20";
    }
    
    @Test
    public void testDivide() {
        int result = calculator.divide(10, 2);
        assert result == 5 : "10 / 2 should equal 5";
    }
    
    @Test
    public void testDivideByZero() {
        try {
            calculator.divide(10, 0);
            assert false : "Should have thrown ArithmeticException";
        } catch (ArithmeticException e) {
            // Expected exception
        }
    }
    
    @After
    public void tearDown() {
        System.out.println("Tearing down test");
        calculator = null;
    }
}

// Test runner
public class TestFrameworkExample {
    public static void main(String[] args) {
        runTests(CalculatorTest.class);
    }
    
    public static void runTests(Class<?> testClass) {
        Object testInstance = null;
        
        try {
            testInstance = testClass.getDeclaredConstructor().newInstance();
        } catch (Exception e) {
            System.err.println("Could not create test instance: " + e.getMessage());
            return;
        }
        
        Method beforeMethod = null;
        Method afterMethod = null;
        Method[] testMethods = new Method[0];
        
        // Find before, after, and test methods
        Method[] methods = testClass.getDeclaredMethods();
        for (Method method : methods) {
            if (method.isAnnotationPresent(Before.class)) {
                beforeMethod = method;
            } else if (method.isAnnotationPresent(After.class)) {
                afterMethod = method;
            } else if (method.isAnnotationPresent(Test.class)) {
                // Add to test methods array
                Method[] newTestMethods = new Method[testMethods.length + 1];
                System.arraycopy(testMethods, 0, newTestMethods, 0, testMethods.length);
                newTestMethods[testMethods.length] = method;
                testMethods = newTestMethods;
            }
        }
        
        int passed = 0;
        int failed = 0;
        
        // Run tests
        for (Method testMethod : testMethods) {
            System.out.println("\nRunning test: " + testMethod.getName());
            
            try {
                // Run setup method
                if (beforeMethod != null) {
                    beforeMethod.invoke(testInstance);
                }
                
                // Run test method
                testMethod.invoke(testInstance);
                System.out.println(testMethod.getName() + ": PASSED");
                passed++;
                
            } catch (Exception e) {
                System.out.println(testMethod.getName() + ": FAILED");
                System.out.println("  Reason: " + e.getCause());
                failed++;
                
            } finally {
                // Run teardown method
                if (afterMethod != null) {
                    try {
                        afterMethod.invoke(testInstance);
                    } catch (Exception e) {
                        System.err.println("Error in tearDown: " + e.getMessage());
                    }
                }
            }
        }
        
        // Print summary
        System.out.println("\nTest Summary:");
        System.out.println("Total: " + (passed + failed));
        System.out.println("Passed: " + passed);
        System.out.println("Failed: " + failed);
    }
}
```

## Best Practices

1. **Use built-in annotations** when appropriate instead of creating custom ones.

2. **Document your custom annotations** thoroughly, especially their purpose and usage.

3. **Choose the appropriate retention policy** based on your needs:
   - `SOURCE` for compile-time checks or code generation
   - `CLASS` for bytecode analysis tools
   - `RUNTIME` for reflection-based processing

4. **Specify the target elements** to restrict where your annotation can be applied.

5. **Use meaningful element names** in your annotation interfaces.

6. **Provide default values** for optional elements to make annotations easier to use.

7. **Consider using the `value` element** for single-element annotations to enable simplified syntax.

8. **Use annotation processors** for compile-time validation or code generation rather than runtime reflection when possible.

9. **Avoid overusing annotations** - they should enhance code, not obscure it.

10. **Test annotation processing code** thoroughly, as it can be complex and error-prone.

## Conclusion

Java annotations provide a powerful way to add metadata to your code, enabling various features like compile-time checking, runtime processing, and code generation. By understanding how to use built-in annotations, create custom annotations, and process them effectively, you can enhance your Java applications with cleaner, more maintainable, and more feature-rich code.

Annotations are widely used in modern Java frameworks and libraries, such as Spring, Hibernate, JUnit, and many others, making them an essential part of a Java developer's toolkit.