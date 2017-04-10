---
title: Testing from the ground up
author: maryrosecook
layout: post
permalink: /blog/post/testing-from-the-ground-up
categories:
  - Uncategorized
---
Tests are pieces of code that check if your main code works. I write tests to catch bugs when I refactor. I write tests to force myself to think through and handle edge cases. I write tests to show the users of my project that my code does what I say it does.

For this essay, I will describe the code and tests for a tiny web app that draws a blue sky if it's day time.

<img src="/images/bluesky.png" width="600" height="100" />

And a black sky if it's night time.

<img src="/images/blacksky.png" width="600" height="100" />

I will describe all the code I wrote. The web app. The microscopic testing framework. The tests for the client side code. The mocks that fake layers of the web app and technologies that are not pertinent. The tests that use these mocks. The refactored code that simplifies the mocks by dividing the web app code into different pieces that have single responsibilites.

Along the way, I will talk about many fun things. Temporarily patching libraries written by other people. Writing code that pretends to be the internet. Making Ajax requests by hand. Writing a little web server. Examining a function to find out how many arguments it expects. Making asynchronous blocks of code run serially so they don't tread on each other's toes.

### The code

To see the code from this essay in runnable form, go to the [Testing From The Ground Up GitHub repository][1]. At each evolution of the code, I will include a link to the corresponding commit.

### The web app

This is the HTML that defines the only page in the web app. It has a canvas element that displays the sky. It loads the client side JavaScript, `client.js`. When the DOM is ready, it calls `loadTime()`, the one and only function.

<pre class="prettyprint">&lt;!doctype html&gt;
&lt;html&gt;
  &lt;head&gt;
    &lt;script src="client.js"&gt;&lt;/script&gt;
  &lt;/head&gt;

  &lt;body onload="loadTime();"&gt;
    &lt;canvas id="canvas" width="600" height="100"&gt;&lt;/canvas&gt;
  &lt;/body&gt;
&lt;/html&gt;
</pre>

Below is the client side JavaScript.

`loadTime()` starts by making an Ajax request to the server to get the current time. This is done in several steps. First, it creates an `XMLHttpRequest` object. Second, near the bottom of the function, it configures the object to make a `GET` request to `"/time.json"`. Third, it sends the request. Fourth, when the requested time data arrives at the client, the function bound to `request.onload` fires.

The bound function grabs the drawing context for the canvas element. It parses `"day"` or `"night"` from the JSON returned by the server. If the value is `"day"`, it sets the draw color to blue and draws a rectangle that takes up the whole canvas. If the value is `"night"`, the color is black.

<pre class="prettyprint">;(function(exports) {
  exports.loadTime = function() {
    var request = new XMLHttpRequest();
    request.onload = function(data) {
      var ctx = document.getElementById("canvas").getContext("2d");
      var time = JSON.parse(data.target.responseText).time;
      var skyColor = time === "day" ? "blue" : "black";

      ctx.fillStyle = skyColor;
      ctx.fillRect(0, 0, ctx.canvas.width, ctx.canvas.height);
    };

    request.open("GET", "/time.json", true);
    request.send();
  };
})(this);
</pre>

Below is the server-side JavaScript. It is run in [Node.js][2]. Near the bottom, the code uses the Node HTTP module to create a web server. It specfies that every web request should be handled by `requestHandler()`. Each time this function is called, Node passes it a `request` object that has the URL that was requested, and a `response` object that can be used to send data back to the client.

If the client requested `"/"`, the root, the `index.html` file is read from disk and its contents are sent back to be displayed in the user's browser. If `"/time.json"` was requested, the server looks up the time, creates a piece of JSON that looks something like `{ "time": "day" }` and sends it back to the user's web browser.

<pre class="prettyprint">var http = require("http");
var fs = require("fs");

var requestHandler = function (request, response) {
  if (request.url === "/") {
    fs.readFile(__dirname + "/index.html", function (err, data) {
      response.writeHead(200, { "Content-Type": "text/html" });
      response.end(data);
    });
  } else if (request.url === "/client.js") {
    fs.readFile(__dirname + "/client.js", function (err, data) {
      response.writeHead(200, { "Content-Type": "application/javascript" });
      response.end(data);
    });
  } else if (request.url === "/time.json") {
    response.writeHead(200, { "Content-Type": "application/json" });

    var hour = new Date().getHours();
    var time = hour > 6 &#038;&#038; hour &lt; 20 ? "day" : "night";

    response.end('{ "time": "' + time + '" }');
  }
};

