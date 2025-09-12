# Arrays in Java

Arrays are one of the most fundamental data structures in Java. They allow you to store multiple values of the same type in a single variable. This document covers everything you need to know about arrays in Java.

## Array Basics

### Declaration and Creation

There are several ways to declare and create arrays in Java:

```java
// Declaration only
int[] numbers;            // Preferred syntax
int numbers[];           // Also valid but less common

// Declaration and creation
numbers = new int[5];    // Creates an array of 5 integers

// Declaration, creation, and initialization in one step
int[] scores = new int[3];  // Creates an array of 3 integers with default values (0)

// Declaration and initialization with specific values
int[] points = {95, 85, 75, 65, 55};  // Array literal
int[] values = new int[] {10, 20, 30, 40, 50};  // Alternative syntax
```

### Default Values

When you create an array using `new`, all elements are initialized with default values:

- Numeric types (`byte`, `short`, `int`, `long`, `float`, `double`): `0`
- `boolean`: `false`
- `char`: `'\u0000'` (null character)
- Reference types (objects): `null`

```java
int[] numbers = new int[3];       // {0, 0, 0}
boolean[] flags = new boolean[2]; // {false, false}
String[] names = new String[2];   // {null, null}
```

### Accessing Array Elements

Array elements are accessed using zero-based indexing:

```java
int[] scores = {95, 85, 75};

int firstScore = scores[0];  // 95
int secondScore = scores[1]; // 85
int thirdScore = scores[2];  // 75

// Modifying an element
scores[1] = 90;  // Now the array is {95, 90, 75}
```

### Array Length

The length of an array is fixed when it's created and can be accessed using the `length` property:

```java
int[] numbers = {10, 20, 30, 40, 50};
int length = numbers.length;  // 5

// Using length in a loop
for (int i = 0; i < numbers.length; i++) {
    System.out.println(numbers[i]);
}
```

### Common Errors

1. **ArrayIndexOutOfBoundsException**: Occurs when you try to access an index that is outside the valid range (0 to length-1).

```java
int[] numbers = {1, 2, 3};
int value = numbers[3];  // Throws ArrayIndexOutOfBoundsException
```

2. **NullPointerException**: Occurs when you try to access an array that hasn't been initialized.

```java
int[] numbers;  // Declared but not initialized
numbers[0] = 10;  // Throws NullPointerException
```

## Array Operations

### Iterating Through Arrays

There are several ways to iterate through arrays in Java:

```java
int[] numbers = {10, 20, 30, 40, 50};

// Using for loop with index
for (int i = 0; i < numbers.length; i++) {
    System.out.println("Element at index " + i + ": " + numbers[i]);
}

// Using enhanced for loop (for-each)
for (int number : numbers) {
    System.out.println("Element: " + number);
}

// Using Java 8 Stream API
Arrays.stream(numbers).forEach(number -> System.out.println("Element: " + number));
```

### Copying Arrays

There are multiple ways to copy arrays in Java:

```java
int[] original = {1, 2, 3, 4, 5};

// Using System.arraycopy()
int[] copy1 = new int[original.length];
System.arraycopy(original, 0, copy1, 0, original.length);

// Using Arrays.copyOf()
int[] copy2 = Arrays.copyOf(original, original.length);

// Using Arrays.copyOfRange()
int[] partialCopy = Arrays.copyOfRange(original, 1, 4);  // {2, 3, 4}

// Using clone() method
int[] copy3 = original.clone();

// Using Java 8 Stream API
int[] copy4 = Arrays.stream(original).toArray();
```

### Searching in Arrays

```java
int[] numbers = {10, 20, 30, 40, 50};

// Linear search
int searchValue = 30;
int index = -1;

for (int i = 0; i < numbers.length; i++) {
    if (numbers[i] == searchValue) {
        index = i;
        break;
    }
}

System.out.println("Found at index: " + index);  // 2

// Binary search (requires sorted array)
int[] sortedNumbers = {10, 20, 30, 40, 50};
int binarySearchIndex = Arrays.binarySearch(sortedNumbers, 30);
System.out.println("Binary search found at index: " + binarySearchIndex);  // 2
```

### Sorting Arrays

```java
int[] numbers = {5, 2, 9, 1, 7};

// Sorting in ascending order
Arrays.sort(numbers);
// Now numbers is {1, 2, 5, 7, 9}

// Sorting objects
String[] names = {"Charlie", "Alice", "Bob"};
Arrays.sort(names);
// Now names is {"Alice", "Bob", "Charlie"}

// Custom sorting with Comparator (for objects)
Student[] students = {
    new Student("Alice", 85),
    new Student("Bob", 92),
    new Student("Charlie", 78)
};

// Sort by score in descending order
Arrays.sort(students, (s1, s2) -> s2.getScore() - s1.getScore());
```

### Comparing Arrays

