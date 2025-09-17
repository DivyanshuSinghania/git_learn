# Git Notes

## Fundamental Git Operations

```bash
git status
git add <file_name>
git commit -m "message"
```

## Modifying the Most Recent Commit

```bash
git commit --amend -m "corrected message"
```

## Commit Log Retrieval

```bash
git log
git log -n 10
git --no-pager log -n 10
```

## Analysis of Git Objects

### Interrogating Commit Metadata

```bash
git cat-file -p <commit>
```

This reveals the structured internal representation of a commit object, including:

* `tree <tree_hash>` — references the root of the directory structure at that point in the repository.
* `author <User_name> <User_id> <Timestamp> <Time_Zone>` — denotes the originator of the commit.
* `committer <User_name> <User_id> <Timestamp> <Time_Zone>` — specifies the individual who finalized the commit into the repository history.
* The commit message, which contextualizes the changes.

> **Flag Specification: `-p` (pretty-print)**
>
> The `-p` flag directs Git to render object contents in a human-readable format:
>
> * For a **commit**, it outputs metadata (author, committer, tree) and the log message.
> * For a **tree**, it enumerates file entries, associated blobs, permissions, and subordinate trees.
> * For a **blob**, it renders the raw file content.

### Examining a Tree Object

```bash
git cat-file -p <tree_hash>
```

This reveals the internal directory snapshot structure, listing entries in the form:

* `<permission_mode> blob <blob_hash> <file_name>`

### Inspecting Raw File Data

```bash
git cat-file -p <blob_hash>
```

Displays the literal content stored in a blob, corresponding to a file at the time of its commit.


## Git Plumbing vs Porcelain Commands

In Git, commands are categorized into two conceptual layers:

* **Porcelain commands** are the high-level, user-friendly commands intended for daily use. Examples include `git status`, `git add`, `git commit`, and `git log`. These abstract away much of the underlying complexity and provide a clean interface.

* **Plumbing commands** are the lower-level commands that interact directly with Git’s object database. They are more granular and intended for scripting or advanced manipulation. Examples include `git cat-file`, `git hash-object`, and `git update-index`.

Understanding this distinction enables deeper insight into Git's architecture and facilitates advanced diagnostics and customization.

I am gonna write stuf dow and ask gpt in end to create  perfect notes in these 

## Merging and branching
-> explain merge process with a graph example

git branch -> gives name of available branches and current branch
git switch -c <branch_name> -> this switches branches and "-c" creates branch if not existing

git log --oneline --graph --all
explain this command in short but dont give examples, just flags and other most used flags

git merge new-branch-adding-a-random-file --no-edit
comment on this command and give what no editor do, dont give examples

-> when you are on a branch then the editor (vscode, vim, etc), also just shows the file intended in that branch.

git branch -d Branch_name
delets a branch, after merge, delete  the brach, its not deleted automatically if in future the branch is needed

## Rebase
"Rebase vs Merge" is one of the most hotly debated topics in the Git world. A lot of the discussions you'll see online come down to the fact that many developers (yes, even professionals) don't understand the purpose of rebase and use it incorrectly, causing a bunch of Git havoc, and then blame the rebase command.
It's not Git's fault, it's a skill issue.
state to not use rebase on a shared branch without causion, give reason too in short without any exapmles

git switch -c update_dune COMMITHASH
this branches off from a specific commit from its hash, see logs and tree for the exact commit you would branch off

git rebase main
get your "current" branch to upto date with "main"

## Reset
git reset --soft <commit_hash> -> saves the changes in staged area while rolling back the commit
git reset --hard <commit_hash> -> it rolls back the branch and remove every thing, no staged files left

## Git Remote
just create notes which are simple to understand and dont include any examples

## GitHub
curl -sS https://webi.sh/gh | sh
gh auth login
git remote add origin https://github.com/your-username/git_learn.git
git ls-remote
make sense of these and add on the notes, for how to download github clint, gh auth login then setup remote

### push
git push origin main
git push origin <localbranch>:<remotebranch>
git push origin :<remotebranch>
-> Used to push the local code from system to remote on github (this is neccary to remember and grasp)

### pull
-> Run this to make sure git will merge on a pull
git config pull.rebase false

## Merge Pull
-> give notes on pull request on github

# my workflow
My Workflow
While on the topic of pull requests, I want to share a note on my simple workflow. 90% of the time, you're only using a handful of git commands to get your coding work done.

Keep Stuff on GitHub
I keep all my serious projects on GitHub. That way if my computer explodes, I have a backup, and if I'm ever on another computer, I can just clone the repo and get back to work.

Rebase vs. Merge
I've configured Git to rebase by default on pull, rather than merge so I keep a linear history. If you want to do the same, you can add this to your global Git config:

git config --global pull.rebase true

My Solo Workflow
When I'm working by myself, I usually stick to a single branch, main. I mostly use Git on solo projects to keep a backup remotely and to keep a history of my changes. I only rarely use separate branches.

Make changes to files
git add . (or git add <files> if I only want to add specific files)
git commit -m "a message describing the changes"
git push origin main
It really is that simple for most solo work. git log, git reset, and some others are, of course, useful from time to time, but the above is the core of what I do day-to-day.

My Team Workflow
When you're working with a team, Git gets a bit more involved (and we'll cover more of this in part 2 of this course). Here's what I do:

Update my local main branch with git pull origin main
Checkout a new branch for the changes I want to make with git switch -c <branchname>
Make changes to files
git add .
git commit -m "a message describing the changes"
git push origin <branchname> (I push to the new branch name, not main)
Open a pull request on GitHub to merge my changes into main
Ask a team member to review my pull request
Once approved, click the "Merge" button on GitHub to merge my changes into main
Delete my feature branch, and repeat with a new branch for the next set of changes

This is a text snippet for what a github flow should be, so make points and be sure to write it in short and consice manner

## Gitignore

tell about scope of .gitignore
and make notes of patterns like use of "*", "/", "!", order of operations, comments, etc make notes short and no example needed.

## Restore
git restore --staged <file> -> this unstages the changes ("add")
git restore --staged . -> this unstages all the additions.
