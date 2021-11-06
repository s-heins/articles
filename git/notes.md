# Notes

## When creating directories, subdirectories and files

In order to find out what git does when we change files, we're going to create this directory and file hierarchy:

```shell
.
├── top-level-dir
│   ├── second-level-file.md
│   └── sub-level-dir
│       └── third-level-file.md
└── top-level-file.md
```

To do so, we can run this script in an empty git repository:

```shell
echo "Lorem ipsum top level file" > top-level-file.md
mkdir top-level-dir
cd top-level-dir
echo "Lorem ipsum second level file" > second-level-file.md
mkdir sub-level-dir
cd sub-level-dir
echo "Lorem ipsum third level file" > third-level-file.md
```

### Result after `add`

After just running `git add .`, **not** commit, the contents of the `.git/objects` folder now look like this:

```shell
$ tree .

.
├── 1e
│   └── 86cd88d41dabd1342deb658da84a2bc9ab83cd
├── 51
│   └── 4ef3078e9106262d76be15bf23d83e8cd3bbe8
├── de
│   └── 8fcf5858a255ce7a9a60db7a004fa5b6ad80e5
├── info
└── pack
```

By examining the content with `git cat-file -p <object hash>`, we can find out that `1e86cd8` is the third-level file, `514ef30` is the top-level file, and `de8fcf5` is the second-level file.

### Insights

* git already adds objects in the objects folder even if they are not committed
* apparently, it does not add `tree` objects yet, just the blobs

Git will only add `tree` objects after we commit our changes.

### Result after `commit`

After running `git commit`, git has created a commit with the shortened hash `343d8dd`.
The git objects folder now looks like this (comments after `#` sign):

```shell
$ tree .

.
├── 1e
│   └── 86cd88d41dabd1342deb658da84a2bc9ab83cd      # third-level-file.md
├── 34
│   └── 3d8dd527a9740dc15f8be4f5a8c71308b96926      # new, commit object
├── 3a
│   └── 2e7f0d5abbe9b0d1bb26e6118f1c0070489e17      # new
├── 4d
│   └── 9f43d4557e71eb6e5f540493ae5b92dfef7a67      # new
├── 51
│   └── 4ef3078e9106262d76be15bf23d83e8cd3bbe8      # top-level-file.md
├── a6
│   └── 9f01451a19e5ba5fce7c8bd1c584b9e2e711ed      # new
├── de
│   └── 8fcf5858a255ce7a9a60db7a004fa5b6ad80e5      # second-level-file.md
├── info
└── pack
```

Looking at information on the commit object:

```shell
$ git cat-file -p 343d8dd

tree 4d9f43d4557e71eb6e5f540493ae5b92dfef7a67
author Sonja Heins <sonja.heins@example.com> 1636191282 +0100
committer Sonja Heins <sonja.heins@example.com> 1636191282 +0100

Add nested dirs and files
```

We can look at all files and directories contained in the root tree `4d9f43d` with the `git ls-tree` command with the `-r` flag for recursing into subtrees and the `-t` flag for showing trees when recursing. I used `--abbrev=7` here so git will abbreviate object hashes to seven digits.

```shell
$ git ls-tree -rt 4d9f43d --abbrev=7

040000 tree a69f014	top-level-dir
100644 blob de8fcf5	top-level-dir/second-level-file.md
040000 tree 3a2e7f0	top-level-dir/sub-level-dir
100644 blob 1e86cd8	top-level-dir/sub-level-dir/third-level-file.md
100644 blob 514ef30	top-level-file.md
```

If we only want the files listed and not the tree objects, we can use `git ls-tree -r`:

```shell
$ git ls-tree -r 4d9f43 --abbrev=7

100644 blob de8fcf5	top-level-dir/second-level-file.md
100644 blob 1e86cd8	top-level-dir/sub-level-dir/third-level-file.md
100644 blob 514ef30	top-level-file.md
```

So, after the first commit, our hierarchy is like this:

```shell
.
├── commit 343d8dd 
│   └── tree 4d9f43d
│       ├── blob 514ef30            (top-level-file.md)
│       └── tree a69f014            (top-level-dir)
│            ├── blob de8fcf5       (second-level-file.md)
│            └── tree 3a2e7f0       (sub-level-dir)
│                └── blob 1e86cd8   (third-level-file.md)
```

## Changing a file

```shell
$ echo "Adding a line to the second-level file" >> top-level-dir/second-level-file.md
```

Now we have added a line to the second-level file. Before we run `git add`, nothing changes in our object directory:

