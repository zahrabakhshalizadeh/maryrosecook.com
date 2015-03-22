---
title: Springs, weights and the morning light
author: maryrosecook
layout: post
permalink: /post/springs-weights-and-the-morning-light
categories:
  - Uncategorized
---
Tom Klein Peter wrote a [wonderful series of essays][1] about being the lead dev at Audiogalaxy. The excerpt below is one of my favourite pieces of writing about programming. It combines an analogy of springs and weights that illustrates the way programming can change the way you think about systems, and a description of the loveliness of the light in the morning after a night spent hacking that illustrates how the demands of your art can show you the world in a new way.

In short, it demonstrates how a part life of productivity and a part life of fun can enrich each other to produce something that makes your head explode with associative pyrotechnics.

> As we worked through the bugs, the uptime turned into minutes, then hours, and eventually the service virtually never crashed. With hundreds of instances deployed, we got so much traffic that we were able to remove all the bugs we were likely to run into. We had one or two machines that would crash every month or two with inscrutable core files. Because it was always the same machine, I eventually attributed this to faulty memory. The idea that you could write software that was more reliable than hardware was fascinating to me.
> 
> In fact, almost everything about the scale of the software fascinated me. I found that a system with hundreds of thousands of clients and thousands of events per seconds behaved like a physical machine built out of springs and weights. If one server process stalled for a moment, effects could ripple throughout the cluster. Sometimes, it seemed like there were physical oscillations – huge bursts of traffic would cause everything to slow down and back off, and then things would recover enough to trigger another burst of load. I had never even imagined that these sorts of problems existed in the software world and I found myself wishing I had taken control theory more seriously in college.
> 
> Keeping up with the traffic at this time was difficult, but in retrospect, it was really a lot of fun. I had graduated from UT in December of 2000 and moved downtown within walking distance of both 6th Street and the office. I spent the summer on a completely nocturnal cycle, partially because of the Texas heat, but mainly because restarting services was easier at 3 in the morning. I was tired of staying up late to deploy new code, so I just changed my schedule. Audiogalaxy users had led me to a set of live trance mixes from clubs in Europe which turned me into a diehard electronica fan, and driving around Texas to catch DJs on the weekend was much easier if staying up until 8am was normal. I bought some turntables and a lot of vinyl. And a couch. The light in my apartment when I got home in the morning was very lovely.

 [1]: http://www.tomkleinpeter.com/the-audiogalaxy-chronicles/