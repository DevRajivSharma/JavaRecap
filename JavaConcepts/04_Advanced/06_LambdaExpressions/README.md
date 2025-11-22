# Lambda Expressions and Functional Interfaces in Java

Lambda expressions were introduced in Java 8 as a way to implement functional programming concepts in Java. This document covers lambda expressions, functional interfaces, and related features like method references and the Stream API.

## Introduction to Lambda Expressions

Lambda expressions provide a concise way to represent an anonymous function that can be passed around as if it were an object.

### Syntax of Lambda Expressions

The basic syntax of a lambda expression is:

```
(parameters) -> expression
```

or

```
(parameters) -> { statements; }
```

### Examples of Lambda Expressions

```java
// Lambda with no parameters
Runnable noParams = () -> System.out.println("Hello, World!");

// Lambda with one parameter (type is inferred)
Consumer<String> oneParam = s -> System.out.println(s);

// Lambda with multiple parameters
BiFunction<Integer, Integer, Integer> add = (a, b) -> a + b;

// Lambda with explicit type declarations
BiFunction<Integer, Integer, Integer> multiply = (Integer a, Integer b) -> a * b;

// Lambda with multiple statements
BiFunction<Integer, Integer, Integer> subtract = (a, b) -> {
    System.out.println("Subtracting " + b + " from " + a);
    return a - b;
};
```

## Functional Interfaces

A functional interface is an interface with exactly one abstract method. Lambda expressions can be used to create instances of functional interfaces.

### Creating Custom Functional Interfaces

```java
// Custom functional interface
@FunctionalInterface  // Optional but recommended annotation
public interface Calculator {
    int calculate(int a, int b);
    
    // Default methods are allowed in functional interfaces
    default void printInfo() {
        System.out.println("This is a calculator interface");
    }
    
    // Static methods are allowed in functional interfaces
    static Calculator getAdditionCalculator() {
        return (a, b) -> a + b;
    }
}

// Using the custom functional interface
public class CalculatorExample {
    public static void main(String[] args) {
        // Lambda expression implementing the calculate method
        Calculator addition = (a, b) -> a + b;
        Calculator subtraction = (a, b) -> a - b;
        Calculator multiplication = (a, b) -> a * b;
        Calculator division = (a, b) -> a / b;
        
        System.out.println("Addition: " + addition.calculate(10, 5));        // 15
        System.out.println("Subtraction: " + subtraction.calculate(10, 5));   // 5
        System.out.println("Multiplication: " + multiplication.calculate(10, 5)); // 50
        System.out.println("Division: " + division.calculate(10, 5));         // 2
        
        // Using default method
        addition.printInfo();  // This is a calculator interface
        
        // Using static method
        Calculator adder = Calculator.getAdditionCalculator();
        System.out.println("Static adder: " + adder.calculate(7, 3));  // 10
    }
}
```

### Built-in Functional Interfaces

Java provides many built-in functional interfaces in the `java.util.function` package.

#### Function<T, R>

Represents a function that takes an argument of type T and returns a result of type R.

```java
import java.util.function.Function;

public class FunctionExample {
    public static void main(String[] args) {
        // Function that converts a string to its length
        Function<String, Integer> lengthFunction = s -> s.length();
        
        // Apply the function
        int length = lengthFunction.apply("Hello, World!");
        System.out.println("Length: " + length);  // 13
        
        // Function composition
        Function<Integer, Integer> doubleFunction = n -> n * 2;
        Function<Integer, Integer> incrementFunction = n -> n + 1;
        
        // First apply doubleFunction, then incrementFunction
        Function<Integer, Integer> doubleAndIncrementFunction = 
            incrementFunction.compose(doubleFunction);
        
        // First apply incrementFunction, then doubleFunction
        Function<Integer, Integer> incrementAndDoubleFunction = 
            incrementFunction.andThen(doubleFunction);
        
        System.out.println(doubleAndIncrementFunction.apply(5));  // 5*2+1 = 11
        System.out.println(incrementAndDoubleFunction.apply(5));  // (5+1)*2 = 12
        
        // Identity function
        Function<String, String> identityFunction = Function.identity();
        System.out.println(identityFunction.apply("Hello"));  // Hello
    }
}
```

