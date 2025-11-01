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

