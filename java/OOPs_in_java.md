## OOPs in JAVA

### What do you mean by data encapsulation?
Data Encapsulation is one of the core principles of Object-Oriented Programming (OOP).
It means wrapping the data (variables) and code (methods) together as a single unit, and restricting direct access to some of the object's components.

    Encapsulation is the process of hiding internal details of an object and only exposing what is necessary, usually through public methods (getters/setters).

Java uses access modifiers and getter/setter methods to implement encapsulation.

Benifits of Encapsulation:
1. Data hiding - prevents outside access to internal state
2. Improved security - can control what is set or returned
3. Code flexibility - Can change internal implementation without affecting external code
4. Better maintainability - Encapsulated code is easier to debug and manage.

### Briefly explain the concept of constructor overloading
Constructor Overloading is the concept where a class can have multiple constructors with different parameter lists (different number or types of parameters). It allows you to create objects in different ways, depending on what information you have at the time of object creation.

```java
public class Person {
    String name;
    int age;

    // Constructor 1 – no arguments
    public Person() {
        this.name = "Unknown";
        this.age = 0;
    }

    // Constructor 2 – one argument
    public Person(String name) {
        this.name = name;
        this.age = 0;
    }

    // Constructor 3 – two arguments
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

### Define Copy constructor in java.
A Copy Constructor in Java is a special constructor used to create a new object by copying the data from an existing object of the same class.

```java
public class Person {
    String name;
    int age;

    // Regular constructor
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    // Copy constructor
    public Person(Person other) {
        this.name = other.name;
        this.age = other.age;
    }
}
```

Copy Constructor is used to clone or duplicate an  object safely. It ensures deep copying instead of using .clone().

`Note:` Java does not provide a default copy constructor. You must define it manually if needed.

### Can the main method be Overloaded?
Yes, It is possible to overload the main method. We can create as many overloaded main methods we want. However, JVM has a predefined calling method that JVM will only call the main method with the definition of – 

    Public static void main(String[] args)

Only `main(String[] args)` is recognized and called automatically by the JVM. All other overloaded versions must be called manually from within the main method or other methods.

### Comment on method overloading and overriding by citing relevant examples.

Method Overloading:

    Method Overloading means defining multiple methods with the same name in the same class, but with different parameter lists (type, number, or order of parameters).

```java
public class Calculator {

    // Method 1
    public int add(int a, int b) {
        return a + b;
    }

    // Method 2: Overloaded with different number of parameters
    public int add(int a, int b, int c) {
        return a + b + c;
    }

    // Method 3: Overloaded with different data types
    public double add(double a, double b) {
        return a + b;
    }
}

```

Method Overriding:

    Method Overriding means providing a new implementation of a method in a subclass that is already defined in the superclass.  

```java
class Animal {
    public void sound() {
        System.out.println("Animal makes a sound");
    }
}

class Dog extends Animal {
    @Override
    public void sound() {
        System.out.println("Dog barks");
    }
}

```

| Feature          | Method Overloading                                  | Method Overriding                            |
| ---------------- | --------------------------------------------------- | -------------------------------------------- |
| **Where?**       | Same class                                          | Between superclass and subclass              |
| **Signature**    | Must differ (parameters)                            | Must be same as superclass method            |
| **Return type**  | Can differ                                          | Must be same (or covariant)                  |
| **Access level** | No restrictions                                     | Cannot reduce visibility                     |
| **Polymorphism** | Compile-time (static)                               | Runtime (dynamic)                            |
| **Use case**     | To perform similar operations with different inputs | To modify behavior inherited from superclass |


### Explain the use of final keyword in variable, method and class.
In Java, the final keyword is a non-access modifier used to restrict the modification of variables, methods, and classes.

`final` Variable:
* When a variable is declared as final in Java, the value can’t be modified once it has been assigned.
* It essentially becomes a constant.
* If not initialized at the time of declaration, it must be initialized in the constructor.

```java
final int x = 10;
x = 20;   // ❌ Compilation error: cannot assign a value to final variable
```

`final` Method:
* A method declared as final cannot be overridden by any subclass.
* Used to prevent modification of core logic in subclass.
* Helpful in securing critical methods.
* Constructors cannot be final, because they are not inherited. Attempting to mark a constructor final will result in a compile-time error.

```java
class Parent {
    final void show() {
        System.out.println("Final method in Parent");
    }
}

