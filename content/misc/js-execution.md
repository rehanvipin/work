---
weight: 4
title: "JavaScript execution"
# bookFlatSection: true
# bookToc: false
bookCollapseSection: false
---

# How does JS run in the browser & node?
Info gotten from coding and these wonderful talks, in this order:
* [WTH is the Event Loop?](https://www.youtube.com/watch?v=8aGhZQkoFbQ)
* [Further Adventures of the Event Loop](https://www.youtube.com/watch?v=u1kqx6AenYw)
* [Jake Archibald on the Event Loop](https://www.youtube.com/watch?v=cCOL7MC4Pl0)

## Overview
The v8 runtime provides a **call stack** and heap.
Then in the browser, we have the WebAPIs, which provides FUNctions which can make code execute elsewhere.
We have : 
* Task queues (sometimes called Macrotask queues)
* Microtask queues
* Rendering pipeline

All these are enough for async single-thread execution of JS.

To do parallel exec, the browser provides Web Workers. Those have their own task & microtask queues.
However, they can't maniupilate the DOM and they don't have any rendering pipelines.
The single rendering pipeline runs in the main thread.

In the case of Node, it has similar task & microtask queues,
but bit different kinds than those in the browser.

Fun fact, after going through the talks, it should make sense why this code will never cause `el` to appear on the screen:
```javascript
document.body.append(el);
el.style.display = 'none';
```

Think you know it all? Good for you. Test out your skills [with these questions](https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/#level-1-bossfight).

## Event loop
The event loop is a piece of code in the browser (and nodeJS as well, but it's bit different in node),
that looks for something to execute from the queues (it has a priority order to decide which queue) and then puts that
on the call stack.

It doesn't do anything as long as there is something executing in the call stack.
It will wait for all that to be done before picking up the next task (yes, even page repaint is a task)

## Task Queues
Wait what? There should be only one right? Haha, no.
There can be one for network events (fetch), setTimeouts and other use a different one. That's why fetch completes before setTimeout
even if setTimeout may queue the task first.
Calling setTimeout won't queue a task, it will do it after its waiting time.

## Microtask Queues
Tasks from this queue are executed immediately when the call stack is empty.
Thus, these can be executed even in the middle of a regular task (NOT WHEN JS CODE IS EXECUTING ON THE STACK).
Whaaat? it's possible that a task is executing without JS code? Yeah.
Imagine an event, after each node that it bubbles on and executes listener callbacks, there is no code on the call stack, 
but since it is still bubbling, the task itself is not done.
Hence, microtasks can execute in between this task, but regular tasks will have to wait for all the bubbling etc to be complete.

When the event loop picks up microtasks, it drains the queue till is empty.
What action puts tasks in the microtask queue? Promises and Mutation Observers.

Fun fact, the browser doesn't add a new mutation observer task on the same element-attribute combo if one is already pending in the queue.

## Rendering pipeline
The browser provides us a way to add tasks to the rendering pipeline, through a "rendering queue".
When the event loop starts processing this queue, it drains the queue till all existing tasks of the queue are processed.
How to add things to this queue? `requestAnimationFrame(callback)`.
The rendering pipeline consists of first executing tasks from rendering queue and then performing CSS calculations and painting.
The event loop looks at this queue every 16 ms (if it's a 60Hz screen), unless it's in the background.
Remember, the event loop cannot look at this queue if there is something being executed on the call stack.
Also, it will check the microtask queue first, before looking at this queue.

## Web Workers
Web workers and cross-origin iframes are said to have their own runtimes.
Meaning, they will have their own queues, stacks, and heaps.
Runtimes can communicate using the `postMessage` function.

## Events
Well, it's called the *event* loop after all. So when do browser events such as clicking a button get executed?
What if the button is programatically clicked through JS?

The click event (as an example) causes the browser's event processing task to be added to the queue.
When that task is picked up by the loop, it goes through the DOM tree
and executes all the event listener callback functions on the element it encounters.

If the click event task is programatically called (e.g., `el.click()`), all the callbacks are added to the call stack.
All the event listener callbacks can be synchronously added to the call stack via `dispatchEvent(event: Event)`.
Events dispatched programatically also follow all the regular bubbling and capturing rules.

When adding multiple callbacks via addEventListener, it's not guaranteed which one will be added the queue first.

## Generators
These are similar to Python generators. You can define one like so: 
```javascript
function* ayo(val) {
    yield 'x';
    yield 'y';
    yield 'z';
}

const genr = ayo();
console.log(genr.next()); // {value: 'x'; done: false;}
console.log(genr.next()); // {value: 'y'; done: false;}
console.log(genr.next()); // {value: 'z'; done: false;}
console.log(genr.next()); // {value: undefined; done: true;}

const othergen = ayo('hey there');
```

like an array, you can iterate over them.

TODO : Learn more about them here : <https://javascript.info/generators>

## Execution flow : 
JS is an interpreted language. Here is how the JS execution engine in the browser
handles it.
1. Parse a line
2. Compile
3. Optimize
3. Execute
5. Garbage Collection

However, to make if faster, there is a Just-In-Time compiler,
which is done by monitoring the code for hot-spots (e.g., code run frequently) and
then compiling / compiling with optimizations so machine code is available fast.

WebAssembly is faster because it is precompiled and optimized for the architecture.
Often the machine code it produces is more optimized than the one JS engine produces.