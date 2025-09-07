# Javascript

### How does javascript file executed in a machine? Explain the flow.
When you run a JavaScript file:
1. The JS engine reads the code line by line. During this phase, variables and functions are stored in memory `(hoisting)`. Execution context (global or functional scope) is created.
2. JS runs your code in a single thread (synchronously) using the call stack. Statements are executed one after another.
3. If JS encounters async code (like `setTimeout`, `fetch`, Promises), it delegates them to Web APIs (in browser) or C++ APIs (in Node.js). Once async work finishes, callbacks are placed in the callback queue or microtask queue, waiting for the event loop to push them back into the call stack.


### Explain Hoisting.
Hoisting is JavaScript's behavior of moving variable and function declarations to the top of their scope (memory allocation phase) before code execution. 

Example 1: Variable hoisting with `var`:
```javascript
console.log(a); // undefined (not error)
var a = 10;
console.log(a); // 10
```

How JS sees it internally:
```javascript
var a;            // declaration hoisted
console.log(a);   // undefined
a = 10;           // initialization (not hoisted)
console.log(a);   // 10
```

Example 2: `let` and `const` (Temporal dead zone)
```javascript
console.log(b); // ❌ ReferenceError
let b = 20;
```
* `let` and `const` are hoisted too, but they stay in a temporal dead zone from the start of the scope until the line where they are declared. They cannot be accessed before initialization.

Example 3: Function Hoisting:
```javascript
sayHi();  // ✅ Works, prints "Hello"

function sayHi() {
  console.log("Hello");
}
```

Function declarations are fully hoisted (both name and body). But function expressions behave differently:

```javascript
sayBye(); // ❌ TypeError: sayBye is not a function

var sayBye = function () {
  console.log("Goodbye");
};
```

`var sayBye;` is hoisted - initializd with undefined. At the time of `sayBye()`, it still undefined.

### Undefined vs not defined
Undefined : A variable is declared but not assigned a value. The JS engine automatically initializes it with `undefined`. Undefined means this variable exists in memory but no value is assigned.

```javascript
var a;
console.log(a);  // ✅ undefined (declared but no value)

function test(x) {
  console.log(x);  // ✅ undefined (parameter not passed)
}
test(); 

```

Not Defined: A vairable is never declared in the scope. Tying to access it throws a ReferenceError.
```javascript
console.log(b);  // ❌ ReferenceError: b is not defined
```

### Explain the difference between how `var` and `let/const` behave with scope, hoisting, initialization, and re-declaration.

1. Hoisting & initialization: All variables (var, let, const) are hoisted during the memory creation phase, but they are treated differently.
    * var → hoisted and initialized to undefined.
    * let / const → hoisted but kept in the Temporal Dead Zone (TDZ) until the line of declaration. TDZ = the period between scope creation and actual declaration, where accessing the variable throws an error.

```javascript
console.log(a); // ✅ undefined
var a = 10;

console.log(b); // ❌ ReferenceError (TDZ)
let b = 20;

console.log(c); // ❌ ReferenceError (TDZ)
const c = 30;

```

2. Scope: 
    * var → function-scoped (or global if not inside a function).
    * let/const → block-scoped ({ }).

```javascript
if (true) {
  var x = 1;
  let y = 2;
  const z = 3;
}
console.log(x); // ✅ 1 (var leaks out of block)
console.log(y); // ❌ ReferenceError
console.log(z); // ❌ ReferenceError
```

3. Re-declaration:
    * var → allows re-declaration in the same scope.
    * let/const → cannot be re-declared in the same scope.

```javascript
var a = 5;
var a = 10; // ✅ Works

let b = 5;
// let b = 10; // ❌ SyntaxError

const c = 5;
// const c = 10; // ❌ SyntaxError
```

4. Re-assignment:
    * var → can be reassigned.
    * let → can be reassigned.
    * const → cannot be reassigned.

```javascript
var a = 1;
a = 2; // ✅ Works

let b = 3;
b = 4; // ✅ Works

const c = 5;
c = 6; // ❌ TypeError
```

5. Global Object Property: If you declare a variable in the global scope:
    * var → becomes a property of the global object (window in browsers).
    * let/const → do NOT attach to the global object.

```javascript
var a = 10;
let b = 20;
const c = 30;

console.log(window.a); // ✅ 10
console.log(window.b); // ❌ undefined
console.log(window.c); // ❌ undefined
```

### Explain scope chain and lexical environment in terms of JS.
A lexical environment is a structure that holds variable/function bindings in a given scope, and a reference to its outer (parent) environment.

Every execution context (global, function, block) gets itws own lexical environment.

```javascript
function outer() {
  let a = 10;

  function inner() {
    let b = 20;
    console.log(a); // inner can "see" outer's variables
    console.log(b);
  }

  inner();
}

outer();

```

