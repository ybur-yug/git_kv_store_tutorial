# Exlporing Git: From git init to a KV store 

## Why?
It was a good excuse to get to know git's innards a bit better, as well as work on something
that, while somewhat useless, is technically functional and interesting.

## For Who?
Anyone with a casual knowledge of git shouldn't get too lost and hopefully learns something.
Ambitious beginners are more than welcome, and [tweet me](https://www.twitter.com/yburyug) if something comes up
that you think could make it better :)

## Beginning 
```bash
mkdir git_exploration
cd git_exploration
git init
```

This gets us a git repo created. Let's check out what we have:

```
ls -a
. .. .git

```

And if we check out the `.git` subdirectory in our editor:

`vim .git`
Note that I use vim+NERDTree, but any editor will do. Sublime Text, Textmate, RubyMine, Notepad++, etc will
work. All you need is a file tree browser to view this.

```
.git/
  branches/
  hooks/
  info/
    exclude
  objects
    info/
    pack/ refs/
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

#### HEAD
HEAD is a reference to the last commit in the current checked out branch. 

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
... 1433706112 -0400	commit (initial): initial commit
... 1433796852 -0400	checkout: moving from master to my_feature_branch
```

`.git/refs/HEAD`
```bash
ref: refs/heads/my_feature_branch
```

It has logged our checkout and pointed us at the new branch. Also it is worth noting we have not created
any new objects. This is one of the finer pieces of git, it is differentials rather than copies and 
copies as one would have saving `my_documentv1`, `my_documentv2`, `my_documentvN` etc.

Let's add another commit by creating a directory in here and logging its boilerplate.

```
mkdir test && echo "test > test/file.txt
cd test
git status
# => ./
```

Okay, lets add this project and commit. If you don't have Volt installed locally, feel free to substitute it
with anything from rails to django to meteor. It doesn't really matter for our studies here.

```bash
cd ..
git add test 
git commit -m 'add file + dir'
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
add file + dir
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
    refs/
      heads/
      tags/
      COMMIT_EDITMSG
      config
      description
      HEAD
      index
  test/
    file.txt

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

## Merging
Now, since we have completed some work in our feature branch, we should merge this feature into
master. This is quite simple, we will have no conflicts. To merge one branch with another we simply

`git merge branch_to_be_merged branch_to_merge_into`

or

`git merge my_feature_branch master`

And now we have our work in master again. Let's check out the git directory:

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
    ORIG_HEAD
  test/
    file.txt
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
  test/
    file.txt
```

Not a ton of changes now. We simply head added a `remotes` directory in refs, and also in `logs/refs`.
Again, without any duplication we have made it even more simple and cohesive to back up and alter our
project.

Now, let's make a change in the remote and attempt to pull and merge it. In the web editor on Github
I will make some trivial change in the `README.md`.

`README.md`
```markdown
# Git Explorinz
```

And commit it. Now, doing this we can make another change locally:

`vim README.md` line 1:
```markdown
...
la dee dahh, this document is different now
```

Now, if we commit this:

`git commit -am 'Trivial change'`
`commit bd7f97fbd35622a4d0c7540ed8f5e2598fffa455`

and try to push we will get an error saying we need to pull from the remote since it is at a different
state. To do this:

`git pull origin master`

Now, we have a conflict. If we look at what our conflict is in README.md we get:

```markdown
<<<<<<< HEAD
# Git Exploration

la dee dahh, this document is different now
=======
# Git Explorinz
>>>>>>> 8d0bedbda8bf7661687a7f1b47d178a1f2cdb9aa
```

Now this looks scary, but we can see the difference quite easily. One commit was editing the body,
while the other the title. We can resolve this by simply changing it to:

```markdown
# Git Explorinz

la dee dahh, this document is different now
```

And then, 

`git commit -am 'resolve merge conflict'`

`commit 099e761c18849bffe43090b58c18cf661c30fa1c`

