---
title: Git in six hundred words
author: maryrosecook
layout: post
permalink: /post/git-in-six-hundred-words
categories:
  - Uncategorized
---
(This essay is a companion piece to [Gitlet][1], my implementation of Git in JavaScript.)

Imagine you have a directory called `alpha`. It contains a file called `number.txt` that contains the text `first`.

You run `git init` to set up `alpha` as a Git repository.

You run `git add number.txt` to add `number.txt` to the index. The index is a list of all the files that Git is keeping track of. It maps filenames to the content of the files. It now has the mapping `number.txt -> first`. Running the add command has also added a blob object containing `first` to the Git object database.

You run `git commit -m first`. This does three things. First, it creates a tree object in the objects database. This object represents the list of items in the top level of the alpha directory. This object has a pointer to the `first` blob object that was created when you ran `git add`. Second, it creates a commit object that represents the version of the repository that you have just committed. This includes a pointer to the tree object. Third, it points the master branch at the new commit object.

You run `git clone . ../beta`. This creates a new directory called `beta`. It initializes it as a Git repository. It copies the objects in the alpha objects database to the beta objects database. It points the master branch on beta at the commit object that the master branch points at on the alpha repository. It sets the index on beta to mirror the content of the first commit. It updates your files - `number.txt` - to mirror the index.

You move to the beta repository. You change the content of `number.txt` to `second`. You run `git add number.txt` and `git commit -m second`. The commit object that is created has a pointer to its parent, the first commit. The commit command points the master branch at the second commit.

You move back to the alpha repository. You run `git remote add beta ../beta`. This sets the beta repository as a remote repository.

You run `git pull beta master`.

Under the covers, this runs `git fetch beta master`. This finds the objects for the second commit and copies them from the beta repository to the alpha repository. It points alpha's record of beta's master at the second commit object. It updates `FETCH_HEAD` to show that the master branch was fetched from the beta repository.

Under the covers, the pull command runs `git merge FETCH_HEAD`. This reads `FETCH_HEAD`, which shows that the master branch on the beta repository was the most recently fetched branch. It gets the commit object that alpha's record of beta's master is pointing at. This is the second commit. The master branch on alpha is pointing at the first commit, which is the ancestor of the second commit. This means that, to complete the merge, the merge command can just point the master branch at the second commit. The merge command updates the index to mirror the contents of the second commit. It updates the working copy to mirror the index.

You run `git branch red`. This creates a branch called `red` that points at the second commit object.

You run `git checkout red`. Before the checkout, `HEAD` pointed at the master branch. It now points at the red branch. This makes the red branch the current branch.

You set the content of `number.txt` to `third`, run `git add numbers.txt` and run `git commit -m third`.

You run `git push beta red`. This finds the objects for the third commit and copies them from the alpha repository to the beta repository. It points the red branch on the beta repository at the third commit object, and that's it.

 [1]: http://gitlet.maryrosecook.com