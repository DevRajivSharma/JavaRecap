# Java Collections Framework

The Java Collections Framework provides a unified architecture for representing and manipulating collections of objects. It contains interfaces, implementations, and algorithms that allow you to store, retrieve, manipulate, and communicate aggregate data efficiently.

## Collection Hierarchy

The Java Collections Framework is organized into a hierarchy of interfaces and classes:

```
Iterable
└── Collection
    ├── List
    │   ├── ArrayList
    │   ├── LinkedList
    │   └── Vector
    │       └── Stack
    ├── Set
    │   ├── HashSet
    │   │   └── LinkedHashSet
    │   └── SortedSet
    │       └── NavigableSet
    │           └── TreeSet
    └── Queue
        ├── PriorityQueue
        └── Deque
            ├── ArrayDeque
            └── LinkedList

Map (not a Collection)
├── HashMap
│   └── LinkedHashMap
├── Hashtable
├── SortedMap
│   └── NavigableMap
│       └── TreeMap
└── ConcurrentMap
    └── ConcurrentHashMap
```

## Core Interfaces

### Collection

The root interface in the collection hierarchy. A collection represents a group of objects, known as its elements.

```java
public interface Collection<E> extends Iterable<E> {
    // Basic operations
    boolean add(E e);
    boolean remove(Object o);
    boolean contains(Object o);
    int size();
    boolean isEmpty();
    void clear();
    
    // Bulk operations
    boolean addAll(Collection<? extends E> c);
    boolean removeAll(Collection<?> c);
    boolean retainAll(Collection<?> c);
    boolean containsAll(Collection<?> c);
    
    // Array operations
    Object[] toArray();
    <T> T[] toArray(T[] a);
    
    // Iterators
    Iterator<E> iterator();
}
```

### List

An ordered collection (also known as a sequence). Lists can contain duplicate elements and provide positional access.

```java
public interface List<E> extends Collection<E> {
    // Positional access
    E get(int index);
    E set(int index, E element);
    void add(int index, E element);
    E remove(int index);
    
    // Search
    int indexOf(Object o);
    int lastIndexOf(Object o);
    
    // List iteration
    ListIterator<E> listIterator();
    ListIterator<E> listIterator(int index);
    
    // View
    List<E> subList(int fromIndex, int toIndex);
}
```

### Set

A collection that cannot contain duplicate elements.

```java
public interface Set<E> extends Collection<E> {
    // Inherits methods from Collection
    // No additional methods, but guarantees no duplicates
}
```

### Queue

A collection designed for holding elements prior to processing. Queues typically order elements in a FIFO (first-in-first-out) manner.

```java
public interface Queue<E> extends Collection<E> {
    // Inserts
    boolean offer(E e);  // Adds an element if possible, otherwise returns false
    
    // Retrieval
    E peek();  // Retrieves, but does not remove, the head of the queue, or returns null if empty
    E element();  // Retrieves, but does not remove, the head of the queue (throws if empty)
    
    // Removal
    E poll();  // Retrieves and removes the head of the queue, or returns null if empty
    E remove();  // Retrieves and removes the head of the queue (throws if empty)
}
```

### Deque

A double-ended queue that supports element insertion and removal at both ends.

```java
public interface Deque<E> extends Queue<E> {
    // Insertion
    void addFirst(E e);
    void addLast(E e);
    boolean offerFirst(E e);
    boolean offerLast(E e);
    
    // Removal
    E removeFirst();
    E removeLast();
    E pollFirst();
    E pollLast();
    
    // Retrieval
    E getFirst();
    E getLast();
    E peekFirst();
    E peekLast();
    
    // Stack methods
    void push(E e);  // Adds an element to the front (equivalent to addFirst)
    E pop();  // Removes and returns the first element (equivalent to removeFirst)
}
```

### Map

An object that maps keys to values. A map cannot contain duplicate keys; each key can map to at most one value.

```java
public interface Map<K, V> {
    // Basic operations
    V put(K key, V value);
    V get(Object key);
    V remove(Object key);
    boolean containsKey(Object key);
    boolean containsValue(Object value);
    int size();
    boolean isEmpty();
    void clear();
    
    // Bulk operations
    void putAll(Map<? extends K, ? extends V> m);
    
    // Views
    Set<K> keySet();
    Collection<V> values();
    Set<Map.Entry<K, V>> entrySet();
    
    // Entry interface
    interface Entry<K, V> {
        K getKey();
        V getValue();
        V setValue(V value);
    }
}
```

## Common Implementations

### List Implementations

#### ArrayList

A resizable array implementation of the List interface. Provides fast random access but slower insertions and deletions.

```java
// Creating an ArrayList
List<String> names = new ArrayList<>();

// Adding elements
names.add("Alice");
names.add("Bob");
names.add("Charlie");

// Accessing elements
String first = names.get(0);  // "Alice"

// Iterating
for (String name : names) {
    System.out.println(name);
}

// Removing elements
names.remove("Bob");
names.remove(1);  // Removes element at index 1
```

#### LinkedList