Note that the use of `-am` is the equivalent of doing a `git add .` before using the `commit` command.

## Stashes
Now, sometimes you may have work you dont want to commit, but are quite interested in keeping for
use after a merge or pull. Enter `git stash`. It is exactly what it sounds like. Let's try something:

`vim README.md`

```markdown
...
This is a test repository. The password to the secret club is `swordfish`
```

But wait, we want to merge another remote change before we put this in. In the web UI on github we
will change `README.md` again:

```markdown
# GIT EXPLORINZ
...
```

Now, commit this in the web UI. But before we pull again:

`git status`
```bash
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   README.md

    no changes added to commit (use "git add" and/or "git commit -a")

```

Okay, now we stash that change:

`git stash`

And see our new status:

`git status`
```bash
On branch master
nothing to commit, working directory clean
```

And we can now pull:

`git pull origin master`

And we first have to resolve the merge conflict by keeping the web UI's all-caps title, and then we commit
that and apply our stash again by using:

`git stash apply`

This is the dead basics of stashes. But you can see the utility very clearly. Now, let's commit
our work and move into the next section: Objects.

`git commit -am 'detail clubs entry information'`

`git push origin master`

`commit eeec611ef4e608a94ea517c10c01daa500f73a57`


## Objects
```bash
$ find .git/objects
.git/objects/pack
.git/objects/info
.git/objects/b7
.git/objects/b7/37ffe03e6f22c28bc4786f4b11925f2d864e00
...
.git/objects/4c
.git/objects/4c/2be36223ca4d07cbd7ce8c28419ba1c4144334
```

Here we see a list of a butt ton of what looks like SHA-1 hashes. So what is git doing with all of
these?

Let's make a hashed git object.

`echo 'test content' | git hash-object -w --stdin`

```bash
d670460b4b4aece5915caf5c68d12f560a9fe3e4
```

So, it took the string 'test content' and hashed it then spat it back out in SHA-1 form. Cool.

We should examine this further.

```bash
mkdir test
cd test
git init
echo "test" > test.txt
```

`git hash-object -w test.txt`

`9daeafb9864cf43055ae93beb0afd6c7d144bfa4`

`vim test.txt`
```txt
test
test 2
```

If we save this change and run the command again:

`git hash-object -w test.txt`

`6375a2690c50e28c8c351fc552e2fd8a24b01031`

And if we check out our objects directory we can now see what git has done:

```bash
bobby@bobdawg-devbox:~/code/git_test/test$ find .git/objects/ -type f
.git/objects/9d/aeafb9864cf43055ae93beb0afd6c7d144bfa4
.git/objects/63/75a2690c50e28c8c351fc552e2fd8a24b01031
```

It has a hash for each of our objects saved. Woo! But wait. We haven't committed anything. How is git
tracking all this?

Well it turns out git just keeps some headers with these SHA-1's, and does a bunch of cool stuff so it
only has to track changes. Not entire new versions of each document. So each of these objects simply
represents a given state of some blob of our data.'

## Git as a Key:Value Store

### Note: Do not use this for real software

### Addendum: Apparently [crates.io](http://crates.io) does this, and those guys are wicked smart, so maybe its a good idea but definitely not at this capacity we are building

Since the `cat-file` and `hash_object` pattern functions simply as a key:value store for git, we
can utilize this to our advantage. Normal storing large strings in-memory in Ruby can get quite
taxing, but if we simply store the string of the SHA-1 hash to a given key, we can greatly reduce
the memory footprint of our master dictionary and allow it to grow far larger in size (theoretically).
So, let's code up a pseudo-class for this and fill it in after we get that far.

`vim git_database.rb`
```ruby
module GitDatabase
  class Database
    def initialize
      # set initliazers and master dictionary
    end

    def set
      # set a given key to a value
    end

    def get
      # get a given key's value
    end

    def hash_object
      # hash a given input that is coerced to a string
    end

    def cat_file
      # cat out a given file based on SHA-1 hash
    end
  end
end
```

