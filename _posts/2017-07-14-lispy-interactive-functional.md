---
css: lispy-interactive-functional
title: Lispy, interactive and functional
author: maryrosecook
layout: post
permalink: /blog/post/lispy-interactive-functional
hidden: true
---
Clojure is a mind-expanding language.  Even if you never use it in your work, learning it will broaden your programming mind and improve your programming skills.  This is because it introduces you to some simple ideas that produce deep results:

#### Writing code in a functional style

Functional programming boils down to writing code that has no side effects.  A function takes some arguments and returns some data.  That's it.  There are no instance variables or global variables.

#### Writing code using Lisp syntax

Lisp code has a very simple syntax: it's just lists.  A Lisp creates lists to do everything.

#### Programming interactively

Interactive programmers tend to create their programs like a sculptor works with clay.  They have a thing, and they slowly morph it into the thing they want.

### Let's get started!

Lisp expressions, called S-expressions, are lists.  Here's an S-expression to print to the console:

(println "Hello")

This is typical.  The parentheses indicate the start and end of the list.  The first element in the list is the name of the function to call.  The rest of the elements in the list are arguments to the function call.

> How is this different from how you print to the console in JavaScript?

### Lisp syntax is homogenous

The magic thing is that you now know *all* of Lisp's syntax.  Compare a conditional in JavaScript:

<pre class="prettyprint">
if (true) {
  console.log("Hello");
} else {
  console.log("Goodbye");
}
</pre>

And Clojure:

<pre class="prettyprint lang-clj">
(if true
  (println "Hello")
  (println "Goodbye"))
</pre>

In JavaScript, there's a different syntax for creating condionals and calling functions.  In Clojure, a conditional is, essentially, just another function call.

### Interactive programming

Let's define a function called `say-hi` that prints "Hi" to the console.  We'll do this interactively.

#### The process

The process for interactive programming goes like this:

1. Run the code.
2. Inspect the result.
3. Plan the code change that will get you a bit closer to the result you want.
4. Make the code change.  Return to 1.

#### Example part 1

Click the Run button (step 1).

Nothing happens (step 2).

You look at your goal: define a function called `say-hi` that prints "Hi" to the console.  You try to find the smallest step that will take you closer to this goal.  "Small" means both "a small change to the code" and also "requires the smallest amount of new knowledge".  You decide to write code that will print "Hi" to the console.  (Step 3.)

You add this code (step 4).

You click Run (step 1).

Hopefully, you see "Hi" in the console (step 2).  If you don't, debug your program by following the same four steps.

You decide that the next smallest step is to define a function.  But you don't want to try and integrate your `println` code.  That would be too big a step.  You decide to define a function that does nothing. (Step 3.)

You type the code into the program to define a function that (step 4):

* Is called `say-hi`.
* Takes no arguments.
* Does nothing.

You click Run (step 1).

What happened? If you got errors, use the 4 steps to fix them.

Does `say-hi` actually work? We don't know, because we're not running it.  Let's run it. (Step 3.)

Type `(say-hi)` into the **REPL** (not the program) (step 4).

Press the RETURN key (step 1).

You see `nil` in the REPL (step 2).

Use the four steps to add your `println` code to the `say-hi` definition.

#### Which code am I running?

Let's take a step back.  There's something funny going on.  Earlier, you typed a function definition into the program and ran it.  Now, you can somehow run that function from the REPL.

Draw a diagram that shows the following:

* The functions defined in your program code.
* The functions defined in the computer's memory.
* The functions you can currently run from the REPL.
* The actions you can take to change each of these things.  Actions include typing in code to your program, typing in code to the REPL, clicking the Run button, and pressing RETURN at the REPL.

To help you, you could:

* Define some more functions in your program.  (Make sure they have different names and print different things so you can distinguish between them.)
* Define some functions from the REPL.

#### Interactive programming vs TDD

> Can interactive programming replace TDD?
> What *drives* TDD and what drives interactive programming?
> Why do we write code using TDD?
> Do tests have a place in interactive programming?

### Functional programming

#### Definition

Programming in a functional style boils down to:

* Writing functions that take some arguments and return a piece of data.

There are two interesting properties of this style:

* Behaviour and state are separated.
* Code has no side effects.

#### Functions that take some arguments and return some data.

Let's say you're writing an app to help you remember todos.  Things like "Get milk" and "Walk the dog".

In Ruby, you might write:

```
class Todos
  def initialize
    @todos = []
  end

  def add(text)
    @todos << text
  end
end

todos = Todos.new
todos.add("Get milk")
```

