# How to GIT Better

This is a guide for using git to keep your work from getting destroyed by you or the monsters! This guide explains the confusing terminology simplified for artists, and also includes some of the most regularly used commands for programmers.

## What is git?
Git is a version control software with built-in collaboration tools. It allows people to work on "multiple versions" of a project and merge these versions together. It is efficient and is used by millions of people every day. GitHub, GitHub Desktop, SourceTree, GitLab, and many other tools are built on top of git.

## Terminology

**Version Control Software** - A piece of software that keeps track of the changes made to a file and allows a user to jump back to a previous state if they accidentally broke something.

**Repository** - A folder A.K.A. a directory that is being tracked and observed by git. Inside every git repository is a hidden folder named *.git* where all the version-tracking magic happens. Never touch this folder unless you know what you are doing.

**Commit** - A collection of changes to files in a repository that should be saved as a "version" in git. You can think of this like a save state on an emulated game. A commit can track added, removed, moved, and modified files. You can attach a message to each commit to summarize what changes you made.

**Staging** - A list of files to commit. If you have a bunch of modified files but only want to commit one or two. You can add the files to the staging area and then only commit staged files.

**Branch** - A sequence of commits. Branches are useful when multiple people want to work from the same base state without having to constantly integrate each other's changes. It also allows you to work on two different features from the same base state without losing work. For example, if you started building a more advanced AI, but accidentally broke something that causes NPCs to run off the map, and then you get asked to update a particular NPC's dialog, you can switch to a new branch without the broken AI and update the dialog, and then later return to your broken AI and fix it. 

**Remote** - A remote is a repository on a server (often a Cloud Service) that is tied to your local git repository. This allows multiple users to work together to build the project on the remote and share work while all working on their own individual versions. GitHub is a cloud service that holds the remote. Somewhere in the world, there is a giant room filled with hundreds of high-powered computers, and one or more of these computers may be holding the remote repositories that we work with every day. It's pretty cool, but there's a couple of gotchas.

1)  **You can make commits while not connected to the internet!**
2)  **Commits are NOT automatically synced to GitHub!** Just because it shows up in GitHub desktop doesn't mean it is on the website version.

**Push/Pull** - The means to "sync" your local git repository to the remote server. Push sends your commits to the server. Pull grabs commits from the server. 

**Merge** - The process of combining one branch with another.

**Rebase** - The process of re-ordering and combining commits. Rebasing can also merge branches together and reorder the final commit order.

**Conflict** - The point when two commits from different branches interfere with each other during a merge or a rebase and the computer doesn't know what to keep and what to throw away. As an example, imagine Mary was working on the player character and realized that the speed powerup wasn't good enough. So she changed the effect to 1.8x. At the same time, Drew was working on the enemies who can also collect and use the same speed powerup. He also discovered that the powerup wasn't good enough, so he changed the effect to 1.6x. When Mary and Drew tried to merge their changes, they were alerted of the conflicting speed powerup multiplier values. 

**Workflow** - The process someone takes for creating a new feature and eventually merging it with everyone else's work.

## The GCO GIT Workflow
The workflow GCO uses is based on one of the most common workflows with git. The branch that contains everyone's merged work is named the *master* branch. The workflow can be broken down into the following steps:

1)  Pull the most up-to-date master branch.
2)  Create a new local branch based on your local master. 
3)  Add your new features and changes to this local branch.
4)  Push your branch to GitHub.
5)  Repeat steps 4 and 5 until you are ready to merge.
6)  Create a merge request (unintuitively called "Pull Request" in GitHub) for your feature branch into master.
7)  Wait for someone else to review and approve the changes. 
8)  Celebrate other peoples' really good merge requests by spamming LGTM! (Let's Get This Merged!) in the chats.
9)  Rebase merge the remote master branch into the remote feature branch to ensure compatibility.
10)  Merge into master.

For feature branches that are expected to have a lot of conflicts with master, it is a good idea to create a new branch based on the feature branch and then rebase the latest master. Once the rebase is successful, push and create the merge request. This prevents the working feature branch from being destroyed by the rebase process. 

**Artists and Designers:** If any of these steps confuse you, please ask a programmer for assistance.

## Power GIT using Git Bash
Git was originally built for programmers, and as such has a powerful command line interface. If you are a programmer, you are highly encouraged to learn the command-line interface. If you are on Windows, this tutorial assumes you will be using Git Bash. It normally comes installed with GitHub Desktop.

### General Command-Line commands and tricks to get started.

List out all the files and directories in the current directory.
```
ls
```

Change your current directory to the subdirectory inside named subfolder1.
```
cd subfolder1
```

Change your current directory to the parent directory.
```
cd ..
```

