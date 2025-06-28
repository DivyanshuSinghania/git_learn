# Git Basics Notes

## 1. What is Git?

Git is a distributed version control system that helps track changes in source code during software development.

---

## 2. How Does Git Store Data?

Git stores data as snapshots (not diffs). Every version of a file is saved as a **blob**, grouped into **trees**, and tracked through **commits**.

* **Blob**: Stores file contents.
* **Tree**: Directory object that stores blobs or other trees.
* **Commit**: A snapshot pointing to a tree and referencing parent commits.

---

## 3. `.git` Directory (Minimal Overview)

This hidden folder is where Git keeps all the metadata and objects for the repo.

* **refs/**: Pointers to branches and tags.
* **objects/**: Stores all data (blobs, trees, commits) in hashed format.
* **index**: Staging area storing changes before commit.
* **hooks/**: Custom scripts triggered by git events.
* **config**: Repo-specific configurations.
* **HEAD**: Points to the current branch/ref.

---

## 4. Git Config

Git can have configurations at three levels: system, global, and local.

* **Global** config: Affects all repos of the current user (`~/.gitconfig`).
* **Local** config: Affects the current repo (`.git/config`).

```bash
git config --global user.name "Your Name"
git config --global user.email "your@example.com"
```
we can also inspect the values with get

```bash
git config get --global user.name
git config get --global user.email
```
or by simply cheking cofig file in .git folder in our system files / local repos.

---

## 5. Staging Changes

* `git add .` – Adds all modified/new files to staging.
* `git add <file_or_directory>` – Adds specific files/directories to staging.

---

## 6. git status – Understanding File States

* **Tracked**: Files Git is already monitoring.
* **Untracked**: New files not yet added to Git.
* **Modified**: Tracked files with changes not staged.
* **Deleted**: Files removed from working directory.

---

## 7. `git status` – What It Tells

Shows the current state of the working directory and staging area:

* Modified, untracked, staged files
* Whether a merge conflict is ongoing
* If you're in the middle of a **rebase**, **bisect**, or **cherry-pick**
* Any hints or next commands Git expects from the user

(These deeper topics are introduced here but explained later.)

---

## 8. `git commit -m` and `--amend`

* `git commit -m "message"` – Commits staged changes with a message
* `git commit --amend -m "new message"` – Modifies the last commit (message or content)

---

## 9. `git log` and Related Flags

* `git --no-pager log -n <number>` – Lists latest commits with:

  * Full commit hash
  * Author and date
  * Commit message
  * Points to the tree structure (via hash)

* `git log --oneline` – Shortens each commit to a single line with abbreviated hash

* `git log --graph` – Shows a visual commit tree

* `git log --all` – Includes all refs (branches/tags)

---

## 10. Plumbing – Exploring Git Internals

* **Commit metadata**: `git cat-file -p <commit_hash>` → Shows tree hash, parent, author, message
* **Tree contents**: `git cat-file -p <tree_hash>` → Lists contents (files/blobs and subtrees)
* **Raw file (blob)**: `git cat-file -p <blob_hash>` → Shows the file content itself

These commands allow exploring low-level Git structure.

---

## 11. Plumbing vs Porcelain (Short Intro)

* **Plumbing**: Low-level commands meant for scripts and internal inspection (`cat-file`, `hash-object`)
* **Porcelain**: User-friendly high-level commands (`git add`, `git status`, `git commit`)

(Deeper separation will be covered in detail later.)

---

## 12. Branching

* `git branch <name>` – Creates a new branch.
* `git switch -c <name>` – Creates and switches to a new branch.
* `git branch` – Lists all branches; current branch is shown with `*` (HEAD points to this branch).
* `git log --graph` – Visualizes the commit history with branching.
* `git branch -d <name>` – Deletes the specified branch (if already merged).

The **HEAD** pointer tells Git which branch or commit your working directory reflects. Changing branches moves HEAD to point to a different branch.

---

## 13. Merging

* `git merge <branch>` – Merges `<branch>` into the current branch.
* If changes conflict, Git marks the file and stops merge with a conflict.
* To resolve, open the file and manually choose between:

  ```
  <<<<<<< HEAD
  your current branch code
  =======
  code from <branch>
  >>>>>>> <branch>
  ```
* Edit the file to keep desired changes and remove conflict markers.
* Then run:

  ```bash
  git add .
  git commit
  ```

  to complete and secure the merge.

---

## 14. Rebase

* **What is Rebase?**

  * Breaks your branch’s history from its original base and reattaches it onto a new base commit (e.g., tip of `main`).
  * Results in replaying your branch’s commits onto the latest `main`, generating new commit hashes and a linear history.

* `git switch -c <name> <commit_hash>` – Branch off from a specific commit.

* `git rebase main` – Reapply current branch commits onto the tip of `main`.

* **Caution:** Rebase rewrites commit hashes and alters history. Avoid on shared branches—anyone branching off will face broken histories and extra work. If you’re the sole user of a branch, rebasing is safe for a clean, linear log.

---

## 15. Reset

* `git reset --soft <commit_hash>` – Move HEAD to `<commit_hash>`, keep changes staged.
* `git reset --hard <commit_hash>` – Move HEAD to `<commit_hash>`, discard all staged and working changes.

---

## 16. Git Remote

* **What is a Git remote?**

  * A named connection to another Git repository (often on a server), not a separate repo.

* `git remote add <name> <url>` – Add a new remote connection.

* `git remote remove <name>` – Remove an existing remote.

* `git remote rename <old> <new>` – Rename a remote.

* `git remote -v` – Show URLs for each remote and its fetch/push status.

---

## 17. GitHub CLI & Setup

* Install: `curl -sS https://webi.sh/gh | sh`
* Authenticate: `gh auth login`
* Add remote: `git remote add origin <repo_url>`
* Verify connection: `git ls-remote`

### Push

* `git push origin main` – Push `main` to remote.
* `git push origin <local>:<remote>` – Push a local branch to a specific remote branch.
* `git push origin :<remote>` – Delete a remote branch.

### Pull

* Configure default pull strategy: `git config pull.rebase false` (merge on pull).

### Merge Pull (Pull Requests)

* Open a pull request on GitHub to merge a branch.
* Peer review and discussion happen in the PR.
* Merge via GitHub UI and optionally delete the source branch.

---

## 18. Workflow

### Solo Workflow

* Work on `main` branch only.
* Typical cycle: `git add` → `git commit` → `git push origin main`.
* Ensures remote backup and history tracking.

### Team Workflow

1. Update local main: `git pull origin main`
2. Create feature branch: `git switch -c <branch>`
3. Develop: `git add` → `git commit`
4. Push branch: `git push origin <branch>`
5. Open PR on GitHub for code review.
6. Merge via GitHub once approved, then delete branch.

---

## 19. .gitignore

* Specifies untracked files for Git to ignore.
* Pattern rules:

  * `*` matches any string.
  * Leading `/` anchors to repository root.
  * Leading `!` negates a match.
* Order matters: later rules override earlier ones.
* Lines starting with `#` are comments.