http.createServer(requestHandler).listen(4000);

exports.requestHandler = requestHandler;
</pre>

Here is the [code][3] for the basic web app.

### A miniscule testing framework

It is possible to write tests that each include the generic code for reporting their outcome, for reporting errors, for calling the next test. But, that means a lot of repetition. It's easier to use a testing framework. This is the one I wrote.

<pre class="prettyprint">var test = function() {
  gatherTests(arguments).forEach(function(userTest) {
    userTest();
    process.stdout.write(".");
  });

  console.log();
};

test.isEqual = function(a, b) {
  if (a !== b) {
    throw a + " and " + b + " are not equal";
  }
};

var gatherTests = function(testArgs) {
  return Array.prototype.slice.call(testArgs).filter(function(x, i) {
    return i % 2 === 1;
  });
};

module.exports = test;
</pre>

`test()` could be used to write a test like this:

<pre class="prettyprint">var test = require("./test");
test(
  "should test 1 equals 1", function() {
    test.isEqual(1, 1);
  });
</pre>

Which, when run, would look like this:

<pre class="prettyprint">$ node test.js
  .
</pre>

How does the testing framework run the tests it is given?

`test()` takes a series of arguments that alternate between string descriptions and test functions. It throws away the ones that are strings, which are merely human-readable window-dressing. It puts the functions into an array. It walks through that array, calling each function and printing a period to indicate that the test passed.

The test functions use `test.isEqual()` to assert that certain variables have certain values. (A real testing framework would have a more permissive and pragmatic version of `isEqual()` that returned true in a case like `test.isEqual({ love: "you" }, { love: "you" })`.) If an assertion turns out to be unfounded, an exception is thrown, an error is printed and the tests stop running.

#### Mocking the server and the internet

I don't want to have to run the server when I run the tests. That would be an extra step. It would add statefulness that would need to be reset before each test. It would necessitate network communications between the test and the server.

This is the code I wrote that pretends to be the server deciding what time it is and the internet relaying that information back to the client. It does this by faking an Ajax request. It replaces the `XMLHttpRequest` constructor function with a function that returns a home-made Ajax request object. This object swallows the web app's call to `open()`. When the web app calls `send()`, it calls the function that the web app has bound to `onload`. It passes some fake JSON that makes it seem like it is always day time.

<pre class="prettyprint">global.XMLHttpRequest = function() {
  this.open = function() {};

  this.send = function() {
    this.onload({
      target: { responseText: '{ "time": "day" }' }
    });
  };
};
</pre>

#### Mocking the canvas and the DOM

When the real code runs in a real web browser, it renders the sky to the real canvas in real blue. This is problematic. First, I don't want to require a browser to test my code. Second, even if I capitulated to that requirement, it would be hard to check if the right thing happened. I would probably need to look at the individual pixels of the canvas drawing context to see if they were bluey.

Instead of walking down that horrid road, I wrote some code that pretends to be the browser DOM and the canvas element. It redefines `getElementById()` to return a fake canvas. This has a fake `getContext()` that returns a fake drawing context that has a fake `fillRect()` and a fake reference to a fake canvas that has a `width` and `height`. Instead of drawing, this function checks that the arguments passed to it have the expected values.

<pre class="prettyprint">global.document = {
  getElementById: function() {
    return {
      getContext: function() {
        return {
          canvas: { width: 300, height: 150 },
          fillRect: function(x, y, w, h) {
            test.isEqual(x, 0);
            test.isEqual(y, 0);
            test.isEqual(w, 300);
            test.isEqual(h, 150);
            test.isEqual(this.fillStyle, "blue");
          }
        };
      }
    };
  }
};
</pre>

This is the full test.

<pre class="prettyprint">var test = require("./test");
var client = require("../client");

test(
  "should draw blue sky when it is daytime", function() {
    global.XMLHttpRequest = function() {
      this.open = function() {};

      this.send = function() {
        this.onload({
          target: { responseText: '{ "time": "day" }' }
        });
      };
    };

    global.document = {
      getElementById: function() {
        return {
          getContext: function() {
            return {
              canvas: { width: 300, height: 150 },
              fillRect: function(x, y, w, h) {
                test.isEqual(x, 0);
                test.isEqual(y, 0);
                test.isEqual(w, 300);
                test.isEqual(h, 150);
                test.isEqual(this.fillStyle, "blue");
              }
            };
          }
        };
      }
    };

    client.loadTime();
  });
