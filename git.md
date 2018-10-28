Git
===

Index
-----

1. [Install and Setup](#s1)
2. [HowTos](#s2)
   1. [Combine Multiple Git Repos](#s2.1)
   2. [Use logs](#s2.2)
   3. [Work With Relative Refs](#s2.3)
3. [Other Useful Resources](#s3)
4. [Glossary](#s4)

<a name="s1">1: Install and Setup</a>
-------------------------------------

### 1: Install git ###
Update the apt package cache

```console
sudo apt-get update
```

Install git

```console
sudo apt-get install git
```

### <a name="s1.2">2: Configure global settings</a> ###
username:

```console
git config --global user.name "<name>"
```

user.email:

```console
git config --global user.email <email_address>
```

Note that you can view git's settings using the `-l` (list) argument, and additionally view the source of the settings with 
the `--show-origin` flag:
```console
$ git config -l --show-origin
file:/home/userName/.gitconfig      user.name=UserName
file:/home/userName/.gitconfig      user.email=user@email.com
file:.git/config        remote.origin.url=git@github.com:UserName/some_repo.git
```

In the above example, the first two settings are globals (saved in `$HOME/.gitconfig`), while the third is a repo-level setting.

You can also edit the ~/.gitconfig file directly. It has the following format:
```
[user]
	name = UserName
	email = user@email.com
[push]
	default = simple
[alias]
    hist = log --pretty=format:'%h %ad | %s%d [%an]' --graph --date=short
```


### 3: Configure system settings ###
Append the following to `~/.bashrc`:

```bash
# set $VISUAL and $EDITOR (including for child shells)
export EDITOR='vim'
export VISUAL='vim'
```

Reload bashrc:

```console
. ~/.bashrc
```

Confirm that bashrc gets loaded for [login][link01] sessions by checking .profile and .bash_profile:

```console
less ~/.bash_profile
less ~/.profile
```

One of them (at least) should load .bashrc.


### 4: Link SSH key with Github ###
a) Create a key (if there isn't one already at `~/.ssh/id_rsa.pub`)

```console
ssh-keygen -t rsa -b 4096 -C "<email_address>"
```

b) copy the contents of the created public key to the clipboard

c) go to github and select add a new SSH key, pasting the contents of the ssh key into the field


### 5: Clone a repo ###
```console
git clone git@github.com:<user_id>/<repo>.git
```


<a name="s2">2: HowTos</a>
--------------------------

###  <a name="s2.1">1: Combining Two Git Repos</a> ###

1. [Create a new empty repository New.](#s2.1.1)
2. [Make an initial commit because we need one before we do a merge.](#s2.1.2)
3. [Create a new branch for old repo A's files](#s2.1.3)
4. [Create a remote association to old repo A](#s2.1.4)
5. [Pull the remote's files](#s2.1.5)
6. Move all the remote's files into a subdirectory
7. Checkout master
8. Merge the branch back into master
9. Repeat steps 3–8 for all remaining old repos

#### <a name="s2.1.1">Create a new empty repository New</a> ####

```console
mkdir new
cd new
git init
```

#### <a name="s2.1.2">Make an Initial Commit</a> ####
This creates a new master branch.
```console
echo New > README.md
git add -A
git commit -m "Initial commit"
```

#### <a name="s2.1.3">Create a new branch for old repo A's files</a> ####
For clarity, branches are in snake case, remote names in Pascal case

```console
git checkout -b repo_a
```

#### <a name="s2.1.4">Create a remote association to old repo A</a> ####
`RepoA` is the name we are using to associate the remote repo's url inside this repo.

```console
git remote add -f RepoA https://www.git.files/path/to/repo
```

#### <a name="s2.1.5">Pull the remote's files</a> ####
Will probably need to explicitly allow unrelated histories

```console
git pull RepoA master --allow-unrelated-histories
```

Then will probably need to do a merge


### <a name="s2.2">Use Logs</a> ###

- `git log`  
  Lists all commits in reverse chronological order.  
  Example:  
  ```console
  $ git log
  commit 8da33089a28c38b2106caa6f89c761861e8dec97 (HEAD -> master)
  Author: UserName <user@email.com>
  Date:   Thu Oct 25 15:06:33 2018 +0100

      Added a comment
  ```

- `git log --pretty=oneline`  
  List all commits in reverse chronological order, showing only hash and commit message.  
  Example:
  ```console
  $ git log --pretty=oneline
  d91d1fd7a5a1ec809d7994a6d5c11409163429d3 (HEAD -> master, origin/master) Convert initial test to unittest test case
  ```
  
- You can add additional flags to further customise the output (these are mostly (all?) stackable), such as:
  ```console
  $ git log --pretty=oneline --max-count=2  # show just the last two commits
  $ git log --since='7 days ago' --until='5 minutes ago'  # show commits from the specified date range
  $ git log --author=<name>  # show commits only for the specified user, quotes optional, username or email accepted
  $ git log --all  # Pretend as if all the refs in refs/, along with HEAD, are listed on the command line as <commit>.
  ```

- Here is an example of a [custom format](http://gitimmersion.com/lab_10.html) 
  (see the link for detailed explanation of each of the flags):  
  ```console
  $ git log --pretty=format:'%h %ad | %s%d [%an]' --graph --date=short
  * 7bf0bf1 2018-09-28 | Added a comment (HEAD -> master) [Jim Weirich]
  * 9cf3f21 2018-09-28 | Added a default value [Jim Weirich]
  * 94e1b8b 2018-09-28 | Using ARGV [Jim Weirich]
  * f656098 2018-09-28 | First Commit [Jim Weirich]
  ```

### <a name="s2.3">Work with Relative References</a> ###

Instead of always using the commit hash, we can use a couple of symbols to reference commits relatively:
- `^`  
  Refers to the previous commit.  
  Example:  
  `HEAD^` - the previous commit before the current HEAD
- `~`  
  Refers to the commit *n* commits before the specified commit.  
  Example:  
  `v1~2` - the commit two commits prior to the one tagged `v1`.

We can also use tags (get a list of tags using `git tag`) to name a particular commit. Let's suppose we wanted to tag the current commit 
as `v1` and the immediately previous commit as `v1-beta`:
```console
$ git tag v1
$ git checkout v1^  # alternatively we could have used v1~1
Note: checking out 'v1^'.

You are in 'detached HEAD' state. You can look around, make experimental
<...>

HEAD is now at e283143 Handle case where no arg
$ git tag v1-beta
$ git checkout master
Previous HEAD position was e283143 Handle case where no arg
Switched to branch 'master'
$ git hist  # this is an alias, see s1.2 for how to create
* 951db27 2018-10-28 | Add comment' (HEAD -> master, tag: v1) [UserName]
* e283143 2018-10-28 | Handle case where no arg (tag: v1-beta) [UserName]
* d1ed2ea 2018-10-28 | Add command line arg [UserName]
* 25dff66 2018-10-28 | create ruby file [UserName]
* 48c6a21 2018-10-27 | initial commit [UserName]
```

<a name="s3">Other Useful Resources</a>
---------------------------------------

- [Git Flight Rules](https://github.com/k88hudson/git-flight-rules)  
  Inspired by NASA's flight rules for the space program, detailed step-by-step instructions for a wide range of git tasks


<a name="s4">Glossary</a>
-------------------------

- `detached`  
  A state where HEAD is not pointing to any branch. This occurs when checking out a specific commit instead of a branch. When detached
  any commits will also not belong to a branch. Note that while in the detached state, you can create a new branch using 
  `git checkout -b <branch-name>` and it will add all the commits in the tree from the checkout that caused the detached state through
  to the current commit into this new branch.
- `HEAD`  
  A pointer to the currently checked-out commit
- `master`  
  An arbitrary name that by convention refers to the main/default branch of the repo
- `origin`  
  An arbitrary name that by convention refers to the canonical version of the repo
- `remote`  
  A reference to a URL for a repo


[link01]: https://github.com/Crossroadsman/TerminalTips/blob/master/BashEnvironmentVariables.md