Change your current directory to the subdirectory of a sibling directory.
```
cd ../siblingB/siblingSubDirectory1
```

*Pressing tab will try to autocomplete or partially autocomplete if possible. 
*Pressing tab twice will list out all the child directories if you are typing out a path. 
*Pressing the up and down arrows will autofill with a previous command. 

### git commands
We'll assume you already have the repository cloned onto your machine with GitHub Desktop.

You only have to do this once when you install git. It prevents your local commits from getting interleaved with the remote commits when pulling. This saves a ton of headaches.
```
git config --global pull.rebase true
```

Shows what changes are staged, which are uncommitted, and if any files have been added or removed. Also shows how your branch compares to a remote branch.
```
git status
```

Prints out the most recent commits on the branch. Use this for sanity checks when doing pulls and rebases.
Press q to exit the log.
```
git log
```

Lists all the branches you have locally and places a * next to the branch you are currently on.
```
git branch
```

Pulls the remote version of the branch. Most of the time you want to be on your local master with a clean git status when you do this.
```
git pull
```

Switch to another local branch named MyFeatureBranch. MyFeatureBranch can be autocomplete-able using tab after typing enough unique letters.
```
git checkout MyFeatureBranch
```

Copy and sync a remote branch and switch to it. *Note for power users: You do not need to use `git fetch` to do this. In fact, it is possible to use the entire workflow without ever typing `git fetch`. Hence why that command is not included in this list. Under this workflow, it is more likely to pollute your local repository with stale branches than do anything useful.
```
git checkout SomeRemoteBranch
```
Yup. It is exactly the same as above.

Create a new local branch based on the current branch and switch to it.
```
git checkout -b NewBranchName
```

Commit any modified and/or deleted tracked files as well as all staged files.
```
git commit -a
```

Same as above except that it allows you to add the commit message directly in the command.
```
git commit -am "This is the commit message where you can type whatever you feel is useful here."
```

Stage a file or folder. Note that you must stage a file to get git to start tracking it.
This example stages all untracked and modified code files and .meta files in a Unity Project inside a folder named _Code.
```
git add Assets/_Code/
```

Commits all staged files.
```
git commit
```

Same as above except with the commit message in the command.
```
git commit -m "This commit only commits the staged files."
```

Compare your local uncommitted changes to the last commit. This is useful if your status isn't clean and you forgot why. Press q to exit when you are done looking.
```
git diff
```


Push your commits to the associated remote branch.
```
git push
```

Push your commits to a new remote branch named the same as your local branch and associate the branches.
```
git push -u origin FeatureBranchName
```

If the above fails due to the remote not being named *origin*, use the following instead.
```
git push -u $(git remote) FeatureBranchName
```

Update your local branch with local master by copying master and re-adding your commits on top.
Run this while on your feature branch.
```
git rebase master
```

If you did the global setting thing, you can also update your local feature branch with the remote master without interleaving commits with the following.
```
git pull master
```

The following two sets of commands are equivalent to the above process but do not require that global setting thing.
```
git pull master --rebase
```

```
git checkout master
git pull --rebase
git checkout FeatureBranchName
git rebase master
```

### Damage control commands

Something is broken. You need to backtrack until things stopped being broken to find which commit broke things.
Switch to the commit 1 back from where you are.
```
git checkout HEAD~1
```

Switch to the commit 2 back from where you are.
```
git checkout HEAD~2
```

Jump back to the present.
```
git checkout BranchName
```

You accidentally started making changes to your local master branch when you wanted to make the changes to a new feature branch.
Use git stash to copy your uncommitted changes and new files. You can swap the middle command with a regular checkout to switch to an already existing branch.
```
git stash
git checkout -b NewFeatureBranch
git stash apply
```

You accidentally made changes you don't want to keep.
```
git stash
git stash drop
```
or
```
git reset --hard HEAD
```

You accidentally committed something you weren't ready to commit yet.
Do not include the `--hard` because you still want to keep the changes.
```
git reset HEAD~1
```

You were riding the Ballmer's Peak for a while and then accidentally went past it for the last three commits. You want those last three commits erased from existence, but want to keep everything before it.
```
git reset --hard HEAD~3
```

You want to combine the last 4 commits and give it a new commit message.
```
git reset HEAD~4
```
Then add and commit the changes you want to keep in the new commit.

You want to combine the last 4 commits, but you liked the commit messages and want to combine them.
Or maybe you want to compress the last 4 commits into two separate commits. `-i` brings up an editor where you can do all sorts of fancy stuff.
```
git rebase -i HEAD~4
```
*Squash* means that you combine the commit with the previous commit. 
*Pick* means you keep the commit. 
After updating which commits to squash and which to commit, you'll then be able to update the messages.

### Got your own favorites? Add them below!