</pre>

Don't worry. That code is as bad as this essay gets.

Here is the [code][4] that includes the testing framework and the first client side test.

#### The drawing code refactored into its own module

Those mocks are pretty horrid. They make the test very long, which discourages me from writing tests to check for other ways the code might go wrong. The mocks are horrid because the client side code is one big function that communicates with the server, asks what time it is, parses the response and draws in the canvas.

To solve this problem, I refactored the code by pulling the drawing code out into its own module, `renderer`. This module includes `fillBackground()`, a function that fills the canvas with the passed color. As a side benefit, the main web app code is now easier to understand and change.

<pre class="prettyprint">;(function(exports) {
  exports.loadTime = function() {
    var request = new XMLHttpRequest();
    request.onload = function(data) {
      var time = JSON.parse(data.target.responseText).time;
      var color = time === "day" ? "blue" : "black";
      renderer.fillBackground(color);
    };

    request.open("GET", "/time.json", true);
    request.send();
  };

  var renderer = exports.renderer = {
    ctx: function() {
      return document.getElementById("canvas").getContext("2d");
    },

    fillBackground: function(color) {
      var ctx = this.ctx();
      ctx.fillStyle = color;
      ctx.fillRect(0, 0, ctx.canvas.width, ctx.canvas.height);
    }
  };
})(this);
</pre>

This lets me replace the complex `document` mock with a short `renderer.ctx()` mock. The test becomes shorter, simpler and less brittle.

<pre class="prettyprint">var test = require("./test");
var client = require("../client");

test(
  "should draw sun and blue sky in canvas when it is daytime", function() {
    global.XMLHttpRequest = function() {
      this.open = function() {};

      this.send = function() {
        this.onload({
          target: { responseText: '{ "time": "day" }' }
        });
      };
    };

    client.renderer.ctx = function() {
      return {
        canvas: { width: 300, height: 150 },
        fillRect: function(x, y, w, h) {
          test.isEqual(x, 0);
          test.isEqual(y, 0);
          test.isEqual(w, 300);
          test.isEqual(h, 150);
          test.isEqual(this.fillStyle, "blue");
        }
      }
    };

    client.loadTime();
  });
</pre>

Here is the [code][5] for the modularised renderer and resulting simplified client side test.

I modularised the client side code further by splitting out three more functions.

This is `get()`. It makes an Ajax request to the passed URL. The Ajax request object calls the passed callback with the response.

<pre class="prettyprint">var get = exports.get = function(url, callback) {
  var request = new XMLHttpRequest();
  request.onload = callback;
  request.open("GET", url, true);
  request.send();
};
</pre>

This is `getTime()`. It uses `get()` to make an Ajax request to `"/time.json"` and parses `"day"` or `"night"` from the response.

<pre class="prettyprint">exports.getTime = function(callback) {
  exports.get("/time.json", function(data) {
    callback(JSON.parse(data.target.responseText).time);
  });
};
</pre>

This is `displayTime()`. It takes a string with the value `"day"` or `"night"` and draws either a blue sky or a black sky.

<pre class="prettyprint">exports.displayTime = function(time) {
  var color = time === "day" ? "blue" : "black";
  renderer.fillBackground(color);
};
</pre>

I changed the `body` tag in the HTML page. It now calls `getTime()`, passing `displayTime()` as the callback.

<pre class="prettyprint">&lt;body onload="getTime(displayTime);"&gt;
  &lt;canvas id="canvas" width="600" height="100"&gt;&lt;/canvas&gt;
&lt;/body&gt;
</pre>

Having more modular code means that I can mock parts of the web app API, rather than mocking code written by third parties. This makes it easier to write tests that test a specific piece of functionality.

Using this refactor, I could write tests that are more extensive. The first test checks that `getTime()` correctly parses JSON sent by the server. The second test checks that a call to `fillBackground()` draws a rectangle at the correct position and size. The third test checks that `displayTime()` draws a rectangle of the correct color for the time of day.

<pre class="prettyprint">var test = require("./test");
var client = require("../client");

