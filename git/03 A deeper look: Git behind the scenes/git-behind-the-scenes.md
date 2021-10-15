# A deeper look: Git behind the scenes

In the [first article](https://dev.to/sheins/-a-practical-introduction-to-git-jumping-in-with-both-feet-2o56), we have looked at how to initialize a git repo locally, add and commit, and set some configuration options.
Next, the second article covered how to work with remotes and branches, how to resolve conflicts, and how to merge and delete branches both from the CLI or from the GitHub UI.

Now that we have gained some overview over the basic functions of git, it is time for an excursion into how git works behind the scenes. How does git notice that a file has changed so that it can show us this in our status? How does git realize branches? How does git know the order if the commits in each branch?

As a resource for this article, I have used the [Pro Git book, written by Scott Chacon and Ben Straub](https://git-scm.com/book/en/v2) which is available for free and may be shared non-commercially.

## How does git know a file has changed?

Git does not store the exact differences between files (for example, add the line "house cat" to your file list-of-animals-to-write-about), but rather, it stores **snapshots**.

It has a snapshot for each file, and each file also has a SHA-1 hash value that is used for checksumming. If the checksum of a file has changed, the file itself must have changed. If a file was deleted and another was added but they share the same checksum, they must have the same contents – this is how git knows that a file was renamed.

## Back to the start – creating a repository

As mentioned in the [first article](https://dev.to/sheins/-a-practical-introduction-to-git-jumping-in-with-both-feet-2o56), git adds a `.git` folder in your working directory when you run the `git init` command.

![Initializing the repository creates a hidden `.git` folder](initialize-git.png)

## Exploring the .git folder

This is what the content of the hidden git folder looks like on the top level:

![Contents of the hidden git folder at the top level](toplevel-hidden-git-folder.png)



Let's say our collaborators Anna and Wolfgang have been busy and our git tree currently looks like this:

![The current git tree](current-git-tree.png)

To be able to use the `lg` command alias, add this line to your `~/.gitconfig` file under the [alias] section:

```shell
[alias]
lg = !clear && git log --all --graph --pretty='format:%C(auto)%h%d %s  %C(magenta)[%an] (%ad)%C(reset)' --date=format:'%d.%m.%y %H:%M'
```
