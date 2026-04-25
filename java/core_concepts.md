## Introduction to Java

### What is Java?
Java is a high-level programming language. It is based on the principles of object-orient programming.

### Why is Java platform independent language?
Java was developed in such a way that it does not depend on any hardware or software since the compiler compiles the code and then converts it to platform-independent byte code which can be run on multiple systems.
The only condition to run that byte code is for the machine to have a runtime environment (JRE) installed in it.

Step 1: Compile .java files. It gets convereted into bytecode(.class files), which is not machine-specific.
    
    javac Myclass.java

Step 2: Bytecode is then executed by the JVM(Java Virtual Machine), not directly by the OS. Each platform has its own JVM implementation. The JVM takes the same .class bytecode and translates it into native machine instructions.

### Why is Java not a pure object-oriented language?
Java supports primitive data types - byte, boolean, char, short, int, float, long, and double and hence it is not a pure object oriented language.

### Difference between Heap and Stack Memory in java. And how java utilizes this.
Stack memory is the portion of memory allocated to each thread by the JVM. It has a fixed size, and is used for storing:
* Method calls
* Local variables (including primitives)
* Object references

Since it's limited and managed in a Last-In-First-Out (LIFO) manner, it's very fast but not suitable for dynamic memory needs.

Heap memory, on the other hand, is a larger memory area not pre-assigned to individual programs. It is shared across all threads and is used for:

* Creating Objects
* Class instances
* Arrays

Heap memory is allocated at runtime and managed by the Garbage Collector, which frees memory when objects are no longer referenced.

Utilization of stack and heap memory:
1. When you write and run a Java program, all method calls, local variables, and references to objects are stored in stack memory.
2. When you create an object using new, it is stored in the heap memory.
3. The reference to that object is stored in the stack, allowing your program to access the object in heap.

![stack_vs_heap](./files/images/java_programming/stack_vs_heap.png "stack_vs_heap")

### Pointers are used in C/ C++. Why does Java not make use of pointers?
Pointers are quite complicated and unsafe to use by beginner programmers. Java focuses on code simplicity, and the usage of pointers can make it challenging. Pointer utilization can also cause potential errors. Moreover, security is also compromised if pointers are used because the users can directly access memory with the help of pointers.

Thus, a certain level of abstraction is furnished by not including pointers in Java. Moreover, the usage of pointers can make the procedure of garbage collection quite slow and erroneous. Java makes use of references as these cannot be manipulated, unlike pointers.

### Can you tell the difference between equals() method and equality operator(==) in Java?
We are already aware of the (==) equals operator. That we have used this to compare the equality of the values. But when we talk about the terms of object-oriented programming, we deal with the values in the form of objects. And this object may contain multiple types of data. So using the (==) operator does not work in this case. So we need to go with the .equals() method.

Both [(==) and .equals()] primary functionalities are to compare the values, but the secondary functionality is different. (==) Operators compares the memory location. equals() method compares the values and returns the result accordingly.

In the cases where the equals method is not overridden in a class, then the class uses the default implementation of the equals method that is closest to the parent class.
Object class is considered as the parent class of all the java classes. The implementation of the equals method in the Object class uses the == operator to compare two objects. This default implementation can be overridden as per the business logic.


```java
// Example with Primitives
int a = 10;
int b = 10;

System.out.println(a == b);     // true — compares values
```
```java
// Example with Objects(String)
String s1 = new String("hello");
String s2 = new String("hello");

System.out.println(s1 == s2);         // false — different objects in memory
System.out.println(s1.equals(s2));    // true — same content
```
```java
// Example with Custom Class
class Person {
    String name;

    Person(String name) {
        this.name = name;
    }
}

Person p1 = new Person("Arpan");
Person p2 = new Person("Arpan");

System.out.println(p1 == p2);         // false — different objects
System.out.println(p1.equals(p2));    // false — still false unless we override equals()

```

For equals() method to work in custom class we have to override it in our custom class.

```java
// Example with Custom Class
class Person {
    String name;

    Person(String name) {
        this.name = name;
    }
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Person)) return false;
        Person person = (Person) o;
        return this.name.equals(person.name);
    }
}

Person p1 = new Person("Arpan");
Person p2 = new Person("Arpan");

System.out.println(p1 == p2);         // false — different objects
System.out.println(p1.equals(p2));    // true - same content
```

### Do final, finally and finalize keywords have the same function?
All three keywords have their own utility while programming.

