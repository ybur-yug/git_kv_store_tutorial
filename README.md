# Exploring Git: From git init to a KV store

#### This is a talk I'm giving soon at [ColumbusRB](http://columbusrb.com), the slides can be found [here](http://slides.com/bobbygrayson/deck-1/live#/)

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

```
$ tree .git

.git
├── branches
├── config
├── description
├── HEAD
├── hooks
│   ├── applypatch-msg.sample
│   ├── commit-msg.sample
│   ├── post-update.sample
│   ├── pre-applypatch.sample
│   ├── pre-commit.sample
│   ├── prepare-commit-msg.sample
│   ├── pre-push.sample
│   ├── pre-rebase.sample
│   └── update.sample
├── info
│   └── exclude
├── objects
│   ├── info
│   └── pack
└── refs
    ├── heads
        └── tags

9 directories, 13 files
```

A note, you may need to install tree depending on your OS. On Ubuntu, I used

`sudo apt-get install tree`

I imagine it is about the same on mac with `brew`. I Have no idea on Windows as I barely know how to list
a directory in Powershell (sorry).

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

`.git/config`
```
[core]
repositoryformatversion = 0
filemode = true
bare = false
logallrefupdates = true
```
It would appear this is just some general configuration for a boilerplate initialized repo.

`.git/description`
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
$ tree .git
.git
├── branches
├── COMMIT_EDITMSG
├── config
├── description
├── HEAD
├── hooks
│   ├── applypatch-msg.sample
│   ├── commit-msg.sample
│   ├── post-update.sample
│   ├── pre-applypatch.sample
│   ├── pre-commit.sample
│   ├── prepare-commit-msg.sample
│   ├── pre-push.sample
│   ├── pre-rebase.sample
│   └── update.sample
├── index
├── info
│   └── exclude
├── logs
│   ├── HEAD
│   └── refs
│       └── heads
│           └── master
├── objects
│   ├── 1b
│   │   └── f567a9cee63cd3036628c1519b818461905b27
│   ├── 9d
│   │   └── aeafb9864cf43055ae93beb0afd6c7d144bfa4
│   ├── c1
│   │   └── 2d7c0ed49ad9c7aa938743ba6fdee54b6b7fe1
│   ├── info
│   └── pack
└── refs
  ├── heads
      │   └── master
          └── tags

15 directories, 21 files
```

It appears we have some simple additions with adding one file. To start, we have expanded our
info directory to now include a `logs` directory. We also have several subdirectories inside of our
`objects` directory now, each containing a hash. refs subdirectory `heads` now includes a
`master` file, and we also have added `COMMIT_EDITMSG`, and index at the root level of `.git`.

If we examine `COMMIT_EDITMSG` we see:

```
initial commit