#### Predicate<T>

Represents a predicate (boolean-valued function) of one argument.

```java
import java.util.Arrays;
import java.util.List;
import java.util.function.Predicate;

public class PredicateExample {
    public static void main(String[] args) {
        // Predicate that tests if a string is empty
        Predicate<String> isEmpty = s -> s.isEmpty();
        
        // Test the predicate
        System.out.println("Is empty: " + isEmpty.test(""));       // true
        System.out.println("Is empty: " + isEmpty.test("Hello"));  // false
        
        // Predicate composition
        Predicate<String> isNotEmpty = isEmpty.negate();
        Predicate<String> isShort = s -> s.length() < 5;
        
        // Logical AND
        Predicate<String> isNotEmptyAndShort = isNotEmpty.and(isShort);
        
        // Logical OR
        Predicate<String> isEmptyOrShort = isEmpty.or(isShort);
        
        List<String> strings = Arrays.asList("", "Hi", "Hello", "HelloWorld");
        
        System.out.println("Not empty and short strings:");
        filterStrings(strings, isNotEmptyAndShort);
        
        System.out.println("Empty or short strings:");
        filterStrings(strings, isEmptyOrShort);
    }
    
    private static void filterStrings(List<String> strings, Predicate<String> predicate) {
        for (String s : strings) {
            if (predicate.test(s)) {
                System.out.println(s);
            }
        }
    }
}
```

#### Consumer<T>

Represents an operation that accepts a single input argument and returns no result.

```java
import java.util.Arrays;
import java.util.List;
import java.util.function.Consumer;

public class ConsumerExample {
    public static void main(String[] args) {
        // Consumer that prints a string
        Consumer<String> printer = s -> System.out.println(s);
        
        // Apply the consumer
        printer.accept("Hello, World!");  // Hello, World!
        
        // Consumer composition
        Consumer<String> logger = s -> System.out.println("Logging: " + s);
        Consumer<String> printerAndLogger = printer.andThen(logger);
        
        printerAndLogger.accept("Test");
        // Output:
        // Test
        // Logging: Test
        
        // Using Consumer with forEach
        List<String> names = Arrays.asList("Alice", "Bob", "Charlie");
        names.forEach(printer);  // Prints each name
    }
}
```

#### Supplier<T>

Represents a supplier of results, with no input arguments.

```java
import java.util.function.Supplier;
import java.util.Random;

public class SupplierExample {
    public static void main(String[] args) {
        // Supplier that returns a random integer
        Supplier<Integer> randomSupplier = () -> new Random().nextInt(100);
        
        // Get a value from the supplier
        int randomValue = randomSupplier.get();
        System.out.println("Random value: " + randomValue);
        
        // Supplier that returns the current timestamp
        Supplier<Long> timestampSupplier = () -> System.currentTimeMillis();
        
        System.out.println("Current timestamp: " + timestampSupplier.get());
        
        // Using Supplier for lazy evaluation
        printIfTrue(true, () -> "This is a heavy computation");
        printIfTrue(false, () -> "This won't be evaluated");
    }
    
    private static void printIfTrue(boolean condition, Supplier<String> supplier) {
        if (condition) {
            System.out.println(supplier.get());
        }
    }
}
```

#### BiFunction<T, U, R>

Represents a function that takes two arguments and produces a result.

