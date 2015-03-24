---
title: 'My first open source app: Playmary'
author: maryrosecook
layout: post
permalink: /post/my-first-open-source-app-playmary-2
categories:
  - Uncategorized
---
Grab a copy from the [Github repository][1]. There are full set up instructions in the README.

First, I cleaned up the app folder. I put references to all my personal scripts, documentation files, deploy.rb and database.yml into .gitignore. I put database.yml in the shared folder on the server, then added a task to my Capistrano deploy.rb so it would create a symlink from shared/config/database.yml to current/config

Second, I cleaned all the secret and Playmary.com-specific stuff out of the code: Amazon AWS login details, the link to the default audiography, contact information. I put it all into shared/config/config.yml and then wrote a method that returns the data referenced by the passed key.

Finally, I had to get rid of all this secret stuff from the project's git history. After wrestling with rebase for quite a while, I gave in. I deleted the .git folder in my local copy of the code, changed the name of the Github repository to playmary-old, created a new repository on Github called playmary and pushed my local copy of the code into it.

 [1]: http://github.com/maryrosecook/playmary