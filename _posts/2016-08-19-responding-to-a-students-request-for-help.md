---
title: Responding to a student's request for help
author: maryrosecook
layout: post
permalink: /blog/post/responding-to-a-students-request-for-help
---

For the last three months, I've been working at <a href="http://makersacademy.com">Makers Academy</a> as a programming coach.  I've learnt a lot about the craft of coaching.  I spend part of my time working with students who have asked for help.  To help me train my ability to help students learn to learn, I wrote down a process that I follow to respond to a request for help.  The process has changed as I've learnt more.  Here's the latest version:

### Process

1. Map the student's request for help to one of the [underlying problems](#problems-underlying-a-request-for-help) listed below.

2. Apply one of the suggested [solutions](#solutions).

3. Ask the student for feedback on your help.

### Problems underlying a request for help

#### The student doesn't have a clear problem explanation

* [Help student clarify their problem explanation](#help-student-clarify-their-problem-explanation).

#### The student wants a concrete piece of information

* Maybe suggest they follow the [escalation process](#escalation-process).

* Maybe suggest they look it up.  Make them feel [held](#feeling-held).

#### The student doesn't know how to implement a piece of functionality

* Maybe suggest they follow the [escalation process](#escalation-process).

* Maybe figure out a technique that will solve their problem.  [Suggest reading material](#suggest-reading-material) on the topic.

* Maybe [identify and train a developer skill](#identify-and-train-a-developer-skill) that will help them.

#### The student doesn't know how to implement a piece of functionality, and nor do you

* Suggest they employ the [developer skill](#identify-and-train-a-developer-skill) that you would use in this situation.  Stay with them as they apply the skill.  Give instructions that are as high level as possible.  Suggest they work on their own once it becomes clear they are on the right track.

#### The student wants your opinion on their idea

* Maybe suggest they follow the [escalation process](#escalation-process).

* Maybe [identify and train a developer skill](#identify-and-train-a-developer-skill) like fast prototyping or diagramming.

* Maybe [identify and train a developer behaviour](#identify-and-train-a-developer-behaviour) like reflection.

* Maybe, if the idea is bad, guide them towards another idea.

#### The student wants an intro to a topic

* Maybe suggest they follow the [escalation process](#escalation-process).

* [Suggest reading material](#suggest-reading-material).

#### The student has a bug they should be able to solve on their own

* Maybe suggest the student follow the [debugging process](#debugging-process).

* Maybe suggest they follow the [escalation process](#escalation-process).

#### The student has a bug they don't have the background knowledge to fix on their own

* [Suggest reading material](#suggest-reading-material) that will provide the background knowledge.

#### The student has a bug for which you can't predict the likely cause

* Run through the [debugging process](#debugging-process) with the student, suggesting high level causes like "maybe the server is erroring when it responds to the Ajax request" and letting the student worry about the details.  Suggest they work on their own once it becomes clear they are on the right track.

#### The student has a bug that will take them a long time to fix

* Debug and fix the problem for them.  Whilst keeping the process efficient, try and make it as [open](#open-process-vs-closed) as possible.  Try and explain how you know to try the things you're trying.

### Solutions

#### Escalation process

* Make the student feel [held](#feeling-held) first.  Then suggest the [escalation process](https://github.com/makersacademy/course/blob/master/pills/escalation_process.md). [The link is to an internal document. It describes a process that helps the student help themselves.  It suggests: gathering words to describe the problem, Googling, talking to a pair programming partner, and asking on the student Slack channel.]

#### Identify and train a developer skill

For example: diagramming, delegating behaviour, breaking classes into single responsibilities, debugging asynchronous code, 5 whys, falsifying assumptions, following the flow etc.

* You can identify a skill that's lacking by looking at how the student makes their request, what solutions they've tried, and how they've tried to find the problem.

* Name the skill you want the student to train to set their expectations.  Suggest they employ the skill to solve the problem.  Name the skill at the end of the session to help them remember to use it.

* Figure out how much to scaffold the skill.  Maybe the first time that you suggest diagramming, you'll need to walk the student through it.  But, in future interactions, you should be able to reduce the scaffolding.

#### Identify and train a developer behaviour

* For example: the XP values.

#### Help the student clarify their problem explanation

* Maybe ask them to clarify their request for help.

* Maybe use 5 whys.

#### Debugging process

1. Tighten the loop.

2. Get visibility (aka `p` everywhere).

#### Suggest reading material

* Suggest a blog post. Or suggest a pill [an internally written condensed introduction].

### Definitions

#### Feeling held

* Reassure the student that you have heard and understood their request and that you care about it.  This may involve explaining why you're not giving them the answer to their request.

#### Open process (vs closed)

* An open process is one that, though directed, helps the student to learn more than a closed process.  For example, letting the student type, rather than typing yourself, or saying "can you use the filter function for that?" rather than "type dot f i l t e r ...".

### Traps I sometimes fall into

* Giving into the student's desire to get a solution NOW.  In these cases, I should try to get them out of their writing code brain and into their observing brain.

* Asking breadcrumb questions that really just require reading my mind.

* Not having a clear plan for where I want the interaction to go, and jumping between methods of helping the student.