```java
import java.util.function.BiFunction;

public class BiFunctionExample {
    public static void main(String[] args) {
        // BiFunction that concatenates two strings
        BiFunction<String, String, String> concat = (s1, s2) -> s1 + s2;
        
        // Apply the BiFunction
        String result = concat.apply("Hello, ", "World!");
        System.out.println(result);  // Hello, World!
        
        // BiFunction that calculates the power
        BiFunction<Integer, Integer, Double> power = 
            (base, exponent) -> Math.pow(base, exponent);
        
        System.out.println("2^3 = " + power.apply(2, 3));  // 8.0
        
        // BiFunction with andThen
        BiFunction<Integer, Integer, Integer> sum = (a, b) -> a + b;
        Function<Integer, String> toString = n -> "Result: " + n;
        
        String sumResult = sum.andThen(toString).apply(5, 3);
        System.out.println(sumResult);  // Result: 8
    }
}
```

#### Other Functional Interfaces

Java provides many other functional interfaces for different use cases:

```java
import java.util.function.*;

public class OtherFunctionalInterfacesExample {
    public static void main(String[] args) {
        // BiConsumer<T, U> - accepts two inputs, returns no result
        BiConsumer<String, Integer> printWithCount = 
            (s, count) -> System.out.println(s + ": " + count);
        printWithCount.accept("Apples", 5);  // Apples: 5
        
        // BiPredicate<T, U> - predicate with two inputs
        BiPredicate<String, Integer> isLongerThan = 
            (s, length) -> s.length() > length;
        System.out.println(isLongerThan.test("Hello", 3));  // true
        
        // UnaryOperator<T> - function where input and output are the same type
        UnaryOperator<String> toUpperCase = s -> s.toUpperCase();
        System.out.println(toUpperCase.apply("hello"));  // HELLO
        
        // BinaryOperator<T> - BiFunction where all types are the same
        BinaryOperator<Integer> max = (a, b) -> a > b ? a : b;
        System.out.println(max.apply(10, 5));  // 10
        
        // IntPredicate, LongPredicate, DoublePredicate - primitive specializations
        IntPredicate isEven = n -> n % 2 == 0;
        System.out.println(isEven.test(4));  // true
        
        // IntFunction, LongFunction, DoubleFunction - functions with primitive input
        IntFunction<String> intToString = n -> Integer.toString(n);
        System.out.println(intToString.apply(42));  // "42"
        
        // ToIntFunction, ToLongFunction, ToDoubleFunction - functions with primitive output
        ToIntFunction<String> stringToInt = s -> Integer.parseInt(s);
        System.out.println(stringToInt.applyAsInt("42"));  // 42
        
        // IntToLongFunction, IntToDoubleFunction, etc. - primitive to primitive functions
        IntToDoubleFunction intToDouble = n -> n / 100.0;
        System.out.println(intToDouble.applyAsDouble(75));  // 0.75
    }
}
```

## Method References

Method references provide a shorthand notation for lambda expressions that call a single method.

### Types of Method References

```java
import java.util.Arrays;
import java.util.List;
import java.util.function.BiFunction;
import java.util.function.Function;
import java.util.function.Supplier;

public class MethodReferenceExample {
    public static void main(String[] args) {
        // 1. Reference to a static method
        Function<String, Integer> parseInt = Integer::parseInt;
        System.out.println(parseInt.apply("42"));  // 42
        
        // 2. Reference to an instance method of a particular object
        String greeting = "Hello";
        Supplier<String> toUpperCase = greeting::toUpperCase;
        System.out.println(toUpperCase.get());  // HELLO
        
        // 3. Reference to an instance method of an arbitrary object of a particular type
        Function<String, Integer> length = String::length;
        System.out.println(length.apply("Hello"));  // 5
        
        // 4. Reference to a constructor
        Supplier<StringBuilder> newStringBuilder = StringBuilder::new;
        StringBuilder sb = newStringBuilder.get();
        sb.append("Hello, World!");
        System.out.println(sb.toString());  // Hello, World!
        
        // Using method references with arrays
        List<String> names = Arrays.asList("Charlie", "Alice", "Bob");
        
        // Sort using a method reference
        names.sort(String::compareToIgnoreCase);
        System.out.println(names);  // [Alice, Bob, Charlie]
        
        // Method reference with BiFunction
        BiFunction<String, String, Boolean> startsWith = String::startsWith;
        System.out.println(startsWith.apply("Hello", "He"));  // true
    }
    
    // Example for constructor reference with parameters
    static class Person {
        private String name;
        
        public Person(String name) {
            this.name = name;
        }
        
        @Override
        public String toString() {
            return "Person{name='" + name + "'}";
        }
    }
    
    public static void constructorReferenceExample() {
        // Reference to a constructor with parameters
        Function<String, Person> personCreator = Person::new;
        Person person = personCreator.apply("Alice");
        System.out.println(person);  // Person{name='Alice'}
    }
}
```

