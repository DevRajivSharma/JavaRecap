# Generics in Java

Generics were introduced in Java 5 to provide compile-time type safety and eliminate the need for explicit casting. This document covers the fundamentals of Java generics, their benefits, and advanced usage patterns.

## Introduction to Generics

Generics allow you to define classes, interfaces, and methods with placeholder types that are specified when the code is used.

### Before Generics

Before generics, collections could hold any type of object, requiring explicit casting when retrieving elements:

```java
// Without generics
List list = new ArrayList();
list.add("Hello");
list.add(42);  // Mixing types is allowed but not type-safe

// Requires explicit casting, potential ClassCastException at runtime
String s = (String) list.get(0);
Integer i = (Integer) list.get(1);

// Runtime error if you get the index wrong
try {
    String error = (String) list.get(1);  // ClassCastException
} catch (ClassCastException e) {
    System.out.println("Runtime type error!");
}
```

### With Generics

With generics, the compiler enforces type safety:

```java
// With generics
List<String> stringList = new ArrayList<>();
stringList.add("Hello");
// stringList.add(42);  // Compile-time error

// No casting needed, type safety guaranteed
String s = stringList.get(0);
```

## Generic Classes and Interfaces

### Defining Generic Classes

You can define your own generic classes using type parameters:

```java
// Generic class with a type parameter T
public class Box<T> {
    private T value;
    
    public Box() {
    }
    
    public Box(T value) {
        this.value = value;
    }
    
    public T getValue() {
        return value;
    }
    
    public void setValue(T value) {
        this.value = value;
    }
}

// Usage
Box<Integer> intBox = new Box<>(42);
Integer intValue = intBox.getValue();  // No casting needed

Box<String> stringBox = new Box<>("Hello");
String stringValue = stringBox.getValue();
```

### Multiple Type Parameters

Classes can have multiple type parameters:

```java
// Generic class with multiple type parameters
public class Pair<K, V> {
    private K key;
    private V value;
    
    public Pair(K key, V value) {
        this.key = key;
        this.value = value;
    }
    
    public K getKey() {
        return key;
    }
    
    public V getValue() {
        return value;
    }
    
    @Override
    public String toString() {
        return key + "=" + value;
    }
}

// Usage
Pair<String, Integer> pair = new Pair<>("age", 30);
String key = pair.getKey();    // "age"
Integer value = pair.getValue();  // 30
```

### Generic Interfaces

Interfaces can also be generic:

```java
// Generic interface
public interface Repository<T, ID> {
    T findById(ID id);
    List<T> findAll();
    void save(T entity);
    void delete(T entity);
}

// Implementation for a specific type
public class UserRepository implements Repository<User, Long> {
    @Override
    public User findById(Long id) {
        // Implementation
        return null;
    }
    
    @Override
    public List<User> findAll() {
        // Implementation
        return null;
    }
    
    @Override
    public void save(User entity) {
        // Implementation
    }
    
    @Override
    public void delete(User entity) {
        // Implementation
    }
}
```

## Generic Methods

Methods can also have their own type parameters, independent of the class's type parameters:

```java
// Generic method
public class Utilities {
    // Generic method with type parameter T
    public static <T> T getLastElement(List<T> list) {
        if (list == null || list.isEmpty()) {
            return null;
        }
        return list.get(list.size() - 1);
    }
    
    // Generic method with multiple type parameters
    public static <T, U> Map<U, T> reverseMap(Map<T, U> map) {
        Map<U, T> result = new HashMap<>();
        for (Map.Entry<T, U> entry : map.entrySet()) {
            result.put(entry.getValue(), entry.getKey());
        }
        return result;
    }
}

// Usage
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");
String lastName = Utilities.<String>getLastElement(names);  // Explicit type argument
String lastName2 = Utilities.getLastElement(names);  // Type inference

Map<Integer, String> idToName = new HashMap<>();
idToName.put(1, "Alice");
idToName.put(2, "Bob");
Map<String, Integer> nameToId = Utilities.reverseMap(idToName);
```