We can now tackle this piece by piece.

#### Initializers

`vim git_database.rb`
```ruby
...
  class Database
    attr_accessor :items

    def initialize
      @items = {}
      `git init`
    end
...

```

Simple enough. We ensure we have a git repository initialized, and we ensure that we setup our
master dictionary.

#### Hashing
`vim git_database.rb`
```ruby
...
    def hash_object(string)
      # What do we do?
    end
...
```

Well, to start, lets fire up irb and see what we can do calling git from Ruby.

```
irb
irb(main):001:0> string = "test"
=> "test"
irb(main):002:0> `echo #{string}`
=> "test\n"
irb(main):003:0> `echo #{string} | git hash-object -w --stdin`
=> "9daeafb9864cf43055ae93beb0afd6c7d144bfa4\n"
irb(main):004:0> `echo #{string} | git hash-object -w --stdin`.strip!
=> "9daeafb9864cf43055ae93beb0afd6c7d144bfa4"
```

So, it appears we can essentially call exactly what we were prior. We can now reasonable change the
function to be:

```ruby
...
    def hash_object(data)
      `echo #{data} | git hash-object -w --stdin`.strip!
    end
...
```

And this will get that blob hashed up and stored for us. Now, notice we get the exact hash here, but if
we do a 

`find .git/objects -type f`

and look at a sampling of what we get:

```
.git/objects/e4/ea753518a47496350473b8eb0972ad2985d964
```

You might notice that objects has subdirectories of seemingly random 2 letter combos. There are the first 2
characters of the hash, but git does this to save on overhead. So, if looking in the git directory for hashes
you must account for the parent directory of the longer string to get the entire SHA-1.

#### Cattin'
Since the prior method returns us a hash directly, we can use the same command as earlier and interpolate.

```ruby
...
    def cat_file(hash)
      `git cat-file -p #{hash}`
    end
...
```

And now we just need a way to map keys to the hashes we have saved. 

#### Set
```ruby
...
    def set
      # get key, data
      # hash data
      # save key to SHA-1 hash in @items
    end
...
```

This is a reasonable fleshed out idea of a simple set implementation. So, first we need to take in a key:

```ruby
...
    def set(key, value)
      hash = hash_object(value.to_s)
      @items[key] = value
    end
...
```

And now, we can move onto a get implementation

#### Get
To get, we have a little more to do. We will have a key, and that gets us an SHA-1 hash. However,
we still need to decrypt it using our `cat_file` function. So, if we pseudocode this out:

```ruby
...
    def get 
      # find hash by key 
      # cat-file hash 
    end
...
```

So, with our functions already set up we can simply go in and do this:

```ruby
...
    def get(key)
      cat_file(@items[key.to_s])
    end
...

```

And now, we have a finished class that can function as a reasonable minimal database. Consider
it an equally ghetto but more interesting version of the good 'ole CSV store.

```ruby
module GitDatabase
  class Database 
    attr_accessor :items
    def initialize
      `git init`
      @items = {}
    end
   
    def set(key, value)
      hash = hash_object(value)
      @items[key] = hash 
    end

    def get(key)
      cat_file(@items[key.to_s])
    end

    def hash_object(data)
      `echo #{data.to_s} | git hash-object -w --stdin`.strip!
    end

    def cat_file(hash)
      `git cat-file -p #{hash}`
    end
  end
end
```

Now, to use this. We can make a very simple sinatra API to take input remotely:

```ruby
... # below the class
require 'sinatra'
require 'json'
DB = GitDatabase::Database.new
post '/set' do
  DB.set(params['key'], params['value']
  rescue
    { error: 'please send key and value parameters' }.to_json
  end
end

get '/get/:key' do
  { result: DB.get(params['key'] }.to_json
end
```

This is a very simple wrapper, but if gives the general idea of where you could take this with a toy application.


## Happy Hacking