`final`: If any restriction is required for classes, variables, or methods, the final keyword comes in handy. Inheritance of a final class or overriding of a final method is restricted using the final keyword. The variable value becomes fixed after incorporating the final keyword.

```java
final int x = 10;          // x cannot be reassigned
final class A {}           // Cannot be extended
final void method() {}     // Cannot be overridden
```

`finally`: Always executes after try-catch, whether an exception is thrown or not. Useful for resource cleanup like closing files, database conenctions, etc.

```java
try {
    int a = 10 / 0;
} catch (Exception e) {
    System.out.println("Exception caught");
} finally {
    System.out.println("This block always runs");
}
```

`finalize()`: This is a method of the Object class, called by the Garbage Collector before destroying an object - used to perform cleanup operations. Deprecated and not recommednded in modern Java(use AutoClosable or try-with-resources instead).

```java
@Override
protected void finalize() throws Throwable {
    System.out.println("Object is being garbage collected");
}
```

### Is it possible that the ‘finally’ block will not be executed? If yes then list the case.

Yes, although the finally block is almost always executed, there are a few rare cases where it will not execute. By default, finally block executes always - even if there is an exception or return statement. But it will not execute in the following special cases:

| # | Scenario                             | Description                                                                                                          |
| - | ------------------------------------ | -------------------------------------------------------------------------------------------------------------------- |
| 1 | `System.exit()` is called            | If the program exits explicitly using `System.exit()`, the JVM terminates immediately — skipping `finally`.          |
| 2 | Infinite loop or crash               | If the JVM crashes or the program goes into an infinite loop before reaching `finally`.                              |
| 3 | Power failure / OS crash             | If the system shuts down abruptly or the process is forcibly killed.                                                 |
| 4 | `kill -9` or force stop              | In native environments, if the Java process is killed forcefully (e.g., `kill -9` in Unix/Linux), finally won't run. |
| 5 | Error in `finally` before completion | If an exception occurs **inside** the `finally` block and is **not handled**, the block may terminate prematurely.   |

### What is a ClassLoader?
In Java, a ClassLoader is a part of the Java Runtime Environment (JRE) that dynamically loads classes into memory during runtime.

Java uses ClassLoaders to load .class files (compiled bytecode) when they are referenced for the first time in a program — not at the start of the application.

Java supports dynamic class loading — i.e., it doesn’t load all classes at once. Instead, it loads them on demand, saving memory and improving efficiency.



### What is the difference between the ‘throw’ and ‘throws’ keyword in java?

The ‘throw’ keyword is used to manually throw the exception to the calling method. And the ‘throws’ keyword is used in the function definition to inform the calling method that this method throws the exception. So, if you are calling, then you have to handle the exception.

### Java works as “pass by value” or “pass by reference” phenomenon?
Java always works as a “pass by value”. There is nothing called a “pass by reference” in Java. However, when the object is passed in any method, the address of the value is passed due to the nature of object handling in Java. When an object is passed, a copy of the reference is created by Java and that is passed to the method. The objects point to the same memory location. 

### How to not allow serialization of attributes of a class in Java?

In Java, if you want to prevent certain attributes of a class from being serialized, the simplest and most common way is to mark them as transient. `transient` tells Java’s serialization mechanism to skip this field when serializing the object. When the object is deserialized, the transient field will have its default value:
* null for objects
* 0 for numbers
* false for booleans

```java
import java.io.Serializable;

public class User implements Serializable {
    private String username;
    private transient String password; // Will not be serialized

    public User(String username, String password) {
        this.username = username;
        this.password = password;
    }

    // getters and setters
}

```

There are several reasons why you might want to mark a field as transient:
1. Security - If a field contains sensitive information, you should mark it as transient to prevent it from being stored in a file or transmitted over a network.
2. Performance - Serializing and deserializing large objects can be time-consuming. By marking fields as transient, you can reduce the amount of data that needs to be serialized and improve the performance of your program.
3. State Management - Sometimes, the state of an object can be reconstructed based on other information. In these cases, you can mark the fields that represent this state as transient and avoid the overhead of serializing and deserializing them.

### Is it mandatory for a catch block to be followed by a try block?
No, it is not necessary for a catch block to be present after a try block. - A try block should be followed either by a catch block or by a finally block. If the exceptions likelihood is more, then they should be declared using the throws clause of the method.

### What are the various access specifiers in Java?