## Type Bounds

Type bounds restrict the types that can be used as type arguments.

### Upper Bounds

Upper bounds specify that a type parameter must be a subtype of a specific type:

```java
// Upper bound: T must be a Number or a subclass of Number
public class MathBox<T extends Number> {
    private T value;
    
    public MathBox(T value) {
        this.value = value;
    }
    
    public double sqrt() {
        return Math.sqrt(value.doubleValue());  // Can call Number methods
    }
    
    public T getValue() {
        return value;
    }
}

// Usage
MathBox<Integer> intBox = new MathBox<>(16);
double sqrt = intBox.sqrt();  // 4.0

MathBox<Double> doubleBox = new MathBox<>(25.0);
double sqrt2 = doubleBox.sqrt();  // 5.0

// MathBox<String> stringBox = new MathBox<>("Hello");  // Compile-time error
```

### Multiple Bounds

A type parameter can have multiple bounds (one class and multiple interfaces):

```java
// Multiple bounds: T must extend Comparable and implement Serializable
public class SortableBox<T extends Comparable<T> & Serializable> {
    private T value;
    
    public SortableBox(T value) {
        this.value = value;
    }
    
    public T getValue() {
        return value;
    }
    
    public int compareTo(SortableBox<T> other) {
        return value.compareTo(other.value);  // Can call Comparable methods
    }
    
    public void saveToFile(String filename) throws IOException {
        // Can serialize because T implements Serializable
        try (ObjectOutputStream oos = new ObjectOutputStream(
                new FileOutputStream(filename))) {
            oos.writeObject(value);
        }
    }
}
```

## Wildcards

Wildcards provide flexibility when working with generic types.

### Unbounded Wildcards

Unbounded wildcards (`?`) represent an unknown type:

```java
// Method that works with any type of list
public static void printList(List<?> list) {
    for (Object elem : list) {
        System.out.println(elem);
    }
}

// Usage
List<Integer> integers = Arrays.asList(1, 2, 3);
List<String> strings = Arrays.asList("one", "two", "three");

printList(integers);  // Works with List<Integer>
printList(strings);   // Works with List<String>
```

### Upper Bounded Wildcards

Upper bounded wildcards (`? extends T`) represent an unknown type that is a subtype of T:

```java
// Method that works with lists of Number or any subclass of Number
public static double sumOfList(List<? extends Number> list) {
    double sum = 0.0;
    for (Number n : list) {
        sum += n.doubleValue();
    }
    return sum;
}

// Usage
List<Integer> integers = Arrays.asList(1, 2, 3);
List<Double> doubles = Arrays.asList(1.1, 2.2, 3.3);

double sum1 = sumOfList(integers);  // Works with List<Integer>
double sum2 = sumOfList(doubles);   // Works with List<Double>
```

### Lower Bounded Wildcards

Lower bounded wildcards (`? super T`) represent an unknown type that is a supertype of T:

```java
// Method that adds integers to a list of Integer or any superclass of Integer
public static void addNumbers(List<? super Integer> list) {
    for (int i = 1; i <= 5; i++) {
        list.add(i);  // Can add Integers to the list
    }
}

// Usage
List<Integer> integers = new ArrayList<>();
List<Number> numbers = new ArrayList<>();
List<Object> objects = new ArrayList<>();

addNumbers(integers);  // Works with List<Integer>
addNumbers(numbers);   // Works with List<Number>
addNumbers(objects);   // Works with List<Object>
```

## PECS Principle (Producer Extends, Consumer Super)

A useful guideline for using wildcards:

- Use `? extends T` when you only need to read from a collection (Producer)
- Use `? super T` when you only need to write to a collection (Consumer)

