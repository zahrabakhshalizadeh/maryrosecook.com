---
title: Little Lisp interpreter
author: maryrosecook
layout: post
permalink: /post/little-lisp-interpreter
categories:
  - Uncategorized
---
[Little Lisp][1] is an interpreter that supports function invocation, lambdas, lets, ifs, numbers, strings, a few library functions, and lists. I wrote it for a lightning talk at [Hacker School][2] to show how easy it is to write an interpreter. The [code][1] is 116 lines of JavaScript. I will explain how it works.

First, let&#8217;s learn some Lisp.

An atom, the simplest Lisp form:

<pre class="prettyprint">1
</pre>

Another atom. This time, a string:

<pre class="prettyprint">"a"
</pre>

An empty list:

<pre class="prettyprint">()
</pre>

A list containing an atom:

<pre class="prettyprint">(1)
</pre>

A list containing two atoms:

<pre class="prettyprint">(1 2)
</pre>

A list containing an atom and another list:

<pre class="prettyprint">(1 (2))
</pre>

A function invocation. This comprises a list where the first element is the function and the rest of the elements are the arguments. `first` takes one argument, `(1 2)`, and returns `1`.

<pre class="prettyprint">(first (1 2))

 => 1
</pre>

A lambda. That is: a function definition. The lambda takes a parameter, `x`, and just returns it.

<pre class="prettyprint">(lambda (x)
 x)
</pre>

A lambda invocation. This comprises a list where the first element is a lambda and the rest of the elements are the arguments. The lambda takes one argument, `"Lisp"`, and returns it.

<pre class="prettyprint">((lambda (x)
  x)
 "Lisp")

 => "Lisp"
</pre>

Writing a Lisp interpreter is really easy.

The code for Little Lisp has two parts: the parser and the interpreter.

The parser.

Parsing has two phases: tokenizing and parenthesizing.

`tokenize()` takes a string of Lisp code, puts spaces around every parenthesis and splits on whitespace. For example, it takes something like `((lambda (x) x) "Lisp")`, transforms it into ` ( ( lambda  ( x )  x )  "Lisp" ) ` and transforms that into `['(', '(', 'lambda', '(', 'x', ')', 'x', ')', '"Lisp"', ')']`.

<pre class="prettyprint linenums">var tokenize = function(input) {
   return input.replace(/\(/g, ' ( ')
               .replace(/\)/g, ' ) ')
               .trim()
               .split(/\s+/);
 };
</pre>

`parenthesize()` takes the tokens produced by `tokenize()` and produces a nested array that mimics the structure of the Lisp code. Each atom in the nested array is labelled as an identifier or a literal. For example, `['(', '(', 'lambda', '(', 'x', ')', 'x', ')', '"Lisp"', ')']` is transformed into:

<pre class="prettyprint">[[{ type: 'identifier', value: 'lambda' }, [{ type: 'identifier', value: 'x' }],
  { type: 'identifier', value: 'x' }],
 { type: 'literal', value: 'Lisp' }]
</pre>

`parenthesize()` goes through the tokens, one by one. If the current token is an opening parenthesis, it starts building a new array. If the current token is an atom, it labels it with its type and appends it to the current array. If the current token is a closing parenthesis, it stops building the current array and continues building the enclosing array.

<pre class="prettyprint linenums">var parenthesize = function(input, list) {
   if (list === undefined) {
     return parenthesize(input, []);
   } else {
     var token = input.shift();
     if (token === undefined) {
       return list.pop();
     } else if (token === "(") {
       list.push(parenthesize(input, []));
      return parenthesize(input, list);
    } else if (token === ")") {
      return list;
    } else {
      return parenthesize(input, list.concat(categorize(token)));
    }
  }
};
</pre>

When `parenthesize()` is first called, the `input` parameter contains the array of tokens returned by `tokenize()`. For example:

<pre class="prettyprint">['(', '(', 'lambda', '(', 'x', ')', 'x', ')', '"Lisp"', ')']
</pre>

When `parenthesize()` is first called, the `list` parameter is undefined. Lines 2-3 run and `parenthesize()` recurses with `list` set to an empty array.

In the recursion, line 5 runs and removes the first opening parenthesis from `input`. Line 9 starts a new, empty list by recursing with a new, empty array.

In the recursion, line 5 runs and removes another opening parenthesis from `input`. Line 9 starts another new, empty list by recursing with another new, empty array.

In the recursion, `input` is `['lambda', '(', 'x', ')', 'x', ')', '"Lisp"', ')']`. Line 14 runs with `token` set to `lambda`. It calls `categorize()` and passes `lambda` as the `input` argument. Line 7 of `categorize()` runs and returns an object with `type` set to `identifier` and `value` set to `lambda`.