class Child extends Parent {
    // void show() {}  // ❌ Error: cannot override final method
}

```

`final` Class:
* A class declared as final cannot be subclassed (inherited).
* Used to prevent the class from being extended and to preserve its implementation.
* A final class can still extend another class, but nothing can extend it.

```java
final class Vehicle {
    void run() {
        System.out.println("Running");
    }
}

// class Car extends Vehicle {}  // ❌ Error: cannot inherit from final class
```

### When can you use super keyword?
The super keyword is used in Java to refer to the immediate parent class of a subclass. It's especially useful when the subclass wants to:
1. Access parent class data members - When a subclass has a field with the same name as the parent class, super helps distinguish between them.
```java
class Parent {
    int x = 100;
}

class Child extends Parent {
    int x = 200;

    void display() {
        System.out.println("Child x: " + x);        // 200
        System.out.println("Parent x: " + super.x); // 100
    }
}

```
2. Invoke parent class methods - If the subclass overrides a method from the parent, you can still call the parent version using super.
```java
class Parent {
    void show() {
        System.out.println("Parent method");
    }
}

class Child extends Parent {
    void show() {
        super.show();  // Calls Parent's version
        System.out.println("Child method");
    }
}

```
3. Call parent constructor - You can use super() to invoke the parent class's constructor from the child class's constructor. It must be the first statement in the child class constructor.

```java
class Parent {
    Parent() {
        System.out.println("Parent default constructor");
    }

    Parent(String msg) {
        System.out.println("Parent parameterized constructor: " + msg);
    }
}

class Child extends Parent {
    // Calls Parent default constructor
    Child() {
        super();  // Optional, Java calls it implicitly if no super() is written
        System.out.println("Child default constructor");
    }

    // Calls Parent parameterized constructor
    Child(String msg) {
        super(msg);  // Must be the first statement
        System.out.println("Child parameterized constructor: " + msg);
    }
}


```

### Can the static methods be overridden?
No, static methods cannot be overridden in Java — they are bound at compile-time and belong to the class, not to an instance of the class. In Java, method overriding is based on runtime polymorphism, which works only with instance methods. Static methods are resolved using the reference type, not the actual object.

If a subclass defines a static method with the same signature as a static method in its superclass, it is called method hiding, not overriding.

### Difference between static methods, static variables, and static classes in java.

`static` Variable:
* Shared by all instances of a class.
* Memory is allocated once per class, not per object.

```java
class Student {
    static String school = "ABC School";  // Static variable
    String name;

    Student(String name) {
        this.name = name;
    }

    void show() {
        System.out.println(name + " goes to " + school);
    }
}

Student s1 = new Student("Ram");
Student s2 = new Student("Shyam");
s1.show();  // Ram goes to ABC School
s2.show();  // Shyam goes to ABC School

```

`static` Methods:
* Belongs to the class and not instances.
* Can be called without creating an object.
* Cannot access non-static members directy (must use object reference).
* Commonly used for utility or helper methods.

```java
class MathUtils {
    static int square(int x) {
        return x * x;
    }
}
int result = MathUtils.square(5);  // No object needed
System.out.println(result);  // 25
```

`static` Classes:
* Only nested classes (inner classes) can be declared static.
* a static nested class does not need a reference to the outer class.
* Can only access static members of the outer class.

```java
class Outer {
    static int outerValue = 10;

    static class Inner {
        void show() {
            System.out.println("Outer value is " + outerValue);
        }
    }
}
Outer.Inner obj = new Outer.Inner();
obj.show();  // Outer value is 10