In Java, access specifiers are the keywords which are used to define the access scope of the method, class, or a variable. In Java, there are four access specifiers given below.
* Public: The classes, methods, or variables which are defined as public, can be accessed by any class or method.
* Protected: Protected can be accessed by the class of the same package, or by the sub-class of this class, or within the same class.
* Default: Default are accessible within the package only. By default, all the classes, methods, and variables are of default scope.
* Private: The private class, methods, or variables defined as private can be accessed within the class only.


### What are wrapper classes?
Wrapper classes are classes that allow primitive types to be accessed as objects. In other words, we can say that wrapper classes are built-in java classes which allow the conversion of objects to primitives and primitives to objects. The process of converting primitives to objects is called autoboxing, and the process of converting objects to primitives is called unboxing.

Wrapper classes needed when working with Collections (like ArrayList, HashMap) because collections store only objects, not primitives.

### What is lambda expression in Java and How does a lambda expression relate to a functional interface?
A lambda expression in Java is an anonymous function that provides a concise way to write code. It can be used primarily to implement functional interfaces (interfaces with a single abstract method). The lambda expression provides the implementation of that method directly.

```java
@FunctionalInterface
interface MyFunctionalInterface {
    void display(String msg);
}

public class Test {
    public static void main(String[] args) {
        // Lambda implementing the display method
        MyFunctionalInterface obj = (msg) -> System.out.println("Message: " + msg);
        obj.display("Hello Lambda!");
    }
}

```

### What are functional interfaces?
A functional interface is an interface in Java that contains exactly one abstract method. Marked with the annotation `@FunctionalInterface` (not mandatory, but helps catch mistakes at compile time). They can have:
* Only one abstrat method (mandatory).
* Any number of default methods and static methods.
```java
@FunctionalInterface
interface MyFunctionalInterface {
    void sayHello(); // Single Abstract Method
}

```

### Can a functional interface extend/inherit another interface?

Yes, a functional interface can extend another interface. However, after inheritance, the resulting interface must still contain exactly one abstract method.
* If it inherits a single abstract method and does not add new ones → it remains a functional interface.
* If multiple abstract methods exist (either defined or inherited), it will no longer be a functional interface.

### A single try block and multiple catch blocks can co-exist in a Java Program. Explain.
In Java, you can use multiple catch blocks with a single try block to handle different types of exceptions separately. This is useful when the code inside the try block can throw multiple kinds of exceptions, and you want to handle each differently.

The JVM evaluates exceptions top to bottom. The first matching block is executed. Only one catch block runs per exception - others are skipped.

Specific exception should come prior to the general exception because only the first catch block satisfying the catch condition is executed.

### What is a singleton class in Java? And how to implement a singleton class?
A Singleton class in Java is a class that allows only one instance of itself to be created throughout the entire JVM lifecycle. It provides a global point of access to that single instance.

Key Features:
1. Only one object exists for the entire application.
2. Constructor is private (to prevent direct instantiation).
3. Has a static method to provide access to the single instance.
4. Often used in scenations like:
    * Configuration managers
    * Database connection pools
    * Loggin frameworks
5. Resource Sharing - avoids creating multiple costly objects
6. Centralized control - same object can be used across the app
7. Thread safety - ensures consistent data access.

Implementation:
1. Eager Initialization
    * Pros - Simple, thread-safe by default.
    * Cons - Instance is created even if it's never used.   
```java
class Singleton {
    // Step 1: Create a private static instance
    private static final Singleton instance = new Singleton();
    
    // Step 2: Private constructor to prevent instantiation
    private Singleton() {
        System.out.println("Singleton instance created");
    }
    
    // Step 3: Public method to provide access
    public static Singleton getInstance() {
        return instance;
    }
}

public class Main {
    public static void main(String[] args) {
        Singleton obj1 = Singleton.getInstance();
        Singleton obj2 = Singleton.getInstance();
        
        System.out.println(obj1 == obj2); // true
    }
}
```

2. Lazy Initialization
    * Pros - Instance created only when needded
    * Cons - Not safe in multi-threaded environments.
```java
class Singleton {
    private static Singleton instance;
    
    private Singleton() {}
    
    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}

```

3. Thread-Safe Singleton (Double-Checked Locking)
    * Pros - Lazy, thread-safe, and efficient.
    * Cons - Slightly more complex code
```java
class Singleton {
    private static volatile Singleton instance;
    
    private Singleton() {}
    
    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}

```