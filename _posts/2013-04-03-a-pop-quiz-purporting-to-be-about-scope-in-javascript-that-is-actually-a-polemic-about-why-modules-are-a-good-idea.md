---
title: A pop quiz purporting to be about scope in JavaScript that is actually a polemic about why modules are a good idea
author: maryrosecook
layout: post
permalink: /blog/post/a-pop-quiz-purporting-to-be-about-scope-in-javascript-that-is-actually-a-polemic-about-why-modules-are-a-good-idea
categories:
  - Uncategorized
---
This is the blog post version of a lightning talk I gave at [the Recurse Center][1] last week.

I will ask you some questions. For each question, there will be some JavaScript files loaded into an HTML page. In these JavaScript files, there will be some `console.log()` invocations. Your job is to predict what happens after each invocation.

For this first question, there is one JavaScript file, `a.js`, loaded into this HTML page:

``` html
<!doctype html>
<html>
  <head>
    <script src="a.js"></script>
  </head>
</html>
```

### One

<pre class="prettyprint">// a.js
var blah = "blah";
console.log(blah); // Option 1: ReferenceError: blah is not defined
                   // Option 2: print 'blah' to the console
</pre>

`blah` will be printed. The variable, `blah`, is in the same scope as the `console.log()` invocation.

For this rest of the questions, there will be two JavaScript files, `a.js` and `b.js`, loaded into this HTML page:

``` html
<!doctype html>
<html>
  <head>
    <script src="a.js"></script>
    <script src="b.js"></script>
  </head>
</html>
```

### Two

``` javascript
// a.js
blah = "blah";

// b.js
console.log(blah); // Option 1: ReferenceError: blah is not defined
                   // Option 2: print 'blah' to the console
```

`blah` will be printed. It doesn't matter that `blah` was declared in a different file. All files in JavaScript are loaded into a shared scope.

### Three

``` javascript
// a.js
var blah = "blah";

// b.js
console.log(blah); // Option 1: ReferenceError: blah is not defined
                   // Option 2: print 'blah' to the console
```

`blah` will be printed. It doesn't matter that the `var` keyword was used to declare `blah`. The declaration was in a shared scope, and that scope is global, so `blah` becomes a global variable.

### Four

``` javascript
// a.js
var blahFn = function() {
  return "blah";
};

// b.js
console.log(blahFn()); // Option 1: ReferenceError: blah is not defined
                       // Option 2: print 'blah' to the console
```

`blah` will be printed. All the logic shown so far applies to functions.

### Five

``` javascript
// a.js
function blahFn() {
  var blah = "blah";
};

// b.js
blahFn();
console.log(blah); // Option 1: ReferenceError: blah is not defined
                   // Option 2: print 'blah' to the console
```

There will be a reference error. The `blah` variable is created inside a function, in the function's own, private scope. It is not available in the global scope.

### Six

``` javascript
// a.js
function blahFn() {
  blah = "blah";
};

// b.js
blahFn();
console.log(blah); // Option 1: ReferenceError: blah is not defined
                   // Option 2: print 'blah' to the console
```

`blah` will be printed. With the var keyword omitted, the `blah` variable goes into the global scope.

### Seven

``` javascript
// a.js
;(function() {
  function blahFn() {
    return "blah";
  };

  console.log(blahFn()); // Option 1: ReferenceError: blahFn is not defined
                         // Option 2: print 'blah' to the console
})();
```

`a.js` is loaded. The anonymous function at the top is invoked, creating our first JavaScript module. The module instantiates `blahFn`. The `console.log()` invocation prints `blah` because the code inside a module has a shared scope.

``` javascript
// b.js
console.log(blahFn()); // Option 1: ReferenceError: blahFn is not defined
                       // Option 2: print 'blah' to the console
```

When `console.log()` is invoked in `b.js`, a ReferenceError is thrown. The variables inside the module in `a.js` are locked away. The global scope is not polluted.

### Eight

``` javascript
// a.js
;(function(exports) {
  var Blah = {};

  Blah.blahFn = function() {
    return "blah";
  };

  exports.Blah = Blah;
})(this);

// b.js
console.log(Blah.blahFn()); // Option 1: ReferenceError: Blah is not defined
                            // Option 2: print 'blah' to the console
```

When the `console.log()` is invoked, `blah` is printed. The module has made itself available as an object in the global scope. It has also made available one of its variables on this object.

To do this, it made an empty object, `Blah`, and attached `blahFn` to it as an attribute. The anonymous function enclosing the module was passed `this`. Because the anonymous function was invoked in the global context, `this` is the global object, also referenced with `window`. The module attached `Blah` to the `exports/global/window` object, making it available to the `console.log()` invocation.

### Nine

``` javascript
// a.js
(function woo() {
  return function() {
    console.log("woo")
  };
})

// b.js
(function() {
  console.log("boo")
})();
```

What is printed? `woo` or `boo`?

It's `boo`. The code in `a.js` makes a function called `woo` and does nothing with it.

In questions seven and eight, both modules were preceded by a semicolon. Why is this?

Often, a module is put in its own JavaScript file. Often, JavaScript files that are part of a website are joined together. This is part of a process to reduce download time. If you join the `a.js` and `b.js` together and then execute that, `woo` will be printed. The operation of the boo module was changed by the random code preceding it in the `a.js` file. Because the random code in `a.js` had no semicolon at the end, the parentheses around the boo module invoked the `woo` function. This returned the anonymous function inside the `woo` function which was then invoked by the parentheses sitting at the end of `b.js`.

Modules protect themselves from these sorts of nightmares with a leading semicolon.

 [1]: http://recurse.com
