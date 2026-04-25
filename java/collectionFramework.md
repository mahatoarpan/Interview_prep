## List
### Contiguous memory locations are usually used for storing actual values in an array but not in ArrayList. Explain.

In Java, a primitive array, like int[], stores its elements in a single block of contiguous memory. For primitives, these are the actual values; for objects, these are references stored contiguously. This is why arrays have a fixed size — the JVM reserves that continuous block at allocation time.

An ArrayList, however, is a resizable data structure built on top of an internal Object[] array. That internal array stores object references in contiguous memory, but the actual objects those references point to can be located anywhere in the heap, not necessarily next to each other.

So, in an array of primitives, the actual values are contiguous in memory. In an ArrayList, only the references are contiguous; the underlying objects are scattered in the heap. This design allows ArrayList to be dynamic, but adds an extra level of indirection compared to arrays.

### How does the size of ArrayList grow dynamically? And state how it is implemented internally.

An ArrayList in Java is backed by an internal array called elementData. When we add elements and the internal array becomes full, the ArrayList automatically grows by creating a new, larger array and copying the existing elements into it.

The default growth strategy in the JDK is:

```
newCapacity = oldCapacity + (oldCapacity >> 1)
```

That is, it increases the size by 50% of the current capacity. For example, if the capacity is 10 and it’s full, it grows to 15.

Internally, this resizing happens in the ensureCapacity() method, which is triggered when adding elements via add(). The actual array copying is done using Arrays.copyOf() — which allocates a new array in memory and copies all references from the old array.

This dynamic resizing provides amortized O(1) time complexity for add(), but occasional resize operations are O(n) because of the copy.

Extra:
* Initial capacity is 10 by default (unless specified in the constructor).
* Removing elements does not shrink the internal array automatically — trimToSize() must be called manually.
* Because resizing requires copying, frequent expansions can be expensive, so it’s good to set an initial capacity if you know the expected size.

## Map
### What are the differences between HashMap and HashTable in Java?

1. Thread Safety
    * HashMap - Not synchronized (not thread-safe). If multiple threads access it simultaneously and at least one modifies it, external synchronization is needed.
    * HashTable - Synchronized (thread-safe). Only one thread can access it at a time.

2. Null Keys & Values
    * HashMap - Allows one null key and multiple null values.
    * HashTable - Does not allow null keys or null values.

3.  Performance
    * HashMap - Faster because it's not synchronized.
    * HashTable - Slower due to synchronized methods.

4. Iteration
    * HashMap - Uses Iterator (fail-fast -> thros `ConcurrentModificationException` if modified during iteration)
    * HashTable - Uses Enumeration (not fail-fast) and also supports Iterator in newer versions.

5. Inheritance
    * HashMap - Extends `AbstractMap<K,V>`
    * HashTable - Extends `Dictionary<K,V>`

6. Ordering
    * HashMap - Supports order of insertion by making use of its subclass `LinkedHashMap`
    * HashTable - Order of insertion is not guaranteed.

    
### How data is stored in HashMap.

A HashMap in Java stores data as key–value pairs and uses hashing for fast lookups.

STEP 1: Hashing the Key
* When you insert a key-value pair, the key is passed through a hash function (`hashCode()`).
*  This generates an integer hash code for the key.
* Example: "John".hashCode() → 12345678

STEP 2: Calculating the index
* The hash code is converted into an array index where the entry will be stored.
```
index = hashCode % arraySize
```
* Example: If arraySize = 16 and hashCode = 12345678, then index is (12345678 % 16).

STEP 3: Storing in the bucket
* Each position in the internal array is called a bucket.
* the  `(key, value)` pair is stored in the bucket at the calculated index.

STEP 4: Handling Collisions
* Two different keys may generate the same index (collision)
* HashMap handles this using:
    1. Linked List (Java 7 and earlier)
    2. Balanced Tree (Red-Black Tree in Java 8+) if a bucket has too many entries (threshold > 8)
