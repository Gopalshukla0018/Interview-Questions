# Interview-Questions



---
# 1. var vs let vs const?
**Answer:**
In JavaScript, `var`, `let`, and `const` are used to declare variables, but they differ mainly in **scope**, **reassignment**, and **hoisting**.

* `var` is **function-scoped**, can be **redeclared and reassigned**, and is **hoisted** with a default value of `undefined`. Because of that, it can cause issues if declared inside blocks like `if` or `for`, since it’s accessible outside them too.
* `let` and `const` are both **block-scoped**, meaning they exist only within the block where they’re defined.
* The difference between `let` and `const` is that `let` can be **reassigned**, while `const` cannot — once you assign a value to a `const`, it stays the same.
* Both `let` and `const` are also hoisted, but they’re in the **temporal dead zone**, so you can’t access them before declaration.



**Example (if they ask):**

```javascript
{
  var a = 10;
  let b = 20;
  const c = 30;
}
console.log(a); // works
console.log(b); // error
console.log(c); // error
```



**In short:**

> "`var` is function-scoped and old-style, `let` is block-scoped and reassignable, and `const` is block-scoped but not reassignable. In modern JavaScript, we generally prefer `let` and `const` to avoid unexpected behavior."
---


# 2. Difference between == and ===?



**Answer:**
In JavaScript, both `==` and `===` are comparison operators, but the difference is in **type checking**.

* `==` is called the **loose equality** operator. It compares **values only** and performs **type conversion** if the types are different.
* `===` is the **strict equality** operator. It compares **both value and data type**, so it doesn’t do any type conversion.


**Example:**

```javascript
5 == "5"   // true  → because "5" is converted to number
5 === "5"  // false → because types are different (number vs string)
```



**In short:**

> "`==` checks only value after converting types, while `===` checks both value and type.
> It’s a best practice to use `===` to avoid unexpected results."


---
# 3.What is Hoisting
**Answer:**
Hoisting in JavaScript is a behavior where **variable and function declarations are moved to the top of their scope** before the code is executed.

It means you can use a function or variable **before it’s actually written in the code**, because JavaScript’s engine processes declarations first during the **compilation phase**.



**Example (with `var`):**

```javascript
console.log(a); // undefined
var a = 10;
```

Here, the declaration `var a;` is hoisted to the top, but not the assignment. That’s why it logs `undefined`, not an error.



**For function declarations:**

```javascript
greet(); // Works fine
function greet() {
  console.log("Hello");
}
```

Function declarations are **fully hoisted**, so you can call them before they’re defined.



**But for function expressions:**

```javascript
sayHi(); // ❌ TypeError: sayHi is not a function

var sayHi = function() {
  console.log("Hi");
};
```

Here, only the variable `sayHi` is hoisted (as `undefined`), but the actual function assignment is **not**.
So when you try to call it before initialization, it throws an error.


**However:**
`let` and `const` are also hoisted, but they stay in a **Temporal Dead Zone** — you can’t access them before their declaration.



**In short:**

> “Hoisting means JavaScript moves declarations (not initializations) to the top of the scope before execution. Function declarations are fully hoisted, but function expressions and variables declared with `var` are hoisted as `undefined`, while `let` and `const` remain in the temporal dead zone.”


---

# 4. What is a closure?



**Answer:**
A **closure** in JavaScript is created when a **function remembers the variables from its outer scope**, even after that outer function has finished executing.

In simple terms, a closure gives an **inner function access to the variables of its outer function**, even after the outer function has returned.
 


**Example:**

```javascript
function outer() {
  let count = 0;

  function inner() {
    count++;
    console.log(count);
  }

  return inner;
}

const counter = outer();
counter(); // 1
counter(); // 2
```

**Explanation:**

* When `outer()` runs, it returns the `inner()` function.
* Even though `outer()` has finished executing, the `inner()` function still **remembers** the variable `count` from its outer scope.
* This happens because of **closure** — `inner()` forms a closure around `count`.

**Real-life use cases:**

* Data privacy (like private variables)
* Creating function factories
* Maintaining state without global variables


**In short:**

> “A closure is when a function retains access to variables from its parent scope, even after that parent function has completed execution.”


---


 # 5. What is event loop?

