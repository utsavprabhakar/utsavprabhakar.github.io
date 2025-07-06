---
layout: post
title: Event Loop in JS
subtitle: How the hell do they do this in single thread!
tags: [tech]
comments: true
mathjax: true
author: Utsav
---

#### Problem

There are tons of questions that come to mind when you just start in JS.

- How does Javascript actually work?
- V8 engine?
- Single threaded?
- Callbacks?

Javascript describes itself as:
- Single threaded
- Non-blocking
- Async
- concurrent 
- language

WHAT?

#### Clearing the fog
JavaScripts has something called `WebAPIs` which contain the `DOM`, `setTimeout`, `ajax` etc. 

JS is a single threaded runtime which means it has a single call stack. 
One thread == one call stack == one thing at a time!

**What happens when things are slow?**
IF no threads, we have to wait till it is done. The problem is because we are writing it in browser. While the thread is doing work, the browser remains stuck (you cant click anywhere).

This is NOT ideal..

**Asynchronous callbacks**
Here is a function, call me maybe?

#### **Concurrency and the event loop**
One thing at a time, _but not really!_

Javascript (the runtime) can only do one thing at a time. **But** the browser is _more than just_ a runtime. 

Browser gives us other things like WebAPIs -> effectively threads, access like threads, make calls to. 

`SetTimeout` is an API provided by the browser as part of webAPIs. It is going to handle the wait. 5 seconds later, the counters completes. 

When the WebAPIs are done, they push your callback function to the _Task queue_. 
_Event Loop_ looks at the stack. If it finds it empty, it checks the task queue and puts what is it in the queue back to the stack. It pushes the callback to the stack. 

_EventLoop needs to wait till the stack is clear_. 
So SetTimeout(function, 0) will also work the same way. 

```
$.get('url', function cb(data) { conosle.log(data);})
```

 `setTimeout` is funny. If you run `setTimeout` multiple times with timeout duration as 1 seconds, it doesnt mean all the `setTimeouts` will be executing their functions at 1 second. 
 They are queued in the _callback queue_ and is dequeued one by one and pushed to the call stack. 
 SetTimeout timeout is not the guaranteed time. It is the minimum time. 

DONT PUT SHITTY SLOW CODE ON THE STACK(MAIN THREAD

#### **Promises**
Promises in JS represents in the eventual completion (or failure) of an async operation and its resulting value. It is a placeholder for a value that might not be available immediately. 
Promise provides chaining (`.then()`, `.catch()`)

```
// Creating a new Promise
const myPromise = new Promise((resolve, reject) => {
  const success = true; // This could be the result of an API call or other async task

  setTimeout(() => {
    if (success) {
      resolve("Operation successful!");
    } else {
      reject("Operation failed!");
    }
  }, 2000); // Simulating a 2-second delay
});

myPromise
  .then((message) => {
    console.log(message); // This runs if the promise is resolved
  })
  .catch((error) => {
    console.error(error); // This runs if the promise is rejected
  });

console.log("Promise initiated..."); // This will log immediately
```

#### **Async/Await**
Built on top of Promises but provides a more sync-looking syntax for working with async code, thus making it easier to read/write.

`async` keyword explicitly means it returns a JS Promise.

We can use Try/Catch around calling async functions. 

```
    async function processData() {
      try {
        const data = await fetchData(); // fetchData returns a Promise
        console.log(data);
      } catch (error) {
        console.error("An error occurred:", error);
      }
    }

    processData();
```

`CompletableFuture` leverages a thread pool for concurrent executions, while JS (being single-threaded) achieves concurrency through event loop. 

#### Promises and Event loop?

**Event loop: JS's concurrency engine**
JS has two queues -> task queue and the microtask queue. 
When the call stack is empty, EventLoop prioritises executing all tasks in the microtask queue before moving to the callback queue.

Event loop has two lists
- Callback queue: Standard tasks that need to be done eventually, liked processing timer callbacks.
- Microtask queue: Urgent tasks that should be handled as soon as possible, like handing Promise that has just been resolved. 

---

#### FAQ

Q1) **Who provides the two queues (callback and microtask)? The V8 engine or something else?**
- V8 engine is responsible for executing Javascript code. However the event loop and the two queues are not part of v8.
- Event loop is part of runtime environment ==(?)== (like web browser or Node.js). the runtime provides the event loop, web APIs (setTimeout, DOM event, fetch) that interact with the V8 engine. 