* Example: If "John" and "Mike" both map to index 5, they are stored in a linked list/tree inside bucket 5.

STEP 5: Resizing (Rehashing)
* HashMap resizes when the number of entries exceeds (capacity * load factor)
* Default capacity = 16
* Default load factor = 0.75
* Resizing = double array size and recalculating indexes for all keys.

### What is the difference between map() and flatMap()? Give examples.


## Set
### What makes a HashSet different from a TreeSet?
A HashSet and a TreeSet in Java are both implementation of the Set interface, but they differ in ordering, performance, and underlying data structure.

1. Underlying Data Structure
    * HashSet - Uses a HashMap internally (hash table)
    * TreeSet - Uses a TreeMap internally (red-black tree)

2. Ordering
    * HashSet - Unordered; elements are stored based on hash code, so order may change over time.
    * TreeSet - Sorted in natural order (or by a custom Comparator).

3. Null Elements
    * HashSet - Allows one null element.
    * TreeSet - Does not alow null (throws `NullPointerException` if you try to add).

4. Performance:
    * HashSet - Add/Remove/Search takes O(1) average (hashing)
    * TreeSet - Add/Remove/Search takes O(log n) (tree traversal)


## Stream APIs

### What is the Java Stream API? Why was it introduced in Java 8?
The Stream API (introduced in Java 8) is a powerful abstraction for processing collections of data (like List, Set, Map, arrays, etc.) in a functional style.

It allows you to perform bulk operations such as filtering, mapping, reducing, grouping, and sorting in a declarative and readable way, instead of writing verbose loops. Think of a stream as a pipeline of data that flows through various operations.

Stream API was introduced to:
1. Simplify Collection Processing - Instead of writing nested loops, you can express operations in a clean and concise way.
```java
// Old way (Java 7 and before)
List<String> names = Arrays.asList("Alen", "John", "Alice", "Bob");
List<String> result = new ArrayList<>();
for (String name : names) {
    if (name.startsWith("A")) {
        result.add(name.toUpperCase());
    }
}
System.out.println(result);

// Java 8 Stream way
List<String> result2 = names.stream()
        .filter(name -> name.startsWith("A"))
        .map(String::toUpperCase)
        .toList();
System.out.println(result2);
```

2. Support functional programming - Streams use lambda expressions and method references.

3. Enable Parallel Processing (Easy Multi-threading) - With just one method call (`parallelStream()`), developers can utilize multi-core CPUs for faster processing.
```java
int sum = numbers.parallelStream()
                 .mapToInt(Integer::intValue)
                 .sum();

```

4. Lazy evaluation for performance - Stream operations are lazy. Intermediate operations (map, filter) are not execulted until a terminal operation (collect, forEach) is called. This helps optimize performance.

### Can a stream be reused? Why or why not?
No, a stream cannot be reused if a terminal operation has been executed. After you perform a terminal operation (like collect(), forEach(), reduce()), the stream is considered consumed and closed. If you try to use it again, Java will throw an `IllegalStateException`.

Streams cannot be reused because:
1. Streams represent a pipeline of data - Once data has flowed through the pipeline and reached a terminal operation, the pipeline is finished.
2. Design choice for immutability & safety - Streams are designed to be used in a functional style (no side-effects, no mutable state). Allowing reuse could cause unpredictable behavior, especially in parallel streams.
3. Efficiency - By making streams one-time-use, Java avoids keeping extra state information that would be required to “reset” them.

### How does filter() work in streams? Give an example.
It is used to select elements from the stream based on a condition (given as a `Predicate`). It returns a new stream containing only elements that satisfy the condition. It is an intermediate operation (executes only when a terminal operation like forEach() pr collect() is called).
```java
public class FilterExample {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(10, 15, 20, 25, 30);

        numbers.stream()
               .filter(n -> n % 2 == 0)   // keep only even numbers
               .forEach(System.out::println);
    }
}
```

### What is the difference between findFirst(), findAny() and collect() in streams?