```java
// Producer (read-only)
public static void printNumbers(List<? extends Number> list) {
    for (Number n : list) {
        System.out.println(n);
    }
    // list.add(1);  // Compile-time error, can't add to a ? extends Number
}

// Consumer (write-only)
public static void addIntegers(List<? super Integer> list) {
    list.add(1);
    list.add(2);
    // Integer n = list.get(0);  // Compile-time error, can't guarantee type
    Object o = list.get(0);  // This works but loses type information
}
```

## Type Erasure

Generics in Java are implemented using type erasure, which means generic type information is removed at compile time.

### How Type Erasure Works

1. Replace type parameters with their bounds or Object if unbounded
2. Insert casts where necessary
3. Generate bridge methods to preserve polymorphism

```java
// Before type erasure
public class Box<T> {
    private T value;
    
    public T getValue() {
        return value;
    }
    
    public void setValue(T value) {
        this.value = value;
    }
}

// After type erasure (conceptually)
public class Box {
    private Object value;
    
    public Object getValue() {
        return value;
    }
    
    public void setValue(Object value) {
        this.value = value;
    }
}
```

### Limitations Due to Type Erasure

1. **Cannot create instances of type parameters**:

```java
public class Creator<T> {
    // Error: Cannot create an instance of the type parameter
    public T create() {
        return new T();  // Compile-time error
    }
}
```

2. **Cannot use primitive types as type arguments**:

```java
// Error: Cannot use primitive types as type arguments
// List<int> numbers = new ArrayList<>();  // Compile-time error

// Must use wrapper classes instead
List<Integer> numbers = new ArrayList<>();
```

3. **Cannot check instanceof with generic types**:

```java
public static <T> boolean isInstance(Object obj, List<T> list) {
    // Error: Cannot perform instanceof check against parameterized type
    // return obj instanceof List<T>;  // Compile-time error
    
    // Can only check raw type
    return obj instanceof List;  // Works but loses type information
}
```

4. **Cannot create arrays of parameterized types**:

```java
// Error: Cannot create arrays of parameterized types
// List<String>[] stringLists = new List<String>[10];  // Compile-time error

// Must use raw types or create a list of lists
List[] rawLists = new List[10];  // Works but not type-safe
List<List<String>> listOfStringLists = new ArrayList<>();  // Better approach
```

## Generic Type Inference

Java can often infer the type arguments for generic methods and constructors.

### Basic Type Inference

```java
// Type inference for generic methods
List<String> names = Arrays.asList("Alice", "Bob");  // Infers List<String>
Map<String, Integer> scores = Map.of("Alice", 95, "Bob", 87);  // Infers types

// Type inference for constructors (Java 7+)
List<String> list = new ArrayList<>();  // Diamond operator <>
```

### Target Typing (Java 8+)

Java 8 improved type inference with target typing:

```java
// Java 8 target typing
List<String> names = Collections.emptyList();  // Infers List<String>

// With method references
List<Person> people = ...
people.sort(Comparator.comparing(Person::getName));
```

## Reifiable and Non-Reifiable Types

A reifiable type is one whose type information is fully available at runtime.

### Reifiable Types

- Primitive types (int, double, etc.)
- Non-generic types (String, Integer, etc.)
- Raw types (List, Map, etc.)
- Unbounded wildcard parameterized types (List<?>, Map<?, ?>, etc.)
- Arrays of reifiable types (int[], String[], etc.)

### Non-Reifiable Types

- Generic types with actual type parameters (List<String>, Map<Integer, String>, etc.)
- Arrays of non-reifiable types (List<String>[], etc.)

## Advanced Generic Patterns

### Type Tokens

Type tokens allow you to pass and use type information at runtime:

```java
public class TypeReference<T> {
    private final Type type;
    
    protected TypeReference() {
        Type superclass = getClass().getGenericSuperclass();
        type = ((ParameterizedType) superclass).getActualTypeArguments()[0];
    }
    
    public Type getType() {
        return type;
    }
}

// Usage
TypeReference<List<String>> typeRef = new TypeReference<List<String>>() {};
Type listStringType = typeRef.getType();  // Represents List<String>
```