```shell
$ tree .

.
├── 1e
│   └── 86cd88d41dabd1342deb658da84a2bc9ab83cd
├── 34
│   └── 3d8dd527a9740dc15f8be4f5a8c71308b96926
├── 3a
│   └── 2e7f0d5abbe9b0d1bb26e6118f1c0070489e17
├── 4d
│   └── 9f43d4557e71eb6e5f540493ae5b92dfef7a67
├── 51
│   └── 4ef3078e9106262d76be15bf23d83e8cd3bbe8
├── a6
│   └── 9f01451a19e5ba5fce7c8bd1c584b9e2e711ed
├── de
│   └── 8fcf5858a255ce7a9a60db7a004fa5b6ad80e5
├── info
└── pack

9 directories, 7 files
```

After we run `git add .`, we have a new directory and file, `f9cc8e0`:

```shell
$ tree .

.
├── 1e
│   └── 86cd88d41dabd1342deb658da84a2bc9ab83cd
├── 34
│   └── 3d8dd527a9740dc15f8be4f5a8c71308b96926
├── 3a
│   └── 2e7f0d5abbe9b0d1bb26e6118f1c0070489e17
├── 4d
│   └── 9f43d4557e71eb6e5f540493ae5b92dfef7a67
├── 51
│   └── 4ef3078e9106262d76be15bf23d83e8cd3bbe8
├── a6
│   └── 9f01451a19e5ba5fce7c8bd1c584b9e2e711ed
├── de
│   └── 8fcf5858a255ce7a9a60db7a004fa5b6ad80e5
├── f9
│   └── cc8e0b7374e973acbbc00c8742997ccd17d473      # new
├── info
└── pack

10 directories, 8 files
```

It contains the new file changes:

```shell
$ git cat-file -p f9cc8e0

Lorem ipsum second level file
Adding a line to the second-level file
```

Now we commit and produce a new commit, `9d2aeae`:

```shell
$ git commit -m "Add line to second-level-file.md"

[main 9d2aeae] Add line to second-level-file.md
 1 file changed, 1 insertion(+)
```

Afterwards, our git object directory looks like this (comments after `#` sign):

```shell
.
├── 1e
│   └── 86cd88d41dabd1342deb658da84a2bc9ab83cd
├── 34
│   └── 3d8dd527a9740dc15f8be4f5a8c71308b96926
├── 3a
│   └── 2e7f0d5abbe9b0d1bb26e6118f1c0070489e17
├── 4d
│   └── 9f43d4557e71eb6e5f540493ae5b92dfef7a67
├── 51
│   └── 4ef3078e9106262d76be15bf23d83e8cd3bbe8
├── 9b
│   └── 676fbb2c624d1541263163ff84447242b5997b        # new
├── 9d
│   └── 2aeae6478aa11d828bc4d969dbfc55186593bf        # new (commit)
├── a0
│   └── 4ef2163c702bea83aa59d0fc5cc1547006b22f        # new
├── a6
│   └── 9f01451a19e5ba5fce7c8bd1c584b9e2e711ed
├── de
│   └── 8fcf5858a255ce7a9a60db7a004fa5b6ad80e5
├── f9
│   └── cc8e0b7374e973acbbc00c8742997ccd17d473        # changed second-level-file.md
├── info
└── pack

13 directories, 11 files
```

In order to change our second-level-file, git needed to change the tree object for the top-level-dir folder as well as the root tree.

We can see the root tree here, in the object with the hash `9b676fb`:

```shell
$ git ls-tree -rt 9b676fb --abbrev=7

040000 tree a04ef21	top-level-dir
100644 blob f9cc8e0	top-level-dir/second-level-file.md
040000 tree 3a2e7f0	top-level-dir/sub-level-dir
100644 blob 1e86cd8	top-level-dir/sub-level-dir/third-level-file.md
100644 blob 514ef30	top-level-file.md
```

As we can see, only the hash for `top-level-dir` has changed, the one for `top-level-file.md` is the same.

Now, our hierarchy is like this:

```shell
.
├── commit 343d8dd 
│   └── tree 4d9f43d
│       ├── blob 514ef30            (top-level-file.md)
│       └── tree a69f014            (top-level-dir)
│            ├── blob de8fcf5       (second-level-file.md)
│            └── tree 3a2e7f0       (sub-level-dir)
│                └── blob 1e86cd8   (third-level-file.md)
├── commit 9d2aeae 
│   └── tree 9b676fb
│       ├── blob 514ef30            (top-level-file.md)
│       └── tree a04ef21            (top-level-dir)
│            ├── blob f9cc8e0       (second-level-file.md)
│            └── tree 3a2e7f0       (sub-level-dir)
│                └── blob 1e86cd8   (third-level-file.md)
```

The object hashes for top-level-file (`514ef30`), sub-level-dir (`3a2e7f0`), and third-level-file (`1e86cd8`) have not changed, only the ones that lead us to second-level-file.md have changed, so the hash for the file itself and the ones for the trees that contain it.

## To do

Does git delete the old objects?

* what are Merkel trees?
* what do I want to just mention, what do I still want to include?