## Stream API

The Stream API, introduced in Java 8, provides a functional approach to processing collections of objects.

### Creating Streams

```java
import java.util.Arrays;
import java.util.List;
import java.util.stream.IntStream;
import java.util.stream.Stream;

public class StreamCreationExample {
    public static void main(String[] args) {
        // 1. From a collection
        List<String> list = Arrays.asList("a", "b", "c");
        Stream<String> streamFromList = list.stream();
        
        // 2. From an array
        String[] array = {"a", "b", "c"};
        Stream<String> streamFromArray = Arrays.stream(array);
        
        // 3. Using Stream.of
        Stream<String> streamOf = Stream.of("a", "b", "c");
        
        // 4. Using Stream.generate (infinite stream)
        Stream<Double> randomStream = Stream.generate(Math::random).limit(5);
        randomStream.forEach(System.out::println);
        
        // 5. Using Stream.iterate (infinite stream)
        Stream<Integer> iteratedStream = Stream.iterate(0, n -> n + 2).limit(5);
        iteratedStream.forEach(System.out::println);  // 0, 2, 4, 6, 8
        
        // 6. Using IntStream, LongStream, DoubleStream for primitives
        IntStream intStream = IntStream.range(1, 6);  // 1, 2, 3, 4, 5
        intStream.forEach(System.out::println);
        
        // 7. Using IntStream.rangeClosed
        IntStream closedRangeStream = IntStream.rangeClosed(1, 5);  // 1, 2, 3, 4, 5
        closedRangeStream.forEach(System.out::println);
        
        // 8. Empty stream
        Stream<String> emptyStream = Stream.empty();
    }
}
```

### Intermediate Operations

Intermediate operations return a new stream and are lazy (not executed until a terminal operation is invoked).

```java
import java.util.Arrays;
import java.util.List;
import java.util.stream.Stream;

public class StreamIntermediateOperationsExample {
    public static void main(String[] args) {
        List<String> words = Arrays.asList("hello", "world", "java", "stream", "lambda", "functional");
        
        // filter - retains elements that match the predicate
        words.stream()
             .filter(word -> word.length() > 5)
             .forEach(System.out::println);  // lambda, functional
        
        // map - transforms each element
        words.stream()
             .map(String::toUpperCase)
             .forEach(System.out::println);  // HELLO, WORLD, JAVA, STREAM, LAMBDA, FUNCTIONAL
        
        // flatMap - transforms and flattens
        List<List<Integer>> nestedLists = Arrays.asList(
            Arrays.asList(1, 2, 3),
            Arrays.asList(4, 5, 6),
            Arrays.asList(7, 8, 9)
        );
        
        nestedLists.stream()
                   .flatMap(list -> list.stream())
                   .forEach(System.out::println);  // 1, 2, 3, 4, 5, 6, 7, 8, 9
        
        // distinct - removes duplicates
        Stream.of(1, 2, 2, 3, 3, 3, 4, 4, 4, 4)
              .distinct()
              .forEach(System.out::println);  // 1, 2, 3, 4
        
        // sorted - sorts elements
        words.stream()
             .sorted()
             .forEach(System.out::println);  // functional, hello, java, lambda, stream, world
        
        // sorted with comparator
        words.stream()
             .sorted((s1, s2) -> s1.length() - s2.length())
             .forEach(System.out::println);  // java, hello, world, stream, lambda, functional
        
        // peek - performs an action on each element without modifying the stream
        words.stream()
             .peek(word -> System.out.println("Processing: " + word))
             .map(String::toUpperCase)
             .forEach(System.out::println);
        
        // limit - truncates the stream to the given size
        words.stream()
             .limit(3)
             .forEach(System.out::println);  // hello, world, java
        
        // skip - discards the first n elements
        words.stream()
             .skip(3)
             .forEach(System.out::println);  // stream, lambda, functional
        
        // takeWhile (Java 9+) - takes elements while the predicate is true
        words.stream()
             .takeWhile(word -> word.length() <= 5)
             .forEach(System.out::println);  // hello, world, java
        
        // dropWhile (Java 9+) - drops elements while the predicate is true
        words.stream()
             .dropWhile(word -> word.length() <= 5)
             .forEach(System.out::println);  // stream, lambda, functional
    }
}
```