<pre class="prettyprint linenums">var categorize = function(input) {
   if (!isNaN(parseFloat(input))) {
     return { type:'literal', value: parseFloat(input) };
   } else if (input[0] === '"' &#038;&#038; input.slice(-1) === '"') {
     return { type:'literal', value: input.slice(1, -1) };
   } else {
     return { type:'identifier', value: input };
   }
 };
</pre>

Line 14 of `parenthesize()` appends to `list` the object returned by `categorize()` and recurses with the rest of the input and `list`.

<pre class="prettyprint linenums">var parenthesize = function(input, list) {
   if (list === undefined) {
     return parenthesize(input, []);
   } else {
     var token = input.shift();
     if (token === undefined) {
       return list.pop();
     } else if (token === "(") {
       list.push(parenthesize(input, []));
      return parenthesize(input, list);
    } else if (token === ")") {
      return list;
    } else {
      return parenthesize(input, list.concat(categorize(token)));
    }
  }
};
</pre>

In the recursion, the next token is a parenthesis. Line 9 of `parenthesize()` starts a new, empty list by recursing with an empty array. In the recursion, `input` is `['x', ')', 'x', ')', '"Lisp"', ')']`. Line 14 runs with `token` set to `x`. It makes a new object with a value of `x` and a type of `identifier`. It appends this object to `list` and recurses.

In the recursion, the next token is a closing parenthesis. Line 12 runs and returns the completed `list`: `[{ type: 'identifier', value: 'x' }]`.

`parenthesize()` continues recursing until it has processed all of the input tokens. It returns the nested array of typed atoms.

`parse()` is the successive application of `tokenize()` and `parenthesize()`:

<pre class="prettyprint linenums">var parse = function(input) {
   return parenthesize(tokenize(input));
 };
</pre>

Given a starting input of `((lambda (x) x) "Lisp")`, the final output of the parser is:

<pre class="prettyprint">[[{ type: 'identifier', value: 'lambda' }, [{ type: 'identifier', value: 'x' }],
  { type: 'identifier', value: 'x' }],
 { type: 'literal', value: 'Lisp' }]
</pre>

The interpreter.

After parsing is complete, interpreting begins.

`interpret()` receives the output of `parse()` and executes it. Given the output from the parsing example above, `interpret()` would construct a lambda and invoke it with the argument `"Lisp"`. The lambda invocation would return `"Lisp"`, which would be the output of the whole program.

As well as the input to execute, `interpret()` receives an execution context. This is the place where variables and their values are stored. When a piece of Lisp code is executed by `interpret()`, the execution context contains the variables that are accessible to that code.

These variables are stored in a hierarchy. Variables in the current scope are at the bottom of the hierarchy. Variables in the enclosing scope are in the level above. Variables in the scope enclosing the enclosing scope are in the level above that. And so on. For example, in the following code:

<pre class="prettyprint linenums">((lambda (a)
   ((lambda (b)
     (b a))
    "b"))
  "a")
</pre>

On line 3, the execution context has two active scopes. The inner lambda forms the current scope. The outer lambda forms an enclosing scope. The current scope has `b` bound to `"b"`. The enclosing scope has `a` bound to `"a"`. When line 3 runs, the interpreter tries to look up `b` in the context. It checks the current scope, finds `b` and returns its value. Still on line 3, the interpreter tries to look up `a`. It checks the current scope and does not find `a`, so it tries the enclosing scope. There, it finds `a` and returns its value.

In Little Lisp, the execution context is modeled with an object made by calling the `Context` constructor. This takes `scope`, an object that contains variables and their values in the current scope. And it takes `parent`. If `parent` is `undefined`, the scope is the top, or global, or outermost one.

<pre class="prettyprint linenums">var Context = function(scope, parent) {
   this.scope = scope;
   this.parent = parent;

   this.get = function(identifier) {
     if (identifier in this.scope) {
       return this.scope[identifier];
     } else if (this.parent !== undefined) {
       return this.parent.get(identifier);
    }
  };
};
</pre>

We have seen how `((lambda (x) x) "Lisp")` gets parsed. Let us see how the parsed code gets executed.

<pre class="prettyprint linenums">var interpret = function(input, context) {
   if (context === undefined) {
     return interpret(input, new Context(library));
   } else if (input instanceof Array) {
     return interpretList(input, context);
   } else if (input.type === "identifier") {
     return context.get(input.value);
   } else {
     return input.value;
  }
};
</pre>

The first time `interpret()` is called, `context` is `undefined`. Lines 2-3 are run to make an execution context.

When the initial context is instantiated, the constructor function takes the `library` object. This contains the functions built in to the language: `first`, `rest` and `print`. These functions are written in JavaScript.

`interpret()` recurses with the original `input` and the new `context`.

`input` contains the full example output from the parsing section:

<pre class="prettyprint">[[{ type: 'identifier', value: 'lambda' }, [{ type: 'identifier', value: 'x' }],
  { type: 'identifier', value: 'x' }],
 { type: 'literal', value: 'Lisp' }]
