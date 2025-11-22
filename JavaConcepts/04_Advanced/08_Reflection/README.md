# Java Reflection API

The Java Reflection API provides a way to inspect and manipulate classes, interfaces, fields, and methods at runtime, without knowing their names at compile time. This document covers the fundamentals of reflection, its use cases, and best practices.

## Introduction to Reflection

Reflection allows you to examine and modify the behavior of a program at runtime. With reflection, you can:

- Inspect classes, interfaces, fields, and methods
- Create new instances of classes
- Get and set field values
- Invoke methods
- Access private members
- Determine the runtime type of an object

## Getting Class Objects

The `Class` object is the entry point for all reflection operations. There are several ways to obtain a `Class` object:

```java
// Method 1: Using .class syntax
Class<?> stringClass = String.class;

// Method 2: Using getClass() method on an object
String str = "Hello";
Class<?> strClass = str.getClass();

// Method 3: Using Class.forName()
try {
    Class<?> cls = Class.forName("java.util.ArrayList");
    System.out.println("Class found: " + cls.getName());
} catch (ClassNotFoundException e) {
    System.out.println("Class not found");
}

// Method 4: Using ClassLoader
ClassLoader classLoader = ClassReflectionExample.class.getClassLoader();
try {
    Class<?> cls = classLoader.loadClass("java.util.HashMap");
    System.out.println("Class loaded: " + cls.getName());
} catch (ClassNotFoundException e) {
    System.out.println("Class not found");
}
```

## Examining Class Information

### Getting Class Metadata

```java
import java.lang.reflect.Modifier;

public class ClassMetadataExample {
    public static void main(String[] args) {
        Class<?> cls = String.class;
        
        // Get the fully qualified name of the class
        System.out.println("Class name: " + cls.getName());
        
        // Get the simple name of the class (without package)
        System.out.println("Simple name: " + cls.getSimpleName());
        
        // Get the package name
        System.out.println("Package: " + cls.getPackage().getName());
        
        // Get the superclass
        Class<?> superclass = cls.getSuperclass();
        System.out.println("Superclass: " + superclass.getName());
        
        // Get the implemented interfaces
        Class<?>[] interfaces = cls.getInterfaces();
        System.out.println("Implemented interfaces:");
        for (Class<?> iface : interfaces) {
            System.out.println("  " + iface.getName());
        }
        
        // Get the modifiers
        int modifiers = cls.getModifiers();
        System.out.println("Modifiers: " + Modifier.toString(modifiers));
        
        // Check specific modifiers
        System.out.println("Is public: " + Modifier.isPublic(modifiers));
        System.out.println("Is final: " + Modifier.isFinal(modifiers));
        System.out.println("Is abstract: " + Modifier.isAbstract(modifiers));
    }
}
```

### Examining Class Hierarchy

```java
public class ClassHierarchyExample {
    public static void main(String[] args) {
        printClassHierarchy(StringBuilder.class, 0);
    }
    
    private static void printClassHierarchy(Class<?> cls, int indentLevel) {
        if (cls == null) {
            return;
        }
        
        // Print indentation
        for (int i = 0; i < indentLevel; i++) {
            System.out.print("  ");
        }
        
        // Print class name
        System.out.println(cls.getName());
        
        // Print interfaces
        for (Class<?> iface : cls.getInterfaces()) {
            for (int i = 0; i < indentLevel + 1; i++) {
                System.out.print("  ");
            }
            System.out.println("(interface) " + iface.getName());
        }
        
        // Recursively print superclass
        printClassHierarchy(cls.getSuperclass(), indentLevel + 1);
    }
}
```

## Working with Constructors

### Getting Constructors