This code is in an OO style, not a functional style.  `initialize` and `add` have effects besides their return values.  The return values of these functions are completely unimportant to the behaviour of the program.  This shows that the code is very much in the OO style and very much not in the functional style.

> Reimplement this code in Ruby in a functional style.  Don't create a class.  Just write functions that do their work by taking arguments and returning data. <sup>[1](#1)</sup>

Click the footnote link to see a possible implementation.

#### Behaviour and state are separated

> Look at the code above that I wrote in an OO style.

> What is the state in that code? <sup>[2](#2)</sup>

> What is the behaviour in that code? <sup>[3](#3)</sup>

> Look at the code above that you wrote in a functional style.

> What is the state in that code? <sup>[4](#4)</sup>

> What is the behaviour in that code? <sup>[5](#5)</sup>

Notice that the code written in a functional style has combined the state and behaviour into a class.  But the code written in a functional style has kept the state and behaviour separate.

Here's the same code written in Clojure's functional style:

```
(let [todos (vector)]
  (conj todos "Get milk"))
```

`vector` is a function that returns a new vector.  (A vector is similar to an array in Ruby.)

`let` binds names to pieces of data.  Above, the name `todos` is bound to an empty vector.

`conj` is a function.  It takes a collection, `todos`, and adds an element to it, `"Get milk"`.

> Look at the Clojure code I wrote above.

> What is the state in that code? <sup>[6](#6)</sup>

> What is the behaviour in that code? <sup>[7](#7)</sup>

Notice that the state and behaviour are kept separate.  `conj` is just a function that can take any collection.

> Where does the concept of encapsulation fit into this functional style? <sup>[8](#8)</sup>

> What advantages are there to keeping state and behaviour separate? <sup>[9](#9)</sup>

#### No side effects

Have a look at this code:

```
def create_todos
  []
end

def add_todo(todos, text)
  todos << text
end

generic_todos = add_todo(create_todos, "Create todo list")

work_todos = add_todo(generic_todos, "Deliver workshop")

home_todos = add_todo(generic_todos, "Make supper")
```

> What's the problem here? Hint: what todos are on the work list and what todos are on home list? <sup>[10](#10)</sup>

Ruby is limited as a functional language.  The `add_todo` function seems like it acts only by taking arguments and returning new data.  But it has side effects.  It changes the value of its `todos` parameter.

It's often possible to avoid side effects in Ruby by careful programming.

> Rewrite `add_todo` so it doesn't have any side effects. <sup>[11](#11)</sup>

It's much easier to write code in a functional style in Clojure than it is in Ruby.  This is because Clojure's data structures (e.g. vectors) are immutable.  Functions don't change them.  Instead, they take an original and produce a new, modifed copy.

> Write some code that creates a vector, `conj`es something on to it and prints out the original, unchanged vector. <sup>[12](#12)</sup>

#### Footnotes

<sup><a name="1">1</a></sup> Ruby implementation of creating and adding todos

```
def create_todos
  []
end

def add_todo(todos, text)
  todos << text
end

todos = add_todo(create_todos, "Get milk")
```
<sup><a name="2">2</a></sup> The state is the `@todos` array.

<sup><a name="3">3</a></sup> The behaviour is the ability to initialize a `Todos` object.  And it's the ability to add a todo.

<sup><a name="4">4</a></sup> The state is the array of todos.

<sup><a name="5">5</a></sup> The behaviour is the `create_todos` and `add_todo` functions.

<sup><a name="6">6</a></sup> The state is the `todos` vector.

<sup><a name="7">7</a></sup> The behaviour is the `vector` and `conj` functions.

<sup><a name="8">8</a></sup> Encapsulation doesn't really exist in functional code.  "Private data" and "implementation details" aren't a focal point.  Why might that be?

<sup><a name="9">9</a></sup> Combining state and behaviour has a disadvantage.  You have some greate behaviour, but you can only use it on one piece of state - the state it's combined with.  In contrast, we can use Clojure's conj function on any array.  It doesn't matter if it's an array of todos or an array of numbers.  It was written once and we get to use it with many different pieces of data.

<sup><a name="10">10</a></sup> The programmer wanted to use `generic_todos` as a starting point for both `work_todos` and `home_todos`.  Unfortunately, they ended up creating only one array.  They repeatedly added todos to this array until it contained `["Create todo list", "Deliver workshop", "Make supper"]`.

<sup><a name="12">12</a></sup> Proving that Clojure's vector data structure is immutable.

```
(let [todos (vector)]
  (println (conj todos "Get milk"))
  (println todos))
```