</pre>

Because `input` is an array and `context` is defined, lines 4-5 are run and `interpretList()` is called.

<pre class="prettyprint linenums">var interpretList = function(input, context) {
   if (input.length > 0 &#038;&#038; input[0].value in special) {
     return special[input[0].value](input, context);
   } else {
     var list = input.map(function(x) { return interpret(x, context); });
     if (list[0] instanceof Function) {
       return list[0].apply(undefined, list.slice(1));
     } else {
       return list;
    }
  }
};
</pre>

In `interpretList()`, line 5 maps over the input array and calls `interpret()` on each element. When `interpret()` is called on the lambda definition, `interpretList()` gets called again. This time, the `input` argument to `interpretList()` is:

<pre class="prettyprint">[{ type: 'identifier', value: 'lambda' }, [{ type: 'identifier', value: 'x' }],
  { type: 'identifier', value: 'x' }]
</pre>

Line 3 of `interpretList()` gets called, because `lambda`, the first element in the array, is a special form. `special.lambda()` is called to create the lambda function.

<pre class="prettyprint linenums">var special = {
   lambda: function(input, context) {
     return function() {
       var lambdaArguments = arguments;
       var lambdaScope = input[1].reduce(function(acc, x, i) {
         acc[x.value] = lambdaArguments[i];
         return acc;
       }, {});
 
      return interpret(input[2], new Context(lambdaScope, context));
    };
  }
};
</pre>

`special.lambda()` takes the part of the input that defines the lambda. It returns a function that, when invoked, invokes the lambda on some arguments.

Line 3 begins the definition of the lambda invocation function. Line 4 stores the arguments passed to the lambda invocation. Line 5 starts creating a new scope for the lambda&#8217;s invocation. It reduces over the part of the input that defines the parameters of the lambda: `[{ type: 'identifier', value: 'x' }]`. It adds a key/value pair to the lambda scope for each lambda parameter in `input` and argument passed to the lambda. Line 10 invokes the lambda by calling `interpret()` on the lambda body: `{ type: 'identifier', value: 'x' }`. It passes in the lambda context that contains the lambda&#8217;s scope and the parent context.

The lambda is now represented by the function returned by `special.lambda()`.

`interpretList()` continues mapping over the `input` array by calling `interpret()` on the second element of the list: the `"Lisp"` string.

<pre class="prettyprint linenums">var interpret = function(input, context) {
   if (context === undefined) {
     return interpret(input, new Context(library));
   } else if (input instanceof Array) {
     return interpretList(input, context);
   } else if (input.type === "identifier") {
     return context.get(input.value);
   } else {
     return input.value;
  }
};
</pre>

This runs line 9 of `interpret()` which just returns the `value` attribute of the literal object: `'Lisp'`. The map operation on line 5 of `interpretList()` is complete. `list` is:

<pre class="prettyprint">[function(args) { /* code to invoke lambda */ },
 'Lisp']
</pre>

Line 6 of `interpretList()` runs and finds that the first element of `list` is a JavaScript function. This means that the list is an invocation. Line 7 runs and invokes the lambda, passing the rest of `list` as arguments.

<pre class="prettyprint linenums">var interpretList = function(input, context) {
   if (input.length > 0 &#038;&#038; input[0].value in special) {
     return special[input[0].value](input, context);
   } else {
     var list = input.map(function(x) { return interpret(x, context); });
     if (list[0] instanceof Function) {
       return list[0].apply(undefined, list.slice(1));
     } else {
       return list;
    }
  }
};
</pre>

In the lambda invocation function, line 8 calls `interpret()` on the lambda body, `{ type: 'identifier', value: 'x' }`.

<pre class="prettyprint linenums">function() {
   var lambdaArguments = arguments;
   var lambdaScope = input[1].reduce(function(acc, x, i) {
     acc[x.value] = lambdaArguments[i];
     return acc;
   }, {});

   return interpret(input[2], new Context(lambdaScope, context));
 };
</pre>

Line 6 of `interpret()` finds that `input` is an identifier atom. Line 7 looks up the identifier, `x`, in `context` and returns `'Lisp'`.

<pre class="prettyprint linenums">var interpret = function(input, context) {
   if (context === undefined) {
     return interpret(input, new Context(library));
   } else if (input instanceof Array) {
     return interpretList(input, context);
   } else if (input.type === "identifier") {
     return context.get(input.value);
   } else {
     return input.value;
  }
};
</pre>

`'Lisp'` is returned by the lambda invocation function, which is returned by `interpretList()`, which is returned by `interpret()`, and that&#8217;s it.

Go to the [GitHub repository][1] to see all the code. And look at [lis.py][3], the dazzlingly simple Scheme interpreter that Peter Norvig wrote in Python.

 [1]: https://github.com/maryrosecook/littlelisp
 [2]: https://www.hackerschool.com
 [3]: http://norvig.com/lispy.html