### Terminal Operations

Terminal operations produce a result or a side effect and cause the stream pipeline to be executed.

```java
import java.util.*;
import java.util.stream.Collectors;

public class StreamTerminalOperationsExample {
    public static void main(String[] args) {
        List<String> words = Arrays.asList("hello", "world", "java", "stream", "lambda", "functional");
        
        // forEach - performs an action for each element
        words.stream().forEach(System.out::println);
        
        // forEachOrdered - like forEach but respects encounter order
        words.parallelStream().forEachOrdered(System.out::println);
        
        // toArray - converts stream to an array
        String[] wordArray = words.stream().toArray(String[]::new);
        System.out.println(Arrays.toString(wordArray));
        
        // reduce - reduces the stream to a single value
        Optional<String> concatenated = words.stream().reduce((s1, s2) -> s1 + ", " + s2);
        concatenated.ifPresent(System.out::println);  // hello, world, java, stream, lambda, functional
        
        // reduce with identity value
        String reducedWithIdentity = words.stream().reduce("", (s1, s2) -> s1 + s2);
        System.out.println(reducedWithIdentity);  // helloworldjavastreamlambdafunctional
        
        // collect - collects elements into a collection or other result
        List<String> collectedList = words.stream().collect(Collectors.toList());
        Set<String> collectedSet = words.stream().collect(Collectors.toSet());
        
        // collect with joining
        String joined = words.stream().collect(Collectors.joining(", "));
        System.out.println(joined);  // hello, world, java, stream, lambda, functional
        
        // min - returns the minimum element
        Optional<String> shortest = words.stream().min(Comparator.comparing(String::length));
        shortest.ifPresent(s -> System.out.println("Shortest: " + s));  // java
        
        // max - returns the maximum element
        Optional<String> longest = words.stream().max(Comparator.comparing(String::length));
        longest.ifPresent(s -> System.out.println("Longest: " + s));  // functional
        
        // count - returns the count of elements
        long count = words.stream().count();
        System.out.println("Count: " + count);  // 6
        
        // anyMatch - returns true if any elements match the predicate
        boolean anyMatch = words.stream().anyMatch(s -> s.startsWith("j"));
        System.out.println("Any match: " + anyMatch);  // true
        
        // allMatch - returns true if all elements match the predicate
        boolean allMatch = words.stream().allMatch(s -> s.length() >= 4);
        System.out.println("All match: " + allMatch);  // true
        
        // noneMatch - returns true if no elements match the predicate
        boolean noneMatch = words.stream().noneMatch(s -> s.contains("z"));
        System.out.println("None match: " + noneMatch);  // true
        
        // findFirst - returns the first element
        Optional<String> first = words.stream().findFirst();
        first.ifPresent(s -> System.out.println("First: " + s));  // hello
        
        // findAny - returns any element (useful with parallel streams)
        Optional<String> any = words.stream().findAny();
        any.ifPresent(s -> System.out.println("Any: " + s));  // hello (usually)
    }
}
```