Here, `inner()` has its own lexical environment `{ b:20 }`. It also has access to outer's lexical environment `{a:10}`. And finally to the global lexical environment. So, when `console.log(a)` runs, JS looks inside inner → not found → checks parent outer → found.

The scope chain is the chain of lexical environments JS follows when resolving variables. Process:
1. JS first looks in the current scope (local lexical environment).
2. If not found, goes up one level (parent environment).
3. Continues until it reaches the global scope.
4. If not found then ReferenceError occurs.

**Important Notes:**
1. Lexical = where it is written in code, not where it is called. That’s why closures work (function remembers its parent scope).
2. Scope chain is created at runtime when execution contexts are set up.
3. If two variables exist with the same name in different scopes, the nearest one shadows the outer one.

### What is Closure in JavaScript?
A closure is a function that retains access to its outer function's variables, even after the outer function has finished executing. It "remembers" the environment in which it was created, allowing it to access variables outside its immediate scope.

```javascript
function outer() {
    let outerVar = "I'm in the outer scope!";
    function inner() {
        console.log(outerVar); 
        outerVar = "Updated"
    }
    return inner;  
}
const closure = outer(); 
closure(); // Output - I'm in the outer scope!
closure(); // Output - Updated
```

The function inner() forms a closure by retaining access to outerVar, which is a variable in the scope of outer().
Even though outer() has completed execution, inner() still has access to outerVar due to the closure.

### What are difference between function statement and function expression.

| Feature            | Function Statement (Declaration)             | Function Expression                    |
| ------------------ | -------------------------------------------- | -------------------------------------- |
| **Hoisting**       | ✅ Fully hoisted (can call before definition) | ❌ Not hoisted (must define first)      |
| **Name**           | Must have a name                             | Can be anonymous or named              |
| **When evaluated** | At compile/parse time                        | At runtime (when execution reaches it) |
| **Use case**       | General, reusable functions                  | Inline functions, callbacks, closures  |

```javascript
// Function statement
sayHi(); // ✅ Works because of hoisting

function sayHi() {
  console.log("Hi!");
}

// Function Expression
sayHi(); // ❌ Error: sayHi is not a function

const sayHi = function() {
  console.log("Hi!");
};
```

### What is an Anonymous Function?
An anonymous function is a function without a name.
It is usually used as a function expression or as a callback where we don’t need to reuse the function elsewhere.

```javascript
setTimeout(function() {
  console.log("Runs after 1 second");
}, 1000);
```

Anonymous functions are commonly seen in callbacks and arrow functions.
But debugging stack traces can be harder because they don’t show a function name.

### What are first class functions?
In JavaScript, functions are first-class citizens, meaning they can be treated like any other value — stored in variables, passed as arguments, or returned from other functions. This makes concepts like callbacks, higher-order functions, and functional programming possible.

```javascript
// Assigned as variable
const greet = function() {
  console.log("Hello!");
};
greet(); // Hello!

// Passed as arguments (callbacks)
function processUserInput(callback) {
  callback("Arpan");
}

processUserInput(function(name) {
  console.log("Hello " + name);
});

// Returned from other functions
function multiplier(factor) {
  return function(number) {
    return number * factor;
  };
}

const double = multiplier(2);
console.log(double(5)); // 10

// Stored in data structures
const funcs = [
  () => console.log("First"),
  () => console.log("Second")
];

funcs[0](); // First

```

### What is a Callback Function in JavaScript?
A callback function is a function that is passed as an argument to another function, and then executed later inside that function.

It’s called “callback” because the function is “called back” at a later time — either synchronously or asynchronously. n JavaScript, callbacks are widely used for asynchronous operations like timers, events, and network requests.

Example:
Imagine you go to a restaurant and order food:
* You tell the waiter: “Bring me a pizza. When it’s ready, call me.”
* The waiter goes to the kitchen → that’s like starting an asynchronous task.
* Instead of waiting there (blocking), you go sit and chat with friends.
* Later, when the pizza is ready, the waiter calls you back and serves the pizza.

The “instruction you gave to the waiter” (call me when pizza is ready) is just like a callback function — it gets executed later, once the task is done.

```javascript
// Customer places an order
function orderPizza(callback) {
  console.log("Customer: I would like a pizza.");
  console.log("Waiter: Sure! I’ll let you know when it’s ready.");

  // Simulate cooking time (asynchronous task)
  setTimeout(() => {
    console.log("👨‍🍳 Kitchen: Pizza is ready!");
    callback(); // Call back the customer
  }, 2000);
}

// Callback function = what to do when pizza is ready
function eatPizza() {
  console.log("Customer: Thanks! Eating my pizza now. 🍕");
}

// Customer is free to do other things
console.log("Customer: While waiting, I’ll chat with my friends...");

// Place order
orderPizza(eatPizza);


```

Output:
```
Customer: While waiting, I’ll chat with my friends...
Customer: I would like a pizza.
Waiter: Sure! I’ll let you know when it’s ready.
👨‍🍳 Kitchen: Pizza is ready!
Customer: Thanks! Eating my pizza now. 🍕
```

