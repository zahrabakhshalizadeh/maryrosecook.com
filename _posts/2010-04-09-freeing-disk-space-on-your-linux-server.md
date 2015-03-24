---
title: Freeing disk space on your Linux server
author: maryrosecook
layout: post
permalink: /post/freeing-disk-space-on-your-linux-server
categories:
  - Uncategorized
---
The websites that I host on [Slicehost][1], [Playmary][2] and [Street Hoarding][3], keep crashing because my slice keeps running out of disk space.

To find out where disk space is being used:

1. Get to the root of your machine by running `cd /`  
2. Run `sudo du -h --max-depth=1`  
3. Note which directories are using a lot of disk space.  
4. `cd` into one of the big directories.  
5. Run `ls -l` to see which files are using a lot of space. Delete any you don't need.  
6. Repeat steps 2 to 5.

 [1]: http://slicehost.com
 [2]: http://playmary.com
 [3]: http://streethoarding.com