### **Event Loop (Simple Explanation)**

The **Event Loop** is the part of JavaScript’s runtime that manages **how code is executed**, especially when dealing with **asynchronous tasks** like `setTimeout`, `fetch`, or `Promises`.

JavaScript is **single-threaded**, meaning it can execute **one thing at a time**.
So how does it handle things like timers, network requests, or user events *without freezing*?

That’s where the **event loop** comes in.



### **How it works (Step-by-step)**

1. **Call Stack**

   * When you run JavaScript, functions get pushed onto the **call stack** and are executed one by one (top to bottom).

2. **Web APIs / Node APIs**

   * When an async function (like `setTimeout` or `fetch`) is called, it’s handled by the **browser (Web APIs)** or **Node.js APIs**, not directly by the JS engine.
   * These APIs run in the background.

3. **Callback Queue / Task Queue**

   * Once the async task finishes, its callback is pushed into a **queue**.

4. **Event Loop**

   * The event loop continuously checks:

     * “Is the call stack empty?”
     * If yes → it moves the first callback from the queue into the call stack for execution.

This process repeats continuously — that’s the “loop.”


### **Example:**

```javascript
console.log("Start");

setTimeout(() => {
  console.log("Inside timeout");
}, 0);

console.log("End");
```

**Output:**

```
Start
End
Inside timeout
```

Even though the timeout delay is `0`,
it still runs **after** `End`, because `setTimeout` is asynchronous —
its callback waits in the queue until the main call stack is empty.



### **In Short:**

> The Event Loop allows JavaScript to handle asynchronous operations efficiently,
> even though it runs on a single thread.

---


# 6. What is Callback Hell and how to solve it?

### **Answer:**

A **callback** is a function passed as an argument to another function, to be executed **after some operation completes** (often asynchronously).

While callbacks are useful for handling async tasks like reading files, making network requests, or using `setTimeout`, they can become messy when you have **multiple async operations depending on each other**.

That’s when you get **Callback Hell** — a situation where callbacks are **nested inside other callbacks**, making the code **hard to read, debug, and maintain**.



### **Example of Callback Hell:**

```javascript
getUser(function(user) {
  getPosts(user.id, function(posts) {
    getComments(posts[0].id, function(comments) {
      console.log(comments);
    });
  });
});
```

This nesting pattern is called **“pyramid of doom”**, because the code starts to move **diagonally to the right**, forming a pyramid shape.


### **Why Callback Hell Happens:**

1. When multiple asynchronous tasks depend on each other
2. When error handling and logic are mixed inside nested functions
3. When the code grows, making it unreadable and unmaintainable



### **How to Solve Callback Hell**

There are **three main ways** to fix or avoid callback hell:

#### **1. Modularize Callbacks (Named Functions)**

Move each callback into its **own named function**, instead of nesting them inline.

```javascript
function handleComments(comments) {
  console.log(comments);
}

function handlePosts(posts) {
  getComments(posts[0].id, handleComments);
}

function handleUser(user) {
  getPosts(user.id, handlePosts);
}

getUser(handleUser);
```

This improves readability, though it still uses callbacks.



#### **2. Use Promises**

**Promises** make async code look more structured and linear by chaining `.then()` calls instead of nesting.

```javascript
getUser()
  .then(user => getPosts(user.id))
  .then(posts => getComments(posts[0].id))
  .then(comments => console.log(comments))
  .catch(error => console.error(error));
```

Each step returns a Promise, and errors are handled in one place using `.catch()`.

---

#### **3. Use Async/Await (Best Modern Way)**

The **async/await** syntax makes asynchronous code look and behave like synchronous code.

```javascript
async function showComments() {
  try {
    const user = await getUser();
    const posts = await getPosts(user.id);
    const comments = await getComments(posts[0].id);
    console.log(comments);
  } catch (error) {
    console.error(error);
  }
}

showComments();
```

This is **cleaner, easier to read, and easier to debug** — no nested callbacks, no messy `.then()` chains.



### **In Short:**

> **Callback Hell** happens when you nest too many callbacks, making code hard to read and maintain.
> You can solve it using **named functions**, **Promises**, or (best option) **async/await**, which makes async code look like normal synchronous code.



