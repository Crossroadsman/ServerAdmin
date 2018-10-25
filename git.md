Git
===

Index
-----
[1 - Install and Setup](#s1)
[2 - HowTos]

<a name="s1">1: Install and Setup</a>
-------------------------------------

### 1: Install git ###
Update the apt package cache

```
sudo apt-get update
```

Install git

```
sudo apt-get install git
```

### 2: Configure global settings ###
username:

```
git config --global user.name "<name>"
```

user.email:

```
git config --global user.email <email_address>
```

### 3: Configure system settings ###
Append the following to `~/.bashrc`:

```
# set $VISUAL and $EDITOR (including for child shells)
export EDITOR='vim'
export VISUAL='vim'
```

Reload bashrc:

```
. ~/.bashrc
```

Confirm that bashrc gets loaded for [login][link01] sessions by checking .profile and .bash_profile:

```
less ~/.bash_profile
less ~/.profile
```

One of them (at least) should load .bashrc.


### 4: Link SSH key with Github ###
a) Create a key (if there isn't one already at `~/.ssh/id_rsa.pub`)

```
ssh-keygen -t rsa -b 4096 -C "<email_address>"
```

b) copy the contents of the created public key to the clipboard

c) go to github and select add a new SSH key, pasting the contents of the ssh key into the field


### 5: Clone a repo ###
```
git clone git@github.com:<user_id>/<repo>.git
```


2: HowTo 1: Combining Two Git Repos
-----------------------------------

1. [Create a new empty repository New.](#s1)
2. [Make an initial commit because we need one before we do a merge.](#s2)
3. [Create a new branch for old repo A's files](#s3)
4. [Create a remote association to old repo A](#s4)
5. [Pull the remote's files](#s5)
6. Move all the remote's files into a subdirectory
7. Checkout master
8. Merge the branch back into master
9. Repeat steps 3–8 for all remaining old repos

### <a name="s1">Create a new empty repository New</a> ###

```shell
mkdir new
cd new
git init
```

### <a name="s2">Make an Initial Commit</a> ###
This creates a new master branch.
```shell
echo New > README.md
git add -A
git commit -m "Initial commit"
```

### <a name="s3">Create a new branch for old repo A's files</a> ###
For clarity, branches are in snake case, remote names in Pascal case

```shell
git checkout -b repo_a
```

### <a name="s4">Create a remote association to old repo A</a> ###
`RepoA` is the name we are using to associate the remote repo's url inside this repo.

```shell
git remote add -f RepoA https://www.git.files/path/to/repo
```

### <a name="s5">Pull the remote's files</a> ###
Will probably need to explicitly allow unrelated histories

```shell
git pull RepoA master --allow-unrelated-histories
```

Then will probably need to do a merge



[link01]: https://github.com/Crossroadsman/TerminalTips/blob/master/BashEnvironmentVariables.md