```

### What are shallow copy and deep copy in java?
In Java, shallow copy and deep copy refer to two different ways of copying objects, especially those containing reference-type fields (like other objects or arrays).

Shallow Copy - A shallow copy copies the field values as-is. If the field is a primitive, it copies the value. If it's a reference, it copies the reference - meaning both objects will point to the same referenced object.

```java
class Address {
    String city;
    Address(String city) {
        this.city = city;
    }
}

class Person implements Cloneable {
    String name;
    Address address;

    Person(String name, Address address) {
        this.name = name;
        this.address = address;
    }

    public Object clone() throws CloneNotSupportedException {
        return super.clone(); // shallow copy
    }
}

public class Main {
    public static void main(String[] args) throws Exception {
        Address addr = new Address("Mumbai");
        Person p1 = new Person("Arpan", addr);
        Person p2 = (Person) p1.clone();

        p2.name = "Rohan";
        p2.address.city = "Delhi"; // affects p1 too!

        System.out.println(p1.address.city); // Output: Delhi
    }
}
```

Deep Copy - A deep copy cpoes everything - including recursively cloning referenced objects - so the copy is completely independent of the original.

```java
class Address implements Cloneable {
    String city;

    Address(String city) {
        this.city = city;
    }

    public Object clone() throws CloneNotSupportedException {
        return super.clone(); // primitive String, so shallow here is fine
    }
}

class Person implements Cloneable {
    String name;
    Address address;

    Person(String name, Address address) {
        this.name = name;
        this.address = address;
    }

    public Object clone() throws CloneNotSupportedException {
        Person cloned = (Person) super.clone();
        cloned.address = (Address) address.clone(); // deep copy
        return cloned;
    }
}

public class Main {
    public static void main(String[] args) throws Exception {
        Address addr = new Address("Mumbai");
        Person p1 = new Person("Arpan", addr);
        Person p2 = (Person) p1.clone();

        p2.address.city = "Delhi"; // does NOT affect p1

        System.out.println(p1.address.city); // Output: Mumbai
    }
}

```

### Using relevant properties highlight the differences between interfaces and abstract classes.


| **Property**                     | **Interface**                                                                | **Abstract Class**                                                          |
| -------------------------------- | ---------------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| **Methods (Java ≤ 7)**           | Only abstract methods (no body).                                             | Can have abstract and concrete methods.                                     |
| **Methods (Java 8+)**            | Can have `default` methods (with body) and `static` methods.                 | Can have both abstract and concrete methods.                                |
| **Fields**                       | By default: `public static final` (constants).                               | Can have instance variables (any access modifier).                          |
| **Constructors**                 | Not allowed.                                                                 | Allowed (for subclass initialization).                                      |
| **Multiple Inheritance**         | A class can **implement multiple interfaces**.                               | A class can **extend only one abstract class**.                             |
| **Access Modifiers for Methods** | All methods are **public** by default.                                       | Methods can be `public`, `protected`, or `private`.                         |
| **State (Instance Variables)**   | Cannot maintain state (no instance fields).                                  | Can maintain state (instance variables).                                    |
| **Implementation**               | A class **must** implement all abstract methods unless it’s abstract itself. | A subclass must implement all abstract methods unless it’s abstract itself. |
| **When to Use**                  | To define a contract (capabilities) without caring about implementation.     | To provide a base class with partial implementation + shared state.         |


```java
// Interface example
interface Vehicle {
    int MAX_SPEED = 120; // public static final by default
    void start();        // public abstract by default
}

