---
title: How I made Street Hoarding, a Node.js and Redis application, or, a super simple explanation of asynchronicity, event loops, non-blocking IO, JavaScript, Comet and Node
author: maryrosecook
layout: post
permalink: /blog/post/how-i-made-street-hoarding-a-node-js-and-redis-application-or-a-super-simple-explanation-of-asynchronicity-event-loops-non-blocking-io-javascript-and-node
categories:
  - Uncategorized
---
**Second update:** thanks to [James Coglan][1] again, I have modified the code. Now, the client message requests are held open until there is a new message to return, thus reducing the load on the server.

**Update:** Thanks to [James Coglan][1] for providing some lovely technical corrections to this article.

Go to the [Street Hoarding][2] homepage and you will see a message in big letters. If you wish, you can type another message in the text box at the bottom of the page, press return, and see it take the place of the old message. Anyone else on the site at that moment will see your words within a few hundredths of a second. It's kind of like a community pin-up board, or some hoarding on a building site, or a promiscuous IM client with a very short memory.

Some of the key parts of the [code][3] were taken from Ryan Dahl's demo [chat app][4] for Node.js.

My aim with this article is to explain how everything works to someone who is like I was before I wrote Street Hoarding: hazy about asynchronicity, event loops, non-blocking IO, JavaScript and [Node.js][5].

There are two elements: the client and the server.

### The client

This is an HTML page that lays out the main message and the text box. It is also the JavaScript that runs on the user's browser. The JavaScript has two key functions.

<script src="http://gist.github.com/336937.js?file=longPoll.js"></script>

`longPoll()` runs the whole time the user has the webpage open. It takes some data. If this data is null, it is ignored. If it is not null and has a message component, that message is displayed on the webpage through a jQuery update to the message div. Either way, an XMLHttpRequest request is then made with jQuery to the `/latest_message` url on the server. This request takes some time, but it is asynchronous. That is very important. When `longPoll()` is run, the data is processed, the URL request is made and, then, the execution of `longPoll()` continues past the `$.ajax()` call, and control is passed back to the computer processor so it can carry on doing other work. When a success response comes back, the success function inside the `$.ajax()` function is run. This pauses for a moment, then calls `longPoll()` again, passing it the data that the server responded with. Next time through, the message inside this data will be written to the HTML page and the user will see it.

<script src="http://gist.github.com/336937.js?file=tryToSendMessage.js"></script>

`tryToSendMessage()` is called when the user submits a new message via the text field on the HTML page. It first sends an (asynchronous, as always) request to the server to ask it whether the message the user entered has ever been said before. If it has, it just tells the user they aren't being original and finishes. Otherwise, it sends an (asynchronous) request to the `/send_message` URL, passing the user's message as a parameter, thus telling the server to save the message.

There are some improvements that could be made to this code. First, when the message is sent to the server, it does not get updated in the user's browser immediately. That will have to wait until the `longPoll()` function gets its next response from the server. Second, the user has no idea whether the `/send_message` request was successful until `longPoll()` updates the webpage. Third, the message is actually sent twice. The uniqueness check request and send message request could have been combined into a single `/send_message` request that had the server respond with either an indication of success or a message saying that the message was not unique.

I keep on saying the word asynchronous. Everyone who talks about Node.js goes on about asynchronous execution and this other thing, non-blocking input and output (IO). What is crazy is that we haven't even got to the Node.js stuff, yet. This is all browser magic that we've had for the last whatever years.

So, let's go back a bit.

When a request goes from the client to the server - either asking for the latest message or sending a new message - the computer processor doesn't hang around waiting for a response. Instead, it moves on and deals with other tasks. The processor returns its attention to the request when the response comes in. Thus, the input and output are non-blocking. Which is to say, waiting for data to arrive or be sent does not hold the processor up from its other tasks. From this, we get asynchronicity - lines of code can get executed out of order. If there is a pause whilst a function waits for something to happen and that something does not require the computer's processor, other work can be done in the meantime.

How does this work?

There is this thing called an event loop and every browser has one. This is a function that just goes around and around, taking note of things that happen like a woman alone in a house at night straining to hear every floorboard creak and passerby's creep. Code, like jQuery, that is running is the browser, can register its interest in different types of event. So, when the `$.ajax()` jQuery function is called in `longPoll()`, it sends out the request and then tells the event loop it would be very interested in hearing about any HTTP responses that come back from the server. The event loop eventually gets the response and passes it to jQuery which looks at the response to see if the request was a success, and then runs one of the two functions that we defined in `longPoll()`.

This is where JavaScript plays its part.  JavaScript has - and you may have heard this term before - first class functions. These confer several abilities, but the one we care about is that functions can be passed as arguments.  In `longPoll()`, functions are passed as the fifth and sixth properties of the `$.ajax()` call. The first is to be run in the case of a response that indicates an error, the second in the case of a successful response.

Now, back to the request from our client code.  Using a browser means we are in an event loop. Making an HTTP request means we have time when the processor is not being used. Using jQuery means that control is handed back to the browser after a request is sent. The browser regaining control means we have non-blocking IO. Non-blocking IO means that the event loop continues to run whilst it awaits a response. The event loop continuing to run means that other tasks can be dealt with in the mean time.

That deals with the wonders of non-blocking IO and asynchronicity on the client. If we already have all that stuff, I ask, Why is Node.js so special? and I answer, Because this is now easy to do on the server, too.