### **Bonus Tip:**

If an interviewer asks follow-up questions:

* Say that **callback hell** was a big problem in early JavaScript (especially before ES6).
* Mention that **Promises** (introduced in ES6) and **async/await** (ES8) were specifically designed to fix it.
* Add that **error handling and readability** are much better with async/await.

---


# 7. What are Promises in JavaScript?

### **Answer:**

A **Promise** in JavaScript is an object that represents the **eventual completion (or failure)** of an asynchronous operation and its resulting value.

You can think of a Promise as a **placeholder** for a value that isn’t available yet — like a “promise” that you’ll get the data in the future (e.g., from an API call or a file read).



### **Why Promises?**

Before Promises, we handled async tasks with **callbacks**, which often led to **callback hell** — messy, deeply nested code.
Promises were introduced in **ES6 (ES2015)** to make asynchronous code easier to write, read, and manage.



### **Promise States**

A Promise can be in one of **three states**:

1. **Pending** – The operation is still running.
2. **Fulfilled** – The operation completed successfully, and you get a result.
3. **Rejected** – The operation failed, and you get an error.

Once a Promise is fulfilled or rejected, it’s considered **settled** (it won’t change state again).



### **Creating a Promise**

```javascript
const myPromise = new Promise((resolve, reject) => {
  let success = true;

  if (success) {
    resolve("Operation successful!");
  } else {
    reject("Something went wrong!");
  }
});
```

Here:

* `resolve()` is called when the async task succeeds.
* `reject()` is called when it fails.

---

### **Consuming a Promise**

You handle the results of a Promise using `.then()` and `.catch()`:

```javascript
myPromise
  .then(result => console.log(result))   // Runs when resolved
  .catch(error => console.error(error)); // Runs when rejected
```

**Output (if success = true):**

```
Operation successful!
```



### **Chaining Promises**

You can chain `.then()` calls to run multiple async operations in sequence, without nesting:

```javascript
fetchUser()
  .then(user => fetchPosts(user.id))
  .then(posts => fetchComments(posts[0].id))
  .then(comments => console.log(comments))
  .catch(error => console.error(error));
```

Each `.then()` returns a new Promise, allowing smooth chaining.



### **Using Promises with Async/Await (Modern Way)**

Instead of chaining `.then()`, you can use `async/await` for cleaner syntax:

```javascript
async function getData() {
  try {
    const user = await fetchUser();
    const posts = await fetchPosts(user.id);
    console.log(posts);
  } catch (error) {
    console.error(error);
  }
}
```

This makes asynchronous code look synchronous and easier to read.



### **In Short:**

> A **Promise** is an object that represents the eventual result of an asynchronous operation.
> It helps write cleaner async code by avoiding callback hell.
> Promises have three states — *pending*, *fulfilled*, and *rejected* — and are handled using `.then()`, `.catch()`, and optionally `async/await`.



### **Bonus Tip (for interviews):**

If they ask **“Why are Promises better than callbacks?”**, say:

* Promises avoid **nested structures** (callback hell).
* They provide **better error handling** with `.catch()`.
* They make code **more predictable and maintainable**.
* They work perfectly with **async/await**, introduced later for even cleaner syntax.

---


# 8. What is the `this` keyword in JavaScript?

### **Answer:**

The **`this`** keyword in JavaScript refers to the **object that is currently executing the code** — it represents the **context** in which a function is called.

However, the value of `this` **depends on how (and where)** the function is called, **not where it’s defined**.
That’s what often confuses people in JavaScript.



### **1. `this` in the Global Context**

In the **global scope** (outside any function):

```javascript
console.log(this);
```

* In **browser**, `this` refers to the **window** object.
* In **Node.js**, it refers to the **global** object (or `{}` in modules).



### **2. `this` Inside a Regular Function**

In a **normal function**, the value of `this` depends on **how** the function is called.

#### Example:

```javascript
function show() {
  console.log(this);
}

show(); // In browser → window | In strict mode → undefined
```

* In **non-strict mode**, `this` defaults to the **global object**.
* In **strict mode**, `this` becomes `undefined`.



### **3. `this` Inside an Object Method**

When a function is called as a **method of an object**, `this` refers to **that object**.