```java
import java.lang.reflect.Constructor;

public class ConstructorReflectionExample {
    public static void main(String[] args) {
        Class<?> cls = StringBuilder.class;
        
        // Get all public constructors
        Constructor<?>[] publicConstructors = cls.getConstructors();
        System.out.println("Public constructors:");
        for (Constructor<?> constructor : publicConstructors) {
            System.out.println("  " + constructor);
        }
        
        // Get all constructors (public, protected, default, private)
        Constructor<?>[] allConstructors = cls.getDeclaredConstructors();
        System.out.println("\nAll constructors:");
        for (Constructor<?> constructor : allConstructors) {
            System.out.println("  " + constructor);
        }
        
        // Get a specific constructor
        try {
            Constructor<?> constructor = cls.getConstructor(String.class);
            System.out.println("\nSpecific constructor: " + constructor);
        } catch (NoSuchMethodException e) {
            System.out.println("Constructor not found");
        }
    }
}
```

### Creating Objects Using Constructors

```java
import java.lang.reflect.Constructor;

public class CreateObjectExample {
    public static void main(String[] args) {
        try {
            // Get the Class object
            Class<?> cls = String.class;
            
            // Get constructor that takes a String parameter
            Constructor<?> constructor = cls.getConstructor(String.class);
            
            // Create a new instance
            String newString = (String) constructor.newInstance("Hello, Reflection!");
            System.out.println("Created string: " + newString);
            
            // Create an object with a private constructor
            Class<?> runtimeClass = Runtime.class;
            Constructor<?> privateConstructor = runtimeClass.getDeclaredConstructor();
            privateConstructor.setAccessible(true);  // Override access check
            Object runtimeInstance = privateConstructor.newInstance();
            System.out.println("Created Runtime instance: " + runtimeInstance);
            
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

## Working with Fields

### Getting Fields

```java
import java.lang.reflect.Field;
import java.lang.reflect.Modifier;

class Person {
    public String name;
    protected int age;
    private boolean employed;
    public static final String SPECIES = "Human";
    
    public Person(String name, int age, boolean employed) {
        this.name = name;
        this.age = age;
        this.employed = employed;
    }
}

public class FieldReflectionExample {
    public static void main(String[] args) {
        Class<?> cls = Person.class;
        
        // Get all public fields (including inherited)
        Field[] publicFields = cls.getFields();
        System.out.println("Public fields:");
        for (Field field : publicFields) {
            System.out.println("  " + field);
        }
        
        // Get all declared fields (excluding inherited)
        Field[] declaredFields = cls.getDeclaredFields();
        System.out.println("\nDeclared fields:");
        for (Field field : declaredFields) {
            int modifiers = field.getModifiers();
            System.out.println("  " + Modifier.toString(modifiers) + " " + 
                              field.getType().getSimpleName() + " " + 
                              field.getName());
        }
        
        // Get a specific field
        try {
            Field nameField = cls.getField("name");
            System.out.println("\nSpecific field: " + nameField);
        } catch (NoSuchFieldException e) {
            System.out.println("Field not found");
        }
    }
}
```

### Getting and Setting Field Values

```java
import java.lang.reflect.Field;

