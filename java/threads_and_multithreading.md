### What are the different ways of thread usage?
We can define and implement a thread in java using two ways:

1. Extending the Thread class
```java
public class ThreadExample extends Thread {
    public void run() {
        System.out.println("Thread runs...");
    }

    public static void main(String[] args) {
        ThreadExample example = new ThreadExample();
        example.start();
    }
} 
```

2. Implementing the Runnable interface
```java
public class ThreadExample implements Runnable {

    public void run() {
        System.out.println("Thread runs...");
    }

    public static void main(String[] args) {
        ThreadExample example = new ThreadExample();
        example.start();
    }
}
```

Implementing a thread using the method Runnable interface is more preferred and advantageous as Java does not have support for multiple inheritance of classes.

Start() method isused for creating a seperate class stack foor the thread execution. Once the call stack is created, JVM calls the run() method for executing the thread in that call stack.

### What are the different types of Thread Priorities in Java? And what is the default priority of a thread assigned by JVM?
There are a total of 3 different types of priority available in Java. 
* MIN_PRIORITY: It has an integer value assigned with 1.
* MAX_PRIORITY: It has an integer value assigned with 10.
* NORM_PRIORITY: It has an integer value assigned with 5.

In Java, Thread with MAX_PRIORITY gets the first chance to execute. But the default priority for any thread is NORM_PRIORITY assigned by JVM. 

### When we extend a class with Thread class we have to override the run method to work with multithreading. But during execution we call start method. Why?

When we call `start()`, HVM does two things:
1. Creates a new thread (at OS level)
2. Then calls `run()` on that new thread

When we call `run()` directly, it's just a normal method call - no new thread is created. It runs on the same thread that called it.

### What is the use of `Thread.sleep()`?

Thread.sleep() pauses the current thread for a specified amount of time.

Thread calls sleep --> Thread state changes (RUNNING to TIMED_WAITING) --> Thread is removed from CPU scheduling queue --> OS sets a timer --> timer expires --> Thread moves to RUNNABLE (ready but not yet running) --> CPU scheduler picks it up --> RUNNING again

Critical things to know :
1. InterrruptedException: sleep() always forces us to handle InterruptedExcetion. Because while a thread is sleeping, another thread can interrupt it by calling `.interrupt()` method of the thread. On itrerrput thread will wake up early.

2. Sleep does NOT release locks: When a thread is sleeping inside a `synchronized` block, it keeps holding the lock. No other thread can acquire that lock until the sleep is over. If many threads are waiting on a lock held by a sleeping thread, whole program slow down. Better to not put a sleep statement in a synchronized block of code.

3. Sleep time is NOT guaranteed exact: `Thread.sleep(1000)` means sleep for atleast 1000ms - it could be a little longer. The OS scheduler decides when exactly to wake it up. When sleep ends, the thread moves to `RUNNABLE` state. But the CPU may be busy with other threads at that exact moment. So thread waits a tiny bit longer before actually getting CPU time.

### What are the two types of threads in Java?

1. User Threads (foreground threads): These threads are normal threads we create by default. The JVM keeps running as long as even one user thread is alive.
```java
Thread t1 = new Thread(() -> {
    for(int i = 0; i < 5; i++) {
        System.out.println("User thread running: " + i);
        try {
            Thread.sleep(1000);
        } catch(InterruptedException e) {
        }
    }
});

t1.start(); // this is a USER thread bby default

```

2. Daemon Threads (background threads): These are background service threads. The JVM does not wait for them to finish. The moment all user threads finish, JVM exits - killing all deamon threads instantly.

```java
Thread t1 = new Thread(() -> {
    while(true)  {
        System.out.println("Deamon thread running");
        try {
            Thread.sleep(1000);
        } catch(InterruptedException e) {
        }
    }
});

t1.setDeamon(true); // must set BEFORE start()
t1.start(); 

```

Critical things about Deamon threads:

1. Must set Before calling start()
```java
    Thread t = new Thread(() -> {});

    t.setDeamon(true);
    t.start();
```

2. Deamon threads spawn Deamon threads: A thread inherits the deamon status of its parent thread.
```java
Thread parent = new Thread(() -> {

    // this child inherits deamon status from parent!
    Thread child = new Thread(() -> {
        System.out.println("Child is daemon: " + Thread.currentThread().isDeamon()); // returns true
    })

    child.start(); // child is ALSO a daemon thread!
});

parent.setDaemon(true);
parent.start();
```

3. No guaranteed cleanup: Since deamon threads are killed abruptly, they should never do tasks need proper cleanup.
```java
// DANGEROUS use of daemon thread
Thread t = new Thread(() -> {
    try {
        // writing to database
        // saving to file
        // network call
    } finally {
        // finally block may not execute!
        // JVM can kill it before reaching here
    }
});

t.setDaemon(true);
t.start();
```

