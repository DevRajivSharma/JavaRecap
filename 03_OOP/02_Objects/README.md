# Objects in Java

An object in Java is an instance of a class. It represents a real-world entity with state (attributes) and behavior (methods). Objects are the basic units of object-oriented programming.

## Creating Objects

There are several ways to create objects in Java:

### 1. Using the `new` Keyword

The most common way to create an object is using the `new` keyword followed by a constructor call.

```java
// Syntax: ClassName objectName = new ClassName();
Student student1 = new Student();
Student student2 = new Student("John Doe", 20);
```

### 2. Using Factory Methods

Some classes provide static factory methods that return instances.

```java
// Example with Integer class
Integer num1 = Integer.valueOf(10);

// Example with Collections
List<String> list = Arrays.asList("Apple", "Banana", "Orange");
```

### 3. Using Clone

Creating a copy of an existing object using the `clone()` method.

```java
public class Person implements Cloneable {
    private String name;
    private int age;
    
    // Constructor and other methods
    
    @Override
    public Person clone() throws CloneNotSupportedException {
        return (Person) super.clone();
    }
}

// Usage
Person original = new Person("Alice", 30);
Person copy = original.clone();
```

### 4. Using Deserialization

Recreating an object from its serialized form.

```java
// Deserializing an object
FileInputStream fileIn = new FileInputStream("person.ser");
ObjectInputStream in = new ObjectInputStream(fileIn);
Person person = (Person) in.readObject();
in.close();
fileIn.close();
```

## Object Lifecycle

Objects in Java go through the following lifecycle phases:

1. **Creation**: Object is created and memory is allocated
2. **Initialization**: Constructor is called to initialize the object
3. **Usage**: Object's methods and fields are accessed
4. **Destruction**: Object becomes eligible for garbage collection when no longer referenced
5. **Garbage Collection**: JVM reclaims memory occupied by unreferenced objects

## Object References

In Java, variables of class types don't hold the objects themselves but references to objects in memory.

```java
Person person1 = new Person("John", 25);  // person1 is a reference to a Person object
Person person2 = person1;                 // person2 now references the same object

person2.setName("Jane");                  // Changes are visible through both references
System.out.println(person1.getName());    // Outputs "Jane"
```

## Object Identity vs. Equality

### Identity (==)

The `==` operator checks if two references point to the same object in memory.

```java
String s1 = new String("Hello");
String s2 = new String("Hello");
System.out.println(s1 == s2);  // false - different objects in memory

String s3 = s1;
System.out.println(s1 == s3);  // true - same object reference
```

### Equality (.equals())

The `equals()` method checks if two objects are logically equivalent based on their content.

```java
String s1 = new String("Hello");
String s2 = new String("Hello");
System.out.println(s1.equals(s2));  // true - same content
```

## Implementing equals() and hashCode()

For custom classes, it's important to override both `equals()` and `hashCode()` methods properly:

```java
public class Employee {
    private int id;
    private String name;
    
    // Constructor and other methods
    
    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (obj == null || getClass() != obj.getClass()) return false;
        
        Employee employee = (Employee) obj;
        return id == employee.id && 
               Objects.equals(name, employee.name);
    }
    
    @Override
    public int hashCode() {
        return Objects.hash(id, name);
    }
}
```

## Object Methods

All Java objects inherit the following methods from the `Object` class:

1. **toString()**: Returns a string representation of the object
2. **equals(Object obj)**: Compares objects for equality
3. **hashCode()**: Returns a hash code value for the object
4. **getClass()**: Returns the runtime class of the object
5. **clone()**: Creates and returns a copy of the object
6. **finalize()**: Called by the garbage collector before reclaiming object's memory
7. **wait()**, **notify()**, **notifyAll()**: Used for thread synchronization

## Best Practices

1. Always initialize objects properly through constructors
2. Override `toString()` to provide meaningful string representation
3. Implement `equals()` and `hashCode()` consistently
4. Make objects immutable when possible
5. Avoid creating unnecessary objects
6. Use factory methods when appropriate
7. Don't rely on finalize() for cleanup operations

```java
// Example of a well-designed immutable class
public final class ImmutablePoint {
    private final int x;
    private final int y;
    
    public ImmutablePoint(int x, int y) {
        this.x = x;
        this.y = y;
    }
    
    public int getX() { return x; }
    public int getY() { return y; }
    
    @Override
    public String toString() {
        return "Point(" + x + ", " + y + ")";
    }
    
    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (obj == null || getClass() != obj.getClass()) return false;
        
        ImmutablePoint point = (ImmutablePoint) obj;
        return x == point.x && y == point.y;
    }
    
    @Override
    public int hashCode() {
        return Objects.hash(x, y);
    }
}
```