1. `findFirst()`: Returns the first element of the stream (wrapped in an `Optional<T>`). It always gives the first element in ordered streams (like List or Stream.of()). In parallel streams, it still respects order.
```java
import java.util.*;

public class FindFirstExample {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("Alex", "John", "Alice", "Bob");

        Optional<String> first = names.stream()
                                      .filter(name -> name.startsWith("A"))
                                      .findFirst();

        System.out.println(first.orElse("No match found"));
    }
}
// output: Alex
```

2. `findAny()`: Returns any element from the stream (wrapped in `Optional<T>`). Ub sequential streams, it usually behaves like findFirst(). In parallel streams, it may return any element for performance reasons (non-deterministic).
```java
import java.util.*;

public class FindAnyExample {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("Alex", "John", "Alice", "Bob");

        Optional<String> any = names.parallelStream()
                                    .filter(name -> name.startsWith("A"))
                                    .findAny();

        System.out.println(any.orElse("No match found"));
    }
}
// Output: Alex or Alice - (depending on parallel execution)
```

3. `collect()`: Used to transform the elements of the stream into a different form, usually a collection like List, Set or Map.
```java
import java.util.*;
import java.util.stream.Collectors;

public class CollectExample {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("Alex", "John", "Alice", "Bob");

        List<String> filteredNames = names.stream()
                                          .filter(name -> name.length() > 3)
                                          .collect(Collectors.toList());

        System.out.println(filteredNames);
    }
}
// output: ["Alex", "John", "Alice"]
```

### How do you sort a list using Stream API?
The sorted() method of Stream is used for sorting. It can be used in two ways:

1. Natural Ordering (using Comparable) - If the elements implement Comparable, you can directly use sorted() method.

```java
public class NaturalSortExample {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("John", "Alex", "Bob", "Alice");

        List<String> sortedNames = names.stream()
                                        .sorted() // natural order (A → Z)
                                        .collect(Collectors.toList());

        System.out.println(sortedNames);
    }
}

```

2. Custom Ordering (using Comparator) - You can pass a Comparator to sorted()

```java
public class CustomSortExample {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("John", "Alex", "Bob", "Alice");

        List<String> sortedByLength = names.stream()
                                           .sorted(Comparator.comparingInt(String::length))
                                           .collect(Collectors.toList());

        System.out.println(sortedByLength);
    }
}
```

### What is reduce() in streams? Can you give an example of its use?
The reduc() method is used to reduce a stream of elements into a single value by repeatedly applying a binary operation. It's often used for sum, min, max, concatenation, multiplication, etc.

There are 3 variants of reduce() in Stream:
1. `Optional<T> reduce(BinaryOperator<T> accumulator)` - No identity value is provided. Returns an Optional, because the stream might be empty.
```java
public class ReduceType1 {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

        Optional<Integer> sum = numbers.stream()
                                       .reduce((a, b) -> a + b);

        System.out.println("Sum: " + sum.get());  // 15
    }
}
```
2. `T reduce(T identity, BinaryOperator<T> accumulator)` - An identity (initial value) is given. Always returns a result, never Optional
```java
public class ReduceType2 {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4);

        int product = numbers.stream()
                             .reduce(1, (a, b) -> a * b);

        System.out.println("Product: " + product);  // 24
    }
}
```
3. `<U> U reduce(U identity, BiFunction<U,? super T,U> accumulator, BinaryOperator<U> combiner)` - Used in parallel streams. Allows different result type(U) than the stream element type (T). `combiner` mergers partial results from parallel executions.
```java
public class ReduceType3 {
    public static void main(String[] args) {
        List<String> words = Arrays.asList("Java", "Stream", "API");

        int totalLength = words.parallelStream()
                               .reduce(0, 
                                       (len, word) -> len + word.length(), // accumulator
                                       (len1, len2) -> len1 + len2);      // combiner

        System.out.println("Total Length: " + totalLength);  // 13
    }
}
```


### Explain short-circuiting operations in streams. Can you give some examples?