```java
int[] array1 = {1, 2, 3};
int[] array2 = {1, 2, 3};
int[] array3 = {1, 2, 4};

// Using equals method
boolean isEqual1 = Arrays.equals(array1, array2);  // true
boolean isEqual2 = Arrays.equals(array1, array3);  // false

// Using deepEquals for multi-dimensional arrays
int[][] matrix1 = {{1, 2}, {3, 4}};
int[][] matrix2 = {{1, 2}, {3, 4}};
boolean isDeepEqual = Arrays.deepEquals(matrix1, matrix2);  // true
```

### Filling Arrays

```java
int[] numbers = new int[5];

// Fill the entire array with a value
Arrays.fill(numbers, 10);
// Now numbers is {10, 10, 10, 10, 10}

// Fill a range with a value
Arrays.fill(numbers, 1, 4, 20);
// Now numbers is {10, 20, 20, 20, 10}
```

## Multi-dimensional Arrays

Java supports multi-dimensional arrays, which are essentially arrays of arrays.

### Two-dimensional Arrays

```java
// Declaration and creation
int[][] matrix = new int[3][4];  // 3 rows, 4 columns

// Declaration and initialization
int[][] grid = {
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9}
};

// Accessing elements
int value = grid[1][2];  // 6 (row 1, column 2)

// Modifying elements
grid[0][0] = 10;  // Changes the top-left element to 10

// Getting dimensions
int rows = grid.length;       // 3
int columns = grid[0].length; // 3
```

### Iterating Through Multi-dimensional Arrays

```java
int[][] matrix = {
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9}
};

// Using nested for loops
for (int i = 0; i < matrix.length; i++) {
    for (int j = 0; j < matrix[i].length; j++) {
        System.out.print(matrix[i][j] + " ");
    }
    System.out.println();
}

// Using enhanced for loops
for (int[] row : matrix) {
    for (int element : row) {
        System.out.print(element + " ");
    }
    System.out.println();
}
```

### Jagged Arrays (Irregular Arrays)

Java allows arrays where each row can have a different length:

```java
// Creating a jagged array
int[][] jagged = new int[3][];
jagged[0] = new int[2];
jagged[1] = new int[4];
jagged[2] = new int[3];

// Initializing a jagged array
int[][] triangle = {
    {1},
    {2, 3},
    {4, 5, 6},
    {7, 8, 9, 10}
};

// Accessing elements
int value = triangle[2][1];  // 5

// Iterating through a jagged array
for (int i = 0; i < triangle.length; i++) {
    for (int j = 0; j < triangle[i].length; j++) {
        System.out.print(triangle[i][j] + " ");
    }
    System.out.println();
}
```

## Arrays Utility Class

The `java.util.Arrays` class provides many useful methods for working with arrays:

```java
import java.util.Arrays;

public class ArraysUtilityExample {
    public static void main(String[] args) {
        int[] numbers = {5, 2, 9, 1, 7};
        
        // Sorting
        Arrays.sort(numbers);
        System.out.println("Sorted: " + Arrays.toString(numbers));
        
        // Binary search
        int index = Arrays.binarySearch(numbers, 7);
        System.out.println("Found 7 at index: " + index);
        
        // Filling
        int[] newArray = new int[5];
        Arrays.fill(newArray, 10);
        System.out.println("Filled: " + Arrays.toString(newArray));
        
        // Comparing
        int[] copy = Arrays.copyOf(numbers, numbers.length);
        boolean isEqual = Arrays.equals(numbers, copy);
        System.out.println("Arrays are equal: " + isEqual);
        
        // Converting to String
        String arrayString = Arrays.toString(numbers);
        System.out.println("Array as string: " + arrayString);
        
        // Parallel sorting (Java 8+)
        int[] largeArray = {9, 8, 7, 6, 5, 4, 3, 2, 1};
        Arrays.parallelSort(largeArray);
        System.out.println("Parallel sorted: " + Arrays.toString(largeArray));
        
        // Stream operations (Java 8+)
        int sum = Arrays.stream(numbers).sum();
        double average = Arrays.stream(numbers).average().orElse(0);
        int max = Arrays.stream(numbers).max().orElse(0);
        System.out.println("Sum: " + sum + ", Average: " + average + ", Max: " + max);
    }
}
```

## Array Performance Considerations

1. **Fixed Size**: Arrays have a fixed size, which can't be changed after creation. If you need a dynamic size, consider using `ArrayList` or other collections.

2. **Memory Allocation**: Arrays are stored in contiguous memory locations, which makes accessing elements by index very fast (O(1)).

3. **Insertion and Deletion**: Adding or removing elements from the middle of an array requires shifting elements, which is inefficient (O(n)).

4. **Primitive vs. Object Arrays**: Arrays of primitives are more memory-efficient than arrays of objects.

5. **Multi-dimensional Arrays**: Accessing elements in multi-dimensional arrays is slightly slower than in single-dimensional arrays due to additional pointer indirection.

## Common Array Algorithms

### Finding the Maximum and Minimum