### Collectors

Collectors are used with the `collect()` terminal operation to accumulate elements into collections or other results.

```java
import java.util.*;
import java.util.function.Function;
import java.util.stream.Collectors;

public class CollectorsExample {
    static class Person {
        private String name;
        private int age;
        private String department;
        
        public Person(String name, int age, String department) {
            this.name = name;
            this.age = age;
            this.department = department;
        }
        
        public String getName() { return name; }
        public int getAge() { return age; }
        public String getDepartment() { return department; }
        
        @Override
        public String toString() {
            return name + " ("+age+", " + department + ")";
        }
    }
    
    public static void main(String[] args) {
        List<Person> people = Arrays.asList(
            new Person("Alice", 25, "HR"),
            new Person("Bob", 30, "Engineering"),
            new Person("Charlie", 35, "HR"),
            new Person("David", 40, "Engineering"),
            new Person("Eve", 45, "Management")
        );
        
        // toList, toSet, toCollection
        List<String> names = people.stream()
                                  .map(Person::getName)
                                  .collect(Collectors.toList());
        System.out.println("Names: " + names);
        
        Set<String> uniqueDepartments = people.stream()
                                             .map(Person::getDepartment)
                                             .collect(Collectors.toSet());
        System.out.println("Departments: " + uniqueDepartments);
        
        LinkedList<String> linkedListOfNames = people.stream()
                                                   .map(Person::getName)
                                                   .collect(Collectors.toCollection(LinkedList::new));
        System.out.println("LinkedList: " + linkedListOfNames);
        
        // joining
        String namesCsv = people.stream()
                              .map(Person::getName)
                              .collect(Collectors.joining(", "));
        System.out.println("Names CSV: " + namesCsv);
        
        // counting
        long count = people.stream().collect(Collectors.counting());
        System.out.println("Count: " + count);
        
        // summingInt, averagingInt, summarizingInt
        int totalAge = people.stream().collect(Collectors.summingInt(Person::getAge));
        System.out.println("Total age: " + totalAge);
        
        double averageAge = people.stream().collect(Collectors.averagingInt(Person::getAge));
        System.out.println("Average age: " + averageAge);
        
        IntSummaryStatistics ageSummary = people.stream().collect(Collectors.summarizingInt(Person::getAge));
        System.out.println("Age summary: " + ageSummary);
        
        // minBy, maxBy
        Optional<Person> oldest = people.stream().collect(Collectors.maxBy(Comparator.comparing(Person::getAge)));
        oldest.ifPresent(p -> System.out.println("Oldest: " + p));
        
        // groupingBy
        Map<String, List<Person>> byDepartment = people.stream().collect(Collectors.groupingBy(Person::getDepartment));
        System.out.println("By department: " + byDepartment);
        
        // groupingBy with downstream collector
        Map<String, Long> countByDepartment = people.stream()
                                                  .collect(Collectors.groupingBy(
                                                      Person::getDepartment, 
                                                      Collectors.counting()));
        System.out.println("Count by department: " + countByDepartment);
        
        // partitioningBy
        Map<Boolean, List<Person>> partitionedByAge = people.stream()
                                                         .collect(Collectors.partitioningBy(p -> p.getAge() > 30));
        System.out.println("Age <= 30: " + partitionedByAge.get(false));
        System.out.println("Age > 30: " + partitionedByAge.get(true));
        
        // toMap
        Map<String, Integer> nameToAge = people.stream()
                                            .collect(Collectors.toMap(
                                                Person::getName, 
                                                Person::getAge));
        System.out.println("Name to age: " + nameToAge);
        
        // toMap with merge function for duplicate keys
        Map<String, String> departmentToNames = people.stream()
                                                   .collect(Collectors.toMap(
                                                       Person::getDepartment,
                                                       Person::getName,
                                                       (name1, name2) -> name1 + ", " + name2));
        System.out.println("Department to names: " + departmentToNames);
    }
}
```