Q2) **Why these two queues? Why not others?**
- Theoretically possible but the distinction between these queues is important for JS concurrency model.
- This order helps manage execution order of different types of aysnc tasks. 
	- Resolving promises is handling quickly 
	- UI updates or timer callbacks can wait
	- Limited queues simplifies event loop logic. 

Q3) **Why is the queue called Microtask queue?**
- No strong points (obviously)
- MDN docs say that microtasks represent tasks that need to be executed at the end of current synchronous execution context but before the browser potentially renders or processes events?

Q4) **Promises function go into microtask queue. Anything else associated with this queue?**
- `MutationObserver` callbacks also go into the microtask queue, allowing for efficient observation and reaction to changes in the DOM.
- In Node.js, `process.nextTick()` and `queueMicrotask()` are also used to queue microtasks

Q5) **Why cant callbacks jump in front of the line as soon as they are complete?** 
This is just how JS works. 
- _Execute all synchronous code first, no exceptions!_
- No interruption: If a Promise does resolve quickly, its callback would not be executed until the call stack is empty and sync code is completely executed.
- This is mainly done to ensure long-running tasks dont block the main thread, keeping user interface working and responsive.

Q6) **How are things different in Java?**
In Java, there is support for _Multithreading_ allowing us to execute multiple tasks truly in parallel.
Thread pools are used to handle async operations. Alows main thread to keep working.
Non blocking waits: If a `CompletableFuture` is waiting on a dependent future or async computation, it does not block the waiting thread. The completion of the op triggers the execution of dependent code on a separate thread.

Q7) **If we do separate calls to multiple Promises, where exactly are all of them being executed?** 
WebAPIs!
When you initiate an asynchronous operation like a network request using `fetch` or `XMLHttpRequest` (which `fetch` often utilizes internally), JavaScript doesn't handle the actual fetching itself on the main thread. It gives this role to the Browser. Browser (or Node.js environment in Node.js) is written in low level c++ and capable of performing these tasks, potentially using multiple threads. 
Actual network call is being handled by the browser networking stack, separate from JS thread.

Q8) **When using async/await model, we can make the thread wait for the Promise to resolve. How does JS achieve that? Because in this case, the call stack will not be empty so event loop cant do its thing of adding the microtask to the call stack?**

Async/await is not like `join()` in java. It doesnt block the call stack. Its just that when JS encounters an `await()`, it basically preserves the state of the async function and then deletes it so that other code can be run. Once the promise resolves(or rejects), event loop picks it up from the Microtask queue and resumes it. 

In essence, the execution flow when using `async/await` is a blend of synchronous and asynchronous behavior:

1. **Synchronous code runs first:** The JavaScript engine executes all the synchronous code in your program in the order it appears.
2. **`async` functions begin execution:** When an `async` function is called, it starts running its synchronous code until it reaches the first `await`.
3. **`await` pauses the `async` function and yields control:** At the `await` keyword, the `async` function's execution is paused, and the engine allows the event loop to process other tasks.
4. **Other tasks (including synchronous code) execute:** The event loop continues processing the task queue and microtask queue as they become available.
5. **Awaited Promise resolves, microtask queued:** When the Promise awaited by the paused `async` function resolves, a microtask is added to the microtask queue.
6. **Event loop resumes the `async` function:** When the call stack is free, the event loop picks up the microtask, restores the `async` function's execution context, and resumes its execution from after the `await`.

**Q9) What are some things java can do but JS cannot?**
- Low level system access (threads, sockets, files)
- Native Android App Dev
- Compiling to native executables
	- Java can compile to native binaries for different platforms?
	- JS cant directly do that - it needs a runtime like Node.js or Browser.
- Strong compile time safety

-----

This is it for now folks. See you in the next one where I will dive deeper into Java side of things. I shall also cover Virtual threads of Java 21. 

Again, these are personal notes not intented to be consumed as a primary source of information. 