### Class Literals as Runtime Type Tokens

Class literals can be used as runtime type tokens for simple types:

```java
public class GenericDao<T> {
    private final Class<T> type;
    
    public GenericDao(Class<T> type) {
        this.type = type;
    }
    
    public T findById(long id) {
        try {
            // Use type information at runtime
            T instance = type.getDeclaredConstructor().newInstance();
            // ... load data into instance
            return instance;
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }
}

// Usage
GenericDao<User> userDao = new GenericDao<>(User.class);
User user = userDao.findById(123);
```

### Self-Bounded Types

Self-bounded types are used to create fluent interfaces:

```java
public class Builder<T extends Builder<T>> {
    @SuppressWarnings("unchecked")
    public T withName(String name) {
        // Implementation
        return (T) this;
    }
    
    @SuppressWarnings("unchecked")
    public T withAge(int age) {
        // Implementation
        return (T) this;
    }
}

public class PersonBuilder extends Builder<PersonBuilder> {
    public Person build() {
        // Create and return Person
        return new Person();
    }
}

// Usage - fluent interface
Person person = new PersonBuilder()
    .withName("Alice")
    .withAge(30)
    .build();
```

### Recursive Type Bounds

Recursive type bounds are useful for comparing objects of the same type:

```java
public class Box<T extends Comparable<T>> implements Comparable<Box<T>> {
    private T value;
    
    public Box(T value) {
        this.value = value;
    }
    
    public T getValue() {
        return value;
    }
    
    @Override
    public int compareTo(Box<T> other) {
        return value.compareTo(other.value);
    }
}

// More complex example
public interface Comparable<T> {
    int compareTo(T other);
}

public class Person implements Comparable<Person> {
    private String name;
    
    @Override
    public int compareTo(Person other) {
        return name.compareTo(other.name);
    }
}
```

## Best Practices

1. **Use generics for type safety**: Always use generics when working with collections and other generic APIs.

2. **Favor composition over inheritance with generics**: Generic inheritance can be complex; composition is often clearer.

3. **Use bounded wildcards appropriately**: Follow the PECS principle (Producer Extends, Consumer Super).

4. **Minimize the scope of type parameters**: Use the most specific type bounds possible.

5. **Avoid raw types**: Raw types bypass generic type checking and should be avoided in new code.

6. **Be aware of type erasure limitations**: Understand what you can and cannot do with generics due to type erasure.

7. **Use generic methods for increased flexibility**: Generic methods can provide type safety without requiring the entire class to be generic.

8. **Consider type tokens for runtime type information**: When you need type information at runtime, use type tokens or class literals.

9. **Document type parameters**: Use clear names and javadoc to explain the purpose of type parameters.

10. **Use the diamond operator**: In Java 7+, use the diamond operator (`<>`) for cleaner code.

```java
// Good practice examples

// Clear type parameter names
public class Pair<K, V> { /* ... */ }

// Bounded type parameter with documentation
/**
 * A box that can hold any number type and perform mathematical operations.
 * @param <T> the type of number stored in this box
 */
public class MathBox<T extends Number> { /* ... */ }

// Generic method with appropriate wildcards
public static <T> void copy(List<? super T> dest, List<? extends T> src) {
    for (int i = 0; i < src.size(); i++) {
        dest.add(src.get(i));
    }
}

// Using the diamond operator
Map<String, List<Integer>> map = new HashMap<>();
```

## Conclusion

Generics are a powerful feature in Java that provide compile-time type safety, eliminate the need for explicit casting, and enable the creation of reusable, type-safe code. While generics have some limitations due to type erasure, understanding these limitations and following best practices allows you to write cleaner, safer, and more maintainable code.

By mastering generics, you can create flexible APIs that work with a variety of types while maintaining type safety, leading to fewer runtime errors and more robust applications.