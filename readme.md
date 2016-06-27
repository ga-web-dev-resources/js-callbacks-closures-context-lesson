---
title: JS Callbacks, Closures and Context
duration: "1:25"
creator:
    name: Mike Dang
    city: Austin
---

# ![](https://ga-dash.s3.amazonaws.com/production/assets/logo-9f88ae6c9c3871690e33280fcf557f33.png) JS Callbacks, Closures and Context

### LEARNING OBJECTIVES
*After this lesson, you will be able to:*

* Discuss callbacks for sync/async functions
* Discuss functions as first class objects
* Checking for the correct type for function parameters
* Explain what closures are
* Review context - `this` vs `self`
* **ES6:** Variable scope with `let`
* **ES6:** `const` keyword
* **ES6:** Arrow functions
 - `this` with arrow functions
* **ES6:** String interpolation with `${}`


### COMPONENTS

- Lecture


### Lesson Objectives

---

## Review

- JS object creation
  * Constructor functions
    - A constructor is any function used to create an object in JS
    - Start with a capital letter for the function name
  * `new` keyword
- Inheritance w/ object prototype
  * All objects have a link to another object, we call this other object the "prototype"
  * The prototype chain
  * What's at the top of the chain? `Object`

```js
// Constructor function example
function Person(name) {
  this.name = name;
  this.sayHello = function() {
   console.log('Hi there, my name is ' + this.name);
  }
}
```

When do we use the object literal to create an object vs using a constructor object to create a new object?
  * Use an object literal for "singletons", where it doesn't make sense to have multiple copies of the same object
  * Use the constructor function when there can be multiple versions of the same object

```js
// This doesn't make sense since we can have multiple "people" objects, we rather create it using a constructor function
var person = {
 name: 'Mike'
}
```

### Callbacks

In JavaScript, functions are first class objects. This means that they are of type `Object` and can be used the same as any other first class data types in JavaScript such as strings and arrays. They can be stored as variables, passed as arguments to functions, created from functions, and returned from other functions.

A callback function is a function passed to another function as an argument. This function can then be executed at a later time. Callback functions are the most common technique you'll see in JavaScript/jQuery code. They are used widely in Node.js due to the asynchronous nature of it.

> Callbacks are also known as higher order functions. Higher order functions can take one or more functions as arguments and also are able to return a function as its result if needed.

```js
// We've already been using callback functions with event listeners!
// Executes when some event has occurred
$('.btn').click(function() {
  alert('A button was clicked! Woohoo!');
});

var clicked = function() {
  alert('A button was clicked! Woohoo!');
};
$('#btn-1').click(clicked);
```

```js
// Synchronous callbacks (Executes immediately)
var friends = ["Mike", "Stacy", "Andy", "Rick"];

friends.forEach(function(eachName, index){
  console.log(index + 1 + ". " + eachName); // 1. Mike, 2. Stacy, 3. Andy, 4. Rick​
});

console.log("I'm done running");
```

```js
// Async callbacks (We don't know when it will get executed)
$('#content').on('click', function() {
  console.log("I've actually run now");
});

console.log("I haven't executed the event handler yet");
```

```js
// Executes at a specific time once
var atFiveSeconds = setTimeout(function() {
  alert('Good afternoon!');
}, 5000);

// Executes at a specific interval repeatedly
var everyFiveSeconds = setInterval(function() {
  console.log('Oh, hello again!');
}, 5000);
```

We're going to see a **ton** of callback functions in Node.js this week
 * Database access
 * Network access
 * File I/O

#### Checking that a callback parameter is callable

```js
function getInput(options, callback) {
    allUserData.push(options);
​
    // Make sure the callback is a function​
    if (typeof callback === "function") {
    // Call it, since we have confirmed it is callable​
        callback(options);
    }
}
```

### ES6 Intro

> ECMAScript (or ES) is a trademarked scripting-language specification standardized by Ecma International. Well-known implementations of the language, such as JavaScript, JScript and ActionScript have come into wide use for client-side scripting on the Web.

> ECMAScript 2015 is the newest version of the ECMAScript standard. This standard was ratified in June 2015. ES2015 is a significant update to the language, and the first major update to the language since ES5 was standardized in 2009. Implementation of these features in major JavaScript engines is underway now.

Confused by what all the below refers to? It's easy, they're all the same thing.  

* ES6
* ECMAScript6
* ES6 Harmony
* ES2015
* ECMAScript 2015

#### Client Side

As of this writing, most browsers do not fully support ES6, however we can use a JavaScript compiler like [Babel](http://babeljs.io/) with a build tool like [Webpack](https://webpack.github.io/) or [Browserify](http://browserify.org/) to convert ES6 to ES5 JavaScript.

#### Node.js

It's safe to use most features of ES6 (check the [Node version documentation](https://kangax.github.io/compat-table/es6/) to see what's ok to use) since we can set what version of Node.js we want to use on our server.

## Exercise