## Parallel Streams

Parallel streams allow stream operations to be executed concurrently.

```java
import java.util.Arrays;
import java.util.List;
import java.util.stream.IntStream;

public class ParallelStreamExample {
    public static void main(String[] args) {
        // Creating a parallel stream
        List<String> list = Arrays.asList("a", "b", "c", "d", "e");
        
        // Method 1: From a collection using parallelStream()
        list.parallelStream().forEach(System.out::println);
        
        // Method 2: From a stream using parallel()
        list.stream().parallel().forEach(System.out::println);
        
        // Performance example
        long start, end;
        
        // Sequential sum
        start = System.currentTimeMillis();
        long sequentialSum = IntStream.rangeClosed(1, 100_000_000)
                                    .sum();
        end = System.currentTimeMillis();
        System.out.println("Sequential sum: " + sequentialSum + 
                           " in " + (end - start) + "ms");
        
        // Parallel sum
        start = System.currentTimeMillis();
        long parallelSum = IntStream.rangeClosed(1, 100_000_000)
                                  .parallel()
                                  .sum();
        end = System.currentTimeMillis();
        System.out.println("Parallel sum: " + parallelSum + 
                           " in " + (end - start) + "ms");
        
        // Caution: Parallel streams can lead to non-deterministic behavior
        list.parallelStream().forEach(s -> System.out.println(s + " " + Thread.currentThread().getName()));
    }
}
```

## Optional

The `Optional` class was introduced in Java 8 to represent a value that might be absent.

```java
import java.util.Optional;

public class OptionalExample {
    public static void main(String[] args) {
        // Creating Optional objects
        Optional<String> empty = Optional.empty();
        Optional<String> present = Optional.of("Hello");
        Optional<String> nullable = Optional.ofNullable(null);  // Creates an empty Optional
        
        // Checking if a value is present
        System.out.println("Empty has value: " + empty.isPresent());
        System.out.println("Present has value: " + present.isPresent());
        
        // Java 11: isEmpty()
        System.out.println("Empty is empty: " + empty.isEmpty());  // Java 11+
        
        // Getting the value
        try {
            String value = empty.get();  // Throws NoSuchElementException
        } catch (Exception e) {
            System.out.println("Exception: " + e.getMessage());
        }
        
        String value = present.get();  // Safe because we know it's present
        System.out.println("Value: " + value);
        
        // Default values
        String defaultValue = empty.orElse("Default");
        System.out.println("Default value: " + defaultValue);
        
        // orElseGet with Supplier
        String suppliedValue = empty.orElseGet(() -> "Supplied Default");
        System.out.println("Supplied value: " + suppliedValue);
        
        // orElseThrow
        try {
            String throwValue = empty.orElseThrow(() -> new IllegalStateException("No value"));
        } catch (Exception e) {
            System.out.println("Exception: " + e.getMessage());
        }
        
        // ifPresent
        present.ifPresent(s -> System.out.println("Value is present: " + s));
        empty.ifPresent(s -> System.out.println("This won't print"));
        
        // ifPresentOrElse (Java 9+)
        present.ifPresentOrElse(
            s -> System.out.println("Value is present: " + s),
            () -> System.out.println("Value is absent")
        );
        
        empty.ifPresentOrElse(
            s -> System.out.println("This won't print"),
            () -> System.out.println("Value is absent")
        );
        
        // filter
        Optional<String> filtered = present.filter(s -> s.length() > 3);
        System.out.println("Filtered is present: " + filtered.isPresent());
        
        // map
        Optional<Integer> mapped = present.map(String::length);
        System.out.println("Mapped value: " + mapped.orElse(0));
        
        // flatMap
        Optional<String> flatMapped = present.flatMap(s -> Optional.of(s.toUpperCase()));
        System.out.println("FlatMapped value: " + flatMapped.orElse(""));
        
        // or (Java 9+)
        Optional<String> result = empty.or(() -> present);
        System.out.println("Or result: " + result.orElse("Not found"));
        
        // stream (Java 9+)
        long count = present.stream().count();  // 1
        System.out.println("Stream count: " + count);
    }
    
    // Example of using Optional in method return types
    public static Optional<String> findUserName(int userId) {
        // Simulated database lookup
        if (userId == 1) {
            return Optional.of("Alice");
        } else {
            return Optional.empty();
        }
    }
    
    public static void optionalInAction() {
        // Using Optional in a chain of operations
        String userName = findUserName(2)
                            .map(name -> "User: " + name)
                            .orElse("User not found");
        System.out.println(userName);  // User not found
        
        // Chaining multiple Optional-returning methods
        Optional<User> user = findUser(1);
        String email = user.flatMap(User::getEmail)  // getEmail returns Optional<String>
                          .orElse("No email");
        System.out.println("Email: " + email);
    }
    
    static class User {
        private String name;
        private Optional<String> email;  // Email might be absent
        
        public User(String name, String email) {
            this.name = name;
            this.email = Optional.ofNullable(email);
        }
        
        public String getName() {
            return name;
        }
        
        public Optional<String> getEmail() {
            return email;
        }
    }
    
    public static Optional<User> findUser(int userId) {
        // Simulated database lookup
        if (userId == 1) {
            return Optional.of(new User("Alice", "alice@example.com"));
        } else {
            return Optional.empty();
        }
    }
}
```

