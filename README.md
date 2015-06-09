# Git Exploration

## Why?
A lot of the time, git seems like a magic mystery to me. I decided that I would take a dive in and
actually explore the repository structure in the mythical `.git` directory, and log my findings as
I do this.
 
## How To Read This
If you clone this repository, inside of the `test` directory there is a separate repository. Inside of it, each commit that is
referenced in this document has it's own hash that can be checked out to see the project at that exact state. So, everything
down to the commit hashes will be exactly as referenced.
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

`.git/refs/config`
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

## Hooks
Inside here, we see a `hooks/` directory. This is one of the more unique pieces compared to the rest
of our offerings. If we open up at random:

```
#!/bin/sh
#
# An example hook script to verify what is about to be committed
# by applypatch from an e-mail message.
#
# The hook should exit with non-zero status after issuing an
# appropriate message if it wants to stop the commit.
#
# To enable this hook, rename this file to "pre-applypatch".

. git-sh-setup
test -x "$GIT_DIR/hooks/pre-commit" &&
	exec "$GIT_DIR/hooks/pre-commit" ${1+"$@"}
:
```

we can see that it comes with an explanation of what it is doing. We will dive further into these 
later, but it is just important for now to know they exist.

#### [TODO] Dive into HEAD

## Adding A File

```bash
echo "# Git Exploration" > README.md
git add README.md
git commit -m 'initial commit'
```
`commit 0812517d73636573118a6eef9151688be148885b`

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

It appears we have some simple additions with adding one file. To start, we have expanded our
info directory to now include a `logs` directory. We also have several subdirectories inside of our
`objects` directory now, each containing a hash. refs subdirectory `heads` now includes a
`master` file, and we also have added `COMMIT_EDITMSG`, and index at the root level of `.git`.

If we examine `COMMIT_EDITMSG` we see:

```
initial commit

```

Logging out commit message.

## Making A Branch
Let's create a new branch to further expand this interesting `.git` directory. 

```bash
git checkout -b my_feature_branch
```

What this does is use the `git checkout` command and the `-b` flag to create and checkout a new branch
named whatever follows `-b`. We have created a branch called `my_feature_branch`. The reason I have
called it a feature branch specifically is because this is a common flow for managing an application's
development with multiple authors. Let's see what changed:

```bash
  .git/
   branches/
    hooks/
    info/
      exclude
    logs/
    refs/
      heads/
        master
        my_feature_branch
      HEAD
    objects/
      08/
      29/
      7a/
      info/
      pack/
    refs/
      heads/
        master
        my_feature_branch
      tags/
    COMMIT_EDITMSG
    config
    description
    HEAD
    index
  README.md
```

Now, if you look at `.git/branches/refs/heads/` we can see we have added `my_feature_branch`. If we
look at our `HEAD` files, we will see an addition to it as well.

`.git/logs/refs/HEAD`
```
0000000000000000000000000000000000000000 0812517d73636573118a6eef9151688be148885b bobby grayson <bobbygrayson@gmail.com> 1433706112 -0400	commit (initial): initial commit
0812517d73636573118a6eef9151688be148885b 0812517d73636573118a6eef9151688be148885b bobby grayson <bobbygrayson@gmail.com> 1433796852 -0400	checkout: moving from master to my_feature_branch
```

`.git/refs/HEAD`
```bash
ref: refs/heads/my_feature_branch
```

It has logged our checkout and pointed us at the new branch. Also it is worth noting we have not created
any new objects. This is one of the finer pieces of git, it is differentials rather than copies and 
copies as one would have saving `my_documentv1`, `my_documentv2`, `my_documentvN` etc.

Let's add another commit by creating a simple application in here and logging its boilerplate.

```
volt new some_project
cd some_project
git status
# => ./
```

Okay, lets add this project and commit. If you don't have Volt installed locally, feel free to substitute it
with anything from rails to django to meteor. It doesn't really matter for our studies here.

```bash
cd ..
git add some_project
git commit -m 'add boilerplate volt project'
```

Now, let us further check out our changes in the git file tree:

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
            my_feature_branch
        HEAD
    objects/
      02/
      04/
      05/
      08/
      0e/
      11/
      22/
      24/
      25/
      29/
      32/
      34/
      38/
      4d/
      5c/
      5d/
      60/
      65/
      69/
      77/
      7a/
      83/
      88/
      8b/
      8e/
      92/
      9e/
      b5/
      b7/
      c4/
      db/
      e2/
      e8/
      ef/
      f6/
      f8/
      f9/
      fe/
      ff/
      info/
      pack/
    refs/
      heads/
          master
          my_feature_branch
      tags/
      COMMIT_EDITMSG
      config
      description
      HEAD
      index
  some_project/
    README.md
```

Now, if we look at `COMMIT_EDITMSG`

```
add boilerplate volt project
```

And again it is our latest message. The other major change is we have a ton of new objects.
Just to see what happens, let's checkout master and see if anything changes:

```bash
git checkout master
```

and we get:

```
  .git/
    branches/
    hooks/
    info/
        exclude
    logs/
      refs/
        HEAD
    objects/
      02/
      04/
      05/
      08/
      0e/
      11/
      22/
      24/
      25/
      29/
      32/
      34/
      38/
      4d/
      5c/
      5d/
      60/
      65/
      69/
      77/
      7a/
      83/
      88/
      8b/
      8e/
      92/
      9e/
      b5/
      b7/
      c4/
      db/
      e2/
      e8/
      ef/
      f6/
      f8/
      f9/
      fe/
      ff/
      info/
      pack/
    refs/
      heads/
      tags/
      COMMIT_EDITMSG
      config
      description
      HEAD
      index
  some_project/
    README.md

```

So we have the same thing, but our `HEAD` file reads:

```
ref: refs/heads/master
```

So we can now see this is our constant anchor as we navigate changes.

Let's checkout our feature branch again

```bash
git checkout my_feature_branch
```

## Remotes
Now, let's say we want to add a remote so that we can back up our work online. To do this is quite
simple, we simply create the repository on Github, Bitbucket, or whatever service you choose, and
retrieve the URL for the git repository. On Github after creating a repo called `git_test` this is
what adding the remote looks like for me:

```bash
git remote add origin https://github.com/ybur-yug/git_test.git
git push -u origin master
```

Now, I will have pushed all of the changes to that repository. Let's see what changes it has induced
in our directory:

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
          my_feature_branch
        remotes/
          origin/
            master
      HEAD
    objects/
    refs/
      heads/
        master
        my_feature_branch
      remotes/
        origin/
          master
    tags/
    COMMIT_EDITMSG
    config
    description
    HEAD
    index
```

Not a ton of changes now. We simply head added a `remotes` directory in refs, and also in `logs/refs`.
Again, without any duplication we have made it even more simple and cohesive to back up and alter our
project.

Now, let's make a change in the remote and attempt to pull and merge it. In the web editor on Github
I will make some trivial change in the `README.md`.

`README.md`
```markdown
...
la dee dahh, this document is different now
```

#### [TODO] Objects

#### [TODO] Stashes

#### [TODO] Cherry Picking

#### [TODO] Rebasing

#### [TODO] Part 2...