public class FieldAccessExample {
    public static void main(String[] args) {
        try {
            // Create a Person instance
            Person person = new Person("Alice", 30, true);
            Class<?> cls = person.getClass();
            
            // Access public field
            Field nameField = cls.getField("name");
            String name = (String) nameField.get(person);
            System.out.println("Name: " + name);
            
            // Modify public field
            nameField.set(person, "Bob");
            System.out.println("Modified name: " + person.name);
            
            // Access protected field
            Field ageField = cls.getDeclaredField("age");
            ageField.setAccessible(true);  // Override access check
            int age = (int) ageField.get(person);
            System.out.println("Age: " + age);
            
            // Modify protected field
            ageField.set(person, 35);
            System.out.println("Modified age: " + ageField.get(person));
            
            // Access private field
            Field employedField = cls.getDeclaredField("employed");
            employedField.setAccessible(true);  // Override access check
            boolean employed = (boolean) employedField.get(person);
            System.out.println("Employed: " + employed);
            
            // Modify private field
            employedField.set(person, false);
            System.out.println("Modified employed: " + employedField.get(person));
            
            // Access static field
            Field speciesField = cls.getField("SPECIES");
            String species = (String) speciesField.get(null);  // null for static fields
            System.out.println("Species: " + species);
            
            // Try to modify final field (will throw exception)
            try {
                speciesField.set(null, "Homo Sapiens");
            } catch (Exception e) {
                System.out.println("Cannot modify final field: " + e.getMessage());
            }
            
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

## Working with Methods

### Getting Methods

```java
import java.lang.reflect.Method;
import java.lang.reflect.Modifier;

class Calculator {
    public int add(int a, int b) {
        return a + b;
    }
    
    protected int subtract(int a, int b) {
        return a - b;
    }
    
    private int multiply(int a, int b) {
        return a * b;
    }
    
    public static int divide(int a, int b) {
        if (b == 0) throw new ArithmeticException("Division by zero");
        return a / b;
    }
}

public class MethodReflectionExample {
    public static void main(String[] args) {
        Class<?> cls = Calculator.class;
        
        // Get all public methods (including inherited)
        Method[] publicMethods = cls.getMethods();
        System.out.println("Public methods:");
        for (Method method : publicMethods) {
            if (method.getDeclaringClass() == Object.class) {
                continue;  // Skip methods from Object class
            }
            System.out.println("  " + method);
        }
        
        // Get all declared methods (excluding inherited)
        Method[] declaredMethods = cls.getDeclaredMethods();
        System.out.println("\nDeclared methods:");
        for (Method method : declaredMethods) {
            int modifiers = method.getModifiers();
            System.out.println("  " + Modifier.toString(modifiers) + " " + 
                              method.getReturnType().getSimpleName() + " " + 
                              method.getName() + "(...)");
        }
        
        // Get a specific method
        try {
            Method addMethod = cls.getMethod("add", int.class, int.class);
            System.out.println("\nSpecific method: " + addMethod);
            
            // Get method parameters
            Class<?>[] paramTypes = addMethod.getParameterTypes();
            System.out.println("Parameter types:");
            for (Class<?> paramType : paramTypes) {
                System.out.println("  " + paramType.getName());
            }
            
            // Get return type
            Class<?> returnType = addMethod.getReturnType();
            System.out.println("Return type: " + returnType.getName());
            
        } catch (NoSuchMethodException e) {
            System.out.println("Method not found");
        }
    }
}
```

### Invoking Methods

```java
import java.lang.reflect.Method;

public class MethodInvocationExample {
    public static void main(String[] args) {
        try {
            // Create a Calculator instance
            Calculator calculator = new Calculator();
            Class<?> cls = calculator.getClass();
            
            // Invoke public method
            Method addMethod = cls.getMethod("add", int.class, int.class);
            Object result = addMethod.invoke(calculator, 10, 5);
            System.out.println("10 + 5 = " + result);
            
            // Invoke protected method
            Method subtractMethod = cls.getDeclaredMethod("subtract", int.class, int.class);
            subtractMethod.setAccessible(true);  // Override access check
            result = subtractMethod.invoke(calculator, 10, 5);
            System.out.println("10 - 5 = " + result);
            
            // Invoke private method
            Method multiplyMethod = cls.getDeclaredMethod("multiply", int.class, int.class);
            multiplyMethod.setAccessible(true);  // Override access check
            result = multiplyMethod.invoke(calculator, 10, 5);
            System.out.println("10 * 5 = " + result);
            
            // Invoke static method
            Method divideMethod = cls.getMethod("divide", int.class, int.class);
            result = divideMethod.invoke(null, 10, 5);  // null for static methods
            System.out.println("10 / 5 = " + result);
            
            // Handle exceptions from invoked methods
            try {
                divideMethod.invoke(null, 10, 0);  // Will throw ArithmeticException
            } catch (Exception e) {
                System.out.println("Exception from invoked method: " + 
                                  e.getCause().getMessage());
            }
            
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

## Working with Arrays

Reflection provides special support for working with arrays.

```java
import java.lang.reflect.Array;

public class ArrayReflectionExample {
    public static void main(String[] args) {
        // Get the Class object for an array type
        Class<?> stringArrayClass = String[].class;
        System.out.println("Array class: " + stringArrayClass.getName());
        
        // Check if a Class represents an array
        System.out.println("Is array: " + stringArrayClass.isArray());
        
        // Get the component type of an array
        Class<?> componentType = stringArrayClass.getComponentType();
        System.out.println("Component type: " + componentType.getName());
        
        // Create a new array instance
        Object array = Array.newInstance(String.class, 3);
        
        // Set array elements
        Array.set(array, 0, "First");
        Array.set(array, 1, "Second");
        Array.set(array, 2, "Third");
        
        // Get array elements
        for (int i = 0; i < Array.getLength(array); i++) {
            System.out.println("Element " + i + ": " + Array.get(array, i));
        }
        
        // Create a multidimensional array
        Object matrix = Array.newInstance(int.class, 2, 3);
        
        // Set values in the multidimensional array
        for (int i = 0; i < 2; i++) {
            Object row = Array.get(matrix, i);
            for (int j = 0; j < 3; j++) {
                Array.setInt(row, j, i * 3 + j);
            }
        }
        
        // Get values from the multidimensional array
        System.out.println("\nMatrix:");
        for (int i = 0; i < 2; i++) {
            Object row = Array.get(matrix, i);
            for (int j = 0; j < 3; j++) {
                System.out.print(Array.getInt(row, j) + " ");
            }
            System.out.println();
        }
    }
}
```

## Dynamic Proxy

Dynamic proxies allow you to create implementations of interfaces at runtime.

```java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

// Interface to be implemented by the proxy
interface Calculator {
    int add(int a, int b);
    int subtract(int a, int b);
    int multiply(int a, int b);
    int divide(int a, int b);
}

// Invocation handler that handles method calls on the proxy
class CalculatorHandler implements InvocationHandler {
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        // Log method call
        System.out.println("Method called: " + method.getName());
        
        // Get method parameters
        int a = (int) args[0];
        int b = (int) args[1];
        
        // Perform operation based on method name
        switch (method.getName()) {
            case "add":
                System.out.println("Adding " + a + " and " + b);
                return a + b;
            case "subtract":
                System.out.println("Subtracting " + b + " from " + a);
                return a - b;
            case "multiply":
                System.out.println("Multiplying " + a + " and " + b);
                return a * b;
            case "divide":
                if (b == 0) {
                    throw new ArithmeticException("Division by zero");
                }
                System.out.println("Dividing " + a + " by " + b);
                return a / b;
            default:
                throw new UnsupportedOperationException(
                    "Unknown method: " + method.getName());
        }
    }
}

public class DynamicProxyExample {
    public static void main(String[] args) {
        // Create the invocation handler
        InvocationHandler handler = new CalculatorHandler();
        
        // Create the proxy instance
        Calculator calculator = (Calculator) Proxy.newProxyInstance(
            Calculator.class.getClassLoader(),
            new Class<?>[] { Calculator.class },
            handler
        );
        
        // Use the proxy
        System.out.println("10 + 5 = " + calculator.add(10, 5));
        System.out.println("10 - 5 = " + calculator.subtract(10, 5));
        System.out.println("10 * 5 = " + calculator.multiply(10, 5));
        System.out.println("10 / 5 = " + calculator.divide(10, 5));
        
        // Handle exception
        try {
            calculator.divide(10, 0);
        } catch (ArithmeticException e) {
            System.out.println("Exception caught: " + e.getMessage());
        }
    }
}
```

## Annotations and Reflection

Reflection can be used to access annotations at runtime.

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import java.lang.reflect.Method;

// Define an annotation
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE, ElementType.METHOD})
@interface Auditable {
    String value() default "";
    String[] tags() default {};
    boolean enabled() default true;
}

// Class with annotations
@Auditable(value = "UserService", tags = {"user", "service"})
class UserService {
    @Auditable("createUser")
    public void createUser(String username) {
        System.out.println("Creating user: " + username);
    }
    
    @Auditable(value = "deleteUser", enabled = false)
    public void deleteUser(String username) {
        System.out.println("Deleting user: " + username);
    }
    
    public void updateUser(String username) {
        System.out.println("Updating user: " + username);
    }
}

public class AnnotationReflectionExample {
    public static void main(String[] args) {
        Class<?> cls = UserService.class;
        
        // Check if class has the annotation
        if (cls.isAnnotationPresent(Auditable.class)) {
            // Get the annotation
            Auditable annotation = cls.getAnnotation(Auditable.class);
            
            System.out.println("Class annotation:");
            System.out.println("  Value: " + annotation.value());
            System.out.println("  Tags: " + String.join(", ", annotation.tags()));
            System.out.println("  Enabled: " + annotation.enabled());
        }
        
        // Check method annotations
        System.out.println("\nMethod annotations:");
        for (Method method : cls.getDeclaredMethods()) {
            if (method.isAnnotationPresent(Auditable.class)) {
                Auditable annotation = method.getAnnotation(Auditable.class);
                
                System.out.println("Method: " + method.getName());
                System.out.println("  Value: " + annotation.value());
                System.out.println("  Tags: " + String.join(", ", annotation.tags()));
                System.out.println("  Enabled: " + annotation.enabled());
            } else {
                System.out.println("Method: " + method.getName() + " (no annotation)");
            }
        }
    }
}
```

## Generics and Reflection

Reflection can be used to work with generic types, although with some limitations due to type erasure.

```java
import java.lang.reflect.Field;
import java.lang.reflect.Method;
import java.lang.reflect.ParameterizedType;
import java.lang.reflect.Type;
import java.util.List;
import java.util.Map;

class GenericExample<T> {
    private List<String> stringList;
    private Map<String, Integer> stringIntMap;
    private T genericField;
    
    public List<T> getGenericList() {
        return null;
    }
    
    public <U extends Number> U convertToNumber(T value) {
        return null;
    }
}

public class GenericReflectionExample {
    public static void main(String[] args) {
        try {
            Class<?> cls = GenericExample.class;
            
            // Get generic fields
            Field stringListField = cls.getDeclaredField("stringList");
            Type stringListType = stringListField.getGenericType();
            
            if (stringListType instanceof ParameterizedType) {
                ParameterizedType pType = (ParameterizedType) stringListType;
                Type rawType = pType.getRawType();
                Type[] typeArgs = pType.getActualTypeArguments();
                
                System.out.println("Field: " + stringListField.getName());
                System.out.println("  Raw type: " + rawType.getTypeName());
                System.out.println("  Type argument: " + typeArgs[0].getTypeName());
            }
            
            // Get field with multiple generic parameters
            Field mapField = cls.getDeclaredField("stringIntMap");
            Type mapType = mapField.getGenericType();
            
            if (mapType instanceof ParameterizedType) {
                ParameterizedType pType = (ParameterizedType) mapType;
                Type rawType = pType.getRawType();
                Type[] typeArgs = pType.getActualTypeArguments();
                
                System.out.println("\nField: " + mapField.getName());
                System.out.println("  Raw type: " + rawType.getTypeName());
                System.out.println("  Key type: " + typeArgs[0].getTypeName());
                System.out.println("  Value type: " + typeArgs[1].getTypeName());
            }
            
            // Get generic method return type
            Method getListMethod = cls.getMethod("getGenericList");
            Type returnType = getListMethod.getGenericReturnType();
            
            if (returnType instanceof ParameterizedType) {
                ParameterizedType pType = (ParameterizedType) returnType;
                Type rawType = pType.getRawType();
                Type[] typeArgs = pType.getActualTypeArguments();
                
                System.out.println("\nMethod: " + getListMethod.getName());
                System.out.println("  Return raw type: " + rawType.getTypeName());
                System.out.println("  Return type argument: " + typeArgs[0].getTypeName());
            }
            
            // Get generic method parameter types
            Method convertMethod = cls.getMethod("convertToNumber", Object.class);
            Type[] genericParamTypes = convertMethod.getGenericParameterTypes();
            
            System.out.println("\nMethod: " + convertMethod.getName());
            System.out.println("  Parameter type: " + genericParamTypes[0].getTypeName());
            
            // Get generic method return type with bounds
            Type genericReturnType = convertMethod.getGenericReturnType();
            System.out.println("  Return type: " + genericReturnType.getTypeName());
            
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

## Practical Examples

### Simple Dependency Injection Container

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.util.HashMap;
import java.util.Map;

// Annotations for dependency injection
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@interface Component {}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
@interface Autowired {}

// Sample components
@Component
class UserRepository {
    public String findUser(int id) {
        return "User " + id;
    }
}

@Component
class UserService {
    @Autowired
    private UserRepository userRepository;
    
    public String getUser(int id) {
        return userRepository.findUser(id);
    }
}

@Component
class UserController {
    @Autowired
    private UserService userService;
    
    public void displayUser(int id) {
        System.out.println("User: " + userService.getUser(id));
    }
}

// Simple dependency injection container
class DIContainer {
    private Map<Class<?>, Object> instances = new HashMap<>();
    
    public void registerComponents(String... packageNames) {
        // In a real implementation, we would scan the packages
        // For simplicity, we'll manually register our components
        register(UserRepository.class);
        register(UserService.class);
        register(UserController.class);
    }
    
    private <T> void register(Class<T> componentClass) {
        if (!componentClass.isAnnotationPresent(Component.class)) {
            return;
        }
        
        try {
            // Create instance
            Constructor<T> constructor = componentClass.getDeclaredConstructor();
            T instance = constructor.newInstance();
            instances.put(componentClass, instance);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    
    public void injectDependencies() {
        for (Object instance : instances.values()) {
            injectFields(instance);
        }
    }
    
    private void injectFields(Object instance) {
        Class<?> cls = instance.getClass();
        
        for (Field field : cls.getDeclaredFields()) {
            if (field.isAnnotationPresent(Autowired.class)) {
                Class<?> fieldType = field.getType();
                Object dependency = instances.get(fieldType);
                
                if (dependency != null) {
                    field.setAccessible(true);
                    try {
                        field.set(instance, dependency);
                    } catch (Exception e) {
                        e.printStackTrace();
                    }
                }
            }
        }
    }
    
    public <T> T getComponent(Class<T> componentClass) {
        return componentClass.cast(instances.get(componentClass));
    }
}

public class DIExample {
    public static void main(String[] args) {
        // Create and initialize the container
        DIContainer container = new DIContainer();
        container.registerComponents("com.example");
        container.injectDependencies();
        
        // Get and use a component
        UserController controller = container.getComponent(UserController.class);
        controller.displayUser(42);  // Should display "User: User 42"
    }
}
```

### Simple ORM (Object-Relational Mapping)

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import java.lang.reflect.Field;
import java.util.ArrayList;
import java.util.List;

// Annotations for ORM
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@interface Table {
    String name();
}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
@interface Column {
    String name() default "";
}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
@interface PrimaryKey {}

// Entity class
@Table(name = "users")
class User {
    @PrimaryKey
    @Column(name = "id")
    private int id;
    
    @Column(name = "username")
    private String username;
    
    @Column(name = "email")
    private String email;
    
    // Transient field (not mapped to a column)
    private transient String tempData;
    
    // Constructor
    public User(int id, String username, String email) {
        this.id = id;
        this.username = username;
        this.email = email;
    }
    
    // Getters and setters
    public int getId() { return id; }
    public void setId(int id) { this.id = id; }
    public String getUsername() { return username; }
    public void setUsername(String username) { this.username = username; }
    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }
}

// Simple ORM framework
class SimpleORM {
    public String generateCreateTableSQL(Class<?> entityClass) {
        if (!entityClass.isAnnotationPresent(Table.class)) {
            throw new IllegalArgumentException("Class is not an entity: " + entityClass.getName());
        }
        
        Table tableAnnotation = entityClass.getAnnotation(Table.class);
        String tableName = tableAnnotation.name();
        
        StringBuilder sql = new StringBuilder();
        sql.append("CREATE TABLE ").append(tableName).append(" (");
        
        List<String> columnDefinitions = new ArrayList<>();
        Field primaryKeyField = null;
        
        for (Field field : entityClass.getDeclaredFields()) {
            if (field.isAnnotationPresent(Column.class)) {
                Column columnAnnotation = field.getAnnotation(Column.class);
                String columnName = columnAnnotation.name().isEmpty() ? 
                                   field.getName() : columnAnnotation.name();
                String columnType = getSQLType(field.getType());
                
                StringBuilder columnDef = new StringBuilder();
                columnDef.append(columnName).append(" ").append(columnType);
                
                if (field.isAnnotationPresent(PrimaryKey.class)) {
                    columnDef.append(" PRIMARY KEY");
                    primaryKeyField = field;
                }
                
                columnDefinitions.add(columnDef.toString());
            }
        }
        
        sql.append(String.join(", ", columnDefinitions));
        sql.append(")");
        
        return sql.toString();
    }
    