## Best Practices

1. **Prefer method references over lambda expressions** when they are more readable.

2. **Use built-in functional interfaces** from `java.util.function` instead of creating custom ones when possible.

3. **Keep lambda expressions short and focused** on a single task.

4. **Use descriptive parameter names** in lambda expressions to improve readability.

5. **Avoid side effects** in lambda expressions and stream operations.

6. **Use parallel streams judiciously**, as they don't always improve performance and can introduce complexity.

7. **Prefer Optional over null** for representing optional values.

8. **Use the Stream API for data processing** instead of imperative loops when it improves readability.

9. **Chain stream operations** for complex data transformations.

10. **Use appropriate terminal operations** based on your needs.

```java
// Good practices example
public class BestPracticesExample {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("Alice", "Bob", "Charlie", "David");
        
        // Good: Using method reference
        names.forEach(System.out::println);
        
        // Good: Using built-in functional interfaces
        Predicate<String> startsWithA = name -> name.startsWith("A");
        Function<String, Integer> nameLength = String::length;
        
        // Good: Short, focused lambda
        List<Integer> nameLengths = names.stream()
                                        .map(String::length)
                                        .collect(Collectors.toList());
        
        // Good: Descriptive parameter names
        Map<String, Integer> nameToLength = names.stream()
                                               .collect(Collectors.toMap(
                                                   name -> name,
                                                   name -> name.length()));
        
        // Good: Avoiding side effects in lambda
        List<String> filteredNames = names.stream()
                                         .filter(name -> name.length() > 4)
                                         .collect(Collectors.toList());
        
        // Good: Using Optional instead of null
        Optional<String> longestName = names.stream()
                                          .max(Comparator.comparing(String::length));
        
        // Good: Chaining stream operations
        double averageLength = names.stream()
                                  .filter(name -> name.length() > 3)
                                  .mapToInt(String::length)
                                  .average()
                                  .orElse(0);
        
        // Good: Using appropriate collectors
        Map<Integer, List<String>> namesByLength = names.stream()
                                                     .collect(Collectors.groupingBy(String::length));
    }
}
```

## Conclusion

Lambda expressions and functional interfaces have significantly enhanced Java's capabilities for functional programming. They enable more concise and expressive code, especially when working with collections and performing data transformations. The Stream API, method references, and the Optional class complement these features, providing a comprehensive toolkit for writing modern Java code.

By understanding and applying these concepts, you can write more readable, maintainable, and efficient Java code that takes advantage of functional programming paradigms while still leveraging Java's object-oriented foundation.