# A deeper look: Git behind the scenes

In the [first article](https://dev.to/sheins/-a-practical-introduction-to-git-jumping-in-with-both-feet-2o56), we have looked at how to initialize a git repo locally, add and commit, and set some configuration options.
Next, the second article covered how to work with remotes and branches, how to resolve conflicts, and how to merge and delete branches both from the CLI or from the GitHub UI.

Now that we have gained some overview over the basic functions of git, it is time for an excursion into how git works behind the scenes. How does git notice that a file has changed so that it can show us this in our status? How does git realize branches? How does git know the order of the commits in each branch?

As a resource for this article, I have used the [Pro Git book, written by Scott Chacon and Ben Straub](https://git-scm.com/book/en/v2) which is available for free and may be shared non-commercially.

## How does git know a file has changed?

Git does not store the exact differences between files (for example, add the line "house cat" to your file "list-of-animals-to-write-about"), but rather, it stores **snapshots**. So it would have a snapshot of the file before adding that line and then compare that to the current version of the file to figure out if something has changed.

To do this, git computes a SHA-1 hash value based on the contents for each file and uses it for checksumming. SHA-1 is a cryptographic hash function that makes it highly unlikely to create the same hash for two different files, so if the hash is the same, we can assume that it was created based on the same input. If the checksum of a file has changed, the file itself must have changed. If a file was deleted and another was added but they share the same checksum, they must have the same contents – this is how git knows that a file was renamed.

If the contents of a file have changed, the name will be the same but its hash will be different.

In case a file has not changed at all in a commit, git will not store the file itself again, just a link to the one it has already saved.

## The three states of git

In the first article, we have already added and committed files and file changes. Git knows three states: **modified**, **staged**, and **committed**.

![Different git states when creating, adding, and committing a file](git-states.png)

* **Working directory**: After adding `new-file`, the file is present in our directory and **untracked**, that means that we created a new file git doesn't know about yet. Any existing files we change will be in the **modified** state.
* **Staging area**: After `git add .`, the changes are now marked as **staged**
* **Repository**: Now we can `git commit` the changes and they will be added to our `.git` repository as **committed**

## File status lifecycle

* **Untracked** (Working Directory)
  Files that are not in the staging area and not in your last snapshot (= commit), i.e. files that git doesn't yet know about
* **Unmodified** (Working Directory)
  Files that have not changed since the last snapshot
* **Modified** (Working Directory)
  Files that have been modified since the last snapshot
* **Staged** (Staging Area)
  Files that were added to the Staging area by `git add`ing them.

File status can be checked by running `git status`.

If you want to skip the staging area, you can use `git commit -a` so that git will commit all tracked files without you having to `add` them first. This does not work for previously untracked files, however.

## Anatomy of a commit

As discussed before, git stores **snapshots**. Specifically, git stores files as `blobs` (binary large objects) within git, and their checksums. Then, it builds a **tree object** (the root project tree) that models the directory structure.

A **commit** then stores some meta information and a **pointer** to that root tree.

Commit metadata includes:

* author of the commit
* the committer
* the parent of the commit (if applicable) – this is a pointer to a commit hash

Since every commit contains a reference to its parent (if it has one), we can think of commits as a linked list where each commit has between zero and two parents.
If it has zero, it is the very first commit. If it has two, it is a merge commit, and if it has one, it is a "normal" commit.

Let's say our collaborators Anna and Wolfgang have been busy and our git tree currently looks like this:

![The current git tree](current-git-tree.png)

> To be able to use the `lg` command alias, add this line to your `~/.gitconfig` file under the [alias] section:
>
>```shell
>[alias]
>lg = !clear && git log --all --graph --pretty='format:%C(auto)%h%d %s  %C(magenta)[%an] (%ad)%C(reset)' --date=format:'%d.%m.%y %H:%M'
>```

In this example, we have merged the branch "articles-on-animals-from-list" into "main" and the commit `2cca46a` is a merge commit. `98369a7` ("Add a house cat") is the first commit and has no parent, and all other commits in between have only one parent. The commits `0a6ebca` and `b6eb6d0` have the same parent, but they only have one parent.

To look at `commit` metadata, we can use `git cat-file -p`, where `-p` lets us pretty-print the object's content.
I'm saying object because git stores multiple pieces of information as objects – such as `blob` objects for files, `commit` objects, and `tree` objects which allow us to reference other `tree` objects and `blob` objects to model a file tree. (Any children `tree` objects would then represent folders and `blob` objects would be files). To read more about this, see [this article in the git pro book](https://git-scm.com/book/en/v2/Git-Internals-Git-Objects).

We can use `git cat-file -p` to look at our merge commit, `2cca46a` for example:

![Using git cat-file -p to look at commit metadata](git-cat-file-on-commit.png)

## Back to the start – creating a repository

As mentioned in the [first article](https://dev.to/sheins/-a-practical-introduction-to-git-jumping-in-with-both-feet-2o56), git adds a `.git` folder in your working directory when you run the `git init` command.

![Initializing the repository creates a hidden `.git` folder](initialize-git.png)

We can now find all our commits in the `objects` folder within the `.git` folder. Git saves these objects in folders that carry the first two characters of the object hash. The rest of the characters are used as the file name within that folder. For example, our commit `2cca46a` is really called `2cca46ab8626e867e2994cac12eb97887b0a82a2` in full. It will be contained in a file within the `2c` folder inside the `objects` folder, and this file will be called `ca46ab8626e867e2994cac12eb97887b0a82a2`.

![Looking at a git commit file inside the objects folder](git-commit-object.png)

## Exploring the .git folder

This is what the content of the hidden git folder looks like on the top level:

![Contents of the hidden git folder at the top level](toplevel-hidden-git-folder.png)