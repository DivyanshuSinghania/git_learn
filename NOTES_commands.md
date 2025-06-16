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
