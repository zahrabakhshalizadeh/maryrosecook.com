---
title: What I do when a student asks for help
author: maryrosecook
layout: post
permalink: /blog/post/responding-to-a-students-request-for-help
---

For the last three months, I've been working at <a href="http://makersacademy.com">Makers Academy</a> as a programming coach.

I spend part of my time working with students who have asked me for help.  My goal is for the student to learn to learn.  To improve my ability to help with this, I wrote down a process that I follow.  The process has changed as I've learnt more.  Here's the latest version:

### Process

1. Map the student's request for help to one of the [underlying problems](#problems-underlying-a-request-for-help) listed below.

2. Apply one of the suggested [solutions](#solutions).

3. Ask the student for feedback on my help.

### Problems underlying a request for help

#### The student doesn't have a clear problem explanation

* [Help the student clarify their problem explanation](#help-the-student-clarify-their-problem-explanation).

#### The student wants a concrete piece of information

* Maybe suggest they follow the [escalation process](#escalation-process).

* Maybe suggest they look it up.  Make them feel [held](#feeling-held).

#### The student doesn't know how to implement a piece of functionality

* Maybe suggest they follow the [escalation process](#escalation-process).

* Maybe figure out a technique that will solve their problem.  [Suggest reading material](#suggest-reading-material) on the topic.

* Maybe [identify and train a developer skill](#identify-and-train-a-developer-skill) that will help them implement the functionality.

#### The student doesn't know how to implement a piece of functionality, and neither do I

* Suggest they employ the [developer skill](#identify-and-train-a-developer-skill) that I would use in this situation.  Stay with them as they apply the skill.  Give instructions that are as [open](#open-process-vs-closed) as possible.

#### The student wants my opinion on their idea

* Maybe [identify and train a developer skill](#identify-and-train-a-developer-skill) like fast prototyping or diagramming.

* Maybe [identify and train a developer behaviour](#identify-and-train-a-developer-behaviour) like reflection.

* Maybe, if the idea is bad, guide them towards another idea.

#### The student wants an intro to a topic

* Maybe suggest they follow the [escalation process](#escalation-process).

* Maybe [suggest reading material](#suggest-reading-material).

#### The student has a bug they should be able to solve on their own

* Maybe suggest the student follow the [debugging process](#debugging-process).

#### The student has a bug they don't have the background knowledge to fix on their own

* [Suggest reading material](#suggest-reading-material) that will provide the background knowledge.

#### The student has a bug for which I can't predict the likely cause

* Run through the [debugging process](#debugging-process) with the student, giving [open](#open-process-vs-closed) suggestions like "maybe the server is erroring when it responds to the Ajax request" and letting the student worry about the details.

#### The student has a bug that will take them a long time to fix

* Debug and fix the problem for them.  Whilst keeping the process efficient, try and make it as [open](#open-process-vs-closed) as possible.  Try and explain how I know to try the things I'm trying.

### Solutions

#### Escalation process

* Make the student feel [held](#feeling-held) first.  Then suggest the [escalation process](https://github.com/makersacademy/course/blob/master/pills/escalation_process.md). [This link isn't publicly accessible, I'm afraid.  It points to an internal Makers Academy document.  The document describes a process that helps the student help themselves.  It suggests: gathering words to describe the problem, Googling, talking to a pair programming partner, and asking on the student Slack channel.]

#### Google it

* Make the student feel [held](#feeling-held) first.  Then suggest they Google their problem.

#### Identify and train a developer skill

For example: diagramming, delegating behaviour, breaking classes into single responsibilities, debugging asynchronous code, 5 whys, falsifying assumptions, following the flow.

* I can identify a skill that's lacking by looking at how the student makes their request, how they've tried to find the problem and what solutions they've tried.

* To set the student's expectations, name the skill I'm going to help them train.  Suggest they employ the skill to solve the problem.  Name the skill at the end of the session to help them remember to use it.

* Figure out how much to scaffold the skill.  Maybe the first time that I suggest diagramming, I'll need to walk the student through it.  But, in the future, I should be able to reduce the scaffolding.

#### Identify and train a developer behaviour

* For example, one of the [XP values](http://www.extremeprogramming.org/values.html).

#### Help the student clarify their explanation of their problem

* Maybe ask them questions to clarify their request for help.

* Maybe use [5 whys](https://en.wikipedia.org/wiki/5_Whys).

#### Debugging process

1. Tighten the loop.  This means following the flow of execution to find the line of code that's causing the bug.

2. Get visibility (aka `p` everywhere).  This means using stdout or a debugger to see the current state of the program.

#### Suggest reading material

* Suggest a blog post. Or suggest a pill [an internal document that gives a condensed introduction to a topic].

### Definitions

#### Feeling held

* Reassure the student that I've heard and understood their request.  Reassure the student that I care about them and their request.  This may involve explaining why I'm not giving them the answer.

#### Open process (vs closed)

* An open process is one that helps the student to learn more than a closed process.  For example, letting the student type, rather than typing myself, or saying "can you use the filter function for that?" rather than "type dot f i l t e r ...".

### Traps I sometimes fall into

* Giving in to the student's desire to get a solution NOW.  In these cases, I should try to get them out of their writing code brain and into their observing brain.

* Asking breadcrumb questions that really just require reading my mind.

* Not having a clear plan, and jumping between methods of helping the student.