A doubly-linked list implementation of the List and Deque interfaces. Provides fast insertions and deletions but slower random access.

```java
// Creating a LinkedList
LinkedList<String> names = new LinkedList<>();

// Adding elements
names.add("Alice");
names.addFirst("Bob");  // Adds at the beginning
names.addLast("Charlie");  // Adds at the end

// Using as a queue
names.offer("David");  // Adds to the end
String first = names.poll();  // Removes and returns the first element

// Using as a stack
names.push("Eve");  // Adds to the beginning
String top = names.pop();  // Removes and returns the first element
```

### Set Implementations

#### HashSet

A Set implementation backed by a hash table (actually a HashMap instance). Does not guarantee order of elements.

```java
// Creating a HashSet
Set<String> uniqueNames = new HashSet<>();

// Adding elements
uniqueNames.add("Alice");
uniqueNames.add("Bob");
uniqueNames.add("Alice");  // Duplicate, will not be added

// Checking if an element exists
boolean hasAlice = uniqueNames.contains("Alice");  // true

// Removing elements
uniqueNames.remove("Bob");

// Size and iteration
int size = uniqueNames.size();  // 1
for (String name : uniqueNames) {
    System.out.println(name);  // Prints "Alice"
}
```

#### LinkedHashSet

A HashSet implementation that maintains insertion order of elements.

```java
// Creating a LinkedHashSet
Set<String> orderedNames = new LinkedHashSet<>();

// Adding elements
orderedNames.add("Charlie");
orderedNames.add("Alice");
orderedNames.add("Bob");

// Iteration preserves insertion order
for (String name : orderedNames) {
    System.out.println(name);  // Prints "Charlie", "Alice", "Bob" in that order
}
```

#### TreeSet

A NavigableSet implementation based on a TreeMap. The elements are ordered using their natural ordering or a Comparator provided at set creation time.

```java
// Creating a TreeSet with natural ordering
TreeSet<String> sortedNames = new TreeSet<>();

// Adding elements
sortedNames.add("Charlie");
sortedNames.add("Alice");
sortedNames.add("Bob");

// Iteration in sorted order
for (String name : sortedNames) {
    System.out.println(name);  // Prints "Alice", "Bob", "Charlie" in alphabetical order
}

// NavigableSet operations
String first = sortedNames.first();  // "Alice"
String last = sortedNames.last();  // "Charlie"
String ceiling = sortedNames.ceiling("B");  // "Bob" (smallest element >= "B")
String floor = sortedNames.floor("B");  // "Alice" (largest element <= "B")

// Creating a TreeSet with custom ordering
TreeSet<Person> personsByAge = new TreeSet<>((p1, p2) -> p1.getAge() - p2.getAge());
personsByAge.add(new Person("Alice", 30));
personsByAge.add(new Person("Bob", 25));
personsByAge.add(new Person("Charlie", 35));

// Iteration in age order
for (Person person : personsByAge) {
    System.out.println(person.getName() + ": " + person.getAge());
}
```

### Map Implementations

#### HashMap

A Map implementation based on a hash table. Provides constant-time performance for basic operations (get and put), assuming the hash function disperses elements properly.

```java
// Creating a HashMap
Map<String, Integer> ages = new HashMap<>();

// Adding key-value pairs
ages.put("Alice", 30);
ages.put("Bob", 25);
ages.put("Charlie", 35);

// Accessing values
Integer aliceAge = ages.get("Alice");  // 30
Integer davidAge = ages.get("David");  // null (key not found)

// Checking if a key or value exists
boolean hasAlice = ages.containsKey("Alice");  // true
boolean hasAge40 = ages.containsValue(40);  // false

// Removing entries
ages.remove("Bob");

// Iterating over a Map
for (Map.Entry<String, Integer> entry : ages.entrySet()) {
    System.out.println(entry.getKey() + ": " + entry.getValue());
}

// Java 8+ forEach
ages.forEach((name, age) -> System.out.println(name + ": " + age));
```

#### LinkedHashMap

A HashMap implementation that maintains insertion order of entries.

```java
// Creating a LinkedHashMap
Map<String, Integer> orderedAges = new LinkedHashMap<>();

// Adding key-value pairs
orderedAges.put("Charlie", 35);
orderedAges.put("Alice", 30);
orderedAges.put("Bob", 25);

// Iteration preserves insertion order
for (Map.Entry<String, Integer> entry : orderedAges.entrySet()) {
    System.out.println(entry.getKey() + ": " + entry.getValue());
    // Prints "Charlie: 35", "Alice: 30", "Bob: 25" in that order
}
```

#### TreeMap

A NavigableMap implementation based on a red-black tree. The map is sorted according to the natural ordering of its keys, or by a Comparator provided at map creation time.