```javascript
const person = {
  name: "Gopal",
  greet() {
    console.log(this.name);
  },
};

person.greet(); // "Gopal"
```

Here, `this` refers to the `person` object because `greet` is called through it.



### **4. `this` in Arrow Functions**

Arrow functions **do not have their own `this`**.
Instead, they **inherit `this` from their surrounding (lexical) scope**.

```javascript
const person = {
  name: "Gopal",
  greet: () => {
    console.log(this.name);
  },
};

person.greet(); // undefined
```

Here, `this` inside the arrow function doesn’t refer to `person`.
It refers to the **outer scope** (global `this`), so `this.name` is `undefined`.

That’s why arrow functions are **not good for methods** that rely on `this`.



### **5. `this` in Constructors (with `new`)**

When you call a function with the `new` keyword, it creates a **new object**, and inside that function, `this` refers to the **newly created object**.

```javascript
function Car(brand) {
  this.brand = brand;
}

const car1 = new Car("Tesla");
console.log(car1.brand); // "Tesla"
```

Here, `this` refers to the new object being created by `new Car()`.



### **6. `this` in Event Listeners (Browser Example)**

In DOM event handlers, `this` refers to the **HTML element** that received the event.

```javascript
button.addEventListener("click", function() {
  console.log(this); // <button> element
});
```

If you use an **arrow function** instead, `this` will not refer to the element, but to the **outer scope**.



### **7. Explicit Binding: `call`, `apply`, and `bind`**

You can **manually set the value of `this`** using these methods:

```javascript
function greet() {
  console.log(`Hello, ${this.name}`);
}

const user = { name: "Gopal" };

greet.call(user);  // "Hello, Gopal"
greet.apply(user); // "Hello, Gopal"

const boundGreet = greet.bind(user);
boundGreet();      // "Hello, Gopal"
```

* `call()` and `apply()` **invoke** the function immediately.
* `bind()` **returns a new function** with `this` permanently set.



### **In Short:**

> The `this` keyword refers to the **object that owns the function at the time it’s called**.
> Its value depends on **how the function is called**, not where it’s defined.

| **Context**            | **Value of `this`**                  |
| ---------------------- | ------------------------------------ |
| Global scope           | Global object (`window` or `global`) |
| Object method          | The object itself                    |
| Function (strict mode) | `undefined`                          |
| Constructor function   | The new instance                     |
| Arrow function         | Inherits from surrounding scope      |
| Event handler          | The DOM element (in browser)         |
| Using call/apply/bind  | Manually set                         |


### **Bonus Tip (for interviews):**

If asked about common confusion:

* Explain that arrow functions **lexically bind** `this` — they take it from where they’re written, not how they’re called.
* Mention that in React, this is why we often use **arrow functions** in class components to keep `this` correctly bound.

---

# 9. What is an IIFE in JavaScript?

**Answer:**
An **IIFE (Immediately Invoked Function Expression)** is a function that runs **immediately after it’s defined**.
It’s used to **execute code instantly** and **create a private scope**, so that variables inside it don’t leak into the global scope.

**Syntax:**

```javascript
(function() {
  console.log("This runs immediately!");
})();
```

Here, the function is wrapped in parentheses to make it an **expression**, and the second pair `()` **calls** it immediately.

**Why it’s used:**

* To avoid polluting the global scope
* To create private variables
* To run setup or initialization code once

**Example:**

```javascript
(function() {
  const msg = "Hello from IIFE";
  console.log(msg);
})();
console.log(msg); // ❌ ReferenceError
```

Here `msg` is private — it can’t be accessed outside the IIFE.

**IIFE with parameters:**

```javascript
(function(name) {
  console.log(`Hello, ${name}!`);
})("Gopal");
```

**Arrow function IIFE:**

```javascript
(() => {
  console.log("Arrow IIFE runs!");
})();
```

**In short:**

> An **IIFE** is a function that runs as soon as it’s defined.
> It helps keep code private and avoids global variable conflicts.

**How to say in interview:**

> “An IIFE is an immediately invoked function expression. It executes right after it’s defined and is mainly used to create a private scope or to run initialization code without affecting the global scope.”

---


# 10. What is the difference between `call()`, `apply()`, and `bind()`?

