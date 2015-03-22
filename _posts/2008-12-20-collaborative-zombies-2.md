---
title: Collaborative zombies
author: maryrosecook
layout: post
permalink: /post/collaborative-zombies-2
categories:
  - Uncategorized
---
Most recommendation systems that one sees on the internet are based on collaborative filtering. The idea here is that if Johnny, Jenny, Joseph, Julian and Gilly all like A and B, there is probably a connection between those items. Thus, if someone likes A, you can recommend them B.

There are a couple of problems with this technique.

First, it skews itself towards popularity and consensus. If A is a really popular thing, it is more likely to appear in people&#8217;s likes and, thus, more likely to be recommended.

Second, the calculations are computationally intensive. At the absolute worst, you could have (items owned ^ users) links to explore. With a modest 100,000 users, each with a tiny collection of 10 items, this would equate to a maximum of 1.0 × 10^50 connections.

Both these problems can be somewhat mitigated. If you want to err towards the eccentric and the niche, you can lower the threshold of people required to deem two items connected. So, maybe you would only require Johnny, Jenny and Julian to like both A and B in order to connect those items.

If you want to make collaborative filtering less computationally expensive, you can do a couple of things. First, cut out low-frequency items. Second, process data in chunks. Doing 50,000 users in one chunk and 50,000 in another will lose you some connections, but it will also massively reduce the computational load.

Recently, I found a way to extract user music library data from [Emusic][1]. First, I found that if you add an Emusic user username onto &#8220;<http://www.emusic.com/profile/ajax/downloads/artists.html?nickname=">, you get back a list of artists by whom the user has downloaded songs. I compiled a [corpus][2] of common words, first names and last names. I wrote a script to fuse these two things and ended up with 382,808 music library artist entries for 15,155 users.

I wrote another script to extract 588,425 artist connections from this data. To deem two artists connected, the script required that they occur together in at least twenty people&#8217;s music libraries.

As some of you may know, [the perceptron][3] uses a number of music recommendation [data sources][4]. Each has a weight that is based on the approval that users have shown the data source&#8217;s recommendations. At the moment, the Emusic data source&#8217;s weight is hovering around 12%. The top two sources each have weights of 34%. However, Emusic&#8217;s weight will increase as its recommendations are exposed to more users.

One of my favourite things about the perceptron is that the vast majority of its [data sources][4] are based on conscious human opinions. The data comes from gig line-ups, mixtapes, human recommendations, artist admiration, record labels and mp3 blogs. I find collaborative filtering a little depressing: it&#8217;s just SO mechanical. One way to a) further improve its efficiency and b) make it a little more human would be to cluster users based upon demographic indicators, and then do the filtering. How ace would it be to be able to do collaborative filtering on the music libraries of everyone who went to the same gig? Or everyone who is the same age? Or groups of people who are friends with each other? Or who all like the same film? If [last.fm][5] aren&#8217;t doing this, they are fucking crazy.

 [1]: http://emusic.com
 [2]: http://github.com/maryrosecook/commonusernames/
 [3]: http://theperceptron.com
 [4]: http://theperceptron.com/stats
 [5]: http://last.fm