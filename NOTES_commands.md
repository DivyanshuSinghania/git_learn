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