```java
// Creating a TreeMap with natural ordering
TreeMap<String, Integer> sortedAges = new TreeMap<>();

// Adding key-value pairs
sortedAges.put("Charlie", 35);
sortedAges.put("Alice", 30);
sortedAges.put("Bob", 25);

// Iteration in sorted key order
for (Map.Entry<String, Integer> entry : sortedAges.entrySet()) {
    System.out.println(entry.getKey() + ": " + entry.getValue());
    // Prints "Alice: 30", "Bob: 25", "Charlie: 35" in alphabetical order
}

// NavigableMap operations
Map.Entry<String, Integer> firstEntry = sortedAges.firstEntry();  // "Alice"=30
Map.Entry<String, Integer> lastEntry = sortedAges.lastEntry();  // "Charlie"=35
Map.Entry<String, Integer> ceilingEntry = sortedAges.ceilingEntry("B");  // "Bob"=25
Map.Entry<String, Integer> floorEntry = sortedAges.floorEntry("B");  // "Alice"=30

// Submap views
SortedMap<String, Integer> subMap = sortedAges.subMap("Alice", "Charlie");  // Includes "Alice", "Bob"
NavigableMap<String, Integer> headMap = sortedAges.headMap("Bob", true);  // Includes "Alice", "Bob"
NavigableMap<String, Integer> tailMap = sortedAges.tailMap("Bob", true);  // Includes "Bob", "Charlie"
```

### Queue and Deque Implementations

#### PriorityQueue

An unbounded priority queue based on a priority heap. The elements are ordered according to their natural ordering or by a Comparator provided at queue construction time.

```java
// Creating a PriorityQueue with natural ordering
PriorityQueue<Integer> numbers = new PriorityQueue<>();

// Adding elements
numbers.add(10);
numbers.add(5);
numbers.add(15);

// Retrieving elements (in priority order)
while (!numbers.isEmpty()) {
    System.out.println(numbers.poll());  // Prints 5, 10, 15
}

// Creating a PriorityQueue with custom ordering
PriorityQueue<Task> tasks = new PriorityQueue<>((t1, t2) -> t1.getPriority() - t2.getPriority());
tasks.add(new Task("Task 1", 3));
tasks.add(new Task("Task 2", 1));
tasks.add(new Task("Task 3", 2));

// Processing tasks in priority order
while (!tasks.isEmpty()) {
    Task task = tasks.poll();
    System.out.println(task.getName() + ": " + task.getPriority());
}
```

#### ArrayDeque

A resizable-array implementation of the Deque interface. It has no capacity restrictions and grows as necessary to support usage.

```java
// Creating an ArrayDeque
ArrayDeque<String> deque = new ArrayDeque<>();

// Using as a queue (FIFO)
deque.offer("First");
deque.offer("Second");
deque.offer("Third");

String first = deque.poll();  // "First"
String second = deque.poll();  // "Second"

// Using as a stack (LIFO)
ArrayDeque<String> stack = new ArrayDeque<>();
stack.push("First");
stack.push("Second");
stack.push("Third");

String top = stack.pop();  // "Third"
String nextTop = stack.pop();  // "Second"

// Using as a double-ended queue
ArrayDeque<String> doubleEnded = new ArrayDeque<>();
doubleEnded.addFirst("First");
doubleEnded.addLast("Last");
doubleEnded.addFirst("New First");

String newFirst = doubleEnded.removeFirst();  // "New First"
String last = doubleEnded.removeLast();  // "Last"
```

## Utility Classes

### Collections

The `java.util.Collections` class provides static methods for operating on collections.

```java
// Creating unmodifiable collections
List<String> originalList = new ArrayList<>(Arrays.asList("Alice", "Bob", "Charlie"));
List<String> unmodifiableList = Collections.unmodifiableList(originalList);

// Creating synchronized collections
List<String> synchronizedList = Collections.synchronizedList(new ArrayList<>());
Map<String, Integer> synchronizedMap = Collections.synchronizedMap(new HashMap<>());

// Creating empty collections
List<String> emptyList = Collections.emptyList();
Set<Integer> emptySet = Collections.emptySet();
Map<String, Integer> emptyMap = Collections.emptyMap();

// Creating singleton collections
List<String> singletonList = Collections.singletonList("Only Element");
Set<Integer> singletonSet = Collections.singleton(42);
Map<String, Integer> singletonMap = Collections.singletonMap("key", 42);

// Sorting
List<String> names = new ArrayList<>(Arrays.asList("Charlie", "Alice", "Bob"));
Collections.sort(names);  // Sorts using natural ordering
Collections.sort(names, Collections.reverseOrder());  // Sorts in reverse order

// Binary search (requires sorted list)
List<Integer> sortedNumbers = Arrays.asList(10, 20, 30, 40, 50);
int index = Collections.binarySearch(sortedNumbers, 30);  // 2

// Finding min/max
Integer min = Collections.min(Arrays.asList(5, 2, 9, 1, 7));  // 1
Integer max = Collections.max(Arrays.asList(5, 2, 9, 1, 7));  // 9

// Frequency and disjoint
int frequency = Collections.frequency(Arrays.asList(1, 2, 3, 1, 2, 1), 1);  // 3
boolean disjoint = Collections.disjoint(
    Arrays.asList(1, 2, 3),
    Arrays.asList(4, 5, 6)
);  // true (no common elements)
```