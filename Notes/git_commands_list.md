# this file will help understand the group workflow commands

# some commands that are gonna help revert some things
git commit --amend
this command will let you cahnge the git commit message of a previous commit
-> caution: dont use this when the last commit is of remote, as if you amend the commit, the SHA changes in your local and the git thinks you have diverged, to solve this you need to force push which will diverge our work and others will have problem

git reset --soft HEAD~1
resets the commit tlil 1 and retain the work in the worktree that is sstaging area
git reset --hard HEAD~1
this will delete the commit from every where


# Reflog
git reflog
this is like logs but it tracks the movement of HEAD reference points and of other references (from other branches), can be hifgly usefull to retrieve a commit lost  to git reset --hard, this also tracks the head, when we move different braches it shows that, its idea is to capture the time steps we took in the work flow
it saves the commits even when erased by git reset where as git log dont
it can be used for retrieving a lost branch commit, as the branch might be deleted, the commit might be deleted the commit hash and its data is always there, hence if we get commit hash we can use git cat-file -p hash to reach the blob hash and print its content, effectively retrieving the data.

Using Git internals is exceptionally inconvenient. We had to copy/paste and use the cat-file command 3 times!

I would not recommend doing it that way, but I wanted to drive home the point that you can always drop down to the plumbing commands if needed.

Luckily, there is a better way. The git merge command actually takes a "commitish" as an argument:

git merge <commitish>

## very very important
A "commitish" is something that looks like a commit (branch, tag, commit, HEAD@{1})

In other words instead of:

git reflog (find the commit sha at HEAD@{1})
git cat-file -p <commit sha>
git cat-file -p <tree sha>
git cat-file -p <blob sha> > slander.md
git add .
git commit -m "B: recovery"

We could have just done:

git merge HEAD@{1}

The more you know!


# Merge Confilcts
these occur when we try to commit merge from one branch to another with lines changed that cannot be automatically mmerged
for example, -> previous line: "hello" in main branch
             -> change added : "world" in feature branch
this will result in a conflict cause git dont know what to keep and what to not
auto merge works when new line are added at end, or the lines changed were empty before

git conflict will look like this

<<<<<<< HEAD
        count = 1
        count += 1
=======
        count = 2
        count++
>>>>>>> main

This message contains two parts head, and "otherBranch" this means that the other branch is bieng merged in the Head (Current Branch) and these lines are conflicted
To resolve this we need to pick what is needed and what is not and only let that remain
The above message should look like this

        count = 1
        count ++

then save the file and commit the changes to merge, actually you are changing the file on current branch so that it can be merged with the incoming brarnch.

theres one more command used to sort confilcts 

git checkout --ours path/to/file
this is used to retain the changes in our feature branch (on the branch you are at that point), it is specified to one file or subdirectory at a time

git checkout --theirs path/to/file
this is used to retain the changes from the main branch (the branch you are merging into the current branch)
# Rebase Conflicts

git rebase main
you are on delete_records branch, and dicided to make the branch catchup to main, but now there some conflicts

you can either abandone the rebase by
git rebase --abort

or you can reslove the coflicts by just resloving the confilcts normally,

we can also use checkout commands

BUT here the analogy flips
in rebase you are ripping the current branch and merging into main or the target branch (just a analogy)
so incoming branch is our feature branch and the current branch is main or target branch
Note when you see git branch, we are not any branch, as while we rip the feature branch we were on it and to do rebase we go in this special state, no branch rebasing feature

git checkout --ours path/tp/file
here the rebase takes main branch as ours rather than feature (or the branch we were on), and hence saves all the changes from the main branches for this file
git checkout --theirs path/to/file
here the rebase takes feature branch as theirs rather than the main (or the branch we were rebasing), and hence saves all the changes from the feature branch for this file 

# git rerere
The git rerere functionality is a bit of a hidden feature. The name stands for “reuse recorded resolution” and, as the name implies, it allows you to ask Git to remember how you’ve resolved a hunk conflict so that the next time it sees the same conflict, Git can resolve it for you automatically.

git config --local rerere.enabled true

this activates the auto tracking for conflicts
a branch gets branched multiple times, let the main brach have three feature branches A, B, C now when A finished its feature it commit making B conflict in a file, this file-k lets say was not touched by B or C but because A changed it, these files will conflict for both, hence when B commits with "rerere" feature on, they save how to merge file-k, hence when C commits they dont have to rechange or re merge file-k, hence increasing reusability and reducing work of next person.

Accidental Commit
You know how with merge conflicts you commit the resolution, but with rebase conflicts you --continue the resolution?

I cannot tell you how many times I have accidentally committed the resolution of a rebase conflict...