Short-circuiting operations are intermediate or terminal operations that can produce a result without processing all elements of the stream. They help improve performance by avoiding unnecessary computation.

1. Intermediate Short-Circuiting Operations - These prevent the need to traverse the full stream.
    * limit(n) → restricts the stream to n elements.
    * skip(n) → skips the first n elements and processes the rest.

```java
public class ShortCircuitExample1 {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);

        numbers.stream()
               .limit(3) // only first 3 numbers
               .forEach(System.out::println);
    }
}
```

2. Terminal Short-Circuiting Operations - These don’t process the full stream if the answer is found early.
    * anyMatch() → returns true if any element matches, stops immediately.
    * allMatch() → returns false immediately if any element doesn’t match.
    * noneMatch() → returns false immediately if any element matches.
    * findFirst() → returns the first element found.
    * findAny() → returns any element (especially useful in parallel streams).

```java
public class ShortCircuitExample2 {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("Arpan", "Rahul", "Amit", "Anu");

        boolean result = names.stream()
                              .anyMatch(name -> name.startsWith("R"));

        System.out.println(result); // true (stops after "Rahul")
    }
}
```

### What is the role of Collectors.toMap()? How do you handle duplicate keys?
The Collectors.toMap() method is a collector that collects elements of a stream into a Map.
It requires:

1. Key Mapper Function → to extract keys.
2. Value Mapper Function → to extract values.
3. (Optional) Merge Function → to resolve duplicate keys.
4. (Optional) Map Supplier → to specify the map implementation (e.g., HashMap, TreeMap, LinkedHashMap).

```java
public class ToMapExample {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("Arpan", "Rahul", "Amit");

        Map<String, Integer> nameLengthMap = names.stream()
                .collect(Collectors.toMap(
                        name -> name,          // Key: name itself
                        name -> name.length()  // Value: length of name
                ));

        System.out.println(nameLengthMap);
    }
}
```

If two elements map to the same key, Collectors.toMap() will throw an IllegalStateException unless you provide a merge function.

```java
List<String> names = Arrays.asList("Arpan", "Rahul", "Amit", "Rahul");

Map<String, Integer> map = names.stream()
        .collect(Collectors.toMap(
                name -> name,
                name -> name.length(),
                (oldVal, newVal) -> oldVal // keep the first one - MERGE FUNCTION
        ));

System.out.println(map);

```

If you want a specific map type (like LinkedHashMap to preserve order):
```java
Map<String, Integer> map = names.stream()
        .collect(Collectors.toMap(
                name -> name,
                String::length,
                (a, b) -> a,
                LinkedHashMap::new
        ));

System.out.println(map.getClass()); // class java.util.LinkedHashMap

```

### What are parallel streams? How are they different from sequential streams?

A parallel stream is a stream that splits its elements into multiple parts, and processes them concurrently on multiple threads (using the ForkJoinPool framework under the hood). In contrast, a sequential stream processes elements one by one in a single thread.

Use when:
1. You have large data (e.g., millions of records).
2. The task is CPU-intensive (not IO-heavy).
3. Each operation is independent (no shared mutable state).

Avoid when:
1. Working with small datasets (thread overhead > benefits).
2. When order matters strictly.
3. When using IO-bound tasks (disk/network waits can block threads).

Note - Parallel streams are not always faster. They’re beneficial only when the dataset is large, the operations are CPU-bound, and thread-safety/order aren’t issues.

### What is lazy evaluation in streams? Can you explain with an example?
In Java Streams, lazy evaluation means that intermediate operations (like map(), filter(), distinct(), etc.) are not executed immediately when you call them. Instead, they are just stored as a pipeline of operations. The actual computation happens only when a terminal operation (like forEach(), collect(), reduce(), etc.) is invoked.

This design allows the stream to:
1. Avoid unnecessary work
2. Process elements efficiently (sometimes even stop early with short-circuiting)
3. Enable optimizations like fusion (combining multiple steps into one pass).