// Abstract class example
abstract class Car {
    String model; // Instance variable
    abstract void drive(); // Abstract method
    void stop() {          // Concrete method
        System.out.println("Car stopped");
    }
}
```

### What is a Comparator in java?
A Comparator in Java is an interface from the java.util package that is used to define a custom sorting order for objects.

Unlike the Comparable interface (which defines the natural ordering inside the class itself), Comparator is typically implemented outside the class so you can have multiple different sorting strategies for the same type of object.

`int compare(T o1, T o2);`

It is a functional interface that returns:
* Negative -> if `o1` should come before `o2`
* Zero -> if `o1` and `o2` are equal in ordering
* Positive -> if `o1` should come after `o2`

Example:
```java
import java.util.*;

class Student {
    String name;
    int age;

    Student(String name, int age) {
        this.name = name;
        this.age = age;
    }
}

// Comparator for sorting by name
class NameComparator implements Comparator<Student> {
    public int compare(Student s1, Student s2) {
        return s1.name.compareTo(s2.name);
    }
}

// Comparator for sorting by age
class AgeComparator implements Comparator<Student> {
    public int compare(Student s1, Student s2) {
        return s1.age - s2.age;
    }
}

public class Main {
    public static void main(String[] args) {
        List<Student> list = Arrays.asList(
            new Student("Ravi", 23),
            new Student("Amit", 21),
            new Student("Vikram", 25)
        );

        Collections.sort(list, new NameComparator());
        System.out.println("Sorted by Name:");
        list.forEach(s -> System.out.println(s.name + " - " + s.age));

        Collections.sort(list, new AgeComparator());
        System.out.println("\nSorted by Age:");
        list.forEach(s -> System.out.println(s.name + " - " + s.age));
    }
}

```

### What are the differences between constructor and method of a class in Java?
Constructor
* Constructor is used for initializing the object state.
* Constructor has no return type.
* Constructor gets invoked implicitly.
* If the constructor is not defined, then a default constructor is provided by the java compiler.
* The constructor’s name should be equal to the class name.
* A constructor cannot be marked as final because whenever a class is inherited, the constructors are not inherited. Hence, making it final doesn’t make sense. Java throws compilation error saying – modifier final not allowed here.
* Final variable instantiations are possible inside a constructor and the scope of this applies to the whole class and its objects.

Method
* Method is used for exposing the object’s behaviour.
* Method should have a return type. Even if it does not return anything, return type is void.
* Method must be invoked on the object explicitly.
* If a method is not defined, then the compiler does not provide it.
* The name of the method can have any name or have a class name too.
* A method can be defined as final, but it cannot be overridden in its subclass.
* A final variable if initialised inside a method ensures that the variable cant be changed only within the scope of the method.

### What is the ‘IS-A’ relationship in OOPs Java?
‘IS-A’ relationship is another name for inheritance. When we inherit the base class from the derived class, then it forms a relationship between the classes. So that relationship is termed as ‘IS-A’ relationship.

### Can we call a constructor of a class inside another constructor?
Yes, in Java, you can call one constructor from another inside the same class using the special keyword `this(...)`.

Rules:
1. Only one `this()` call per constructor.
2. Must be the first statement in the constructor.
3. You can use it for code reuse and to avoid duplication.
4. You cna also call the parent class constructor using` super()` - but not both `this()` and `super()` in the same constructor, because both must be the first statement.

```java
public class Person {
    private String name;
    private int age;

    // Constructor 1
    public Person(String name) {
        this(name, 0); // Calls Constructor 2
    }

    // Constructor 2
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
}

```

### Although inheritance is a popular OOPs concept, it is less advantageous than composition. Explain.

Inheritance and Composition are both ways to achieve code reusability in Object-Oriented Programming (OOP). However, composition is generally preferred over inheritance in real-world design because inheritance introduces certain limitations.

Why inheritance is less advantageous than composition:
1. Tight Coupling:
    * Inheritance creates a strong parent–child relationship.
    * If the parent class changes, it may unintentionally break child classes.
    * Composition allows replacing or changing components without affecting other classes.

```java
// Inheritance
class Bird {
    void fly() { System.out.println("Flying..."); }
}
class Penguin extends Bird {  // logically wrong
    // Penguins cannot fly, but they inherit fly()
}
// Problem: Penguin is tightly coupled to Bird. If Bird has fly(), Penguin also gets it, even though it makes no sense.

