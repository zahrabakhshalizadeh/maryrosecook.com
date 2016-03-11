---
title: Writing an mp3 crawler in Clojure
author: maryrosecook
layout: post
permalink: /blog/post/writing-an-mp3-crawler-in-clojure-4
categories:
  - Uncategorized
---
I've written an mp3 crawler to help me learn Clojure. It's 150 lines. I'm sure could be much shorter. There are some URL parsing bugs.

Like all my projects, the code I talk about in this article is open source. [Get it from GitHub][1].

### The basic flow

  1. Start with a URL, like saidthegramophone.com
  2. Request the page and find all the URLs on it.
  3. Save all the ones that point at mp3s.
  4. Note down how many mp3s were yielded.
  5. Throw away ones that definitely don't point at other HTML pages (images, Javascript).
  6. Throw away ones that are at hosts that don't seem to yield many mp3s.
  7. Add the rest to the list of URLs to crawl.
  8. Go to step 2 with the next item on that list.

### Interesting points

#### Agents

URLs are requested by asynchronous agents in batches of twenty. Thus, they can be crawled much more quickly. `crawl-batch-of-urls` maps the twenty items in `urls-to-crawl` to the `request-url` function. This function creates a new http-agent and tells it to download the (HTML) content at the url. `crawl-batch-of-urls` then waits up to ten seconds for all the agents in the batch to finish, then passes them back.

<script src="https://gist.github.com/316641.js?file=agents.clj"></script>

#### Host scores

A record is kept of the mp3-richness of each host the crawler encounters. Each mp3 found on a host scores it a point. Each crawl of a URL on the host loses it a point. So, say [www.saidthegramophone.com/archives/in_this_box_or_another.php][2] was crawled and five mp3s were found, four points would be added to the score for [www.saidthegramophone.com][3]

`update-host-scores` updates a hash of hosts and scores after a new URL is crawled:

<script src="https://gist.github.com/316641.js?file=update-host-scores.clj"></script>

`gen-host-scores` -

<script src="https://gist.github.com/316641.js?file=gen-host-scores.clj"></script>

- is called twice at the beginning of the program's execution:

<script src="https://gist.github.com/316641.js?file=gen-host-scores-exec.clj"></script>

Each time through the main execution loop, the urls crawled and urls saved thus far are written to disk. Thus, for the first call, an empty hash map is the starting point and each of the urls crawled costs its host one point. The second time, the hash of scores calculated the first time is the starting point and each of the mp3s found scores its host one point.

#### Being encouraged to think better

Through its immutable data structures and passable functions, Clojure is always pushing me to re-use code and employ recursion. I felt very cool when I was able to write the following function that accepts a function to filter a sequence:

<script src="https://gist.github.com/316641.js?file=remove-dupes-and-unwanted.clj"></script>

#### The main loop

Scrawl, the function that runs the show. If the list of passed url-crawl-agents is empty, a new batch is created and `scrawl` is called again. If the next agent on the list failed to complete its data request, it is thrown away and `scrawl` is called again. Otherwise, the function calculates all the required data and calls itself.

<script src="https://gist.github.com/316641.js?file=scrawl.clj"></script>

 [1]: http://github.com/maryrosecook/scrawl
 [2]: http://www.saidthegramophone.com/archives/in_this_box_or_another.php
 [3]: http://www.saidthegramophone.com
