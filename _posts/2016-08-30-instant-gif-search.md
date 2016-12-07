---
title: Instant GIF search
author: maryrosecook
layout: post
permalink: /blog/post/instant-gif-search
---

<div class="video-container"><iframe width="600" height="338" src="https://www.youtube.com/embed/I8vLt5AB2TI?rel=0" frameborder="0" allowfullscreen></iframe></div>

I spent a couple of days making a prototype of a GIF search engine.  My goal was to make it as fast as possible.

Each time the user types a character, the app sends an Ajax request to the [Giphy API](https://api.giphy.com/).  When the response comes back, the app extracts URLs for the GIFs listed in the search results.  To preload these GIFs, the app sends Ajax requests for each one.  When the first image response arrives, the app inserts the corresponding image into the top search result position on the page.  When the second image response arrives, the app inserts the corresponding image into the second search result position on the page.  And so on.  The GIFs are less jarring to the eye because they load from the top of the page down.

Each time the user types a new character, the previous search request and previous image requests are aborted.  This saves bandwidth.  The app is fast because of this bandwidth saving, React's fast rendering, and Giphy's responsive servers.

Sadly, I can't deploy the app publicly.  It doesn't have any features beyond the ones offered by Giphy, which means it violates their terms of service.

I used [Create React App](https://github.com/facebookincubator/create-react-app) to build my project.  This was a dream.  No messing around with Webpack.  Built-in deployment build script.  ES2015 support.