**Pair Exercise - 10 min**

https://gist.github.com/mdang/0a5b081af0c70068a82d

### Scope Review

- Variable scope
  * Global scope
  * Local scope

### Closures

A closure is an inner function with access to an outer function's variables. They allow us to mimic private methods in other programming languages.

You create a closure by adding a function inside another function.

Closures have access to three scope chains:
 - Local scope (variables defined within the function itself)
 - Outer scope (variables defined in the parent function)
 - Global scope

##### Private properties & methods

```js
function person() {
    // Users will be unable to directly access this value
    var personId = 999;

    return {
        getId: function()  {
          return personId;
        },
        setId: function(newId)  {
            personId = newId;
        }
    }
}

var personOne = person();
personOne.setId(900);

console.log(personOne.getId());
```

```js
function makeCounter() {
  var privateCounter = 0;

  function changeBy(val) {
    privateCounter += val;
  }

  return {
    increment: function() {
      changeBy(1);
    },
    decrement: function() {
      changeBy(-1);
    },
    value: function() {
      return privateCounter;
    }
  }  
}

var counter1 = makeCounter();
var counter2 = makeCounter();

console.log(counter1.value()); /* Logs 0 */
counter1.increment();
counter1.increment();
console.log(counter1.value()); /* Logs 2 */
counter1.decrement();
console.log(counter1.value()); /* Logs 1 */
console.log(counter2.value()); /* Logs 0 */
```

##### Closures can be used to solve problems with binding async calls to values

Given the following HTML:

```html
<div id="div1">One</div>
<div id="div2">Two</div>
<div id="div3">Three</div>
<div id="div4">Four</div>

You clicked on div #<span id="result"></span>
```

```js
// The following code will report the incorrect value, the last value bound to `i` no matter what you click on
for (var i=1; i<=4; i++) {
  $('#div' + i).click(function () {
    $('#result').text(i);
  });
}
```

```js
// Create a new closure, a separate scope for the variable. Below is an instantly executed anonymous function, which receives the variable and stores its state as an argument
for (var i=1; i<=4; i++) {
  (function(i) {
    $('#div' + i).click(function () {
      $('#result').text(i);
    });
  })(i);
}
```

#### `let` is the new `var`

* `let` variables are block-scoped
* It's an error to try to use a `let` variable before its declaration is reached
* Redeclaring a variable with `let` is a SyntaxError, even global ones
* Loops of the form `for (let x...)` create a fresh binding for `x` in each iteration

```js
// In ES6, the same problem above can be solved by just using `let` instead of `var`
for (let i=1; i<=4; i++) {
  $('#div' + i).click(function () {
    $('#result').text(i);
  });
}
```

#### `const`

* Block scoped like `let`
* You must specify its value in the same statement in which it's declared
* Value of a constant cannot change through re-assignment
 - However, the actual value can change. For example, an object assigned as a constant can still have properties altered
* Cannot be redeclared

### Context

- `this` keyword within objects
- `this` within constructor functions
  * We **must** use the `new` keyword to create objects from functions, otherwise the `this` keyword will be attached to the window object instead of the function
- `this` within the window
- `this` within callbacks
- `self` as a way to access a parent function/object
  * The `var self = this` is often needed in situations like an asynchronous action such as event binding, AJAX handlers etc, where the resolution of `this` is deferred until it equals something else

```js
// Using `self` to preserve context
function SeatReservation(name) {
  var self = this;
  self.name = name;

  setTimeout(function () {
    // ES6: String interpolation
    alert(`A seat is now reserved for ${self.name}`); // otherwise, this is window; use self to keep a reference to the "reserveSeat" function itself
  }, 100);
}
```

### Arrow functions

```js
// Without arrow function
function SeatReservation(name) {
    var self = this;
    self.name = name;

    setTimeout(function () {
        // otherwise, this is window; use self to keep a reference to the "reserveSeat" function itself
        console.log(self.name);
    }, 100);
}

var mike = new SeatReservation('Mike');
```

```js
// With arrow function
function SeatReservation(name) {
    this.name = name;

    // Notice with arrow functions, `this` refers to the outer function
    setTimeout(() => console.log(this.name), 100);
}

var mike = new SeatReservation('Mike');
```

```js
// ES6 Heaven
class SeatReservation {
	constructor(name) {
		this.name = name;

		setTimeout(() => console.log(`A seat is reserved for ${this.name}`), 100);
	}
}

let mike = new SeatReservation('Mike');
```

### Reference

- http://javascriptissexy.com/understand-javascript-callback-functions-and-use-them/
- http://javascriptissexy.com/understand-javascript-closures-with-ease/
- https://strongloop.com/strongblog/an-introduction-to-javascript-es6-arrow-functions/
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures#Practical_closures
- https://hacks.mozilla.org/2015/07/es6-in-depth-let-and-const/
- http://stackoverflow.com/a/30479554
- https://babeljs.io/docs/learn-es2015/
