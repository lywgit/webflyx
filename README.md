# Learn Git (Webflyx) 

This is a toy repo from the lesson [Learn Git](https://www.boot.dev/courses/learn-git) lesson of [boot.dev](https://www.boot.dev/). It cointains no real program or code.

I am not a beginner of using git and I quickly finished this course for purpose of a refresh. 
It was a nice quick exercise on git, and there were plenty of stuffs I didn't know.

## My Takeaways

### General 

* porcelain vs plumbing commands
* use git swith instead of git checkout
* rebase vs merge
    * A merge B: 
        * Commits from B are merged into A. If histories diverged, Git creates a merge commit.
        * Typical scenario: A=main (or develop) B=feature  
        * Think: I will update the main branch by merging in a finished feature.
    * A rebase B: 
        * Commits on A are re-applied on top of B. History becomes linear, no merge commit.
        * Typical scenario: A=feature B=main
            * Make the feature branch (A) up to date with main (B), before merging it back.
        * Think: I will rebase my finished feature on the main branch.
        * Careful: Could mess up actual history to get a simpler, readable linear history
* git remote add <remote_path>  can accept another local dir as remote


### Basics

```
git switch [-c] <branch>
git status
git add
git commit [-m <message>]
git pull
git push
```

### Inspect commit history

```
git [--no-pager] log 
  --oneline 
  -n <n> 
  --graph
  --all
  --decorate=full/no 
  --parents 
```

#### Examples
```
% git log --oneline

88199be (HEAD -> main, origin/main) N: add advert.md
420bad4  M: add gitignore
9c48e68 Merge pull request #1 from lywgit/add_classics
4881887 (origin/add_classics) L: update classics.csv
7839990 K:  Merge branch 'main' of https://github.com/lywgit/webflyx
c89b0b7 J: update classics.csv
411769e I: update dune.md
2a670b2 H: update dune.md
6e42709 G: update titles
f65e216 F: Merge branch 'add_classics'
80c50f3 E: update contents.md
24dd748 D: add classics
52d7996 C: add quotes
cc9111f B: add titles
cec798a A: add contents.md
```

```
% git log --oneline --graph

* 88199be (HEAD -> main, origin/main) N: add advert.md
* 420bad4  M: add gitignore
*   9c48e68 Merge pull request #1 from lywgit/add_classics
|\  
| * 4881887 (origin/add_classics) L: update classics.csv
| *   7839990 K:  Merge branch 'main' of https://github.com/lywgit/webflyx
| |\  
| |/  
|/|   
* | c89b0b7 J: update classics.csv
| * 411769e I: update dune.md
| * 2a670b2 H: update dune.md
|/  
* 6e42709 G: update titles
*   f65e216 F: Merge branch 'add_classics'
|\  
| * 24dd748 D: add classics
* | 80c50f3 E: update contents.md
|/  
* 52d7996 C: add quotes
* cc9111f B: add titles
* cec798a A: add contents.md
```

```
% git log -n 1

commit 88199bea8726c746e7097a4d8d7bc4824586f975 (HEAD -> main, origin/main)
Author: lywang <wellmax111@gmail.com>
Date:   Tue Jul 22 10:19:55 2025 +0800

    N: add advert.md
```

The commit withg hash: 88199bea8726c746e7097a4d8d7bc4824586f975 has a commit message of "N: add advert.md"

### Inspecting .git/

The hidden directory .git stores all the data. List a few below  

```
.git/
  - config
  - HEAD
  - objects
    - tree: git's way of storing a directory
    - blob: git's way of storing a file 
  - refs
  - ...
```

`.git/config` is just a human readable text file

```
% cat .git/config 

[core]
        repositoryformatversion = 0
        filemode = true
        bare = false
        logallrefupdates = true
        ignorecase = true
        precomposeunicode = true
[remote "origin"]
        url = https://github.com/lywgit/webflyx.git
        fetch = +refs/heads/*:refs/remotes/origin/*
[pull]
        rebase = false
```

`.git/objects` stores objects organized by hash in a two layer structure
```
% ls .git/objects 

04      18      25      40      4a      5b      72      80      98      b2      cc      e2      f6
08      1c      2a      41      4d      61      76      81      9c      c2      ce      e3      fe
0b      23      2c      42      50      6e      77      85      ab      c8      dc      f0      info
0d      24      3d      48      52      71      78      88      b0      ca      e0      f3      pack

% ls .git/objects/88 

199bea8726c746e7097a4d8d7bc4824586f975

```
The file: `.git/objects/88/199bea8726c746e7097a4d8d7bc4824586f975` is not human readable.
The Concated path is the hash: 88199bea8726c746e7097a4d8d7bc4824586f975 (<- this is a commit)


Tho inspect an object (trees and blobs), use `git cat-file`
``` 
git cat-file -p <hash>
```

#### Example

Inspect the commit 88199be (N: add advert.md)

```
% git cat-file -p 88199be                             # a commit has a tree and other info such as its parent, author, committer

tree 4dabae4426a5e83422ea5028ccea14d10233c5a7               # further cat-file this tree to see files and subdirs
parent 420bad41a0a556ea4692777bd567a8bedc210b84             # parent commit = 420bad4 (M: add gitignore)
author lywang <wellmax111@gmail.com> 1753150795 +0800      
committer lywang <wellmax111@gmail.com> 1753150795 +0800


% git cat-file -p 4dabae4426a5e83422ea5028ccea14d10233c5a7   # inspect the tree to see files and sub directories

100644 blob 71cd44506934a2f30b3dca4e65669e8301144ed9    .gitignore
100644 blob b2490156fcf1697d1c2f85dd0d371ebed5e6b72b    advert.md
100644 blob e2e92920471ce9f811eaf64b596a7fd40cfcba99    classics.csv
100644 blob 2364d34556096bdfd293e710a1327fa8e61a8c6b    contents.md
040000 tree 2ab70c0d45a01dd115f25e49a3a73c0de4a2f829    quotes
100644 blob 4af38198e0fc38ae7d81ef3b6269f275d1ae6535    titles.md

```

### Configuration


```
git config
  --list 
  --get <key>
  --add <key> <value>
  --unset <key>   # remove a value
  --remove-section <section>   # remove entire section
  
```

Most common usage
```
git config --add --global user.name "Your user name"
git config --add --global user.email "Your email"
```


#### Configuration file locations

* system: `/etc/gitconfig`, a file that configures Git for all users on the system
* global: `~/.gitconfig`, a file that configures Git for all projects of a user
* local: `.git/config`, a file that configures Git for a specific project
* worktree: `.git/config.worktree`, a file that configures Git for part of a project

(system and worktree are rarely needed)

