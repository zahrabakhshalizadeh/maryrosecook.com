---
title: Using cron with Rails on Slicehost
author: maryrosecook
layout: post
permalink: /post/using-cron-with-rails-on-slicehost-2
categories:
  - Uncategorized
---
I need to run a model method on my Slicehost production app every hour. I tried rake+cron, cronedit and a number of nut.crack(sledgehammer) queueing server solutions. Some didn&#8217;t work and the rest were unreliable. Therefore, for all you Ruby On Rails On Slicehost people, here is my approach. Treasure it.

1. ssh into your Slicehost account.

2. Switch to your user directory: `cd ~/`

3. Make a new directory: `mkdir cronlogs`

4. Make a new cron log file: `nano notifications.log`

5. Add a space to the file and then save it.

6. The crontab is a text file that holds all your scheduled cron jobs. Edit it: `crontab -e`

7. We are going to enter a line into this file. It will have the following structure: `[timings] [switch to your app directory] && [path to ruby] script/runner -e [environment] [method to run] > [path to cron log]`

8. Here is what I entered into my crontab. When you write your own version, make sure that the paths reflect where you have stored ruby and your app, and that you fill in the blank for your app name and set your own timings. `1 1 * * * cd /home/admin/public_html/[your app name]/current && /usr/local/bin/ruby script/runner -e production 'Match.generate_and_save_matches' > ~/cronlogs/notifications.log`

8a. It is a good idea to test the command you enter (minus the scheduling information) in your command line. If it doesn&#8217;t work there, it won&#8217;t work when run by cron, either. 

9. If you try and save your crontab and it says you have errors, make sure the whole thing is on one line.

10. If your model method runs OK, you will probably get no feedback. However, if there was an error, you should either get some mail or something should get written to the cron log you created.

[The tweet I sent once I got this working][1].

 [1]: http://twitter.com/maryrosecook/statuses/806692908