    public String generateInsertSQL(Object entity) {
        Class<?> entityClass = entity.getClass();
        
        if (!entityClass.isAnnotationPresent(Table.class)) {
            throw new IllegalArgumentException("Class is not an entity: " + entityClass.getName());
        }
        
        Table tableAnnotation = entityClass.getAnnotation(Table.class);
        String tableName = tableAnnotation.name();
        
        List<String> columnNames = new ArrayList<>();
        List<String> placeholders = new ArrayList<>();
        List<Object> values = new ArrayList<>();
        
        for (Field field : entityClass.getDeclaredFields()) {
            if (field.isAnnotationPresent(Column.class)) {
                field.setAccessible(true);
                
                try {
                    Column columnAnnotation = field.getAnnotation(Column.class);
                    String columnName = columnAnnotation.name().isEmpty() ? 
                                       field.getName() : columnAnnotation.name();
                    
                    Object value = field.get(entity);
                    
                    columnNames.add(columnName);
                    placeholders.add("?");
                    values.add(formatValue(value));
                    
                } catch (IllegalAccessException e) {
                    e.printStackTrace();
                }
            }
        }
        
        StringBuilder sql = new StringBuilder();
        sql.append("INSERT INTO ").append(tableName).append(" (");
        sql.append(String.join(", ", columnNames));
        sql.append(") VALUES (");
        sql.append(String.join(", ", placeholders));
        sql.append(")");
        
        // In a real ORM, we would use prepared statements with the values
        // For demonstration, we'll replace the placeholders with the values
        String finalSql = sql.toString();
        for (int i = 0; i < values.size(); i++) {
            finalSql = finalSql.replaceFirst("\\?", values.get(i).toString());
        }
        
        return finalSql;
    }
    