// Composition
interface Movement {
    void move();
}
class FlyMovement implements Movement {
    public void move() { System.out.println("Flying..."); }
}
class WalkMovement implements Movement {
    public void move() { System.out.println("Walking..."); }
}
class Penguin {
    private Movement movement;

    Penguin(Movement movement) {
        this.movement = movement;
    }
    void performMove() {
        movement.move();
    }
}
// Now, Penguin can use WalkMovement, while other birds can use FlyMovement.
```

2. Lack of Flexibility:
    * A subclass can inherit only from one superclass.
    * Composition allows replacing or changing components without affecting other classes.

```java
// Inheritance
class Phone {
    void call() { System.out.println("Calling..."); }
}
class CameraPhone extends Phone {
    void takePhoto() { System.out.println("Taking photo..."); }
}
// Problem: What if you want a MusicPhone that also has a camera? You can’t extend two classes in Java.

// Composition
class Camera {
    void takePhoto() { System.out.println("Taking photo..."); }
}
class MusicPlayer {
    void playMusic() { System.out.println("Playing music..."); }
}
class SmartPhone {
    private Camera camera = new Camera();
    private MusicPlayer musicPlayer = new MusicPlayer();

    void useCamera() { camera.takePhoto(); }
    void useMusic() { musicPlayer.playMusic(); }
}
// SmartPhone flexibly uses both Camera and MusicPlayer.
```

3. Fragile Base Class Problem:
    * If you modify the base class, all subclasses are forced to adapt - even if they don't need the new behaviour.
    * Composition avoids this issue because you can plug in only the required parts.

```java
// Inheritance
class Account {
    void withdraw(double amount) {
        System.out.println("Withdrawing " + amount);
    }
}
class SavingsAccount extends Account {
    // Inherits withdraw()
}
// Later, when you modify the parent, all the child classes are affected even if the rule doesn't apply to them.

// Composition
class WithdrawPolicy {
    void withdraw(double amount) {
        System.out.println("Withdrawing " + amount);
    }
}
class SavingsAccount {
    private WithdrawPolicy policy = new WithdrawPolicy();

    void withdraw(double amount) {
        policy.withdraw(amount);
    }
}
// Only classes that use this WithdrawPolicy are affected by changes.
```

4. Encapsulation is broken:
    * In inheritance, the child has access to protected members of the parent. This can expose internal implementation details.
    * In compostion, ibjets interact only through public interfaces, preserving encapsulation.

```java
// Inheritance
class Engine {
    protected int rpm;  // exposed

    void start() { rpm = 1000; }
}
class SportsCar extends Engine {
    void boost() { rpm = 8000; } // directly modifies rpm
}
// SportsCar can directly change rpm, breaking encapsulation.

// Composition
class Engine {
    private int rpm;  // encapsulated

    void start() { rpm = 1000; }
    void boost() { rpm = 8000; }
}
class SportsCar {
    private Engine engine = new Engine();

    void startCar() { engine.start(); }
    void boostCar() { engine.boost(); }
}
// SportsCar cannot mess with rpm directly. Encapsulation is preserved.
```

5. Code Reuse is safer with composition:
    * With inheritance, you may inherit unwanted behavior.
    * With composition, you reuse only the functionality you need by delegating work to composed objects.

```java
// Inheritance
class Printer {
    void printDocument() { System.out.println("Printing..."); }
    void faxDocument() { System.out.println("Faxing..."); }
}
class InkjetPrinter extends Printer {
    // inherits faxDocument() even though inkjet can’t fax
}

// Composition
class Printer {
    void printDocument() { System.out.println("Printing..."); }
}
class Fax {
    void faxDocument() { System.out.println("Faxing..."); }
}
class LaserPrinter {
    private Printer printer = new Printer();
    private Fax fax = new Fax();

