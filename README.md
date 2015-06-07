# Git Exploration

## Why?
A lot of the time, git seems like a magic mystery to me. I decided that I would take a dive in and
actually explore the repository structure in the mythical `.git` directory, and log my findings as
I do this.

## Beginning

```bash
mkdir git_exploration
cd git_exploration
git init
```

This gets us a git repo created. Let's check out what we have:

```
.git/
  branches/
  hooks/
  info/
    exclude
  objects
    info/
    pack/
  refs/
    heads/
    tags/
  config
  description
  HEAD
```
Okay, so this doesn't look too crazy. Let's open up some of the stuff we have on initialization in
here.

`.git/info/exclude`
```
# git ls-files --others --exclude-from=.git/info/exclude
# Lines that start with '#' are comments.
# For a project mostly in C, the following would be a good set of
# exclude patterns (uncomment them if you want to use them):
# *.[oa]
# *~
```
Okay, so it appears that this opens up with the command that the system would use to govern this
behaviour. Knowing a bit about git, one can reasonably infer that this is going to work in hijinks
with the `.gitignore` file that one can use to ignore certain files.

#### [TODO] Research exclude 

`.git/refs/config
```
[core]
	repositoryformatversion = 0
	filemode = true
	bare = false
	logallrefupdates = true
```
It would appear this is just some general configuration for a boilerplate initialized repo.

`.git/refs/description`
```
Unnamed repository; edit this file 'description' to name the repository.
```
Here it seems we can name our little project

`.git/refs/HEAD`
```
ref: refs/heads/master
```
This seems to be referencing the current `HEAD`. 

#### [TODO] Dive into HEAD

## Adding A File

```bash
echo "# Git Exploration" > README.md
git add README.md
git commit -m 'initial commit'
```

Once we do this, we can check out a new directory structure:

```
.git/
  branches/
  hooks/
  info/
    exclude
  logs/
    refs/
      heads/
        master
    HEAD
  objects/
    08/
      12517d73636573118a6ee
    29/
      b478dd6740a8628126c9f
    7a/
      07855d9fff722f83c3d60
    info/
    pack/
  refs/
    heads/
      master
    tags/
    COMMIT_EDITMSG
    config
    description
    HEAD
    index
README.md
```

