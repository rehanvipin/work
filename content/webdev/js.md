---
title: "JS"
weight: 3
# bookFlatSection: false
# bookCollapseSection: false
# bookComments: true
---

# JavaScript
Notes from the freeCodeCamp "JavaScript Algorithms and Data Structures" course and more

## Basics
* Variable names in JS can contain $, \_, letters, and numbers but cannot start with a number.
Declaring a variable without initalizing it will make it an `undefined` type object and it will have the value `undefined`.
* People like writing JS variable names in camelCase.
* Variables declared with `var` can be declared multiple times.
Trying to re-declare a variable declared with `let` results in a SyntaxError. `const` is like `let` but the variable is read-only.
* JS has the `++` and `--` increment and decrement operators.
The `%` in JS is not a modulo operator, it's a remainder operator and can return negative remainders.
* Values of type `String` are immutable. Like Python.
* Constant variables cannot have their value changed, they cannot be re-declared or re-assigned a different value. 
In this case : `const a = [1, 2, 3]`, the variable `a` holds the reference to the array (since it is not a primitive type). 
Therefore the array itself can be modified (e.g. `a.push(1)` or `a[2] = 7`) since the reference won't change and the "const-ness" still holds.
* The `push` function of an `array` takes in one or more arguments and appends them to the end of the array.
* `shift` and `unshift` remove and add values to the beginning to the start of the array 
just like `pop` and `push` do to the end of the array.
* If a variable is declared without `let`, `const`, or `var` it will have global scope regardless of where it is declared. 
**Explanation**: [There aren't any "global variables" in JS](http://blog.niftysnippets.org/2008/03/horror-of-implicit-globals.html)
(They are there when declared by `let` or `const`).
When you do `wowNiceGlobal = 5` where `wowNiceGlobal` is being referenced for the first time thus making it a "global variable"
and giving it global scope, it really just creates and assigns to a property (with the same name) of **the** global object.
In a browser, the `window` variable is a reference to the global object.
You can stop this property setting from happening by adding "use strict" to your code.
* **Scope**. A variable, declared with `var`, `let`, or `const` at the top level of your code is given global scope. `var` has a function scope. If a `var` variable is defined in some block in a function it will be available throughout the function. `let` is block scoped. It does not exist outside the block. Within their scopes both `let` and `var` variables are "hoisted". This means they are defined at the top of their scopes. `var` variables are initialized with `undefined` while `let` variables are not initialized and will cause a ReferenceError if you try to access them. `const` has the same scoping rules as `let`.
* The `==` operator will perform type coercion if the operands aren't of the same type. The `===` operator doesn't convert types and will return false if the types are different. Like that there are also the `!=` and `!==` operators.
* **Type Coercion**. Primitive types are `number`, `string`, `null`, `undefined`, `boolean` and `symbol`(new, added in ES6). Using [this article about type coercion for reference](https://www.freecodecamp.org/news/js-type-coercion-explained-27ba3d9a2839/) we see that you can explicitly convert and that the only implicit conversion allowed (for primitives and others) is "to string", "to number", and "to boolean".
    * Boolean conversion happens with logical operators (but the result of the expression is still the original type). Objects are always truthy values.
    * String conversion happens with the `+` operator when either operands are of the string type.
    * Number and object conversions are complex. Must check the blog post.
* In a switch statement, once a case has been matched, all statements from there on are executed till a break is encountered (regardless of curly braces). Cases are matched with `===`.
* **Objects**. They are a collection of key: value elements. The keys are called properties. All properties are converted to a string and stored. You can access values of properties of objects via `.` (in case the property string is also a valid identifier) or `[]` (works for all properties).
* You can **delete properties** from objects. Just add the keyword `delete` before accessing the property either via the dot notation or bracket notation.
* To check if an object has a property, use this function: `objkt.hasOwnProperty(propname)` which returns a boolean value.
* The `while` loop and the `for` loops are like C's. The `do{}while()` loop too.
* The RNG for JS is `Math.random` which returns numbers in the range [0, 1).
* When you have a variable of the Function type you can check how many args it takes
using `varName.length` and find out the function name using `varName.name`.

## ES6 (Modern JavaScript)
* You can prevent an object from being modified by passing it to `Object.freeze(objkt)` (Not by making a `const` object).
* You can extend an array (a1) with another array (a2) by using `arr1.concat(arr2)`.
* Variable argument functions are possible using the rest parameter.
You specify a parameter as `...paraName` and when you pass multiple arguments they are all available in array with that name (paraName here).
This is the preffered method over using the `arguments` object that's available for all non-arrow functions.
* Similarly, there is the "spread" operator to unpack an array into multiple elements, this can be useful when the function expects multiple parameters instead of an array. It follows the same syntax. Given an array arr, you can do `console.log(...arr)`.
* The object destructuring operator is a clean shortcut to extract multiple values from an object at once. The most basic way to do it is : `{param1, param2} = objkt`. Suppose you want to use variables of different names you can do this : `{"param1": var1, "param2": var2} = objkt`. Nested objects call for nested destructuring. You can destructure in the function arguments.
* Similarly, there is also array destructuring. You can unpack particular values from the array. Like so, `[a, b,,,, c, d,, e] = [1,2,3,4,5,6,7,8,9,0]`. For the last elements, you can also unpack them into a separate array using the rest operator. So if you want a new array but without the first 4 values you would do : `[,,,, ...new_arr] = arr`.
* Format strings are available as template literals. They are used as \` any text \`. In a template literal you use `${ any expression }` to place run-time values. They preserve all whitespace.
* There is also a shorthand for creating objects. You can just do `{x, y}`. The keys will be `x` and `y` (in string format) respectively.
* When defining functions in objects you can omit the `function` keyword and just do `prop_name(arg1, arg2){}`.
* You can use the `class` keyword to have a `constructor`, `get` and `set` methods, and you can create an object of that type with `new className(constructor args)`.
* You can make the variables, functions, and whatnot of one JS file available to others by "exporting" it. You do this by either adding the `export` keyword to it during delcaration or later on, where you can export multiple vars like `export {var1, func1, var2}`.
* To make use of them in another file you can "import" them like this : `import {var2, func1} from "relative_path_to_other_js_file"`. Or you can import everything from that file and place it under an object like this : `import * as containah from "that_other_file"`. Then access them like `containah.var1`.
* There are also "default exports". This means that you can have one export which is the default thing that will be imported from a file when you do this : `import something from "file"`. To set something as a default export you can do `export default var`. They are useful when a file represents one item and you want to import that in some other file. It isn't particulary useful when a file is meant to do multiple things and has multiple items.
* The **new operator** can be used to create objects from **functions** or **classes**. To use it with a function the function must act like a constructor and set properties to `this`. To use it with a class, the class must have a constructor.
* **Promise** is a feature to execute code asynchronously. You can create a `Promise` object with the `Promise` function. It expects you to take in two arguments (both of which are callbacks). As a convention we create a new promise object like this : `let prms = new Promise((resolve, reject) => {});`.
* A promise can be in one of three states: `pending`, `fulfilled`, and `failed`. It is `pending` when the promise is created, `fulfilled` when the `resolve` function is called, and `failed` when the `reject` function is called. Both `resolve` and `reject` take one argument each.
* You can set hanlders for a promise by calling `.then(resolveFunc, rejectFunc)` or `.catch(rejectFunc)`. 
You can pass a function `resolveFunc` (executed when the promise enters the "fulfilled" state)
which takes one argument, i.e. the value passed to `resolve`.
You could also pass the `rejectFunc` (executed when the promise enters the "failed" state)
that takes in one argument i.e., the value passed to `reject`.
* To collect multiple promises into one there's the `Promise.All` function which takes in an iterable and returns a promise which
resolves with an iterable of all resolved values of input promises or rejects when any of the input promises reject.
* It's possible to have multiple handlers for a promise. e.g., `prms.then(() => {alert(1);}); prms.then(() => {alert(2);});`
* It's also possible to chain handlers because `.then()` returns an object which the `.catch` and `.then` functions.
Therefore you could do
`prms.then(rsVal => new Promise((rs, rj) => rs(1)).then(rsVal => new Promise((rs, rj) => rs(3))).catch(rjVal => console.log(rjVal))`

## Regular Expressions
* You can specify a required number of matches, more specific than `*` or `+`. The format is `(some matching pattern){min, max}`.
You can omit min or max if you don't want a lowerbound or upperbound, respectively.
To specify a particular number of matches, use `(pattern){exactNum}`.
## Debugging
console.clear() is useful

## Basic Data Structures
* `Array.splice(indexToStartRemoving, numberOfElementsToRemove, ...itemsToAdd);` allows you to remove elements from anywhere in the array and it returns a subarray of elements removed.
* The third and later arguments of spilce allow you to specify elements that should be put in the place of elements that were removed (can be more or less than the number of elements that were removed)
* `Array.slice(indexToStart[inclusive], indexToEnd[exclusive]);` allows you to extract a part of an array without modifying the array
* `Array.indexOf(elem)` returns the first index of an element in an array or -1 if it is not found.
* A shortcut way to check if an object has a property is `propName in objkt` which returns a boolean value.
* An Array is also really just an object where the keys are indices.
* To loop over the keys of an object there is a convenient format : `for(let keyVar in objkt) { }`
* To get all the keys in an array you can do `object.Keys(objkt);`

## Basic Algorithm Scripting
* `String.prototype.toUpperCase()` is nice

## Object Oriented Programming
* The `this` keyword when used within an object defintion refers to the object itself. You can access other properties of the object with this.
* A constructor function can be used to create similar objects. By convention these functions' names start with an uppercase letter and they don't return anything. They make use of `this` to set the object's properties. They are used along with the `new` operator which creates an object so that the use of `this` inside the function makes sense.
* `instaceof` can be used like `objkt instaceof Constrktr` which returns a boolean value to see if an object was instantiated with a particular constructor.
* By default all objects have their own instances of each property, which takes up memory. To have a common instance for all objects of a particular type you can use a **prototype**. Like so : `Cars.prototype.speed = 60;` and all objects of the type Cars will have a property `speed` that is equal to 60.
* There are two types of properties : **own** and **prototype**. You can check which category a property belongs to by using `objkt.hasOwnProperty(propName)`
* It is convenient to set the `prototype` of a type to a new object with the required properties. Remember to set the `constructor` property of `prototype`.

### Prototype chain and inheritance
All objects in JS have properties and then a link to another object (called the **prototype** of this object). Since the prototype itself is an object, it will have a link to its own prototype. This is called the prototype chain and it ends when an object's prototype is set to `null` as `null` has no prototype.  
When you try to access a property of an object, it is first checked in the object's properties, then the properties of its prototype and so on until it hits `null`.  
* Primitives (Number, undefined, null, etc) don't have methods
* Objects are stored in a variable by reference to the location in memory
* **FUNCTIONS ARE OBJECTS.** They are constructed by the function `Function` which has properties like `call`, `name`, `arguments`, and so on, which was constructed by the function `Object` which has properties like `toString`, `hasOwnProperty`, and so on.
* Let `Car` be a function object that can be used as a constructor. Let `maserati` be an object created by that function. `Car.prototype` is the prototype object of all objects created by `Car`. `maserati.__proto__` points to `Car.prototype`. 
* All functions, just like `Car`, have a `prototype` property. Other than anonymous functions. That is why anonymous functions are not constructors and cannot be used create objects.
* Since Object is a function (i.e. an object that is callable) `Object.prototype` is `{constructor: ƒ, __defineGetter__: ƒ, __defineSetter__: ƒ, hasOwnProperty: ƒ, __lookupGetter__: ƒ, …}`  
* Some objects and their prototypes:
```js
var o = {a: 1};
var b = ['yo', 'whadup', '?'];
function f() {
  return 2;
}
```  
    * The prototype of `o` is `Object.prototype`
    * The prototype of `b` is `Array.prototype`
    * The prototype of `f` is `Function.prototype`
* If an object is created with `let newob = Object.create(objkt)`, the prototype of the new object is the argument passed to `Object.create`. In this case `newob.__proto__` is `objkt`.
* Object creation, when done like this:
```js
var o = new Foo();
```  
is essentially this:
```js
var o = new Object();
o.[[Prototype]] = Foo.prototype;
Foo.call(o); // this is defined as Function.prototype.call(thisArg, ...otherArgs) { ... }
```
* To perform "inheritance" between function `A` and function `B`, i.e. to make A inherit from B, you just need to set `A.prototype = Object.create(B.prototype); A.prototype.constructor = A`.
* **Mixin** is a way to add properties to objects without multiple inhertiance. It can be done like this: 
```js
function flyMixin(obj) {
    obj.altitude = obj.wingspan * 3;
    obj.fly = function() {
        console.log("I believe I can fly...");
    }
}
```
* **IIFE** (Immediately Invoked Function Expression) is an anonymous function that is called right where it is defined. Like this `( () => {...} )()`.

## Functional Programming
A key point of FP is that functions don't rely on external parameters (global states) and they don't change those parameters. The same input gives the same output.  
* Functions that take in other functions as arguments or return functions are known as **higher order functions**.
* `Array.prototype.map(callback)` is useful for FP. Using it on an array applies the callback function on each element and returns a new array with the new elements. The arguments passed to the callback function are: **the current element, the index of the element, and the array that map is operating on.**
* Even if a function takes in **n** arguments you can pass it more args (in which case it will ignore the extra args) or fewer args (in which case the other ones will have be of the type `undefined`).
* `Array.prototype.filter(callback)` is also useful for FP. The returned array will only contain the elements for which the callback function returned true. The arguments passed to the callback function are the same as the map function.
* `Array.prototype.concat(otherArray)` returns a new array which has all the elements of the array on which it is called followed by the elements of the array which is passed as an argument.
* `Array.prototype.reduce(callback, initValue(optional))` can be implemented like this (example): 
```js
Array.prototype.redush = function(callback, initValue=undefined) {
  let start = 0;
  if(initValue === undefined) {
    initValue = this[0];
    start = 1;
  }
  for(let i=start; i<this.length; i++)
    initvalue = callback(initValue, this[i], i, this);
  return initValue;
}
```
* `Array.prototype.sort(callback)` returns a new array which is the array, sorted using the callback function. The rule for sorting is defined by callback(a, b):
    * Less than 0 => a will be before b
    * Equal to 0 => positions will not be modified
    * Greater than 0 => b will be before a
Sorting also modifies the original array.
* `Array.prototype.every(callback)` returns a boolean value which tells if all the elements in the array return true for the callback. There's also `Array.prototype.some(callback)`.

### `this` ?
`this` is a keyword that has different values depending on the scope it is accessed from. When accessed inside a free function it has the value of the global `this` (or `undefined` when strict mode is activated). When the function is a property of an object and called like `objkt.funk(...args)` `this` will be the object. If it's an **arrow function** `this` will be the same as `this` in the parent scope.  
You can also set what `this` will be used inside a function. One way to change `this` is using `funk.call(newThis)` which will call that function with a new `this` for that invocation only.  
Like `.call(newThis)`, there's also `.bind(newThis)` which returns a function which calls the original function but while setting its `this` to `newThis`. E.g. `funk.bind(newThis)()`.  
`.bind(newThis, ...args)` can also be used to create partial functions. You can set some parameters of the function and those won't be required in the new function that `bind` returns.

## Random questions
### Symbols?
They're a special data type and the only one other than string which can be used as a key for an object (all other types are converted to string first).

You can create one like so: `let zoomZoom = Symbol()`.  And use it as a key like so: `let obj = {[zoomZoom]: 'cool key', 123: 'not so cool key'}; console.log(obj[zoomZoom])`.

A symbol cannot be converted to string automatically. To make that happen, use `zoomZoom.toString()`.

Symbols can also optionally have a "description" that you can provide while creation like so: 
```js 
let sym1 = Symbol('the best one'); 
let sym2 = Symbol('the best one'); 
let sym3 = Symbol('some random one'); 
console.log(sym1 == sym2); // prints false 
```
note that even if they have the same description, the symbols are different.

The symbol keys of an object don't show up in `Object.keys` or in the `for in` loop.  To get all the symbol keys of an object, use
`Object.getOwnPropertySymbols(obj)`.

We've seen that every time we create a symbol, we get a new one, even if we use
the same description.  What if we want JS to give us the same symbol in case the
description we provided is the same? Do it like this: 
```js
let sym4 = Symbol.for('nice key');
let sym5 = Symbol.for('nice key');
let sym6 = Symbol.for('bad key');
console.log(sym4 === sym5); // prints true
console.log(sym5 === sym6); // prints false
console.log(Symbol.keyFor(sym4); // prints 'nice key'
console.log(Symbol.keyFor(sym1); // prints undefined since it was not created by Symbol.for, thus doesn't exist in the global registry
```
This method of creating symbols uses a global registry and checks it for
existing descriptions before returning a new symbol.

There are also some inbuilt symbols like this one: `Symbol.iterator`.

### primitive properties? 
How can primitive types be assigned properties and methods be called on them?  That's because when some property is assigned or a
method is called, the primitive is wrapped in a temporary object.  That
temporary object is discarded after the operation.  Examples:
```js
let a = "hi there"; 
a.coolstuff = 123; 
console.log(a.coolstuff); // undefined
console.log("hiphop".includes("ip")); // true
```

### copying stuff
To deep copy just do `structuredClone(objToCopy)`. It has good support [as per caniuse](https://caniuse.com/mdn-api_structuredclone).

### nullish coalescing
`a || b` will evalaute to `b` in case `a` is any falsy value.
Then there is nullish coalescing which tests for nullish values i.e.,
`a ?? b` will evaluate to `b` only when when `a` is `null` or `undefined`.

There is also [logical nullish assignment](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_nullish_assignment)
`??=` which assigns a value only if the variable is nullish.

### event bubbling, capturing, and delegation
Events on an element are first captured by document, then `<html>` then `<body>` and so on
until it reaches the element itself. Then the event is "processed" at the target.
Then the event bubbles up to the parent element and it's parent and so on until document.
If there are any handlers in the path, they are run.

An element could also have multiple handlers for the same event.
In case of multiple handlers on an element for an event, the order they run is browser specific.

To register an event handler you need to specify:
* The event type (e.g. 'click')
* The event handler (a function which will receive an event object)
* An optional boolean to tell whether the handler should run in the capturing phase or not
To remove an event handler you need to specify the same handler function and phase.

Not all events bubble (e.g. 'hover').
The event object has `event.target` and `event.currentTarget`
(the element where the handler is running).

You can stop propagation of an event (upwards / downwards) using `event.stopPropagation()`.
To also stop all other handlers of that event on this element running, use
`event.stopImmediatePropagation()`. To prevent the default behaviour, use `event.preventDefault()`.

Sometimes you may want the same behaviour out of many elements 
(e.g., `<li>`s in a `<ul>` or `<td>`s in a `<table>`)
there's no need to set event handler to each element, you can set it on a common ancestor.
That's called **event delegation**. You can do it like so:
```js
table.addEventListener('click', (event) => {
  let td = event.target.closest('td'); // did the event occur on a td or its children?

  if (!td) return; // if not, exit

  if (!table.contains(td)) return; // make sure the td belongs to this table not some other

  td.classList.add('highlight');
});
```

### closure
Good resource:
https://github.com/getify/You-Dont-Know-JS/blob/2nd-ed/scope-closures/ch7.md

A reference of a variable from an outer scope inside a function is called a closure.
The function _closes_ over the outer variables.
The outer variables don't get garbage collected even when their scopes get over
(as long as the functions exist).

Closures are associated with instances of functions and not their definitions.
Note that the closure is a reference to the outer variable and not a snapshot of it's value
when the function was created (which was a common misconception).

Also note that a closure only requires an "inner" function.
The outer scope need not be a function, it could be a block too. e.g.,
```js
var hits;
{   // an outer scope (but not a function)
    let count = 0;
    hits = function getCurrent(){
        count = count + 1;
        return count;
    };
}
hits();     // 1
hits();     // 2
hits();     // 3
```

That is important in understanding the difference between
```js
var fns = [];

for (let i = 0; i < 3; i++) {
    fns[i] = () => i;
}

console.log(fns[0](), fns[1](), fns[2]());
// 0 1 2
```
and
```js
var fns = [];

for (var i = 0; i < 3; i++) {
    fns[i] = () => i;
}

console.log(fns[0](), fns[1](), fns[2]());
// 3 3 3
```

In the second case, since `i`'s scope is global and it won't be GC'd before the functions are used,
each function need not have a separate live copy (which is what happens in the first case).

That also explains the behaviour in these examples:
```js
var fns = [];

for (let i = 0; i < 3; i++) {
    fns[i] = () => (i += 2);
}

console.log(fns[0](), fns[1](), fns[2]());
// 2 3 4
// i.e., there are three different instances of the closed variable
```

whereas in the case of `var` it becomes
```
var fns = [];

for (var i = 0; i < 3; i++) {
    fns[i] = () => (i += 2);
}

console.log(fns[0](), fns[1](), fns[2]());
// 5 7 9
// i.e., they're all updating the same global i
```
in fact the examples with `var` aren't even closure really since the global variables
are always available.

Note: because closures delay GC they could lead to excess memory usage.

Technically a function instance only closes over the variable it uses from the outer scope
and all other variables in the outer scope can be GCd when the scope gets over.
However that's not what really happens. Since the interpreter cannot always know what variables
from the outer scope are being accessed (looking at you, `eval`), it might end up keeping the entire
outer scope if it thinks there is a possibility of a closure.
Thus, when using closures, try to clear up the variables in the outer scope by setting them to `null`.
Also, one more reason to avoid using `eval`.