**Answer:**
In JavaScript, `call()`, `apply()`, and `bind()` are **methods used to manually set the value of `this`** when calling a function.
They all let you control which object a function should use as `this`, but they differ in **how** they are used and **when** they execute.



### **1. `call()`**

The `call()` method **calls a function immediately**, with a given `this` value and **arguments passed individually**.

**Example:**

```javascript
function greet(city, country) {
  console.log(`Hello ${this.name} from ${city}, ${country}`);
}

const user = { name: "Gopal" };

greet.call(user, "Farrukhabad", "India");
```

**Output:**

```
Hello Gopal from Farrukhabad, India
```

✅ Executes **immediately**
✅ Arguments are passed **separately (comma-separated)**


### **2. `apply()`**

The `apply()` method is **just like `call()`**, but it takes the arguments as an **array**.

**Example:**

```javascript
greet.apply(user, ["Farrukhabad", "India"]);
```

**Output:**

```
Hello Gopal from Farrukhabad, India
```

✅ Executes **immediately**
✅ Arguments are passed **as an array**

**Use Case:** When you already have arguments in an array or array-like object.



### **3. `bind()`**

The `bind()` method **does not call the function immediately.**
Instead, it **returns a new function** with `this` permanently set to the provided object.

**Example:**

```javascript
const greetUser = greet.bind(user, "Farrukhabad", "India");
greetUser(); // Executes later
```

**Output:**

```
Hello Gopal from Farrukhabad, India
```

✅ Does **not execute immediately**
✅ Returns a **new function** with `this` bound

**Use Case:** When you want to store or reuse a function with a fixed `this` context (common in event handlers or callbacks).


### **In short:**

| Method      | Executes Immediately? | How to Pass Arguments          | Returns a Function? | Description                              |
| ----------- | --------------------- | ------------------------------ | ------------------- | ---------------------------------------- |
| **call()**  | ✅ Yes                 | Individually (comma-separated) | ❌ No                | Calls the function right away            |
| **apply()** | ✅ Yes                 | As an array                    | ❌ No                | Same as call(), but with array arguments |
| **bind()**  | ❌ No                  | Individually (comma-separated) | ✅ Yes               | Returns a new function with fixed `this` |


### **In one line (for interviews):**

> “`call()` and `apply()` both call a function immediately with a specified `this` — the only difference is that `call()` takes arguments separately, while `apply()` takes them as an array.
> `bind()` doesn’t execute the function immediately — it returns a new function with that `this` value permanently set.”


# 11. What are Higher-Order Functions?

### **Answer:**

A **Higher-Order Function (HOF)** in JavaScript is a function that:

* Takes **another function as an argument**, or
* **Returns a function** as its result, or
* Does both.

In simple words, a higher-order function is a function that works with other functions.

---

### **Example 1: Function as an Argument**

```javascript
function greet(name) {
  return "Hello " + name;
}

function processUser(fn, user) {
  return fn(user);
}

console.log(processUser(greet, "Gopal")); 
// Output: Hello Gopal
```

Here, `processUser` is a higher-order function because it accepts another function (`greet`) as a parameter.

---

### **Example 2: Function Returning Another Function**

```javascript
function multiplier(factor) {
  return function(number) {
    return number * factor;
  };
}

const double = multiplier(2);
console.log(double(5)); // 10
```

`multiplier` is a higher-order function because it **returns a function**.

---

### **Common Built-in Higher-Order Functions**

Many array methods in JavaScript are higher-order functions because they accept functions as callbacks:

* `map()`
* `filter()`
* `reduce()`
* `forEach()`
* `find()`
* `sort()`

**Example with `map`:**

```javascript
const numbers = [1, 2, 3];
const squares = numbers.map(n => n * n);

console.log(squares); // [1, 4, 9]
```

---

### **Why Higher-Order Functions are Useful**

* Cleaner and more readable code
* Helps avoid repetition
* Makes code more modular
* Encourages functional programming style

---

### **In short:**

> A **Higher-Order Function** is a function that takes another function as input, returns a function, or both.
> They help write cleaner, reusable, and more flexible code.

---

### **One-liner for interview use:**

> “A higher-order function is a function that operates on other functions by taking them as arguments or returning them.”

---
