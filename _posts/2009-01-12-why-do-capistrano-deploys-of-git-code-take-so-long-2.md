---
title: Why do Capistrano deploys of git code take so long?
author: maryrosecook
layout: post
permalink: /blog/post/why-do-capistrano-deploys-of-git-code-take-so-long-2
categories:
  - Uncategorized
---
1. I am a bumbrain.  
2. I am using git. Thus, the whole repository, including all previous revisions, is kept in a folder called .git which lives inside my local code copy. A Capistrano deploy involves *uploading* a tarred copy of the project via *ssh* to the server. This is slow.

I plan to dump Capistrano and change my deployment process to: log into server, git pull from [Github][1] (only a diff, so super quick), restart mongrels.

 [1]: http://github.com