4. Main thread is always a User thread: The main thread is always a user thread. You cannot change it.
```java
public static void main(String[] args) {
    System.out.println(Thread.currentThread().isDaemon()); // always false
}
```

Real examples of daemon threads:

1. JVM internal daemon threads:
    - Garbage Collector: cleans memory in background
    - JIT Compiler: optimizes bytecode in background
    - Finalizer thread: calls finalize() on objects
    - Signal dispatcher: handles OS signals

2. Application daemon threads:
    - Auto-save feature: saves document every x minutes
    - Log monitoring: watches log files
    - Cache cleanup: removes exprired cache entires

### Can you explain the Java thread lifecycle?

In Java, a thread goes through different states from creation to termination. The lifecycle is managed by the JVM and the Thread Scheduler.

Thread Lifeclye:
```
NEW --> RUNNABLE --> RUNNING --> WAITING/TIMED_WAITING/BLOCKED --> RUNNABLE --> TERMINATED
```

1. NEW (Created State):
    * A thread is created using the Thread class but not yet started.
    * It stays here until  `start()` is called.

```java
Thread t = new Thread(() -> System.out.println("Thread running..."));
// Thread is in NEW state here
```

2. RUNNABLE
    * After calling `start()`, the thread enters the RUNNABLE pool.
    * It's ready to run, but the CPU decides when to actually execute it.

```java
t.start();  // Thread moves from NEW → RUNNABLE
```

3. RUNNING
    * When the thread scheduler picks the thread from RUNNABLE, it goes into RUNNING state.
    * At any point, only one thread per CPU core can be RUNNING.

```java
public void run() {
    System.out.println("Thread is running...");
}
```

4. BLOCKED
    * A thread is in BLOCKED state if it tries to access a resource that another thread has locked.
    * It waits until the resource becomes available.

```java
synchronized(lock) {
    // If another thread already holds lock, this thread is BLOCKED
}
```

5. WAITING / TIMED_WAITING
    * WAITING - Thread waits indefinitely until another thread notifies it using `notify()` / `notifyAll()`.
    * TIMED_WAITING - Thread waits for a specified time.

```java
// WAITING
synchronized(lock) {
    lock.wait(); // Thread goes into WAITING until notified
}

// TIMED_WAITING
Thread.sleep(2000); // Thread waits for 2 seconds
```

6. TERMINATED (Dead State)
    * Once the `run()` method finishes execution, the thread enters the TERMINATED state.
    * A terminated thread cannot be restarted.

```java
System.out.println("Thread finished.");
// Thread is TERMINATED now
```


### Why is synchronization necessary? 

1. Concurrency Issues: When multiple threads execute simultaneously and share resources (like variables, files, databases), they may interfere with each other. This leads to problems such as:
    * Race Condition (two threads modifying the same variable at the same time)
    * Data inconsistency (wrong results due to interleaved operations)
    * Unexpected behavior (one thread reading half-updated data)

2. Synchronization ensures:
    * Mutual Exclusion - Only one thread accesses a critical section at a time.
    * Consistency - Shared resources remain in a valid state.
    * Thread Safety - Multiple threads can run safely without corrupting data.

```java
// Example Without Synchronization (Problem)
class Counter {
    private int count = 0;

    public void increment() {
        count++; // Not thread-safe
    }
    public int getCount() {
        return count;
    }
}

public class Test {
    public static void main(String[] args) throws InterruptedException {
        Counter counter = new Counter();

        Thread t1 = new Thread(() -> {
            for(int i=0; i<1000; i++) counter.increment();
        });

        Thread t2 = new Thread(() -> {
            for(int i=0; i<1000; i++) counter.increment();
        });

        t1.start();
        t2.start();
        t1.join();
        t2.join();

        System.out.println("Final count: " + counter.getCount());
    }
}
// Expected result: 2000
// Actual result: Less than 2000 (because both threads update count at the same time).
```

```java
// Example With Synchronization (Solution)
class Counter {
    private int count = 0;

    public synchronized void increment() { // synchronized method
        count++;
    }

    public int getCount() {
        return count;
    }
}
// Now, only one thread can access increment() at a time, ensuring correctness.
```

Synchronization is necessary in multithreading to avoid race conditions and ensure data consistency when multiple threads access shared resources. It guarantees that only one thread executes a critical section at a time, making the program thread-safe.

Every java object has a hidden monitor lock (also called intrinsic lock or mutex) built into it. `synchronized` keyword uses this lock. The lock is per Object and not per method - if there are two synchronized methods in an object, same lock applied to both. If t1 is inside methodA() and t2 tries to enter methodB(), t2 is blocked. If there is a static synchorized method then it is Class lock - completely different from the object lock.

There are two ways to use `synchronized`:
1. Synchronized Method: locks entire method, even code that doesn't need protection. 
2. Synchronized Block: locks only what needs protection, rest of the method runs freely.

