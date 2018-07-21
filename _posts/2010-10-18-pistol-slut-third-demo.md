---
title: Pistol Slut third demo
author: maryrosecook
layout: post
permalink: /blog/post/pistol-slut-third-demo
categories:
  - Uncategorized
---
It's starting to turn into a game. Last night, as I played through the level, I felt a first jolt of fun. I've made the frame much wider to allow the pitched battles that I think are going to be the core of the game. I've added mortars, floating Chinese lanterns and four planes of parallax scrolling. I added and then removed cycling of the colour of the sky from dark to light to dark. The enemies don't accidentally shoot each other any more, and they throw grenades.

It is so fun to work on a game. It's a completely different type of programming from making a website. There are far more interactions between different elements. It is a machine with parts working in concert, whereas a website is a landscape with disparate lanes leading away from each other. [Tom Klein Peter][1] said [this][2] of building Audiogalaxy:

> In fact, almost everything about the scale of the software fascinated me. I found that a system with hundreds of thousands of clients and thousands of events per seconds behaved like a physical machine built out of springs and weights. If one server process stalled for a moment, effects could ripple throughout the cluster. Sometimes, it seemed like there were physical oscillations – huge bursts of traffic would cause everything to slow down and back off, and then things would recover enough to trigger another burst of load. I had never even imagined that these sorts of problems existed in the software world and I found myself wishing I had taken control theory more seriously in college.

I am constantly generalising code so it can be shared: the code that moves objects around, the code for operating a weapon, the code for firing different types of ordinance, the code for different types of particle and particle effects. And I am constantly fighting to improve performance. Usually, when I add something new, the game becomes laggy. I then work on optimisation for a while until I get performance reasonable again. Sometimes, the optimisations involve re-writing the code to make it more efficient - for example, I rewrote the code that triggers events in the level to make the checks for trigger trips far quicker - this requires being clever. Other times, the optimisations come from finding something that is computationally expensive and removing it - for example, I chopped up the graphic for the front-most parallax level into smaller parts to stop the engine wasting time drawing the great tracts of empty space - this requires sacrificing code elegance.

 [1]: http://www.tomkleinpeter.com/
 [2]: http://www.tomkleinpeter.com/2008/03/13/users-with-a-tattoo-of-your-logo-check/
