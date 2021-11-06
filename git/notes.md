# Notes

## When creating directories, subdirectories and files

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

```shell
$ tree .

.
├── top-level-dir
│   ├── second-level-file.md
│   └── sub-level-dir
│       └── third-level-file.md
└── top-level-file.md

2 directories, 3 files
```

After just running `git add .`, **not** commit, this got created in the `.git/objects` folder:

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

Where `1e86cd` is the third-level file, `514ef3` is the top-level file, and `de8fcf` is the second-level file.

### Insights

* git already adds objects in the objects folder even if they are not committed
* it will just not add a commit object
* apparently, it does not add `tree` objects either, just the blobs

### Questions

* Why is there no tree object?

### Result after `commit`

After running `git commit`, git has created a commit with the shorted hash `343d8dd`.
The git objects folder now looks like this:

```shell
$ tree .

.
├── 1e
│   └── 86cd88d41dabd1342deb658da84a2bc9ab83cd      // third-level file
├── 34
│   └── 3d8dd527a9740dc15f8be4f5a8c71308b96926      // new, commit object
├── 3a
│   └── 2e7f0d5abbe9b0d1bb26e6118f1c0070489e17      // new
├── 4d
│   └── 9f43d4557e71eb6e5f540493ae5b92dfef7a67      // new
├── 51
│   └── 4ef3078e9106262d76be15bf23d83e8cd3bbe8      // top-level file
├── a6
│   └── 9f01451a19e5ba5fce7c8bd1c584b9e2e711ed      // new
├── de
│   └── 8fcf5858a255ce7a9a60db7a004fa5b6ad80e5      // second-level file
├── info
└── pack
```

Looking at information on the commit object:

```shell
$ git cat-file -p 343d8dd52

tree 4d9f43d4557e71eb6e5f540493ae5b92dfef7a67
author Sonja Heins <sonja.heins@example.com> 1636191282 +0100
committer Sonja Heins <sonja.heins@example.com> 1636191282 +0100

Add nested dirs and files
```

On `3a2e7f`:

```shell
$ git cat-file -p 3a2e7f

100644 blob 1e86cd88d41dabd1342deb658da84a2bc9ab83cd	third-level-file.md
```

On `4d9f43`:

```shell
$ git cat-file -p 4d9f43

040000 tree a69f01451a19e5ba5fce7c8bd1c584b9e2e711ed	top-level-dir
100644 blob 514ef3078e9106262d76be15bf23d83e8cd3bbe8	top-level-file.md
```

On `a69f01`:

```shell
$ git cat-file -p a69f01

100644 blob de8fcf5858a255ce7a9a60db7a004fa5b6ad80e5	second-level-file.md
040000 tree 3a2e7f0d5abbe9b0d1bb26e6118f1c0070489e17	sub-level-dir
```

If we only want the files listed and not the tree objects, we can use `git ls-tree -r`:

```shell
$ git ls-tree -r 4d9f43

100644 blob de8fcf5858a255ce7a9a60db7a004fa5b6ad80e5	top-level-dir/second-level-file.md
100644 blob 1e86cd88d41dabd1342deb658da84a2bc9ab83cd	top-level-dir/sub-level-dir/third-level-file.md
100644 blob 514ef3078e9106262d76be15bf23d83e8cd3bbe8	top-level-file.md
```

## To do

* find out what happens if we change a file (in the top dir)
* find out what happens if we change a file (in a sub-level dir)

Does git delete the old objects?

* what are Merkel trees?
* what do I want to just mention, what do I still want to include?