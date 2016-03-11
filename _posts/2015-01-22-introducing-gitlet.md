---
title: Introducing Gitlet
author: maryrosecook
layout: post
permalink: /blog/post/introducing-gitlet
categories:
  - Uncategorized
---
For the last few months, I have been writing [Gitlet][1], an implementation of Git in JavaScript. This post is about the code and what it was like to write it.

I had two goals with Gitlet.

First, I wanted to learn the innards of Git really well.

Second, I wanted to use that knowledge to produce an essay, <a href="/blog/post/git-from-the-inside-out">Git from the inside out</a>, that gives a clear, deep explanation of Git and the ideas underlying it.

My implementation of Git is not intended to be fast or feature-complete. It is intended to support commands that are commonly used and that demonstrate the core ideas in Git: `init`, `add`, `rm`, `commit`, `branch`, `checkout`, `diff`, `remote`, `fetch`, `merge`, `push`, `pull`, `status` and `clone`. For example, I implemented `remote`, but only supported `git remote add` because `git remote remove` and the rest are just conveniences and their implementation says nothing about the essence of Git.

For another example, I implemented `merge` and supported fast forwards, un-conflicting merges and conflicted merges. But, for conflicted files, the whole file is written as conflicting, rather than just the incompatible sections. The difference between fast forwards and merge commits gets to the heart of Git. Figuring out which sections of a file have changed does not.

I wrote the code to be short and very easy to understand. I eagerly welcome pull requests that make the code shorter or easier to understand. And, of course, bug fixes are much appreciated.

It's hard to say how much you need to know about Git to understand the code. If you don't know much about the inner logic of Git, it is probably worth it to first read my essay, [Git in six hundred words][2]. But, if you already know what blobs and trees and fast forwards are, the code should be interesting. Though the entire project is 1000 lines, the implementation of the main API commands is only 350 lines.

Finally, I want to talk about what it was like to write the code.

First, it was hard to figure out how Git works. I went to the actual source to answer a few questions. But doing that was time-consuming because I don't know C well. [A Hacker's Guide to Git][3] was a killer introduction to the internals of Git. It gave me a grounding in most of the commands I implemented. Stack Overflow was helpful on some obscure questions. I read a lot of Git documentation. I referred again and again to [this elegant, detailed Quora answer][4] on how merging works. I didn't implement recursive merges in the end, but this great [Codice Software post][5] helped me understand them. But, mostly, I could only answer my questions by creating repositories and running commands and diving into the `.git` directory to see what had happened.

Second, I had a strange feeling of discovery. This feeling is hard to talk about. Some behaviours were inexplicably difficult to implement. I'd write ten lines that felt like they should be two. But, slowly, patterns began to emerge. I found that particular shapes of data occurred again and again. I could represent directory hierarchies with nested objects. I could represent indices by mapping file paths to hashes. I could represent diffs by mapping paths to a change status and the hashes of the old, new and base versions. Once I had code to create and translate these data structures, the Git API became much easier to implement.

And I found that fundamental operations on the `.git` directory could be composed to implement Git API commands. For example: implementing checkout. Read the current `HEAD`, get the commit hash it points at, read the commit object, read the commit tree content and convert the content to an index. Repeat for the branch to be checked out. Diff the two indices. Write that diff to the working copy. Write the name of the branch being checked out to `HEAD`. Convert the commit being checked out to an index. Write that index to the `index` file.

These basic data types and operations seemed to express something of the fundamental nature of Git. I've had this feeling before when working on novel projects. But it was funny to feel the sensation of discovery when working on something that already existed.

 [1]: http://gitlet.maryrosecook.com
 [2]: /blog/post/git-in-six-hundred-words
 [3]: https://wildlyinaccurate.com/a-hackers-guide-to-git
 [4]: https://www.quora.com/How-does-Git-merge-work/answer/Anders-Kaseorg?srid=I5Y&#038;share=1
 [5]: http://codicesoftware.blogspot.com/2011/09/merge-recursive-strategy.html
