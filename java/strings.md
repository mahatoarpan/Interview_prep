### How would you differentiate between a String, StringBuffer, and a StringBuilder?

| Feature           | **String**                                                         | **StringBuffer**                                  | **StringBuilder**                                     |
| ----------------- | ------------------------------------------------------------------ | ------------------------------------------------- | ----------------------------------------------------- |
| **Mutability**    | **Immutable** — once created, cannot be changed.                   | **Mutable** — can be modified after creation.     | **Mutable** — can be modified after creation.         |
| **Thread Safety** | Thread-safe (immutable by nature).                                 | **Thread-safe** — methods are synchronized.       | **Not thread-safe** — no synchronization.             |
| **Performance**   | Slower for frequent modifications (creates new objects each time). | Slower than StringBuilder due to synchronization. | Faster than StringBuffer because it’s unsynchronized. |
| **Package**       | `java.lang.String`                                                 | `java.lang.StringBuffer`                          | `java.lang.StringBuilder`                             |


### What are the reasons behind making strings immutable in Java?

1. Security - Strongs are widely used in security-sensitive operations, such as:
    * File Paths
    * Network connections(hostname, URLs)
    * Class Loading (via Class.forName("...))

    If strings were mutable, a malicoious piece of code could change the value after it's been validated, which would cause security breaches.

2. String Pooling (Performance & Memory Efficiency) - Java uses a String Pool in the heap to store unique string literals. If strings were mutable, changing "Hello" in one place could unintentionally change it everywhere in the pool. Immutability allows safe sharing of strings across the application without copies.

3. Thread Safety - Since Stirng objects can't be modified after creation, they are authomatically thread-safe. Multiple threads can share the same string without synchronization.

4. Caching of HashCode - String is often used as a key in hash-based collection(HashMap, HashSet). Its hashCode is cached after being computed the first time. If strings were mutable, the hash code could change after insertion, breaking the integrity of hash collections.

### What is String Pool?
The String Pool is a special memory region inside the heap where Java stores String literals. When you create a String using literals, Java checks the pool first. If the string already exists in the pool, the same reference is returned (no new object is created). If it does not exist, a new string is created and placed in the pool. This helps in memory optimization and improves performance since strings are immutable.

```java
public class Test {
    public static void main(String[] args) {
        String s1 = "Java";
        String s2 = "Java";
        String s3 = new String("Java"); // creates a separate object in heap memory (outside the pool).

        System.out.println(s1 == s2); // true (both point to the same object in pool)
        System.out.println(s1 == s3); // false (different objects)
    }
}
```

Using `intern()` method, you can force a string into the pool.
```java
public class Test {
    public static void main(String[] args) {
        String s1 = new String("Hello");
        String s2 = s1.intern(); // puts "Hello" in pool
        String s3 = "Hello";

        System.out.println(s2 == s3); // true (both point to pool object)
    }
}

```