    void print() { printer.printDocument(); }
    void fax() { fax.faxDocument(); }
}
// Only classes that need fax capability get it. No unnecessary methods.
```

### What are Composition and Aggregation? State the difference.

Composition is a "has-a" relationship where the child object cannot exist without the parent object. It represents strong ownership. If the parent is destroyed, the child is also destroyed.

```java
class Engine {
    void start() { System.out.println("Engine starts..."); }
}

class Car {
    private Engine engine; // Composition

    Car() {
        engine = new Engine();  // Car owns the Engine
    }

    void drive() {
        engine.start();
        System.out.println("Car is driving...");
    }
}

```
Here, Car and Engine → If Car is destroyed, its Engine also goes away.

Aggregation is also a "has-a" relationship but the child object can exist independently of the parent. It represents a weaker association. If the parent is destroyed, the child can still live.

```java
class Student {
    private String name;

    Student(String name) {
        this.name = name;
    }

    String getName() { return name; }
}

class School {
    private List<Student> students; // Aggregation

    School(List<Student> students) {
        this.students = students;  // School uses students, but doesn’t own them
    }

    void showStudents() {
        for(Student s : students) {
            System.out.println(s.getName());
        }
    }
}
```
School and Student → Students can exist even if the School is gone.

Composition and Aggregation are both “has-a” relationships in OOP. Composition is a strong association where the child object’s lifecycle is dependent on the parent (e.g., Car–Engine). Aggregation is a weaker association where the child object can exist independently of the parent (e.g., School–Student).

### What is this keyword in java?
The `this` keyword is a reference variable that refers to the current object. There are the various uses of `this` keyword in Java. It can be used to refer to current class properties such as instance methods, variable, constructors, etc. It can also be passed as an argument into the methods or constructors. It can also be returned from the method as the current class instance.

There are the following uses of this keyword.
* this can be used to refer to the current class instance variable.
* this can be used to invoke current class method (implicitly)
* this() can be used to invoke the current class constructor.
* this can be passed as an argument in the method call.
* this can be passed as an argument in the constructor call.
* this can be used to return the current class instance from the method.

### What is the Inheritance?

Inheritance is a mechanism by which one object acquires all the properties and behaviour of another object of another class. It is used for Code Reusability and Method Overriding. The idea behind inheritance in Java is that you can create new classes that are built upon existing classes. When you inherit from an existing class, you can reuse methods and fields of the parent class. Moreover, you can add new methods and fields in your current class also. Inheritance represents the IS-A relationship which is also known as a parent-child relationship.

There are five types of inheritance in Java:
1. Single Inheritance - A class inherits from one parent class.
```java
class Animal {
    void eat() { System.out.println("Eating..."); }
}
class Dog extends Animal {
    void bark() { System.out.println("Barking..."); }
}
```

2. Multilevel Inheritance - A class is derived from another derived class. 
```java
class Animal {
    void eat() { System.out.println("Eating..."); }
}
class Dog extends Animal {
    void bark() { System.out.println("Barking..."); }
}
class BabyDog extends Dog {
    void weep() { System.out.println("Weeping..."); }
}
```

3. Multiple Inheritance (Not Supported with Classes in Java) - A class inherits from more than one parent. Java does not support multiple inheritance with classes (to avoid diamond problem). But Java supports it using interfaces.
```java
interface CanRun {
    void run();
}
interface CanBark {
    void bark();
}
class Dog implements CanRun, CanBark {
    public void run() { System.out.println("Running..."); }
    public void bark() { System.out.println("Barking..."); }
}
```

4. Hierarchical Inheritance - Multiple child classes inherit from the same parent class.
```java
class Animal {
    void eat() { System.out.println("Eating..."); }
}
class Dog extends Animal {
    void bark() { System.out.println("Barking..."); }
}
class Cat extends Animal {
    void meow() { System.out.println("Meowing..."); }
}
```