How to Undo the Screw-Up
If you accidentally commit the resolution of a rebase conflict, just:

git reset --soft HEAD~1

The --soft flag will keep your changes, and just undo the commit. Then you can simply --continue the rebase as normal.

# Squashing
its merging of all the commits into one before a pull request in the main branch its done via rebase.
git rebase -i HEAD~n

How to Squash
Perhaps confusingly, squashing is done with the git rebase command! Here are the steps to squash the last n commits:

Start an interactive rebase with the command git rebase -i HEAD~n, where n is the number of commits you want to squash.
Git will open your default editor with a list of commits. Change the word pick to squash for all but the first commit.
edit the commit message, however you want.
Save and close the editor.
The -i flag stands for "interactive," and it allows us to edit the commit history before Git applies the changes. HEAD~n is how we reference the last n commits. HEAD points to the current commit (as long as we're in a clean state) and ~n means "n commits before HEAD."

Why Does Rebase Squash?
Remember, rebase is all about replaying changes. When we rebase onto a specific commit (HEAD~n), we're telling Git to replay all the changes from the current branch on top of that commit. Then we use the interactive flag to "squash" those changes so that Rebase will apply them all in a single commit.

squashing erases the commits, no way to return, history deleted, same with rebase

steps:
- squash
- git push your brsnch in the main, git push --force origin branch_name
- merge using Pull Request of github
- delete you squashed branch in local, pull main branch in to local from remote

# Stash

The git stash command records the current state of your working directory and the index (staging area). It's kinda like your computer's copy/paste clipboard. It records those changes in a safe place and reverts the working directory to match the HEAD commit (the last commit on your current branch).

To stash your current changes and revert the working directory to match HEAD:
git stash

To list your stashes:
git stash list:`

few frquently used feature:
git stash -> stash the changes
git stash pop -> pop will remove from stash too, while applying the stash changes.
git stash apply -> pop while not removing stash from stash list.
git stash list -> list the stash stack
git stash drop -> remove from the stash without applying the changes 


What Is the Stash
The git stash command stores your changes in a stack (LIFO) data structure. That means that when you retrieve your changes from the stash, you'll always get the most recent changes first.

A "stash" is a collection of changes that you've not yet committed. They might just be "raw" working directory changes, or they might be staged changes. Both can be stashed. So, for example, you can:

Make some changes to your working directory
Stage those changes
Make some more changes without staging them
Stash all of that
When you do, the "stash entry" will contain both the staged and unstaged changes and both your working directory and index will be reverted to the state of the last commit. It's a very convenient way to "pause" your work and come back to it later.

can also be used when pulling remote changes to your local, to get the code or your branch upto date.

Multiple stashes:
we can also add stash messages, this might be useful for deep stash stack for better understanding of what the stash may change and affect when later poping them back.
git stash -m "the stash message."

can also use indexing for applying speciffic stashs
git stash apply stash@{2} -> apply 2nd stash starting from 0.

git stash drop stash@{2} -> drop 2nd stash starting from 0.

one more thing, your stashes do show in git log --oneline --graph --all, so dont be confused, its no new branch but a stash.

# Diff
The git diff command shows you the differences between... stuff. Differences between commits, the working tree, etc.

I frequently use it to look at the changes between the current state of my code and the last commit. For example:

show the changes between the working tree and the last commit
git diff

show the differences between the previous commit and the current state, including the last commit and uncommitted changes
git diff HEAD~1

show the change between two commits
git diff COMMIT_HASH_1 COMMIT_HASH_2

# Revert

Where git reset is a sledgehammer, git revert is a scalpel.

A revert is effectively an anti commit. It does not remove the commit (like reset), but instead creates a new commit that does the exact opposite of the commit being reverted. It undoes the change but keeps a full history of the change and its undoing.

git revert <commit_hash>
Revert vs. Reset
git reset --soft: Undo commits but keep changes staged
git reset --hard: Undo commits and discard changes
git revert: Create a new commit that undoes a previous commit

When to Reset
If you're working on your own branch, and you're just undoing something you've already committed, say you're cleaning everything up so you can open a pull request, then git reset is probably what you want.

When to Revert
However, if you want to undo a change that's already on a shared branch (especially if it's an older change), then git revert is the safer option. It won't rewrite any history, and therefore won't step on your coworkers' toes.


# Cherry Pick

There comes a time in every developer's life when you want to yoink a commit from a branch, but you don't want to merge or rebase because you don't want all the commits.

The git cherry-pick command solves this.

git cherry-pick <commit-hash>

# Git Bisect

So we know how to fix problems in our code. We can either:

- Revert the commit with the bug (this is more common on large teams)
- "Fail forward" by just writing a new commit that fixes the bug (this is more common on small teams)

But there's another question:
How do we find out when a bug was introduced?

That's where the git bisect command comes in. Instead of manually checking all the commits (O(n) for Big O nerds), git bisect allows us to do a binary search (O(log n) for Big O nerds) to find the commit that introduced the bug.

For example, if you have 100 commits that might contain the bug, with git bisect you only need to check 7 commits to find the one that introduced the bug.

commits to check	max checks to find
1	                1
2	                1
10	                4
100	                7
1000	            10
10000	            14

git bisect isn't just for bugs, it can be used to find the commit that introduced any change, but issues like bugs and performance regressions are a common use case.

How to Bisect

There are effectively 7 steps to bisecting:
- Start the bisect with git bisect start
- Select a "good" commit with git bisect good <commitish> (a commit where you're sure the bug wasn't present)
- Select a bad commit via git bisect bad <commitish> (a commit where you're sure the bug was present)
- Git will checkout a commit between the good and bad commits for you to test to see if the bug is present
- Execute git bisect good or git bisect bad to say the current commit is good or bad
- Loop back to step 4 (until git bisect completes)
- Exit the bisect mode with git bisect reset

Bisect run
       If you have a script that can tell if the current source code is good or bad, you can
       bisect by issuing the command:

           $ git bisect run my_script arguments

       Note that the script (my_script in the above example) should exit with code 0 if the
       current source code is good/old, and exit with a code between 1 and 127 (inclusive),
       except 125, if the current source code is bad/new.

this way you can automate the bisect process and leave it to compile till it finds the bad commit, but you need to make a .sh execuatble that can output the bad and gppd commit according to above comments.
one more thing, you have to sometimes make the .sh file executable yourslf, for mac i use chmod +x script.sh


# Git Blame
If you're interested, the git blame command can be used to see who made the change, not just when it was made.


# Git WorkTree

git worktree list
lists all the worktrees.

We've talked about:

Stash (temporary storage for changes)
Branches (parallel lines of development)
Clone (copying an entire repo)
Worktrees accomplish a similar goal (allow you to work on different changes without losing work), but are particularly useful when:

You want to switch back and forth between the two change sets without having to run a bunch of git commands (not branches or stash)
You want to keep a light footprint on your machine that's still connected to the main repo (not clone)
The Main Worktree
Contains the .git directory with the entire state of the repo
Heavy (lots of data in there!). To get a new main working tree requires a git clone or git init
A Linked Worktree
Contains a .git file with a path to the main working tree
Light (essentially no data in there!), about as light as a branch
Can be complicated to work with when it comes to env files and secrets

Create a Linked Worktree
To create a new worktree at a given path:

git worktree add <path> [<branch>]

No Duplicate Branches
Linked worktrees behave just like a "normal" git repo. You can create new branches, switch branches, delete branches, create tags, etc etc.

BUT there is one thing you cannot do... you cannot work on a branch that is currently checked out by any other working tree (main or linked).

Upstream
When you make a change in a linked worktree, that change is automatically reflected in the main worktree!

It makes sense: the linked worktree doesn't have a .git directory, so it's not a separate repository. It's just a different view of the same repository.

You can almost think of a linked worktree as just another branch in the same repo, but with its own space on the filesystem.
git branch -> will result in showing new branch named with this linked worktree, and its chekedout if it appears with a symbol of "+".

Delete Worktrees
You may never need to stash again! Okay, stash is still useful for tiny stuff, but worktrees are so much better for long-lived changes.

However, at some point you will need to clean up your worktrees. The simplest way is the remove subcommand:

git worktree remove WORKTREE_NAME

An alternative is to delete the directory manually, then prune all the worktrees (removing the references to deleted directories):

git worktree prune
Run git branch. Notice the ultracorp branch is not deleted, only the worktree.


# Tags
How to Tag
To list all current tags:

git tag

To create a tag on the current commit:

git tag -a "tag name" -m "tag message"

"Semver", or "Semantic Versioning", is a naming convention for versioning software.
It has two primary purposes:

To give us a standard convention for versioning software
To help us understand the impact of a version change and if it's safe (how hard it will be) to upgrade to
Each part is a number that starts at 0 and increments upward forever. The rules are simple:

MAJOR increments when we make "breaking" changes (this is typically a big release, for example, Python 2 -> Python 3)
MINOR increments when we add new features in a backward-compatible manner
PATCH increments when we make backward-compatible bug fixes
To sort them from highest to lowest, you first compare the major versions, then the minor versions, and finally the patch versions. For example, a major version of 2 is always greater than a major version of 1, regardless of the minor and patch versions.

As a special case, major version 0 is typically considered to be pre-release software and thus the rules are more relaxed.