test(
  "should parse time from server", function() {
    client.get = function(_, callback) {
      callback({ target: { responseText: '{ "time": "day" }' }});
    };

    client.getTime(function(time) { test.isEqual(time, "day"); });
  },

  "should draw rect of passed size and color when fillBackground() called", function() {
    client.renderer.ctx = function() {
      return {
        canvas: { width: 300, height: 150 },
        fillRect: function(x, y, w, h) {
          test.isEqual(x, 0);
          test.isEqual(y, 0);
          test.isEqual(w, 300);
          test.isEqual(h, 150);
        }
      }
    };

    client.renderer.fillBackground("blue");
  },

  "should draw blue sky when it is daytime", function() {
    client.renderer.fillBackground = function(color) {
      test.isEqual(color, "blue");
    };

    client.displayTime("day");
  });
</pre>

Here is the [code][6] for the more modular client code and more extensive tests.

### Testing the server side code

#### Asynchronous tests

Some of the responsibilities of a web server require asynchronous operations. If a browser sends a request to the web app for the URL `"/"`, it gets back the contents of the `index.html` file. To make this happen, the file needs to be read from disk asynchronously and the contents sent to the client once they have been read. Similarly, if the browser requests the URL `"/client.js"`, it gets back the contents of the `client.js` file.

These are the tests I wrote to check that both cases are handled correctly.

<pre class="prettyprint">var test = require("./test");
var requestHandler = require("../server").requestHandler;
var http = require("http");

test(
  "should send index.html when / requested", function() {
    http.ServerResponse.prototype.end = function(data) {
      test.isEqual(data.toString().substr(0, 9), "&lt;!doctype");
    };

    requestHandler({ url: "/" }, new http.ServerResponse({}));
  },

  "should send client.js when /client.js requested", function() {
    http.ServerResponse.prototype.end = function(data) {
      test.isEqual(data.toString().substr(0, 10), ";(function");
    };

    requestHandler({ url: "/client.js" }, new http.ServerResponse({}));
  });
</pre>

The output when I run these tests:

<pre class="prettyprint">$ node server_tests.js
  ..

  test.js:12
    throw a + " and " + b + " are not equal";
                          ^
  &lt;!doctype  and ;(function are not equal
</pre>

An exception is thrown, yet there are two periods indicating two passed tests. Something is wrong.

In the first test, `response.end()` is mocked with a function that checks that   
`"<!doctype"` is sent to the user. Next, `requestHandler()` is called, requesting the URL `"/"`. `requestHandler()` starts reading `index.html` from disk. While the file is being read, the test framework presses on with its work. Uh oh. That is, the framework prints a period and starts the second test, though the `response.end()` mock has not asserted the value of the response. `response.end()` is re-mocked with a function that checks that `";(function"` is sent to the user. Double uh oh. `requestHandler()` is called by the second test. It requests the URL `"/client.js"`. `requestHandler()` starts reading `client.js` from disk. The framework prints another premature period.

At some point later, `index.html` is read from disk. This means that the callback in `requestHandler()` is called and it calls `response.end()` with the contents of `index.html`. Unfortunately, by this time, `response.end()` has been mocked with a function expecting `";(function"`. The assertion fails.

This problem can be solved by running tests serially. A test is run. It signals it has finished. The next test is run.

This may seem pedantic. Shouldn't tests that are testing asynchronous behaviour be able to cope with with the dangers of asynchrony? Well, yes and no. They should certainly test the asynchronous behaviours of `requestHandler()`. But they should not have to cope with other tests messing about with their execution environment part way through their execution.

(It would be possible to go further and make the tests completely functionally pure. This could be done in a fundamentalist way: the test framework resets the execution context before each test. Or it could be done in a pragmatic way: each test undoes the changes it made to the execution environment. Both ways are outside the scope of this essay.)

I rewrote the testing framework to run asynchronous tests serially. Each asynchronous test binds a `done()` callback parameter. It calls this when it has made all its assertions. The testing framework uses the execution of this callback as a signal to run the next test. Here are the rewritten tests.

<pre class="prettyprint">var test = require("./test");
var requestHandler = require("../server").requestHandler;
var http = require("http");

test(
  "should send index.html when / requested", function(done) {
    http.ServerResponse.prototype.end = function(data) {
      test.isEqual(data.toString().substr(0, 9), "&lt;!doctype");
      done();
    };

    requestHandler({ url: "/" }, new http.ServerResponse({}));
  },

  "should send client.js when /client.js requested", function(done) {
    http.ServerResponse.prototype.end = function(data) {
      test.isEqual(data.toString().substr(0, 10), ";(function");
      done();
    };

    requestHandler({ url: "/client.js" }, new http.ServerResponse({}));
  });
</pre>

#### A miniscule asynchronous testing framework

Below is the code for the asynchronous testing framework.

Look at `runTests()`. It takes `userTests`, an array that contains the test functions to be run. If that array is empty, the tests are complete and the program exits. If it is not empty, it looks at the `length` attribute of the next test function. If the attribute has the value `1`, the test expects one argument: a `done()` callback. It runs `testAsync()`, passing the test function and a callback that prints a period and recurses on `runTests()` with the remaining test functions.

`testAsync()` creates a timeout that will fire in one second. It runs the test function, passing a `done()` callback for the test to run when it is complete. If the callback gets run, the timeout is cleared and `testDone()` is called to indicate that the next test can run. If the `done()` callback is never run by the test function, something went wrong. The timeout will fire and throw an exception, and the program will exit.

If the `length` attribute of the test function has the value `0`, the function is run with `testSync()`. This is the same as `testAsync()`, except there is no timeout and the `testDone()` callback is called as soon as the test function has completed.

<pre class="prettyprint">var test = function() {
  runTests(gatherTests(arguments));
};

var runTests = function(userTests) {
  if (userTests.length === 0) {
    console.log();
    process.exit();
  } else {
    var testDone = function() {
      process.stdout.write(".");
      runTests(userTests.slice(1));
    };

    if (userTests[0].length === 1) {
      testAsync(userTests[0], testDone);
    } else {
      testSync(userTests[0], testDone);
    }
  }
};

var testSync = function(userTest, testDone) {
  userTest();
  testDone();
};

var testAsync = function(userTest, testDone) {
  var timeout = setTimeout(function() {
    throw "Failed: done() never called in async test.";
  }, 1000);

  userTest(function() {
    clearTimeout(timeout);
    testDone();
  });
};

test.isEqual = function(a, b) {
  if (a !== b) {
    throw a + " and " + b + " are not equal";
  }
};

var gatherTests = function(testArgs) {
  return Array.prototype.slice.call(testArgs).filter(function(x, i) {
    return i % 2 === 1;
  });
};

module.exports = test;
</pre>

Here is the [code][7] for the asynchronous testing framework and the new server tests.

#### An exercise

Now that it is possible to write asynchronous tests, I can write the tests for the server. Or, rather: you can.

If you are not sure where to start, try refactoring the server so the code is more modular. Write a function that sends the passed string with the passed `Content-Type` to the client. Write a function that reads a static file from disk and responds with the contents. Write a function that converts the current date into `"day"` or `"night"`. Write a function that takes a request for a certain URL and sends the right response.

Once your refactor is complete, or maybe while it is in progress, you can write your tests.

### Summary

I wrote a simple web app. I wrote tests for it and discovered that I could mock the pieces I didn't want to run when I ran the tests. I discovered that scary things like Ajax and the canvas API really aren't so scary when I wrote skeletal versions of them. I realised that the mocks I had written were quite verbose. I refactored the web app code to make it more modular. This made the code better and easier to change in the future. It meant I could mock the interfaces I had written, rather than those invented by other people. This meant the mocks became simpler or unnecessary. This made it easier to write tests, so I could write more of them and test the web app more extensively.

I wrote two tests for the server. I discovered that the test framework ran them in parallel, which meant they interfered with each other. I rewrote the test framework to run tests serially. I modified the tests to signal when they were finished. I handed over the rest of the job to you.

 [1]: https://github.com/maryrosecook/testing-from-the-ground-up
 [2]: http://nodejs.org/
 [3]: https://github.com/maryrosecook/testing-from-the-ground-up/tree/f5665151b0ba1ded8cbf9615d676ca7ab343a535
 [4]: https://github.com/maryrosecook/testing-from-the-ground-up/tree/849ee4eb19f9d4558d4ee91b97d567b31c0d4849
 [5]: https://github.com/maryrosecook/testing-from-the-ground-up/tree/03b3da9180a7f9f6ba380c881cee2dcabea7e828
 [6]: https://github.com/maryrosecook/testing-from-the-ground-up/tree/596e3d7af644e8153427f463c221e0efa3e2dcb8
 [7]: https://github.com/maryrosecook/testing-from-the-ground-up/tree/1e166453f98b22f73a7736f62a951022b4d1e06f