```

Logging our commit message.



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
$ tree .git
.git
├── branches
├── COMMIT_EDITMSG
├── config
├── description
├── HEAD
├── hooks
│   ├── applypatch-msg.sample
│   ├── commit-msg.sample
│   ├── post-update.sample
│   ├── pre-applypatch.sample
│   ├── pre-commit.sample
│   ├── prepare-commit-msg.sample
│   ├── pre-push.sample
│   ├── pre-rebase.sample
│   └── update.sample
├── index
├── info
│   └── exclude
├── logs
│   ├── HEAD
│   └── refs
│       └── heads
│           ├── master
│           └── my_feature_branch
├── objects
│   ├── 1b
│   │   └── f567a9cee63cd3036628c1519b818461905b27
│   ├── 9d
│   │   └── aeafb9864cf43055ae93beb0afd6c7d144bfa4
│   ├── c1
│   │   └── 2d7c0ed49ad9c7aa938743ba6fdee54b6b7fe1
│   ├── info
│   └── pack
└── refs
    ├── heads
        │   ├── master
            │   └── my_feature_branch
                └── tags

15 directories, 23 files
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
mkdir test && echo "test" > test/file.txt
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
.git
├── branches
├── COMMIT_EDITMSG
├── config
├── description
├── HEAD
├── hooks
│   ├── applypatch-msg.sample
│   ├── commit-msg.sample
│   ├── post-update.sample
│   ├── pre-applypatch.sample
│   ├── pre-commit.sample
│   ├── prepare-commit-msg.sample
│   ├── pre-push.sample
│   ├── pre-rebase.sample
│   └── update.sample
├── index
├── info
│   └── exclude
├── logs
│   ├── HEAD
│   └── refs
│       └── heads
│           ├── master
│           └── my_feature_branch
├── objects
│   ├── 1b
│   │   └── f567a9cee63cd3036628c1519b818461905b27
│   ├── 2b
│   │   └── 297e643c551e76cfa1f93810c50811382f9117
│   ├── 5e
│   │   └── c1f4ac6015a50b5d8462582d7ae50d7029d012
│   ├── 70
│   │   └── cc10cfcc770f6b0ea11cdd9a876ee1a3184d77
│   ├── 9d
│   │   └── aeafb9864cf43055ae93beb0afd6c7d144bfa4
│   ├── c1
│   │   └── 2d7c0ed49ad9c7aa938743ba6fdee54b6b7fe1
│   ├── info
│   └── pack
└── refs
    ├── heads
        │   ├── master
            │   └── my_feature_branch
                └── tags

18 directories, 26 files
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

and we get the same tree, but we can check out our HEAD item in the `.git` directory.

So we have the same thing, but our `HEAD` file reads:

```
ref: refs/heads/master
```

So we can now see this is our constant anchor as we navigate changes.

Let's checkout our feature branch again

```bash
git checkout my_feature_branch
```

## Objects
```bash
$ find .git/objects
.git/objects/pack
.git/objects/info
.git/objects/b7
.git/objects/b7/37ff03e6f22c28bc4786f4b11925f2d864e00
...
.git/objects/4c
.git/objects/4c/2be36223ca4d07cbd7ce8c28419ba1c4144334
```

Here we see a list of a butt ton of what looks like SHA-1 hashes. So what is git doing with all of
these?

Let's create a clean repository and proceed to start an empty git repo in it.

`cd .. && mkdir git_testing`

Initialize a repository

`git init`

And now, let's try creating one of these hash objects. We do this with the git command `hash-object`.
If we simply use some bash, we can do this without even needing a file. By doing this we will pipe
an echoed statement into the hash-object command through stdin and receive our hash in stdout.

`echo 'test content' | git hash-object -w --stdin`

```bash
d670460b4b4aece5915caf5c68d12f560a9fe3e4
```

So, it took the string 'test content' and hashed it then spat it back out in SHA-1 form. Cool.

We should examine this further.

`echo "test" > test.txt`

`git hash-object -w test.txt`

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

If we dive in to do the reverse of this, we can look up our input using git's `cat-file` command, which
intakes a hash.

`git cat-file 6375a2690c50e28c8c351fc552e2fd8a24b01031`

```
test
test 2
```

Now, if we make another change on this, we will be able to see the new version.

`vim test.txt`
```
test "one"
```

If we delete everything and replace it with this line, the do our typical:

`git hash-object -w test.txt`

We get a new hash, which when called with `cat-file` will output a the new value,
while still keeping our old object in history.

What this really is at it's core is a key:value store. Using this, we can leverage
a very simple database that only relies on single key/value types (symbol, string)
to store any data we need to and look it up. So, let's move on.

## Aside: Git: A Directed Acyclic Graph
In the broadest of terms, git is a [directed acyclic graph](https://en.wikipedia.org/wiki/Directed_acyclic_graph). This sounds quite fancy and/or
scary depending on how hard in the paint you go with mathematica, but it truly isn't that crazy. 
Let's ignore Wikipedia's terse entry, and instead break it down on our own.

## Storage
In its most basic state, git functions to make one of these graphs connecting a series of objects. These
objects also have a handful of types.

### Types

#### Blob
A `blob` is a blob of bytes. It usually is a file, but can also be a symlink or a myriad of other things.
It is all simply semantics as long as there is a pointer to the `blob`.

#### Tree
Directories are represented by a `tree` object. They refer to `blobs` and other `trees`. When one of these nodes
(a `tree` or a `blob`, in this case) points to another in the graph, it *depends* on that node. It is a connection
that cannot be broken. You can garbage collect, filesystem check, and a myriad of other functions
but we do not need to truly know more other than that without a referent of dependence, a node
is essentially useless, as it is disconnected.

#### Commit
A `commit` refers to a tree that represents the state of a group of `blobs`' state at the time of that given
commit. It refers to a range `X` of other commits that are its parents. More than one parent means a merge,
no parent means an initial commit, and a single just means its a regular old commit. As we saw earlier,
the body of a commit is its message.

#### Refs
Refs have two functions: storing `HEAD`s, and `branches`. They are essentially notes left on a given
node. These notes can be moved around freely and arent stored in history, and arent transferred
between repositories. They are simply a means to namespace 'I am working here'.

#### Visualizing It
[A Typical remote/local DAG](http://eagain.net/articles/git-for-computer-scientists/git-history.6.dot.svg)

As you can see, these nodes form a `tree` of functioning between master and a remote with a few merges
thrown in (any of the nodes with 2 parents).

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

### Accessing Object History
Currently we are only returning the latest version of a given item. However, we have already stored it at every
state it hash ever been hashed. So, if we were to add in some functionality for grabbing versions, it would be
quite simple.

```ruby
module GitDatabase
  class Database
    attr_accessor :items
    def initialize
      `git init`
      @items = {}
    end

    def set(key, value)
      unless key in @items.keys
        @items[key] = [hash_object(value)]
      else
        @items[key] << value
      end
    end

    def get(key)
      cat_file(@items[key.to_s].first)
    end

    def get_version(key, version)
      # 0 = latest, numbers = older
      @items[key][version]
    end
    
    def versions(key)
      @items[key].count
    end
    
    private
    
    def hash_object(data)
      `echo #{data.to_s} | git hash-object -w --stdin`.strip!
    end

    def cat_file(hash)
      `git cat-file -p #{hash}`
    end
  end
end
```

Now, we can do something like:

```ruby
db = GitDatabase::Database.new
db.set("Apples", "12")
db.get("Apples")
# => "12"
db.set("Apples", "10")
db.get_version("Apples", 0)
# => "12"
db.get("Apples")
# => "10"
```

Abd to use this. We can make a very simple sinatra API to take input remotely:

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