    private String getSQLType(Class<?> javaType) {
        if (javaType == int.class || javaType == Integer.class) {
            return "INTEGER";
        } else if (javaType == long.class || javaType == Long.class) {
            return "BIGINT";
        } else if (javaType == String.class) {
            return "VARCHAR(255)";
        } else if (javaType == boolean.class || javaType == Boolean.class) {
            return "BOOLEAN";
        } else if (javaType == double.class || javaType == Double.class) {
            return "DOUBLE";
        } else {
            return "VARCHAR(255)";
        }
    }
    
    private String formatValue(Object value) {
        if (value == null) {
            return "NULL";
        } else if (value instanceof String) {
            return "'" + value + "'";
        } else {
            return value.toString();
        }
    }
}

public class ORMExample {
    public static void main(String[] args) {
        SimpleORM orm = new SimpleORM();
        
        // Generate CREATE TABLE SQL
        String createTableSQL = orm.generateCreateTableSQL(User.class);
        System.out.println("Create Table SQL:\n" + createTableSQL);
        
        // Generate INSERT SQL
        User user = new User(1, "john_doe", "john@example.com");
        String insertSQL = orm.generateInsertSQL(user);
        System.out.println("\nInsert SQL:\n" + insertSQL);
    }
}
```

## Performance Considerations

Reflection is powerful but comes with performance costs:

1. **Slower execution**: Reflection operations are significantly slower than direct code.
2. **Runtime type checking**: The JVM cannot optimize reflection code as effectively.
3. **Security restrictions**: Security managers may restrict reflection operations.

### Performance Comparison

```java
import java.lang.reflect.Method;

public class ReflectionPerformanceExample {
    public static void main(String[] args) {
        // Object to test with
        String testString = "Hello, World!";
        int iterations = 1000000;
        
        // Direct method call
        long start = System.nanoTime();
        for (int i = 0; i < iterations; i++) {
            int length = testString.length();
        }
        long directTime = System.nanoTime() - start;
        
        // Reflection method call
        try {
            Method lengthMethod = String.class.getMethod("length");
            
            start = System.nanoTime();
            for (int i = 0; i < iterations; i++) {
                int length = (int) lengthMethod.invoke(testString);
            }
            long reflectionTime = System.nanoTime() - start;
            
            // Reflection with accessibility override
            lengthMethod.setAccessible(true);
            start = System.nanoTime();
            for (int i = 0; i < iterations; i++) {
                int length = (int) lengthMethod.invoke(testString);
            }
            long accessibleReflectionTime = System.nanoTime() - start;
            
            // Print results
            System.out.println("Direct method call: " + directTime / 1000000.0 + " ms");
            System.out.println("Reflection method call: " + reflectionTime / 1000000.0 + " ms");
            System.out.println("Accessible reflection method call: " + 
                              accessibleReflectionTime / 1000000.0 + " ms");
            System.out.println("Reflection is " + (reflectionTime / directTime) + 
                              " times slower than direct call");
            System.out.println("Accessible reflection is " + 
                              (accessibleReflectionTime / directTime) + 
                              " times slower than direct call");
            
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

## Best Practices

1. **Use reflection sparingly**: Only use reflection when there's no alternative.

2. **Cache reflection objects**: Store `Class`, `Method`, and `Field` objects rather than looking them up repeatedly.

```java
// Bad practice
public void badPractice(Object obj, String methodName) throws Exception {
    for (int i = 0; i < 1000; i++) {
        Method method = obj.getClass().getMethod(methodName);
        method.invoke(obj);
    }
}

// Good practice
public void goodPractice(Object obj, String methodName) throws Exception {
    Method method = obj.getClass().getMethod(methodName);
    for (int i = 0; i < 1000; i++) {
        method.invoke(obj);
    }
}
```

3. **Use setAccessible wisely**: Setting `setAccessible(true)` improves performance but bypasses access control.

4. **Handle exceptions properly**: Reflection methods throw checked exceptions that should be handled appropriately.

5. **Prefer compile-time solutions**: When possible, use interfaces, inheritance, or generics instead of reflection.

6. **Be aware of security implications**: Reflection can bypass encapsulation and access controls, which may pose security risks.

7. **Test thoroughly**: Code using reflection is harder to test and may have runtime errors that aren't caught at compile time.

8. **Document reflection usage**: Make it clear when and why reflection is being used in your code.

## Conclusion

The Java Reflection API is a powerful tool that allows for dynamic inspection and manipulation of classes, fields, methods, and objects at runtime. While it should be used judiciously due to performance and security considerations, reflection enables many advanced features in frameworks and libraries, such as dependency injection, ORM, serialization, and testing tools.

By understanding how to use reflection effectively and following best practices, you can leverage its capabilities to create more flexible and dynamic Java applications.