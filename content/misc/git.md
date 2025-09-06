---
weight: 5
title: "Git"
# bookFlatSection: true
# bookToc: false
bookCollapseSection: false
---
# Git - Version Control System
Understanding the fundamentals & inner model of git to be able to better work with it.

This should be a good learning resource : [How Git Works](https://app.pluralsight.com/library/courses/how-git-works/table-of-contents)

## Storing data
How does git store data?
The raw data, AKA blobs, are stored in the .git/objects directory along with some metadata in a compresssed format. They are addressed by SHA-1 hashes.
Blobs are one type of object. Others being commits, and trees.
It's possible to store a new blob by doing `git hash-object path-to-the-file-containing-data -w`.
You can find out the type of any object by doing `git cat-file the-hash-of-the-objkt -t`, and print the file by doing `git cat-file the-hash-of-the-objkt -p`.

There are two main groups of commands, porcelain commands which are used by most users and plumbing commands which are used mostly by internal tools.
The plumbing commands, e.g., `git count-objects`, `git hash-object` etc. are still accessible to users to run.

A tree is list of hashes to other trees and blobs, along with their file-system name and permissions. It's similar to a directory of Linux.

A commit contains a hash to a tree, hash of the parent commit, and info about the author, created timestamp etc.

So, in a way, a commit is a snapshot of a repository at a period of time.
Regarding the storage, git does optimizations to store changes to large files by storing diffs, but the overall interface of a commit being a snapshot is still the same.

Thus, it is possible to see how a git server like GitHub can have an entire repo without storing the actual current status of files. It just needs the object database.

Fun note, even if you stage things, the corresponding blobs are created for them and stored in the object DB.

Every object in the object DB is immutable.

## Branches
A branch is a pointer to a commit.
The branches in a project are listed in the `.git/refs/heads/` folder. Each branch is a file which has the commit hash inside it.

The current branch is identified by the `.git/HEAD` file, which has the name of the current branch's file inside it.

When you make a new commit in the current branch, git just changes the location of the pointer of the current branch to the new commit.
When switching branches, git changes the files to the tree referenced in the commit of that branch.

A merge commit is just like any other commit, just that it has two or more parents. The parents are the commits of the branches which we are merging.

How can you move a branch which has been merged into another branch to the latest commit after the merge?
Well, you can merge the branch pointing to the latest commit into your branch.
Would this create a new commit? No. It doesn't need to. It'll just move the branch to the latest commit, since that already has the changes we need.
This process is called a "fast forward".

It's also possible to move HEAD to a commit itself, rather than a branch. After doing that it's possible to make new commits as well. These won't be tagged to a branch and will be tracked by just HEAD.

How do you create a new branch? Just do `git branch newbranchname` or if you have a commit hash, `git branch newbrname commithash`.

Git periodically garbage collects objects which cannot be reached by HEAD / branches / tags to save space.

A rebase operation, which "moves the base" of a current branch to another branch works by creating clones of all the commits, starting from the first commit which diverges from the other branch to the latest commit of the current branch. In all these clones, the parent commit id is changed and the tree is changed as well (after resolving conflicts).

If you have some remotes for the repo, git fetches information about all the branches in the remote.
They would be stored under `.git/refs/remotes/` for each remote. As an optimization, they might also be stored inside the `packed-refs` file.
Remotes can also have their own HEAD.

## Tags
Tags are similar to branches. They are also pointers to commits.
The only difference is that they don't move like how branches do. Tags just keep pointing to the same commit regardless of the activity that the user has done.
They can be created by `git tag newtagname`. It just creates a new file under `.git/refs/tags/` which has the hash of the commit.

It's also possible to create an annotated tag, where you can provide additional metadata like description.
This also creates a new file under the `refs/tags` directory, but in this case it has the hash of an annotated-tag object, which contains extra info like the tag description and creator.

## Remotes
When syncing a remote repo, either with push or pull, git copies objects and branches.
When working with multiple people, it's a good idea to merge diverging changes than rebasing as it will make everyone else resolve conflicts.

When there are forks, the common naming convention is to call your forked remote as "origin" and the main repo as "upstream".

If you want to learn more about Git, you can check the "deep dive" pluralsight course - https://app.pluralsight.com/library/courses/git-deep-dive/table-of-contents.