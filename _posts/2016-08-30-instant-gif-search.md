---
title: Instant GIF search
author: maryrosecook
layout: post
permalink: /blog/post/instant-gif-search
---

<div class="video-container"><iframe width="600" height="338" src="https://www.youtube.com/embed/I8vLt5AB2TI?rel=0" frameborder="0" allowfullscreen></iframe></div>

I spent a couple of days making a prototype of a GIF search engine.  My goal was to make it as fast as possible.

Each time the user types a character, the app sends an Ajax request to the [Giphy API](https://api.giphy.com/).  When the response comes back, the app extracts URLs for the GIFs listed in the search results.  To preload these GIFs, the app sends Ajax requests for each one.  When the first image response arrives, the app inserts the corresponding image into the top search result position on the page.  When the second image response arrives, the app inserts the corresponding image into the second search result position on the page.  And so on.  This way, the GIFs load from the top of the page down, which makes their appearance less jarring to the eye.

Each time the user types a new character, the previous search request and previous image requests are aborted.  This, combined with Giphy's responsive servers and the fact that I used [React](https://facebook.github.io/react/), make the app fast.

Sadly, I can't deploy the app publicly.  It doesn't have any features beyond the ones offered by Giphy, which means it violates their terms of service.

On a side note, I used [Create React App](https://github.com/facebookincubator/create-react-app) to build my project.  This was a dream.  No messing around with Webpack.  A built in deployment build script.  ES2015 support.  The only thing I missed was the fact that testing is not (currently) supported.
