---
title: Machine.js
author: maryrosecook
layout: post
permalink: /post/machine-js
categories:
  - Uncategorized
---
The first version of the artificial intelligence code that controlled the enemies in [Pistol Slut][1], my 2D platform shooter, was a nightmare thicket of if statements. Fortunately, someone told me about behaviour trees, and, after reading half of [an excellent article about their implementation in Halo 2][2], I rewrote the Pistol Slut AI code as a hierarchical state machine. To do this, I made a little JavaScript library called [Machine.js][3] that implemented a generic behaviour tree. This code is open source, like all of [the code I write in my spare time][4], but, today, I broke it out into its own library and released it as a separate project. This should make it easier for other people to reuse.

Go to the [Machine.js home page][3] to get the code and read a tutorial.

And, in case you are interested, here is the entire behaviour tree for the enemies in Pistol Slut:

 [1]: http://pistolslut.com
 [2]: http://www.gamasutra.com/gdc2005/features/20050311/isla_01.shtml
 [3]: http://machinejs.maryrosecook.com
 [4]: http://github.com/maryrosecook