### Intrinsic Lock vs Extrinsic lock

Intrinsic Lock (Built-in / Monitor Lock): Every java object has a built-in lock called an intrinsic lock. It is automatically used when we use the `synchronized` keyword.

Key points:
1. Lock is tied to the object itself
2. We don't create it - it's always there
3. Only one thread can hold it at a time
4. Simple to use but limited control

Extrinsic Lock (External Lock): An extrinsic lock is a lock object we create explicitly using `java.util.concurrent.locks.ReentrantLock`. We manage it.

```java
public class Counter {
    private int count = 0;
    private ReentrantLock lock = new ReentrantLock(); 

    public void increment {
        lock.lock() // manually aquire lock
        try {
            count++;
        } finally {
            lock.unlock(); // Always unlock in finally block!
        }
    }
}
```

**Rule of thumb: Start with `syncrhonized`. Move to `ReentrantLock` only when you need its extra features.**


### What is ReentrantLock in Java?

Reentrant means - a thread that already holds the lock can aquire it again without blocking itself. Every lock() must have a matching unlock() otherwise lock is never released.

Extra features provided by ReentrantLock:

1. tryLock(): Don't wait forever

```java
if(lock.tryLock()){} // returns true/false immediately

if(lock.tryLock(1, TimeUnits.SECONDS)){} // wait  max 1 second
```

2. lockInterruptibly(): cancel a waiting thread
```java
try {
    lock.lockInterruptibly(); // waiting thread can be interrupted
} catch(InterruptedException e) {
    // thread was interrupted while waiting - handle gracefully
}
```

3. Fair Lock: Threads get lock in order they asked
```java
// Default - no guarantee which thread gets lock next
Lock lock = new ReentrantLock();

// Fair - longest waiting thread gets lock first
Lock lock = new ReentrantLock(true);
```


### What is ReentrantReadWriteLock? What is the use of it?

With regular ReentrantLock, only one thread can access at a time -  even if all threads just want to read.

Solution is to use ReentrantReadWriteLock. It has two locks inside.

```java
ReentrantReadWriteLock lock = new ReentrantReadWriteLock();
ReentrantReadWriteLock.ReadLock readLock = lock.readLock();
ReentrantReadWriteLock.WriteLock writeLock = lock.writeLock();
```

```
// Multiple readers - run simultaneously
Thread-1 reading... 
Thread-2 reading...
Thread-3 reading...

// Writer comes in - all readers must finish first, then writer goes alone
Thread-4 writing... (all ithers blocked)

// After writier fone - readers can go again simultaneously
Thread-1 reading...
Thread-2 reading...
```

**Rule of thumb: If app reads far more than it writes - `ReentrantReadWriteLock` gives you better performance by allowing concurrent reads!**

### Why do we need wait(), nofify() and notifyAll() methods. What is the use of these?

Sometimes threads need to cooperate with each other - one thread waits for another to finish something before proceeding.

```
Consumer thread should WAIT if box is empty
Producer should NOTIFY consumer when item is added
```

All three methods come from `Object` class. They only wokr inside `synchornized` block - otherwise we get `IllegalMonitorStateException`.

```java
synchronized(obj) {
    obj.wait();
    obj.notify();
    obj.notifyAll()
}
obj.wait(); // IllegalMonitorStateException
```

1. `wait()` - thread releases lock and waits
```java
synchronized(this) {
    while(condition){
        wait(); // releases lock and sleeps
        // wakes up when notify() is called
    }
}
```

2. `notify()` - wakes up one waiting thread
```java
synchronized(this) {
    notify(); // wakes one random waiting thread
    // Risk: might wake wrong thread - others starve
}
```

3. `notifyAll()` - wakes up all waiting threads
```java
synchronized(this) {
    notifyAll(); // wakes all waiting theads
    // all threads re-check their condition (while loop)
    // Correct thread proceeds, others go back to wait
}
```

**Rule of thumb: Use `wait/notify` when theads need to tak to each other. Use `sleep` when a thead just needs to pause for some time.**

### What is the use of thread pool?

A thread pool is a collection of pre-created, reusable threads that are managed together to execute tasks efficiently - instead of creating new thread for every task (which is expensive).

Creating a new thread every time has costs:
* Memory allocation for each thread
* CPU time to start/stop threads
* Risk of creating too many threads and crashing the JVM

A thread pool solves this by reusing threads across multiple tasks.

```
How thread pool works:

1. User submit a task to the pool
2. An idle thread picks it up and executes it
3. After finishing, the thread returns to the pool (not destroyed)
4. If all threads are busy, the task waits in a queue

```

Creating thread pools - `Executors` factory class