### What are the issues with callback?
Callbacks are very powerful in JavaScript (used for async tasks like file reading, HTTP requests, timers, etc.), but they also come with several issues/drawbacks:

1. Callback Hell (Pyramid of Doom): When you nest multiple callbacks, the code becomes deeply indented, hard to read, and harder to maintain.
```javascript
doTask1(function(result1) {
  doTask2(result1, function(result2) {
    doTask3(result2, function(result3) {
      doTask4(result3, function(result4) {
        console.log("All tasks done!");
      });
    });
  });
});
```

2. Inversion of Control: You give control of your function to someone else (the callback executor). If that function decides not to call your callback, or calls it multiple times, your program may break.
```javascript
function unreliableTask(callback) {
  // Sometimes calls twice
  callback("done");
  callback("done again");
}

unreliableTask((msg) => console.log(msg));
// Output:
// done
// done again  <-- unexpected!
```

3. Error Handling is Messy: Callbacks usually follow the error-first convention (callback(error, result)), but this requires every nested level to handle errors manually. In deeply nested callbacks, handling errors in each step becomes tedious.
```javascript
fs.readFile("file.txt", (err, data) => {
  if (err) {
    console.error("Error reading file", err);
    return;
  }
  console.log("File data:", data);
});

```

4. Harder to compose and reuse: Callbacks don’t compose well. Reusing or combining asynchronous operations with callbacks is more complex compared to promises or async/await.


### What is callback hell?
Callback Hell happens when we have multiple nested callbacks, usually in asynchronous code.
It makes code:
* Hard to read (rightward drift, “pyramid of doom”)
* Hard to debug
* Hard to maintain

Example: Suppose you order food step by step:
1. Order pizza →
2. After pizza arrives, order pasta →
3. After pasta arrives, order dessert →
4. After dessert, pay the bill

```javascript
function orderPizza(callback) {
  setTimeout(() => {
    console.log("🍕 Pizza is ready!");
    callback();
  }, 1000);
}

function orderPasta(callback) {
  setTimeout(() => {
    console.log("🍝 Pasta is ready!");
    callback();
  }, 1000);
}

function orderDessert(callback) {
  setTimeout(() => {
    console.log("🍨 Dessert is ready!");
    callback();
  }, 1000);
}

// Callback Hell 👇 (nested pyramid)
orderPizza(() => {
  orderPasta(() => {
    orderDessert(() => {
      console.log("💳 Payment done. Enjoy your meal!");
    });
  });
});

```

### What are higher order function in javascript?

A higher-order function in JavaScript is a function that either:
1. Takes another function as an argument (callback)
2. Returns a function as its result

This is possible because in JavaScript, functions are treated as first-class citizens — meaning they can be stored in variables, passed around, and returned just like any other value.

Examples:
1. Take a function as argument: Here, processUserInput is a higher-order function because it takes greet (a function) as an argument.
```javascript
function greet(name) {
  return `Hello, ${name}!`;
}

function processUserInput(callback) {
  const name = "Arpan";
  return callback(name);
}

console.log(processUserInput(greet)); 
// Output: Hello, Arpan!
```

2. Returns another function: Here, multiplier is a higher-order function because it returns another function.
```javascript
function multiplier(factor) {
  return function (number) {
    return number * factor;
  };
}

const double = multiplier(2);
const triple = multiplier(3);

console.log(double(5)); // Output: 10
console.log(triple(5)); // Output: 15
```

3. Built-in Higher-Order Functions
```javascript
const numbers = [1, 2, 3, 4, 5];

// map -> takes a callback function
const squared = numbers.map(num => num * num);
console.log(squared); // [1, 4, 9, 16, 25]

// filter -> takes a callback
const even = numbers.filter(num => num % 2 === 0);
console.log(even); // [2, 4]

// reduce -> takes a callback
const sum = numbers.reduce((acc, curr) => acc + curr, 0);
console.log(sum); // 15

```

### What is a Promise in JavaScript?
A Promise is an object that represents the eventual completion (or failure) of an asynchronous operation. Promises have the following advantages over callbacks:
1. Solve callback hell → no more deep nesting.
2. Better error handling (via .catch()).
3. Can chain async operations easily.

There are 3 states of promise:
1. Pending → initial state (waiting for result).
2. Fulfilled → operation completed successfully (resolve).
3. Rejected → operation failed (reject).

```javascript
let promise = new Promise((resolve, reject) => {
  let success = true;

  setTimeout(() => {
    if (success) {
      resolve("Order delivered 🍕");
    } else {
      reject("Delivery failed ❌");
    }
  }, 2000);
});

promise
  .then(result => console.log(result))   // runs if resolved
  .catch(error => console.error(error)) // runs if rejected
  .finally(() => console.log("Done waiting."));

```