```java
int[] numbers = {5, 2, 9, 1, 7};

// Finding maximum
int max = numbers[0];
for (int i = 1; i < numbers.length; i++) {
    if (numbers[i] > max) {
        max = numbers[i];
    }
}
System.out.println("Maximum: " + max);  // 9

// Finding minimum
int min = numbers[0];
for (int i = 1; i < numbers.length; i++) {
    if (numbers[i] < min) {
        min = numbers[i];
    }
}
System.out.println("Minimum: " + min);  // 1

// Using Java 8 Stream API
int streamMax = Arrays.stream(numbers).max().getAsInt();
int streamMin = Arrays.stream(numbers).min().getAsInt();
System.out.println("Stream Max: " + streamMax + ", Stream Min: " + streamMin);
```

### Calculating Sum and Average

```java
int[] numbers = {5, 2, 9, 1, 7};

// Calculating sum
int sum = 0;
for (int number : numbers) {
    sum += number;
}
System.out.println("Sum: " + sum);  // 24

// Calculating average
double average = (double) sum / numbers.length;
System.out.println("Average: " + average);  // 4.8

// Using Java 8 Stream API
int streamSum = Arrays.stream(numbers).sum();
double streamAverage = Arrays.stream(numbers).average().getAsDouble();
System.out.println("Stream Sum: " + streamSum + ", Stream Average: " + streamAverage);
```

### Reversing an Array

```java
int[] numbers = {1, 2, 3, 4, 5};

// Reversing in-place
for (int i = 0; i < numbers.length / 2; i++) {
    int temp = numbers[i];
    numbers[i] = numbers[numbers.length - 1 - i];
    numbers[numbers.length - 1 - i] = temp;
}

// Now numbers is {5, 4, 3, 2, 1}
System.out.println("Reversed: " + Arrays.toString(numbers));
```

### Checking if an Array Contains a Value

```java
int[] numbers = {5, 2, 9, 1, 7};
int searchValue = 9;

// Linear search
boolean contains = false;
for (int number : numbers) {
    if (number == searchValue) {
        contains = true;
        break;
    }
}
System.out.println("Contains " + searchValue + ": " + contains);  // true

// Using Java 8 Stream API
boolean streamContains = Arrays.stream(numbers).anyMatch(n -> n == searchValue);
System.out.println("Stream contains " + searchValue + ": " + streamContains);
```

### Removing Duplicates

```java
int[] numbers = {1, 2, 2, 3, 4, 4, 5};

// Using Set to remove duplicates
Set<Integer> set = new HashSet<>();
for (int number : numbers) {
    set.add(number);
}

// Convert back to array
int[] uniqueNumbers = new int[set.size()];
int index = 0;
for (int number : set) {
    uniqueNumbers[index++] = number;
}

// Using Java 8 Stream API
int[] streamUniqueNumbers = Arrays.stream(numbers)
                                  .distinct()
                                  .toArray();

System.out.println("Unique numbers: " + Arrays.toString(streamUniqueNumbers));
```

## Best Practices

1. **Use the right type**: Choose the appropriate array type based on your data requirements.

2. **Prefer enhanced for loop**: Use the enhanced for loop (for-each) when you don't need the index.

3. **Check array bounds**: Always ensure that array indices are within bounds to avoid `ArrayIndexOutOfBoundsException`.

4. **Initialize arrays properly**: Always initialize arrays before using them to avoid `NullPointerException`.

5. **Consider collections**: For dynamic size requirements, consider using collections like `ArrayList` instead of arrays.

6. **Use Arrays utility methods**: Leverage the methods in the `Arrays` class for common operations.

7. **Be careful with large arrays**: Very large arrays can cause memory issues, especially on the stack.

8. **Use multi-dimensional arrays judiciously**: They can be harder to understand and maintain.

9. **Consider memory usage**: Arrays of primitives are more memory-efficient than arrays of objects.

10. **Document array indices**: When array indices have specific meanings, document them clearly.

```java
// Example of good array practices
public class ArrayBestPractices {
    public static void main(String[] args) {
        // 1. Choose appropriate type
        int[] scores = new int[10];  // For numeric data
        String[] names = new String[10];  // For text data
        
        // 2. Initialize before use
        Arrays.fill(scores, 0);  // Initialize with default values
        
        // 3. Check bounds before access
        int index = 5;
        if (index >= 0 && index < scores.length) {
            scores[index] = 100;
        }
        
        // 4. Use enhanced for loop when possible
        int sum = 0;
        for (int score : scores) {
            sum += score;
        }
        
        // 5. Use Arrays utility methods
        Arrays.sort(scores);
        System.out.println(Arrays.toString(scores));
        
        // 6. Consider collections for dynamic size
        List<Integer> dynamicScores = new ArrayList<>();
        dynamicScores.add(95);
        dynamicScores.add(85);
        
        // 7. Document array indices when they have specific meanings
        int[] playerStats = new int[5];
        // Index 0: Health, Index 1: Strength, Index 2: Defense, Index 3: Speed, Index 4: Level
        playerStats[0] = 100;  // Health
        playerStats[1] = 75;   // Strength
    }
}
```

## Conclusion

Arrays are a fundamental data structure in Java that provide a way to store and manipulate collections of values. Understanding how to effectively use arrays is essential for Java programming. While arrays have limitations such as fixed size, they offer excellent performance for many common operations and serve as the foundation for more complex data structures.