Java provides `java.util.concurrent.Executors` to create pools easily:
```java
// Fixed Thread Pool - fixed number of threads
ExecutorService fixedPool = Executors.newFixedThreadPool(4);

// Cached Thread Pool - creates threads as needed, reuses idle ones
ExecutorService cachedPool = Executors.newCachedThreadPool();

// Single Thread Executor - only 1 thread, tasks run sequentially
ExecutorService singlePool = Executors.newSingleThreadExecutor();

// Scheduled Thread Pool - for delayed/periodic tasks
ExecutorService scheduledPool = Executors.newScheduledThreadPool(2);
```

### Executor vs ExecutorService

Executor is a simple interface with only one method - `execute()` which runs a task and forgets. No way to track if task finished, get return value or shutdown.
```java
Executor executor = Executors.newFixedThreadPool(3);

executor.execute(() -> {
    System.out.println("Task Running");
});

// Thats it! We can't do anything else with Executor reference
// executor.shutdown() -- not available
// executor.submit() -- not available
// executor.awaitTermination() -- not available

```
Where as ExecutorService setends Executor - so it also have `execute()`. Addition to that it can be used to shutdown the pool, track tasks via `Future`, get return values from tasks and wait for tasks to finish.

```java
ExecutorService pool = Executors.newFixedThreadPool(3);

// execute() -- fire and forget, no tracking
pool.execute(() -> {
    System.out.println("TaskRunning");
    // if exception occurs here - it's LOST silently
});

// submit() -- we got a Future back
Future<String> future = pool.submit(() -> {
    System.out.println("Task Running");
    if(someErrorOccur){
        throw new RuntimeException("Somthing went wrong");
    }
    return "Task Result"; // can return value
});

try {
    String result = future.get(); // waits and gets result, if some error occured in the execution it occurs here
    System.out.println(result);
} catch(ExecutionException e) {
    System.out.println("Task failed: " + e.getCause().getMessage())
}

```

### Callable vs Runnable

Both represents tasks that can be executed by a thread. But they have key differences.

Runnable - Simple task interface
* `run()` returns nothing
* Cannot throw checked exception
* Used with `Thread`, `execute()`, `submit()`

```java
@FunctionalInterface
public interface Runnable {
    void run(); // no return, no checked exception
}
```

Callable - Advanced task interface
* `call()` returns a value of type `V`
* can throw checked exception
* only used with `submit()` - not with `execute()`

```java
@FunctionalInterface
public interface Callable<V> {
    V call() throws Exception; // returns value, can throw Exception
}
```

### What is the purpose of invokeAll?

`invokeAll()` is a method of `ExecutorService` that lets us submit multiple tasks at once and waits for all of them to complete before moving on.

It takes a list of `Callable` tasks and returns a list of `Future` objects (one per task). It blocks the calling thread until every task is done.

```java
ExecutorService pool = Executors.newFixedThreadPool(3);

// Create a list of tasks
List<Callable<String>> tasks = Arrays.asList(
    () -> {Thread.sleep(1000); return "Task 1 done";}
    () -> {Thread.sleep(1000); return "Task 2 done";}
    () -> {Thread.sleep(1000); return "Task 3 done";}
);

// Submit all tasks - waits until all finish
List<Future<String>> futures = pool.invokeAll(tasks);

// Collect results
for(Future future: futures) {
    System.out.println(future.get()); // already done, no extra wait
}

pool.shutdown();
```

### What is ScheduledExecutorService and where it can be used?

`ScheduledExecutorService` is an interface in Java (part of `java.util.concurrent` package) that extends `ExecutorService` and adds the ability to schedule tasks to run after a delay or repeatedly at fixed intervals.

Think of it like a smart task scheduler - instead of running a task immediately, you tell it when and how often to run.

It has 4 key methods:
1. `schedule()` - Run once after a delay
```java
scheduler.schedule(() -> {
    System.out.println("Runs after 5 seconds");
}, 5, TimeUnit.SECONDS);
```

2. `scheduleAtFixedRate()` - run repeatedly at fixed intervals
```java
// starts after 2s, then runs every 10s
scheduler.scheduleAtFixedRate(() -> {
    System.out.println("Running every 10 seconds");
}, 2, 10, TimeUnit.SECONDS);
// If a task takes longer than the interval, the next run starts immediately after it finishes
```

3. `scheduleWithFixedDelay()` - run with a fixed gap between runs
```java
// waits 3s after each task finishes, rhen runs again
scheduler.scheduleWithFixedDelay(() -> {
    System.out.println("Running with 3s gap after each finish");
}, 1, 3, TimeUnit.SECONDS);
// Safer than scheduleAtFixedRate when task duration is unpredictable.
```

4. `shutdown()` - always stop the scheduler when done
```java
scheduler.shutdown();
```

Real-world use cases:
1. health checks - ping a server every 30 seconds
2. cache refresh - reloads config/data every 5 minutes
3. sending reminders - email/notification after a delay