### The server

<script src="http://gist.github.com/336937.js?file=latest_message.js"></script>

There is some crazy fucking shit going on in the first line. It uses fu, an imported piece of JavaScript code that acts as a mini router. When you pass a url and a function to `fu.get()`, you are saying: when the Node.js server gets a request that was sent to this URL, run this function. 

#### A digression on how the router works that explains some things about JavaScript and the Street Hoarding code but that it's not really necessary to read to get the main points of this article

<script src="http://gist.github.com/336937.js?file=fu.js"></script>

`fu.get()` takes a URL and a function and adds the function to a hash, keyed with the URL. `fu.listen()` starts the server defined by the server variable and makes it listen to events coming to the passed host (probably localhost) on the passed port. We've eaten our way around the jam filling, so it's time to get sticky fingers.

`createServer()`, a Node.js function, is called with an anonymous function that takes a request and response, and the resulting server object is assigned to the server variable. That anonymous function gets the URL on the passed request object, `req`, and looks in `getMap` to find the corresponding function to run. For example, in the latest message code defined above, the url is `/latest_message` and the function is the rest of the code snippet.

We now meet a second special feature of JavaScript: prototyping. The passed response object, `res`, has two new methods added to it on the fly: `simpleText()` and `simpleJSON()`. The methods themselves are not that interesting - they just create a string to return to the client as a response to its request - it is the fact that they are stuck on the `res` object without such as a by your leave that I just know is making your head explode.

Finally, the handler function in `getMap` that corresponded to the requested url is called with the request and the super-charged-with-new-functions response.

#### Latest message

<script src="http://gist.github.com/336937.js?file=latest_message.js"></script>

So, the function passed to `fu.get()` extracts the since parameter that the client sent with the request. This indicates when the client last received a user message from the server. If the server has received a message from a user since then, `sendLatestMessageToClient()` is called. 

<script src="http://gist.github.com/336937.js?file=sendLatestMessageToClient.js"></script>

`sendLatestMessageToClient()` creates a new Redis client. It calls `redisClient.stream.addListener()` to connect the Redis client to the Redis server, passing a function as the second argument. Note the asynchronicity. The Redis library does not hang around waiting while the Redis client connects to the Redis server. Instead, behind the scenes, it passes control back to the server event loop which, at some point in the future, gets an I've Finished My Work And My Name Is The Redis Client Connection Function event which then calls the function passed as the second argument.

This function calls `redisClient.lindex()` which retrieves the first item in the messages list in the database. Three arguments are passed: the key of the messages list, a `` to indicate the first item in the list, and yet another callback function. `redisClient.lindex()` retrieves the first message (did you notice the auxiliary bout of asynchronicity?), and the callback is run which closes the Redis client and runs the `simpleJSON()` function to send the message back to the client. (Those of us who read the digression are like fully in a special secret club what knows how totally mind-fucking it is that the `res` object has a `simpleJSON()` function hanging around on it; those who did not read the digression will keep their heads fuck-free.)

#### New message

<script src="http://gist.github.com/336937.js?file=send_message.js"></script>

The function passed to `fu.get()` extracts the message from the request and calls `storeMessage()`, passing the message and yet another function to call back later.

<script src="http://gist.github.com/336937.js?file=storeMessage.js"></script>

`storeMessage()` goes through the familiar routine of creating a Redis client, requesting a connection to the Redis server, calling a Redis function (`redisClient.lpush`, this time), closing the Redis client and calling back the function passed as the second argument which:

Wait, stop a second. Do you remember how I rather trailed off five paragraphs ago when I wrote, "If the server has received a message from a user since then, `sendLatestMessageToClient()` is called"? By which I mean, I didn't say what happened if the server had not received a new message since the last message was sent to the user. Let's have a look.

<script src="http://gist.github.com/336937.js?file=latest_message.js"></script>

Right. Latest message requests that would normally be answered with the message that the client is already displaying are held open. I know that was a long sentence, and this is a long article, and you are tired, but I hope that those last two words didn't slip by you. Held open. A response is not sent immediately. Instead, a new item is pushed onto the `messageRequests` array: a hash of the `res` object and the `sendLatestMessageToClient()` function.

<script src="http://gist.github.com/336937.js?file=send_message.js"></script>

So, back to the `/send_message` code to see how it deals with the held message requests. The code extracts the user's message, stores it and sends a success response back to the client. For each message request that has been pushed onto `messageRequests`, `sendLatestMessageToClient()` is called. This sends the latest message (probably the one received a few lines ago) back to the client, thus ending the request. This is Comet: the client sends a request and no response is sent until there is something useful to send, thus the request is held open.

Ryan Dahl did two really cool things. First, he wrote a library that lets you code an event-driven server in JavaScript. However, this was not new. Second, and more importantly, he wrote the core libraries so that they are non-blocking. The problem with other event-driven programming libraries is that you can't be sure whether the auxiliary libraries you want to use are non-blocking. If they are, you will stall your event loop and it will stop dealing with incoming events and everything will fall apart.

So, from the re-written libraries, we get non-blocking IO, which allows an event loop. The event loop allows the server to run in a single process. A single process means low memory usage.

 [1]: http://jcoglan.com
 [2]: http://streethoarding.heroku.com
 [3]: http://github.com/maryrosecook/streethoarding
 [4]: http://chat.nodejs.org/
 [5]: http://nodejs.org