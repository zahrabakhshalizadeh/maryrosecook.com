---
title: Adding keyboard control to Playmary
author: maryrosecook
layout: post
permalink: /blog/post/adding-keyboard-control-to-playmary-3
categories:
  - Uncategorized
---
[Playmary][1] is my audio autobiography site. You create an account and upload songs that you are listening to a lot, or that symbolise what is going on in your life.

Up until now, you would click on songs to play and pause them, and when one song ended, the next would play.

I've added some new code that lets you move through the playlist with the left and right arrows, and pause and play the current song with the space bar:

<script src="http://gist.github.com/268961.js?file=gistfile1.js"></script>

Code adapted from the source in [this article][2].

The only downer is the space bar is a shortcut for scrolling in Safari, Internet Explorer and Firefox. [Dan W][3] and I couldn't think of any conventions for play controls in the browser. j/k/l seems too obscure, and I really want the controls to mimic iTunes. Hmmm.

 [1]: http://playmary.com
 [2]: http://www.geekpedia.com/tutorial138_Get-key-press-event-using-JavaScript.html
 [3]: http://iamdanw.com